# Ansible When 조건문 트리구조 생성(includ_tasks)

---

저번 시간에는 조건문(when)을 활용해서 조건에 만족하는 경우에만 파일에 내용을 추가하는 기능을 테스트하였다.

하지만 단순히 yes or no 말고도 (조건에 만족하면 start, 아니면 stop) if ~~조건이면 ~~실행, @@조건이면 @@실행 등 조건을 나눠서 사용해보고 싶어졌다.

여러 when을 사용할 수 있는 방법을 조사해봤으나, 하나의 조건이라도 false이 나온다면 그 뒤에 명령들은 전부넘어가버렸다..

그 중 여러 when을 사용할 수 있는 방법을 찾아 해당 페이지로 공유를 해보려고 한다.

## 기능 구조

---

테스트는 EC2 3대를 활용하여 진행하겠다. (ansible 1, hosts 2)

hosts는 각각 chan 디렉터리, woo 디렉터리를 가지고 있다. (중복하지 않고 고유하게 지니고있음)

ansible server에서 chan 디렉터리가 있는 경우에는(if) 각 서버에 있는 local.txt에 a 내용을 추가하고, woo 디렉터리가 있는 경우에는 b 내용을 추가하는 방향으로 테스트해보겠다.

![Untitled](Ansible%20When%20%E1%84%8C%E1%85%A9%E1%84%80%E1%85%A5%E1%86%AB%E1%84%86%E1%85%AE%E1%86%AB%20%E1%84%90%E1%85%B3%E1%84%85%E1%85%B5%E1%84%80%E1%85%AE%E1%84%8C%E1%85%A9%20%E1%84%89%E1%85%A2%E1%86%BC%E1%84%89%E1%85%A5%E1%86%BC(includ_tasks%20d2757ff4fae740be81fc3445c4c1f39d/Untitled.png)

![Untitled](Ansible%20When%20%E1%84%8C%E1%85%A9%E1%84%80%E1%85%A5%E1%86%AB%E1%84%86%E1%85%AE%E1%86%AB%20%E1%84%90%E1%85%B3%E1%84%85%E1%85%B5%E1%84%80%E1%85%AE%E1%84%8C%E1%85%A9%20%E1%84%89%E1%85%A2%E1%86%BC%E1%84%89%E1%85%A5%E1%86%BC(includ_tasks%20d2757ff4fae740be81fc3445c4c1f39d/Untitled%201.png)

> /tmp/chan 생성한 hosts1(상단) , /tmp/woo 생성한 hosts2(하단)
> 

즉, 두 hosts server는 home 디렉터리에 동일하게 local 파일을 지니고 있고 서버에 chan 이 있다면 local에 chan 라인을 추가, woo가 있다면 woo 라인을 추가하는 기능이다.

![Untitled](Ansible%20When%20%E1%84%8C%E1%85%A9%E1%84%80%E1%85%A5%E1%86%AB%E1%84%86%E1%85%AE%E1%86%AB%20%E1%84%90%E1%85%B3%E1%84%85%E1%85%B5%E1%84%80%E1%85%AE%E1%84%8C%E1%85%A9%20%E1%84%89%E1%85%A2%E1%86%BC%E1%84%89%E1%85%A5%E1%86%BC(includ_tasks%20d2757ff4fae740be81fc3445c4c1f39d/Untitled%202.png)

```yaml
---
  - hosts: all
    name: file gogo
    gather_facts: false
    become: true

    tasks:
      - name: Check that the unarchive files exists
        stat:
          path: /tmp/woo/
        register: stat_result

      - name: chan check
        stat:
          path: /tmp/chan/
        register: stat_chan

      - name: woo go
        include_tasks: /home/ansible/0727/woo.yaml
        when: stat_result.stat.exists == True

      - name: chan go
        include_tasks: /home/ansible/0727/chan.yaml
        when: stat_chan.stat.exists == True
```

이렇게 트리 형식으로 조건에 맞게 yaml 파일들을 불러오면 매우 효율적이다.

(chan 디렉터리가 존재하면 chan.yaml 진행하는 식.)

![Untitled](Ansible%20When%20%E1%84%8C%E1%85%A9%E1%84%80%E1%85%A5%E1%86%AB%E1%84%86%E1%85%AE%E1%86%AB%20%E1%84%90%E1%85%B3%E1%84%85%E1%85%B5%E1%84%80%E1%85%AE%E1%84%8C%E1%85%A9%20%E1%84%89%E1%85%A2%E1%86%BC%E1%84%89%E1%85%A5%E1%86%BC(includ_tasks%20d2757ff4fae740be81fc3445c4c1f39d/Untitled%203.png)

```yaml
---
      - name: chan in line
        lineinfile:
          path: /home/local
          line: |

            chan directory
```

![Untitled](Ansible%20When%20%E1%84%8C%E1%85%A9%E1%84%80%E1%85%A5%E1%86%AB%E1%84%86%E1%85%AE%E1%86%AB%20%E1%84%90%E1%85%B3%E1%84%85%E1%85%B5%E1%84%80%E1%85%AE%E1%84%8C%E1%85%A9%20%E1%84%89%E1%85%A2%E1%86%BC%E1%84%89%E1%85%A5%E1%86%BC(includ_tasks%20d2757ff4fae740be81fc3445c4c1f39d/Untitled%204.png)

```yaml
---
      - name: woo in line
        lineinfile:
          path: /home/local
          line: woo directory
```

> 각각 chan.yaml과 woo.yaml의 내용이다. 조건에 해당될때만 진행될것이다.
> 

![Untitled](Ansible%20When%20%E1%84%8C%E1%85%A9%E1%84%80%E1%85%A5%E1%86%AB%E1%84%86%E1%85%AE%E1%86%AB%20%E1%84%90%E1%85%B3%E1%84%85%E1%85%B5%E1%84%80%E1%85%AE%E1%84%8C%E1%85%A9%20%E1%84%89%E1%85%A2%E1%86%BC%E1%84%89%E1%85%A5%E1%86%BC(includ_tasks%20d2757ff4fae740be81fc3445c4c1f39d/Untitled%205.png)

> chan go task와 woo go task가 각각 문제없이 진행되었다.
> 

![Untitled](Ansible%20When%20%E1%84%8C%E1%85%A9%E1%84%80%E1%85%A5%E1%86%AB%E1%84%86%E1%85%AE%E1%86%AB%20%E1%84%90%E1%85%B3%E1%84%85%E1%85%B5%E1%84%80%E1%85%AE%E1%84%8C%E1%85%A9%20%E1%84%89%E1%85%A2%E1%86%BC%E1%84%89%E1%85%A5%E1%86%BC(includ_tasks%20d2757ff4fae740be81fc3445c4c1f39d/Untitled%206.png)

> 각 local 파일에 조건에 맞는 라인이 추가된것을 확인할 수 있다.
> 

이렇게 여러 조건문을 tree형식으로 관리하게 된다면, 직관적이고 유지보수, 즉 관리 측면에서 매우 큰 도움이 될 거 같다.

여기까지 여러 when 조건문을 활용하는 기능을 테스트하였다.

그럼 20000

---