# Ansible_fetch, copy (파일복사)

---

## Fetch, Copy 모듈 사용

---

ansible의 Fetch, Copy 모듈은 linux의 scp 명령어 동작방식과 유사하다.

기본적인 모듈 사용에 관한 설명은 ansible 가이드 페이지에서 확인할 수 있다.

<fetct, copy>

[docs.ansible.com/ansible/2.3/fetch_module.html](https://docs.ansible.com/ansible/2.3/fetch_module.html)

[docs.ansible.com/ansible/2.4/copy_module.html](https://docs.ansible.com/ansible/2.4/copy_module.html)

## Fetch 모듈

---

fetch 모듈은 Remote Server에서 로컬로 파일을 복사할 때 사용한다.

![1](https://user-images.githubusercontent.com/84123877/189779235-e48d38af-b368-4acf-bfb7-b4346fddedb1.png)

> fetch 모듈 동작 방식
> 

### fetch 모듈 사용

```yaml
- name: Test Fetch
  hosts: {{ RemoteServer }}
  remote_user: ubuntu
  tasks:
    - name: Copying files from remote server
      fetch:
        src: "/path/to/RemoteServerFilePath"
        dest: "/path/to/localhostPath"
```

- src : Remote Server의 파일 위치
- dest : 복사할 localhost의 위치

## Copy 모듈

---

copy 모듈은 로컬의 파일을 Remote Server로 복사할 때 사용한다.

![2](https://user-images.githubusercontent.com/84123877/189779237-de254791-8492-4349-b738-e4914aeea86d.png)

> copy 모듈 동작 방식
> 

### Copy 모듈 사용

```yaml
- name: Test Copy
  hosts: {{ RemoteServer }}
  remote_user: ubuntu
  tasks:
    - name: Copying files from remote server
      copy:
        src: "/path/to/localhostFilePath"
        dest: "/path/to/RemoteServerPath"
```

- src : localhost의 파일 위치
- dest : 복사할 Remote Server의 위치

---
