# Ansible_wait_for, wait_for_connection 사용

---

## wait_for 모듈

---

ansible의 wait_for 모듈은 특정 동작을 대기, 확인할 때 쓰이는 모듈이다.

Application의 상태를 확인하거나, 파일의 존재여부를 확인, 파일의 특정 내용의 유무 등을 확인하여 다음 동작의 여부를 판단하는데 주로 사용한다.

- wait_for 모듈 가이드 문서
= [docs.ansible.com/ansible/2.3/wait_for_module.html](https://docs.ansible.com/ansible/2.3/wait_for_module.html)

### Application 관련 사용

```yaml
tasks:
  - name: Waiting for application to start
    wait_for:
      port: "service port"
      delay: "delay seconds
```

- port : Application 실행 포트 (check할 포트)
- delay : 입력한 숫자의 초만큼 지난 후 실행

### 파일 관련 사용

```yaml
tasks:
  - name: check file
    wait_for:
      path: /path/to/file
      search_regex: "정규표현식"
```

정규표현식에 맞는 값을 찾아 진행

- wait_for : 체크할 파일의 경로
- search_regex: 정규표현식

```yaml
#  ex, 파일이 생성되면 다음 내용 진행
tasks:
  - name: check file
    wait_for:
      path: /path/to/file

# ex, 파일이 삭제되면 다음 내용 진행
tasks:
  - name: check file
    wait_for:
      path: /path/to/file
      state: absent
```

### wait_for_connection 모듈

---

wait_for_connection 모듈은 ansible playbook이 host에 접속할 때까지 대기하는 모듈이다.

```yaml
- name: Checking if the server is started
  hosts: "hostname"
  gather_facts: false
  remote_user: ubuntu
  tasks:
    - name: Check instance
      wait_for_connection:
        delay: "delay seconds"
        timeout: "timeout seconds"
```

- host : 접속할 host 명
- gather_facts : false로 설정하면 host에 입력된 주소로 ping 체크 비활성화, 이 옵션을 주지 않으면 접속 에러 발생
- remote_user : 접속할 user
- wait_for_connection : 서버가 실행될때까지 주기적으로 접속가능 여부 체크 (default timeout은 600초)
    - delay : 입력한 시간이 지나고 체크 실행 (초 단위)
    - timeout: timetout 설정 (초 단위)

wait_for_connection default로 사용하러면 delay와 timeout값을 입력하지 않고 사용하면 된다.

```yaml
- name: Checking if the server is started
  hosts: "hostname"
  gather_facts: false
  remote_user: ubuntu
  tasks:
    - name: Check instance
      wait_for_connection:
```

---