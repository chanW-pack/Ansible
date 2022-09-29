# Ansible server NFS mount check

---

![Untitled](Ansible%20server%20NFS%20mount%20check%20c71b020868754d7e9278227aa4ffa464/Untitled.png)

```yaml
---
  - hosts: all
    name: server NFS mount check
    gather_facts: false
    become: true

    tasks:
      - name: server NFS mount check
        shell: df -h | grep 'NFS'
        ignore_errors: yes
        register: bin

    #  - debug:
    #     var: bin.stdout_lines

      - name: NFS not mount
        debug:
         msg: "NFS mount is not set."
        when: bin is failed

      - name: NFS settings nomal
        debug:
         msg: "NFS mount settings are cool."
        when: bin is changed
```

서버에 NFS mount가 잘 되었는지 확인 후 ,

when 조건문을 사용하여 결과에 다른 debug(msg)를 전송한다.

![Untitled](Ansible%20server%20NFS%20mount%20check%20c71b020868754d7e9278227aa4ffa464/Untitled%201.png)

> 현재
>