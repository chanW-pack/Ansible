# Ansible When 조건문 트리구조 생성(includ_tasks)

---

저번 시간에는 조건문(when)을 활용해서 조건에 만족하는 경우에만 파일에 내용을 추가하는 기능을 테스트하였다.

하지만 단순히 yes or no 말고도 (조건에 만족하면 start, 아니면 stop) if aa조건이면 aa실행, bb조건이면 @bb실행 등 조건을 나눠서 사용해보고 싶어졌다.

여러 when을 사용할 수 있는 방법을 조사해봤으나, 하나의 조건이라도 false이 나온다면 그 뒤에 명령들은 전부넘어가버렸다..

그 중 여러 when을 사용할 수 있는 방법을 찾아 해당 페이지로 공유를 해보려고 한다.

## 기능 구조

---

테스트는 EC2 3대를 활용하여 진행하겠다. (ansible 1, hosts 2)

hosts는 각각 chan 디렉터리, woo 디렉터리를 가지고 있다. (중복하지 않고 고유하게 지니고있음)

ansible server에서 chan 디렉터리가 있는 경우에는(if) 각 서버에 있는 local.txt에 a 내용을 추가하고, woo 디렉터리가 있는 경우에는 b 내용을 추가하는 방향으로 테스트해보겠다.

![Untitled](https://user-images.githubusercontent.com/84123877/181406169-38412387-82e8-4b7a-861c-41574f04314b.png)

![Untitled 1](https://user-images.githubusercontent.com/84123877/181406157-663f7d8c-df9d-43a8-b7b0-3d0f86c1c5be.png)

> /tmp/chan 생성한 hosts1(상단) , /tmp/woo 생성한 hosts2(하단)
> 

즉, 두 hosts server는 home 디렉터리에 동일하게 local 파일을 지니고 있고 서버에 chan 이 있다면 local에 chan 라인을 추가, woo가 있다면 woo 라인을 추가하는 기능이다.

![Untitled 2](https://user-images.githubusercontent.com/84123877/181406160-2419d7b3-0862-430a-b7d5-a642d69c044f.png)

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

![Untitled 3](https://user-images.githubusercontent.com/84123877/181406162-f00798fa-fa52-4ce6-85dd-ff67ff62f7a9.png)

```yaml
---
      - name: chan in line
        lineinfile:
          path: /home/local
          line: |

            chan directory
```

![Untitled 4](https://user-images.githubusercontent.com/84123877/181406163-158ce836-a01e-425a-bb3d-46da540080db.png)

```yaml
---
      - name: woo in line
        lineinfile:
          path: /home/local
          line: woo directory
```

> 각각 chan.yaml과 woo.yaml의 내용이다. 조건에 해당될때만 진행될것이다.
> 

![Untitled 5](https://user-images.githubusercontent.com/84123877/181406165-c0a1c323-c275-42b3-87dd-8df128f17390.png)

> chan go task와 woo go task가 각각 문제없이 진행되었다.
> 

![Untitled 6](https://user-images.githubusercontent.com/84123877/181406167-7f6eaee5-31cd-4803-b021-a8ab1a81b4b2.png)

> 각 local 파일에 조건에 맞는 라인이 추가된것을 확인할 수 있다.
> 

이렇게 여러 조건문을 tree형식으로 관리하게 된다면, 직관적이고 유지보수, 즉 관리 측면에서 매우 큰 도움이 될 거 같다.

여기까지 여러 when 조건문을 활용하는 기능을 테스트하였다.

그럼 20000

---
