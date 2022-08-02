# Ansible 모듈 활용

---

### 모듈이란?

CLI나 playbook 작업에서 사용할 수 있는 별도의 코드 단위이다.

### 모듈의 특징

1. 멱등성을 보장해준다.
2. 중복 실행될 여지가 차단된다.
3. 가독성이 좋다.
4. 배포전에 테스트가 가능하다.

이제 서버와 인벤토리가 준비되었다고 가정 하에 모듈 실습을 진행하겠다.
(본인은 EC2 2대 사용하여 진행함. 시작 전 꼭 ping으로 테스트 진행할 것.)

## 1. Shell 모듈

---

쉘을 이용한 작업에 사용되는 모듈이다.

-a로 쉘에서 동작시킬 명령어를 입력시킬 수 있다.

![Untitled](Ansible%20%E1%84%86%E1%85%A9%E1%84%83%E1%85%B2%E1%86%AF%20%E1%84%92%E1%85%AA%E1%86%AF%E1%84%8B%E1%85%AD%E1%86%BC%201b47ee18606e4d09a230dc3d9dab3de3/Untitled.png)

> 위 명령어를 입력하면 inventory에 지정한 [all] 모든 그룹에게 shell 모듈로 ls 명령어를 실행하게 된다.
> 

(참고로 본인은 inventory를 새로 작성하여 진행했기 때문에 inventory를 -i 옵션으로 지정해주었다.)

![Untitled](Ansible%20%E1%84%86%E1%85%A9%E1%84%83%E1%85%B2%E1%86%AF%20%E1%84%92%E1%85%AA%E1%86%AF%E1%84%8B%E1%85%AD%E1%86%BC%201b47ee18606e4d09a230dc3d9dab3de3/Untitled%201.png)

> 마찬가지로 whoami 명령어를 동작시킨 모습.
> 

## 2. User 모듈

---

지정한 서버에 user를 생성하는 모듈이다.

```bash
ansible all -m user -a 'name=a'
```

![Untitled](Ansible%20%E1%84%86%E1%85%A9%E1%84%83%E1%85%B2%E1%86%AF%20%E1%84%92%E1%85%AA%E1%86%AF%E1%84%8B%E1%85%AD%E1%86%BC%201b47ee18606e4d09a230dc3d9dab3de3/Untitled%202.png)

> 위 명령어를 입력하면 user 모듈을 사용하여 cwking 이라는 사용자 계정을 생성한다.
> 

chanwooking 계정이 host 서버에 생성된 것을 확인할 수 있다.

![Untitled](Ansible%20%E1%84%86%E1%85%A9%E1%84%83%E1%85%B2%E1%86%AF%20%E1%84%92%E1%85%AA%E1%86%AF%E1%84%8B%E1%85%AD%E1%86%BC%201b47ee18606e4d09a230dc3d9dab3de3/Untitled%203.png)

```bash
ansible all -m user -a "name=a update_password=always password={{ 'P@ssw0rd' | password_hash('sha512') }}"
```

> 위 명령어는 user 모듈을 사용하여 패스워드를 변경하는 방법이다.
> 

```bash
ansible all -m user -a "name=a state=absent"
```

state=absent를 입력하면 지정한 사용자를 제거할 수 있다.

## 3. yum 모듈

---

지정한 서버에 yum을 사용하여 패키지 설치 및 삭제하는 모듈이다.

```bash
ansible web -m yum -a "name=httpd state=latest"
```

![Untitled](Ansible%20%E1%84%86%E1%85%A9%E1%84%83%E1%85%B2%E1%86%AF%20%E1%84%92%E1%85%AA%E1%86%AF%E1%84%8B%E1%85%AD%E1%86%BC%201b47ee18606e4d09a230dc3d9dab3de3/Untitled%204.png)

> yum 모듈을 사용하여 httpd 패키지를 설치하는 명령어이다.
> 

![Untitled](Ansible%20%E1%84%86%E1%85%A9%E1%84%83%E1%85%B2%E1%86%AF%20%E1%84%92%E1%85%AA%E1%86%AF%E1%84%8B%E1%85%AD%E1%86%BC%201b47ee18606e4d09a230dc3d9dab3de3/Untitled%205.png)

> 다음과 같이 shell 모듈을 사용하여 rpm -qa로 패키지가 설치된 것을 확인할 수 있다.
> 

## 4. Copy 모듈

---

파일을 원하는 서버에 복사하는 모듈이다.

```bash
ansible web -m copy -a "src=index.html dest=/var/www/html/index.html"
```

src는 복사할 원본 파일의 위치를 지정하고 dest는 파일이 복사될 경로를 지정한다.

## 5. service 모듈

---

데몬을 동작시키기 위한 systemctl 명령어를 사용하기 위한 모듈이다.

![Untitled](Ansible%20%E1%84%86%E1%85%A9%E1%84%83%E1%85%B2%E1%86%AF%20%E1%84%92%E1%85%AA%E1%86%AF%E1%84%8B%E1%85%AD%E1%86%BC%201b47ee18606e4d09a230dc3d9dab3de3/Untitled%206.png)

```bash
ansible web -m service -a "name=httpd state=started"
```

> 이후 shell 모듈을 사용하여 웹서비스를 위한 80번 포트를 inventory에 host에 소속된 서버들에게 열어준다.
> 

(근데 난 ec2로 진행해서 이미 열려있다..)

마지막으로 방화벽을 재시작해주면 host에 소속된 서버들의 웹 서비스가 정상적으로 동작함을 확인할 수 있다.

## 6. File 모듈

---

파일에 대한 상세정보 설정, 확인 및 삭제가 가능하다.

```bash
ansible web -m file -a "name=/var/www/html/index.html state=absent"
```

## 인벤토리 별도 생성 및 활용

---

```bash
vi inven.list

[ai]
anm
ann[1:3]

[webi]
ann[1:2]

[dbi]
ann3
```

인벤토리를 원하는 경로에 원하는 이름으로 지정하여 생성할 수 있다.

ann[1:3]은 ann1, ann2, ann3를 묶어서 작성하는 방법이다.

![Untitled](Ansible%20%E1%84%86%E1%85%A9%E1%84%83%E1%85%B2%E1%86%AF%20%E1%84%92%E1%85%AA%E1%86%AF%E1%84%8B%E1%85%AD%E1%86%BC%201b47ee18606e4d09a230dc3d9dab3de3/Untitled%207.png)

> 위 예시처럼 모듈에서 인벤토리를 -i옵션으로 지정하여 사용할 수 있다.
> 

---