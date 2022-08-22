# Ansible 반복문

---

ansible에서 반복문이란, task를 반복하는 것이다.

예를 들어 사용자 10 명을 만들어야 한다고 가정한다면, user 모듈로 진행할 때 task를 10개를 생성해야 한다.

반복문을 이용해 반복하는 작업을 효율적으로 줄일 수 있다.

ansible 2.4 까지는 with_* 키워드를 사용하여 작업했는데, ansible 2.5 부터는 좀 더 명확한 loop 키워드를 사용한다.

패키지 관련된 모듈은 반복문을 쓰지 않을것을 권장한다고 한다.

## wh

## loop 반복문

---

반복문을 사용하여 host 서버의 user를 삭제하는 실습을 진행해보겠다.

(user 모듈로 삭제를 진행한다.)

![2022-08-22 18 08 57.png](Ansible%20%E1%84%87%E1%85%A1%E1%86%AB%E1%84%87%E1%85%A9%E1%86%A8%E1%84%86%E1%85%AE%E1%86%AB%20129ff3c16a8348f1a88f1135e07978f2/2022-08-22_18_08_57.png)

```yaml
---
  - hosts: all
    name: no1.1
    gather_facts: false
    become: true

    vars:
      d_user:
        - cwcw1
        - cwcw2
        - cwcw3
        - cwcw4

    tasks:
      - name: Default user del
        user:
          name: "{{ item }}"
          state: "absent"
        loop:
          "{{ d_user }}"
```

> user 모듈에서 `state:absent` 옵션을 사용하여 사용자 삭제를 진행한다.
> 

사용자명는 d-user 변수에 저장되어 있고, 이를 반복문을 통해 하나씩 불러와 사용한다.

![Untitled](Ansible%20%E1%84%87%E1%85%A1%E1%86%AB%E1%84%87%E1%85%A9%E1%86%A8%E1%84%86%E1%85%AE%E1%86%AB%20129ff3c16a8348f1a88f1135e07978f2/Untitled.png)

> server B에 생성되어있는 cwcw1-4 user.
> 

![Untitled](Ansible%20%E1%84%87%E1%85%A1%E1%86%AB%E1%84%87%E1%85%A9%E1%86%A8%E1%84%86%E1%85%AE%E1%86%AB%20129ff3c16a8348f1a88f1135e07978f2/Untitled%201.png)

> 반복문으로 task가 순차적으로 진행된다.
> 

![Untitled](Ansible%20%E1%84%87%E1%85%A1%E1%86%AB%E1%84%87%E1%85%A9%E1%86%A8%E1%84%86%E1%85%AE%E1%86%AB%20129ff3c16a8348f1a88f1135e07978f2/Untitled%202.png)

> 성공적으로 cwcw1-4 user 가 삭제되었다.
> 

---