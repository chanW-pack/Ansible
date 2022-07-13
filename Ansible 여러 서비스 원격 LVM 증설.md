# Ansible 여러 서비스 원격 LVM 증설

---

여러 서버들의 LVM을 증설해야하는 상황이 있었다.

하나하나의 서버마다 LVM 구성 파일시스템, free 용량, 증설작업을 진행하기에는 번거로우니 ansible playbook을 활용하여 일괄적으로 증설을 진행하는 기능을 테스트하겠다.

준비할 내용으로는,
대상 : 전체 서버 /log 
요청 : 50GB → 100GB

진행 순서

1. 전체 서버 /log 용량 확인 (100GB 초과한 LVM은 증설하지 않음)
2. Vfree 확인 (남아있는 LVM  VG 용량)
3. 파일 타입 확인 (ext.4, xfs, nfs …)
4. LVM 증설 및 적용 진행
5. 증설 적용 확인
6. Vfree 확인

해당 순서로 진행하겠다.

## 원격 LVM 증설

---

[LVM 용량 추가 및 축소](https://www.notion.so/LVM-d2bcf48df91342688e44e6ca57ef3f90)

> LVM 증설 및 축소에 대한 내용은 해당 페이지에서 확인 가능하다.
> 

![Untitled](Ansible%20%E1%84%8B%E1%85%A7%E1%84%85%E1%85%A5%20%E1%84%89%E1%85%A5%E1%84%87%E1%85%B5%E1%84%89%E1%85%B3%20%E1%84%8B%E1%85%AF%E1%86%AB%E1%84%80%E1%85%A7%E1%86%A8%20LVM%20%E1%84%8C%E1%85%B3%E1%86%BC%E1%84%89%E1%85%A5%E1%86%AF%205009256e03e045119213f0464cb4cd53/Untitled.png)

> 50GB의 LVM이 생성되어있다.
> 

하지만 아직 Vfree 용량과 파일시스템을 알 수 없기 때문에 함부러 증설을 진행할 수 없다.

ansible로 하나씩 진행하겠다.

![Untitled](Ansible%20%E1%84%8B%E1%85%A7%E1%84%85%E1%85%A5%20%E1%84%89%E1%85%A5%E1%84%87%E1%85%B5%E1%84%89%E1%85%B3%20%E1%84%8B%E1%85%AF%E1%86%AB%E1%84%80%E1%85%A7%E1%86%A8%20LVM%20%E1%84%8C%E1%85%B3%E1%86%BC%E1%84%89%E1%85%A5%E1%86%AF%205009256e03e045119213f0464cb4cd53/Untitled%201.png)

```bash
---
  - hosts: all
    name: lvm checker
    gather_facts: false
    become: true

    tasks:
      - name: lvm df, filesys, vfree check
        shell: |
          df -h | grep 'lvmdata'
          df -T | grep 'lvmdata'
          vgs

        register: command_output

      - debug:
         var: command_output.stdout_lines
```

![Untitled](Ansible%20%E1%84%8B%E1%85%A7%E1%84%85%E1%85%A5%20%E1%84%89%E1%85%A5%E1%84%87%E1%85%B5%E1%84%89%E1%85%B3%20%E1%84%8B%E1%85%AF%E1%86%AB%E1%84%80%E1%85%A7%E1%86%A8%20LVM%20%E1%84%8C%E1%85%B3%E1%86%BC%E1%84%89%E1%85%A5%E1%86%AF%205009256e03e045119213f0464cb4cd53/Untitled%202.png)

> 해당 playbook 으로 요청 디렉터리의 용량, 파일 타입, 남아있는 VG 용량까지 체크 가능하다.
> 

1. ~~전체 서버 /log 용량 확인 (100GB 초과한 LVM은 증설하지 않음)~~
2. ~~bfree 확인 (남아있는 LVM  VG 용량)~~
3. ~~파일 타입 확인 (ext.4, xfs, nfs …)~~
4. LVM 증설 및 적용 진행
5. 증설 적용 확인
6. bfree 확인

증설 및 적용은 LVM을 off 할 필요 없이 바로 alive 상태에서도 가능하기 때문에 간단하게 실행 가능하다.

![Untitled](Ansible%20%E1%84%8B%E1%85%A7%E1%84%85%E1%85%A5%20%E1%84%89%E1%85%A5%E1%84%87%E1%85%B5%E1%84%89%E1%85%B3%20%E1%84%8B%E1%85%AF%E1%86%AB%E1%84%80%E1%85%A7%E1%86%A8%20LVM%20%E1%84%8C%E1%85%B3%E1%86%BC%E1%84%89%E1%85%A5%E1%86%AF%205009256e03e045119213f0464cb4cd53/Untitled%203.png)

```bash
---
  - hosts: all
    name: lvm filesys checker
    gather_facts: false
    become: true

    tasks:
      - name: filesys check
        shell: |
          df -h | grep 'lvmdata'
          df -T | grep 'lvmdata'
          vgs

        register: command_output

      - debug:
         var: command_output.stdout_lines
```

![Untitled](Ansible%20%E1%84%8B%E1%85%A7%E1%84%85%E1%85%A5%20%E1%84%89%E1%85%A5%E1%84%87%E1%85%B5%E1%84%89%E1%85%B3%20%E1%84%8B%E1%85%AF%E1%86%AB%E1%84%80%E1%85%A7%E1%86%A8%20LVM%20%E1%84%8C%E1%85%B3%E1%86%BC%E1%84%89%E1%85%A5%E1%86%AF%205009256e03e045119213f0464cb4cd53/Untitled%204.png)

> 증설을 진행하였다. 결과 내용을 debug에 띄워놓아 증설 후 용량도 확인이 가능하다.
> 

![Untitled](Ansible%20%E1%84%8B%E1%85%A7%E1%84%85%E1%85%A5%20%E1%84%89%E1%85%A5%E1%84%87%E1%85%B5%E1%84%89%E1%85%B3%20%E1%84%8B%E1%85%AF%E1%86%AB%E1%84%80%E1%85%A7%E1%86%A8%20LVM%20%E1%84%8C%E1%85%B3%E1%86%BC%E1%84%89%E1%85%A5%E1%86%AF%205009256e03e045119213f0464cb4cd53/Untitled%205.png)

> 하지만 LV 증설은 완료되었지만 적용되지는 않은 모습. 적용은 파일시스템에 따라 달리지므로 주의한다.
> 

![Untitled](Ansible%20%E1%84%8B%E1%85%A7%E1%84%85%E1%85%A5%20%E1%84%89%E1%85%A5%E1%84%87%E1%85%B5%E1%84%89%E1%85%B3%20%E1%84%8B%E1%85%AF%E1%86%AB%E1%84%80%E1%85%A7%E1%86%A8%20LVM%20%E1%84%8C%E1%85%B3%E1%86%BC%E1%84%89%E1%85%A5%E1%86%AF%205009256e03e045119213f0464cb4cd53/Untitled%206.png)

```bash
---
  - hosts: all
    name: lvm filesys checker
    gather_facts: false
    become: true

    tasks:
      - name: filesys xfs_growfs
        shell: xfs_growfs /dev/cwtest/lvmdata
        register: command_output

      - debug:
         var: command_output.stdout_linesa
```

![Untitled](Ansible%20%E1%84%8B%E1%85%A7%E1%84%85%E1%85%A5%20%E1%84%89%E1%85%A5%E1%84%87%E1%85%B5%E1%84%89%E1%85%B3%20%E1%84%8B%E1%85%AF%E1%86%AB%E1%84%80%E1%85%A7%E1%86%A8%20LVM%20%E1%84%8C%E1%85%B3%E1%86%BC%E1%84%89%E1%85%A5%E1%86%AF%205009256e03e045119213f0464cb4cd53/Untitled%207.png)

> 본인은 xfs 파일시스템으로 진행하였으므로 xfs 형식대로 진행하였다.
> 

![Untitled](Ansible%20%E1%84%8B%E1%85%A7%E1%84%85%E1%85%A5%20%E1%84%89%E1%85%A5%E1%84%87%E1%85%B5%E1%84%89%E1%85%B3%20%E1%84%8B%E1%85%AF%E1%86%AB%E1%84%80%E1%85%A7%E1%86%A8%20LVM%20%E1%84%8C%E1%85%B3%E1%86%BC%E1%84%89%E1%85%A5%E1%86%AF%205009256e03e045119213f0464cb4cd53/Untitled%208.png)

> 다시 lvmCHECK.yaml 으로 확인하니 100GB로 잘 증설된것을 확인가능하다.
> 

---