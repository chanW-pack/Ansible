# Ansible server NFS mount check

---

![Untitled](https://user-images.githubusercontent.com/84123877/192960291-35977d42-a267-4d90-9064-2616d3a13416.png)

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

![Untitled 1](https://user-images.githubusercontent.com/84123877/192960287-eaef79ba-17ff-4faa-b590-a025db4d99fd.png)

> 현재 전체 서버에 NFS mount가 잘 되어 있어, NFS not mount TASK는 skipping 된 것을 볼 수 있다.
>
