# Ansible server NFS mount Test

---

NFS mount를 확인하였고, 이제 NFS mount에 이상이 없는지 파일을 생성하여 테스트를 진행하겠다.

동일한 네임으로 파일을 생성하면 어느 서버에서 생성하였는지 구별이 어렵기 때문에

각 host server의 ip로 파일을 생성하겠다.

## ****Shell Scirpt (create test file)****

---

![Untitled](https://user-images.githubusercontent.com/84123877/193964452-7ce52e9b-e802-46e7-821f-b427ae868014.png)

```yaml
---
  - hosts: NFS
    name: server NFS mount test
    gather_facts: false
    become: true

    tasks:
      # NFS mount 위치를 변수로 저장
      - shell: df -h | grep ':' | awk '{print $6}'
        register: mp

      # hosts server IP를 변수로 저장
      - name: Get ip
        command: hostname -i
        register: public_ip

      # NFS mount 변수 값(위치)에 hosts server IP(이름)으로 테스트 파일 생성
      - file:
          path: "{{mp.stdout}}/test-{{public_ip.stdout}}"
          state: touch
```

두 변수로 file 모듈의 path(위치)를 지정할 때, 이슈가 있었는데,

문자열에 변수를 넣을 방법을 찾으며 처음에는 `path: mp.stdout/test-public_ip.stdout` 으로 진행하였으나, 오류가 발생했다.

그러나 변수를 나눠서 실행시키면, 변수 값을 잘 가져오는것을 보아하니 변수 저장과 불러오는것까지는 문제가 없었고, 변수 2개를 사용하는 형식에 문제가 있는듯했다.

### 해결

```
문자열(String Text) + '{{     }}'
    -> 'Nice to meet you {{ USERNAME }}'
    -> '{{ WELCOME_MSG }}' + '{{ USERNAME }}'

  "{{     }}" <--- Jinja2 template(replacement기능, 치환기능)
  -----------
  Django/Flask에서 사용 많이함
```

위 방식으로 실행시키니 문제 없이 기능이 동작하였다.

## 테스트 결과

---

![Untitled 1](https://user-images.githubusercontent.com/84123877/193964446-cf4f4f01-3ebd-408a-af6d-6928c191f80b.png)

![Untitled 2](https://user-images.githubusercontent.com/84123877/193964450-148a97a9-a0c2-4845-a145-6fc6e39fe956.png)

![Untitled 3](https://user-images.githubusercontent.com/84123877/193964451-c25624de-f27c-4174-a670-10523c556460.png)

생성이 완료되었다.

---
