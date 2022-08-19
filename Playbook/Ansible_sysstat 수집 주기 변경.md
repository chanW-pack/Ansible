# Ansible sysstat 수집 주기 변경

---

### sysstat 수집 주기 변경 playbook

현재 sysstat-collect.timer 파일을 생성하여 sysstat.service 파일을 참조하도록 적용되어 있고, 10분 주기로 성능 모니터링을 기록한다.

수집 주기를 10분에서 1분으로 변경을 위해서 
vi /usr/lib/systemd/system/sysstat-collect.timer = 10 → 1로 변경하고, systemctl restart를 적용하는  playbook을 작성하겠다.

![Untitled](https://user-images.githubusercontent.com/84123877/185519742-18aaccd4-993c-40d6-9e71-b46df4fd0672.png)

```bash
---
  - hosts: test
    name: server sysstat chage
    gather_facts: false
    become: true

    tasks:
      - name: sysstat-collect.timer unit, timer 10 -> 1 chage
        lineinfile:
          dest: '{{ item.dest }}'
          regexp: '{{ item.regexp }}'
          line: '{{ item.line }}'
          state: present
          backup: yes
        with_items:
          - { dest: '/usr/lib/systemd/system/sysstat-collect.timer',
          regexp: '^OnCalendar',
          line: 'OnCalendar=*:00/1' }

          - { dest: '/usr/lib/systemd/system/sysstat-collect.timer',
          regexp: 'Description=Run system activity accounting tool every 10 minutes',
          line: 'Description=Run system activity accounting tool every 1 minutes' }

       # notify:
       #   - restart sysstat

 #   handlers:
      - name: restart sysstat
        systemd:
          name: sysstat
          state: restarted

      - name: sysstat-collect.timer
        systemd:
          name: sysstat-collect.timer
          state: restarted

      - name: reload sysyemd
        systemd:
          daemon_reload: yes
```

### 기능 목적_(코드 해설)

1. 모듈을 활용하여(lineinfine) 파일내용 탐색 후 내용을 수정한다.
- 중간에 OnCalendar=*:00/10 내용을 OnCalendar=*:00/1 로 수정하는 사항이 있는데 해당 라인만 작동이 안되는 이슈 발생 
- 해당 라인을 앤서블 lineinfine 모듈이 찾지 못함 → 정규표현식에 관한 내용으로, 중간 * 문자(*=앞 문자와 매칭) 를 기능으로 작동하여 이슈가 발생했던 것
- ^ 정규 표현식을 사용하여 ^Oncalendar 로 해당 라인을 찾아 수정 가능하게 구현 완료
- backup 옵션으로 자동으로 수정 전 파일 백업화
         
2. 파일 수정한 뒤 적용하는 핸들러 생성 (systemctl restart  기능)
- 핸들러에 두 restarter 를 생성하고 진행하였으나 기능 작동 x, 
- systemctl restart syssstat-collect.timer는 deamon-reload도 같이 진행해야 작동이 됨
- deamon reload 핸들러도 생성하여 테스트 진행하였으나 미작동 이슈

1. 기능 구현 성공하였다. task로 진행하니  기능이 정상적으로 구현되었음. 
- 하지만 task가실행 되었을때 다음으로 systemctl restart가 진행되는 것이 더욱 안전하므로 결국 핸들러를 사용하는게 안전한 것 같다. 

!! 핸들러 사용시 유의점 !!

1. 플레이 내에서 같은 이벤트를 여러 번 호출하더라도 동일한 핸들러는 한 번만 실행됨
2. 모든 핸들러는 플레이 내에 모든 작업이 완료된 후에 실행됨
3. 핸들러는 이벤트 호출 순서에 따라 실행되는 것이 아니라 핸들러 정의 순서에 따라 실행됨

## 실행 결과

![Untitled 1](https://user-images.githubusercontent.com/84123877/185519750-db0ecc1e-0970-43b6-8993-a5d9b72afd49.png)

> 실행 전
> 

![Untitled 2](https://user-images.githubusercontent.com/84123877/185519751-cf4ed452-f2f7-4b42-b787-09fcca8267ef.png)

> 실행 후
> 

10분 주기로 기록되던 모니터링이 1분 주기로 변경이 완료되었다.

다만, deamon-reload를 진행하기 때문에 다른 사용중인 service를 잘 파악해야 한다.

추가로 핸들러를 잘 적용시키면 기능을 더 안전하게 구현될 수 있을 것 같다.

---
