# Ansible 여러 서비스 원격 LVM 증설

---

여러 서버들의 LVM을 증설해야하는 상황이 있었다.

하나하나의 서버마다 LVM 구성 파일시스템, free 용량, 증설작업을 진행하기에는 번거로우니 ansible playbook을 활용하여 일괄적으로 증설을 진행하는 기능을 테스트하겠다.

준비할 내용으로는, <br/>
대상 : 전체 서버 /log <br/>
요청 : 50GB → 100GB <br/>

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

[LVM 용량 추가 및 축소]
[Linux LVM 용량 추가 및 축소.md](https://github.com/chanW-pack/Linux_OS/blob/main/Linux%20LVM%20%EC%9A%A9%EB%9F%89%20%EC%B6%94%EA%B0%80%20%EB%B0%8F%20%EC%B6%95%EC%86%8C.md)

> LVM 증설 및 축소에 대한 내용은 해당 페이지에서 확인 가능하다.
> 

![Untitled](https://user-images.githubusercontent.com/84123877/178641979-59e75a7c-0c43-4e26-ae51-14eaa3a94edf.png)

> 50GB의 LVM이 생성되어있다.
> 

하지만 아직 Vfree 용량과 파일시스템을 알 수 없기 때문에 함부 증설을 진행할 수 없다.

ansible로 하나씩 진행하겠다. (기능을 하나  보려고 하는거니 합쳐서 진행해도 무관하다.)

![Untitled 1](https://user-images.githubusercontent.com/84123877/178641958-b941f50d-8514-48e3-bb2c-7cb9b3e0340d.png)

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

![Untitled 2](https://user-images.githubusercontent.com/84123877/178641962-22adc9bf-0f0b-4b57-8eea-ff4df152d442.png)

> 해당 playbook 으로 요청 디렉터리의 용량, 파일 타입, 남아있는 VG 용량까지 체크 가능하다.
> 

1. ~~전체 서버 /log 용량 확인 (100GB 초과한 LVM은 증설하지 않음)~~
2. ~~bfree 확인 (남아있는 LVM  VG 용량)~~
3. ~~파일 타입 확인 (ext.4, xfs, nfs …)~~
4. LVM 증설 및 적용 진행 <- 여기 진행중
5. 증설 적용 확인
6. bfree 확인

증설 및 적용은 LVM을 off 할 필요 없이 바로 alive 상태에서도 가능하기 때문에 간단하게 실행 가능하다.

![Untitled 3](https://user-images.githubusercontent.com/84123877/178641966-d679dfab-1994-426c-9489-b369dc167cf6.png)

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

![Untitled 4](https://user-images.githubusercontent.com/84123877/178641969-c668a13f-a046-4bed-9c99-36f1209fda28.png)

> 증설을 진행하였다. 결과 내용을 debug에 띄워놓아 증설 후 용량도 확인이 가능하다.
> 

![Untitled 5](https://user-images.githubusercontent.com/84123877/178641970-57a2f83a-6542-4163-ac26-8a627c35b695.png)

> 하지만 LV 증설은 완료되었지만 적용되지는 않은 모습. 적용은 파일시스템에 따라 달리지므로 주의한다.
> 

![Untitled 6](https://user-images.githubusercontent.com/84123877/178641971-a67ab97c-4600-4701-8da2-4c6f2dde4038.png)

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

![Untitled 7](https://user-images.githubusercontent.com/84123877/178641973-834db5a1-7191-4d4b-a706-200977496c34.png)

> 본인은 xfs 파일시스템으로 진행하였으므로 xfs 형식대로 진행하였다.
> 

![Untitled 8](https://user-images.githubusercontent.com/84123877/178641974-f7323cb3-e270-41b2-8caf-d1084252bded.png)


> 다시 lvmCHECK.yaml 으로 확인하니 100GB로 잘 증설된것을 확인가능하다.
> 

---
