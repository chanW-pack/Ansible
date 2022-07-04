# Ansible 작업 제어 구현 - 오류처리

---

## ansible playbook에서의 오류처리

---

ansible에서는 각 task의 return code를 평가하여 task의 성공 여부를 판단한다.
(**Ansible 은 0이 아닌 return code 를 수신**하거나 모듈에서 오류를 수신하면 해당 호스트에서 실행을 중지하고 다른 호스트로 넘어가게 된다.)

일반적으로 task에서 하나가 실패하는 즉시 ansible은 해당 호스트의 play를 중단하고 종료된다.
하지만 작업이 실패한다 하더라도 play를 계속할 수 있어야 한다.

(예로 특정 작업이 실패할 것으로 예상하고 몇가지 다른 작업을 조건부로 실행하여 복구하는 등)

Ansible 은 이러한 상황을 처리하고 원하는 동작, 출력 및 보고를 얻는 데 도움이 되는 설정을 제공한다.

## 작업 실패 무시하기 : ignore_errors

---

- 각각의 모듈 task에 대해 ignore_errors:yes 라고 명시할 수 있고, 이렇게 명시되면 task가 실패해도 무시하고 그래도 playbook이 진행된다.

```yaml
- name: latest version of notapkg is installed
  yum:
    name: notapkg
    state: latest
  ignore_errors: yes
```

> 해당 예시의 경우 notapkg를 설치하지 못하는 경우 실패할 것이지만 ignore_errors:yes 이므로 그대로 진행된다.
> 

<aside>
💡 본인도 여러 서버의 user 명을 찾는 실습을 진행하고 있었는데 ansible의 모든 값이 다 맞더라도 마지막 줄의 명령이 오류가 발생하면 전부 날아가는 성질덕분에 애좀 먹었다…. (return code 문제인데, 이 내용은 나중에 따로 상세히 작성하겠음)
그 때 ignore_errors를 명시하여 기능을 무사히 구현할 수 있었다.

</aside>

## 작업 실패 후 핸들러 강제 실행하기
: force handlers

---

- 만약 마지막 task가 실패하여 play가 중단되면, 이전 task에 대해 trigger된 모든 핸들러가 실행되지 않는다.

따라서 다른 task에서 작업이 실패해도 핸들러는 작동하게 하려면, force_handlers 지시문을 적용해야한다.

```yaml
---
- hosts: all
  force_handlers: yes

  tasks:
    - name: a task which always notifies its handler
      command: /bin/true
      notify: restart the database

    - name: "a task which fails because the package doesn't exist"
      yum:
        name: notapkg
        state: latest

  handlers:
    - name: restart the database
      service:
        name: mariadb
        state: restarted
```

## 작업 실패 조건 지정 : failed when

---

- 해당 기능은 명령이 성공했다고 하더라도 특정 조건을 만족하면 fail로 만들라는 **조건문**이다.

```yaml
tasks:
  - name: Run user createion script
    shell: /usr/local/bin/create_users.sh
    register: command_result
    failed_when: "'Password missing' in command_result.stdout"
```

> 해당 구문은 task가 성공해도 결과에 ‘Password missing’ 이라는 구문이 있다면 fail로 만들어버린다.
> 

task가 성공이므로 changed라고 나타나겠지만, 실제로는 password에 문제가 있는것이므로 문제가 있는 상태에서 핸들러가 작동할 수도 있다.

또 다른 방식으로 failed 모듈을 사용해 작업 실패를 강제할 수 있다.

```yaml
tasks:
  - name: Run user createion script
    shell: /usr/local/bin/create_users.sh
    register: command_result
    ignore_errors: yes

  - name: Report script failure
    fail:
      msg: "the password is missing in the output"
    when: "'Password missing' in the command_result.stdout"
```

> 위 내용과 결과는 동일하나, 조금 복잡하고 명확한 실패 메세지를 보낼 수 있다.
> 

fail 모듈과 조건문 when을 사용하여 task가 잘 되었다고 하더라도 Password missing 구문이 포함되면 해당 구문을 fail 시키고 정의해둔 메세지를 출력한다.

## 작업 결과를 changed로 보고하는 시점 지정 : changed when

---

### changed_when : false 사용

이 키워드는 task에서 changed 결과를 보고하는 시점을 제어할 수 있다.

changed가 나와야 하는 task에 대해서도 changed로 보고하지 않고 ok로 보고된다.

(true로 한다면 changed로 보고된다.)

![Untitled](Ansible%20%E1%84%8C%E1%85%A1%E1%86%A8%E1%84%8B%E1%85%A5%E1%86%B8%20%E1%84%8C%E1%85%A6%E1%84%8B%E1%85%A5%20%E1%84%80%E1%85%AE%E1%84%92%E1%85%A7%E1%86%AB%20-%20%E1%84%8B%E1%85%A9%E1%84%85%E1%85%B2%E1%84%8E%E1%85%A5%E1%84%85%E1%85%B5%208e4ed2ef58904455b72c5d99cab03367/Untitled.png)

```yaml
---
- name: play to setup web server
  hosts: all
  tasks:
    - name: latest httpd version installed
      yum:
        name: httpd
        state: absent
      register: uninstall_result
      changed_when: false
    - name: debug install_result
      debug:
        var: uninstall_result
```

> 분명하게 변경이 있었지만, (삭제작업수행) ok라고 나타난다.
> 

### changed_when: 조건문 사용

when이 붙었으므로 이것도 조건문을 사용할 수 있다. 이 조건이 맞으면, 즉 TRUE 이면 changed로 보고한다.

모든 모듈에 이 지시문을 사용할 수 있지만, 일반적으로 command, shell, raw 같이 무조건 changed로 보고되는 모듈에 대해 결과에 대한 반환값을 컨트롤하기 위해 자주 사용된다.

```yaml
---
- name: changed_when test
  hosts: web.example.com
  tasks:
    - shell:
        cmd: /tmp/upgrade-database
      register: command_result
      changed_when: "'completed' in command_result.stdout"
      notify:
        - restart_httpd

    - debug:
        var: command_result

  handlers:
    - name: restart_httpd
      service:
        name: httpd
        state: restarted
```

> 현재 httpd와 mariaDB가 설치되어 있고, database를 업그레이드하면 httpd를 재시작하는 핸들러를 가진다.
> 

shell 모듈을 썼으므로 결과는 무조건 changed로 나올 것이다. 
여기서 upgrade-database라는 프로그램의 결과가 "changed failed" 라고 가정하면 (문제 발생을 가정한다). 즉 문제가 발생하면 handler는 실행되면 안된다. 
따라서 위와 같이 changed_when: 조건이 completed가 결과에 포함되어있으면 false를 출력하도록 ansible-playbook을 만든다. 이렇게 false가 되면 ok를 출력하게 된다. 따라서 핸들러를 실행하지 않는다.

---