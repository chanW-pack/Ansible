# [Ansible] 앤서블(Ansible) Playbook 만들기 - update, shutdown, 파일 복사하기

---

[](https://www.notion.so/Ansible-Ansible-ec8bed213ab742e2bb3f032299a75e49)

지난 페이지 호스트 명령 내리기에서 이어 진행하므로, 준비 내용물은 같다.(서버4대)

## 모든 서버 update 하기

---

/home/ec2-user/.ssh/test/playbook

경로로 svc_update.yml 파일을 만들어준다.

(command 창 명령어로는 sudo yum update)

![Untitled](%5BAnsible%5D%20%E1%84%8B%E1%85%A2%E1%86%AB%E1%84%89%E1%85%A5%E1%84%87%E1%85%B3%E1%86%AF(Ansible)%20Playbook%20%E1%84%86%E1%85%A1%E1%86%AB%E1%84%83%E1%85%B3%E1%86%AF%E1%84%80%E1%85%B5%20-%20up%207d2d7ecdb3fd46c6a566465aa4e02b1e/Untitled.png)

```bash
---
- name: all server update
  hosts: all

  tasks:
  - name: upgrade all packages
    yum:
      name: '*'
      state: latest
```

![Untitled](%5BAnsible%5D%20%E1%84%8B%E1%85%A2%E1%86%AB%E1%84%89%E1%85%A5%E1%84%87%E1%85%B3%E1%86%AF(Ansible)%20Playbook%20%E1%84%86%E1%85%A1%E1%86%AB%E1%84%83%E1%85%B3%E1%86%AF%E1%84%80%E1%85%B5%20-%20up%207d2d7ecdb3fd46c6a566465aa4e02b1e/Untitled%201.png)

```bash
$ ansible-playbook svc_update.yml
```

명령어로 playbook을 실행시켰다.

근데 서버들이 전부 update가 이미 된 상태라 진행되지 않았다.

## Ansible 파일을 복사해서 host 서버에 저장‘

---

![Untitled](%5BAnsible%5D%20%E1%84%8B%E1%85%A2%E1%86%AB%E1%84%89%E1%85%A5%E1%84%87%E1%85%B3%E1%86%AF(Ansible)%20Playbook%20%E1%84%86%E1%85%A1%E1%86%AB%E1%84%83%E1%85%B3%E1%86%AF%E1%84%80%E1%85%B5%20-%20up%207d2d7ecdb3fd46c6a566465aa4e02b1e/Untitled%202.png)

> cwCool.txt 이라는 이름의 텍스트 파일을 만들어 내용을 입력했다.
> 

ansible 서버에 존재하는 cwCool.txt의 경로는 /home/ec2-user/.ssh/test/cwCool.txt 이고,
host 서버에 저장할 위치는 /home/ec2-user/cwCool.txt 이다.

![Untitled](%5BAnsible%5D%20%E1%84%8B%E1%85%A2%E1%86%AB%E1%84%89%E1%85%A5%E1%84%87%E1%85%B3%E1%86%AF(Ansible)%20Playbook%20%E1%84%86%E1%85%A1%E1%86%AB%E1%84%83%E1%85%B3%E1%86%AF%E1%84%80%E1%85%B5%20-%20up%207d2d7ecdb3fd46c6a566465aa4e02b1e/Untitled%203.png)

> cp_file.yml 파일 내용을 작성했다.
> 

```bash
---
# copy ansible file to host server
- hosts: all
  remote_user: ec2-user
  
  tasks:
  - name: copy file to host server
    copy:
      src: /home/ec2-user/.ssh/test/cwCool.txt
      dest: /home/ec2-user/cwCool.txt
      backup: yes
```

![Untitled](%5BAnsible%5D%20%E1%84%8B%E1%85%A2%E1%86%AB%E1%84%89%E1%85%A5%E1%84%87%E1%85%B3%E1%86%AF(Ansible)%20Playbook%20%E1%84%86%E1%85%A1%E1%86%AB%E1%84%83%E1%85%B3%E1%86%AF%E1%84%80%E1%85%B5%20-%20up%207d2d7ecdb3fd46c6a566465aa4e02b1e/Untitled%204.png)

> host 서버로의 복사가 성공적으로 완료되었다.
> 

![Untitled](%5BAnsible%5D%20%E1%84%8B%E1%85%A2%E1%86%AB%E1%84%89%E1%85%A5%E1%84%87%E1%85%B3%E1%86%AF(Ansible)%20Playbook%20%E1%84%86%E1%85%A1%E1%86%AB%E1%84%83%E1%85%B3%E1%86%AF%E1%84%80%E1%85%B5%20-%20up%207d2d7ecdb3fd46c6a566465aa4e02b1e/Untitled%205.png)

> host 서버에 들어가서 확인하면 잘 복사 되었음을 확인할 수 있다.
> 

내용도 확인할 수 있다. 찬우 베리 쿨~

![Untitled](%5BAnsible%5D%20%E1%84%8B%E1%85%A2%E1%86%AB%E1%84%89%E1%85%A5%E1%84%87%E1%85%B3%E1%86%AF(Ansible)%20Playbook%20%E1%84%86%E1%85%A1%E1%86%AB%E1%84%83%E1%85%B3%E1%86%AF%E1%84%80%E1%85%B5%20-%20up%207d2d7ecdb3fd46c6a566465aa4e02b1e/Untitled%206.png)

> cwCool.txt 내용을 수정하였다.
> 

![Untitled](%5BAnsible%5D%20%E1%84%8B%E1%85%A2%E1%86%AB%E1%84%89%E1%85%A5%E1%84%87%E1%85%B3%E1%86%AF(Ansible)%20Playbook%20%E1%84%86%E1%85%A1%E1%86%AB%E1%84%83%E1%85%B3%E1%86%AF%E1%84%80%E1%85%B5%20-%20up%207d2d7ecdb3fd46c6a566465aa4e02b1e/Untitled%207.png)

> 다시 ansible 서버에서 ansible-playbook cp_file.yml 명령어를 입력한 뒤,
host 파일을 확인해보면 파일이 2개가 되어 있음을 확인할 수 있다.
> 

cwCool.txt에는 마지막으로 변경된 내용이 들어있고,

cwCool.txt.1140…~~~ 에는 첫 번째 파일을 만들었을때 날짜와 시간, 그래고
내용일 볼 수 있다.

## 모든 서버 shutdown 하기

---

명령어를 치는 ansible 서버를 제외한 모든 서버를 shutdown하기

![Untitled](%5BAnsible%5D%20%E1%84%8B%E1%85%A2%E1%86%AB%E1%84%89%E1%85%A5%E1%84%87%E1%85%B3%E1%86%AF(Ansible)%20Playbook%20%E1%84%86%E1%85%A1%E1%86%AB%E1%84%83%E1%85%B3%E1%86%AF%E1%84%80%E1%85%B5%20-%20up%207d2d7ecdb3fd46c6a566465aa4e02b1e/Untitled%208.png)

```bash
---
# stop server
- hosts: all
  become: yes

  tasks:
    - name: server shutdown
      command: /sbin/shutdown -h now
```

svc_stop.yml 이라는 이름으로 playbook을 생성하였다.

![Untitled](%5BAnsible%5D%20%E1%84%8B%E1%85%A2%E1%86%AB%E1%84%89%E1%85%A5%E1%84%87%E1%85%B3%E1%86%AF(Ansible)%20Playbook%20%E1%84%86%E1%85%A1%E1%86%AB%E1%84%83%E1%85%B3%E1%86%AF%E1%84%80%E1%85%B5%20-%20up%207d2d7ecdb3fd46c6a566465aa4e02b1e/Untitled%209.png)

```bash
ansible-playbook svc_stop.yml
```

명령어를 입력해서 실행시켜주었다.

외부에 있는 ubuntu를 제외한 인스턴스가 종료되었다.

![Untitled](%5BAnsible%5D%20%E1%84%8B%E1%85%A2%E1%86%AB%E1%84%89%E1%85%A5%E1%84%87%E1%85%B3%E1%86%AF(Ansible)%20Playbook%20%E1%84%86%E1%85%A1%E1%86%AB%E1%84%83%E1%85%B3%E1%86%AF%E1%84%80%E1%85%B5%20-%20up%207d2d7ecdb3fd46c6a566465aa4e02b1e/Untitled%2010.png)

![Untitled](%5BAnsible%5D%20%E1%84%8B%E1%85%A2%E1%86%AB%E1%84%89%E1%85%A5%E1%84%87%E1%85%B3%E1%86%AF(Ansible)%20Playbook%20%E1%84%86%E1%85%A1%E1%86%AB%E1%84%83%E1%85%B3%E1%86%AF%E1%84%80%E1%85%B5%20-%20up%207d2d7ecdb3fd46c6a566465aa4e02b1e/Untitled%2011.png)

---