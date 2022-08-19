# Ansible When 조건문 및 파일 내용 추가

---

Ansible when 조건문을 활용하여 해당 파일이 있는 서버에만 파일에 내용을 추가하는 기능을 실습해보려 한다.

EC2 3개 (ansible1, hosts2) 로 테스트를 진행해보겠다.

## Ansible 파일 내용 추가

---

일단 이미 존재하는 파일에서 내용을 추가하는 기능을 실습하겠다.

ansible의 lineinfile 모듈은 regular expression을 사용하여 파일의 내용을 변경하는 작업을 하는 모듈이다. </br>
( regular expression/정규 표현식 은 문자열에서 특정 문자 조합을 찾기 위한 패턴이다.)

![Untitled](https://user-images.githubusercontent.com/84123877/180950879-cf08a590-b6b0-48f0-83b6-0497cdeb4de9.png)

```yaml
---
  - hosts: all
    name: rc-local service status check
    gather_facts: false
    become: true

    tasks:
      - name: rc-local start and check
        lineinfile:
          path: /home/rc.local
          line: |

            cd /app/tomcat8.5/apache-tomcat-8.5.53/bin # tomcat start
            sleep 1
            ./startup.sh
```

> 정리하면 /home/rc.local 파일에 line 부분에 해당하는 내용을 추가하는 기능이다.
> 

~~(파일명이 rc.local 인걸로 실습 이유를 눈치채셨을까요??)~~

![Untitled 1](https://user-images.githubusercontent.com/84123877/180950859-b26078d9-aad0-413a-8389-79a373e44e65.png)

![Untitled 2](https://user-images.githubusercontent.com/84123877/180950861-09116a1d-f99b-4e70-8952-59adf667e120.png)

> (위) hosts server 1 기존 파일 내용 | (아래) hosts server 1 ansible 작동 후 파일 내용
> 

성공적으로 라인이 추가되었다.
옵션을 지정하지 않으면 일단 가장 마지막 내용 다음으로 추가되는듯하다.

## Ansible When 조건문

---

이번에는 when 조건문을 활용하여 조건이 참인 hosts에만 명령을 수행해보겠다.

hosts server1 에는 cwcw 디렉터리가 존재하고, hosts server 2에는 cwcw 디렉터리가 존재하지 않는다.

when 조건문을 활용하여 cwcw 디렉터리의 존재 유무를 파악하고 cwcw 디렉터리가 존재하지 않는 경우에만 cwcw 디렉터리를 생성해주겠다.

![Untitled 3](https://user-images.githubusercontent.com/84123877/180950862-93a9ebc3-c0ac-4a79-92d7-d5f7508449bc.png)

```yaml
---
  - hosts: all
    name: lvm filesys checker
    gather_facts: false
    become: true

    tasks:
      - name: Check that the unarchive files exists
        stat:
          path: /home/cwcw/
        register: stat_result

      - name: Move unarchive directory to root directory
        shell: sudo mkdir /home/cwcw/
        when: stat_result.stat.exists == False
```

> stat 모듈로 디렉터리의 존재 유무를 register(stat_result)로 가져온다.
> 

그리고 값이 저장된 register를 사용하여 조건을 지정하고, cwcw 디렉터리를 생성하는 명령을 실행한다.

(참고로 name에 lvm 어쩌구는 무시해라.. 수정을 깜빡하고 못함)

![Untitled 4](https://user-images.githubusercontent.com/84123877/180950865-bf89e3b2-dca9-4cda-9dae-7451b7f25031.png)

> cwcw 디렉터리가 존재하지 않는 hosts server 2에 cwcw 디렉터리가 생성되면 성공한것이다.
> 

![Untitled 5](https://user-images.githubusercontent.com/84123877/180950866-b2ce3738-5f8a-4bbc-b09b-026dab2c08d8.png)

![Untitled 6](https://user-images.githubusercontent.com/84123877/180950869-bb8ab09e-b364-4545-9443-c376d603e911.png)

> task 실행 결과 내용에 이미 cwcw가 존재하는 hosts 1은 skipped 된 것을 확인할 수 있다.
> 

hosts2에 cwcw 디렉터리가 성공적으로 생성되었다. 성공~

## Ansible 조건에 충족하면 파일에 내용 추가

---

본인은  /etc/rc.local 파일에 service start 기능을 하는 sh을 등록해 서버들의 service가 재부팅시 자동으로 시작하게끔 하기위해 when, lieninfile을 실습해보았다.

(서버 수가 수십대고 각자 서비스도 달라서 rc.local 파일에 내용을 각각 다르게 설정해주어야한다.)

이제 실제로 본인이 필요한 기능을 테스트해보겠다.

![Untitled 7](https://user-images.githubusercontent.com/84123877/180950870-23c10432-2554-4d51-baa8-3291b86bc445.png)

```yaml
---
  - hosts: all
    name: file gogo
    gather_facts: false
    become: true

    tasks:
      - name: Check that the unarchive files exists
        stat:
          path: /home/cwcw/
        register: stat_result

      - name: Move unarchive directory to root directory
        lineinfile:
          path: /home/rc.local
          line: |

            cd /app/tomcat8.5/apache-tomcat-8.5.53/bin
            sleep 1
            ./startup.sh

        when: stat_result.stat.exists == True
```

> 만약 cwcw 디렉터리가 존재한다면, rc.local에 내용을 추가하는 명령이다.
> 

(hosts 2 cwcw는 삭제했다^^)

![Untitled 8](https://user-images.githubusercontent.com/84123877/180951220-07679597-f7e1-4254-a880-a585b03dfce4.png)

> 오류없이 성공적으로 진행되었다.
> 

![Untitled 9](https://user-images.githubusercontent.com/84123877/180950873-7df44133-aa10-4456-a437-490450598dea.png)

> 아무런 변화가 없는 cwcw 미존재 hosts 2.
> 

![Untitled 10](https://user-images.githubusercontent.com/84123877/180950877-b8350975-7340-4af6-9fbf-566c29e42efb.png)

> 반면 cwcw 디렉터리가 존재하는 hosts 1은 rc.local 파일 라인이 더 추가된것을 확인할 수 있다.
> 

이걸로 when 조건문으로 lieninfile(파일 내용 추가 모듈)을 조건에 맞게 사용하는 실습을 완료하였다.

---
