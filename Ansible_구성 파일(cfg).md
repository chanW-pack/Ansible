# ansible.cfg 구성 파일 파일 설정(앤서블 명령 설정, 세팅)

---

## ansible.cfg 파일이란?

---

ansible.cfg 엔진은 ansible 명령을 실행할 때 모든 환경설정 및 세팅이 들어있는 ansible.cfg를 참고하여 명령을 실행한다.

## ansible.cfg 파일 사용

---

ansible.cfg 파일은 여러 위치에 있을 수 있고, ansible 엔진은 정해진 순서대로 구성파일을 확인한다.

1. ansible 명령이 실행되는 디렉터리에 있는 ansible.cfg 파일
2. 홈 디렉터리에서 ansible.cfg를 찾는다. 이게 없으면 default 구성파일을 사용한다.
3. default 구성파일인 /etc/ansible/ansible.cfg

이를 통해 관리자는 다양한 관경이나 프로젝트가 별도의 디렉토리에 저장되는 구조를 생성할 수 있고, 각 디렉터리에는 고유 설정 집합으로 맞춤화된 구성파일을 포함시킬 수 있다.

또한 ansible.cfg 파일은 변경한 후 따로 서비스 등을 재시작할 필요가 없이 변경 후 저장하면 바로 적용된다.

### 참고: 설정의 default 값

![Untitled](https://user-images.githubusercontent.com/84123877/185296916-950eb234-882c-405f-ad4a-6de8da433679.png)

> ansible.cfg 뿐만 아니고 다른 conf도 보통 파일에서 주석처리된 기본 내용이 default 값으로 보면 된다.
> 

![Untitled 1](https://user-images.githubusercontent.com/84123877/185296903-46ad7f13-08c8-4a93-929e-61299eae8932.png)

> ansible —version 명령을 사용하여 현재 사용하고 있는 ansible.cfg가 어떤 파일인지 확인할 수 있다.
> 

또한, 명령어에서 -v 옵션을 쓰면, 어떤 구성파일을 쓰고 있는지 확인할 수 있다.

## ansible.cfg 구성파일 내용

---

ansible.cfg에는 크게 2가지 섹션이 있다.

- [default] : ansible 작업의 기본 환경설정 값을 구성
- [priveilege_escalation] : 관리 호스트에서 ansible이 권한 에스컬레이션을 수행하는 방법을 구성

### [default]

| inventory = ./inventory | 사용할 인벤토리 파일의 경로 </br> - 정적 인벤토리 파일 또는 여러개의 정적 인벤토리를 명시 </br> - 동적 인벤토리 스크립트를 포함하는 디렉토리 명시 |
| --- | --- |
| remote_user = user | 관리 호스트에 연결할 사용자의 이름. 관리 호스트에서 사용하므로 관리 호스트에 해당 유저가 존재해야 한다. </br> 이 구문을 따로 명시하지 않으면 ansible은 ansible 명령을 실행하는 로컬 사용자명을 사용하여 관리 호스트에 연결한다. |
| ask_pass=false | ssh 암호를 요청하는 메세지 표시 여부를 결정 </br> 제어노드에서 관리 호스트에 연결할 사용자에 대해 인증할 수 있는 키가 구성된 경우 따로 비밀번호를 물어보지 않고 자동으로 로그인된다. </br> 이런 경우 ssh 암호를 요청할 필요가 없으므로 false로 설정한다. </br> (default는 false이다.) 즉, 비밀번호를 입력해야 하도록 설정된 경우는 true로 설정한다. |

### [privilege_escalation]

| become=true | remote_user로 연결한 유저를 sudo 또는 su를 사용하여 특정 상급 유저로 전환할지 여부. </br> 사용하는 모듈에 root 권한이 필요할 수 있다. 상급 유저로 전환하러면 true를 설정한다. 하지만 이렇게 설정해도 ansible 명령 혹은 ansible-playbook 실행 시 다양한 방법으로이를 재정의할 수 있다. |
| --- | --- |
| become_method= sudo | 상급 유저로 전환하는 방법을 명시한다. </br> default는 sudo이고, su는 옵션이다. 일반적으로 sudo 그대로 사용한다. |
| become_user=root | 상급 유저로 전환할 때, 어떤 유저로 전환할지 명시한다. </br> default는 root이다. |
| becom_ask_pass=false | become_method의 암호를 요청하는 메세지 표시 여부 </br> default는 false로, 상급 유저로 전환할 때 sudoers 설정을 따로 하지 않아 상급유저의 암호를 입력해야 한다면 이 값을 true로 설정해야 한다. </br> inventory 항목에서 remote_user로 설정한 유저가 sudo 권한이 있다면 이 값은 false로 하면 된다. |

> 구성파일 내에서 주석 사용 :
> 
- ansible.cfg 구성파일은 #와 ; 로 주석을 사용할 수 있다.
- #는 줄 맨앞에 넣으며 전체 줄을 주석처리한다.
- ;는 해당 줄에서 ;의 오른쪽에 있는 모든 내용을 주석처리한다.

### ansible.cfg 설정 예시

![Untitled 2](https://user-images.githubusercontent.com/84123877/185296911-8d42bdd0-baf6-4ec6-b5de-8a1733161ead.png)

> 본인 서버 실제 적용 예시이다.
> 

chanwooking 이라는 유저명으로 관리호스트에 ssh 접속을 수행한다.

키가 설정되어 있다면 비밀번호를 입력하지 않고 바로 로그인되고, 키가 설정되어 있지 않다면 비밀번호를 물어본다.

이후 ansible에서 명시한 task를 실행할 때 root 권한의 sudo를 사용한다.

다음은 해당 설정을 위한 추가적인 설정을 잠깐 설명하겠다.

## ssh 키 설정

---

관리 호스트에서 리눅스 제어노드와 openssh를 사용한다면, ssh 키 기반 인증을 설정할 수 있다.

이렇게 하여 ask_pass=false 설정을 하고, 비밀번호를 따로 입력하지 않고 넘어갈 수 있다.

1. ssh-keygen 명령을 실행하여 키파일을 생성한다.
2. 생성된 공개키를 타 서버에 적용한다.
- 직접 공개키 파일 내 내용을 복사 붙여넣기 하여도 되고,
   ssh-copy-id 명령으로 바로 공개키를 적용시킬 수 있다.
3. 이후 ssh 접속을 테스트한다. (비밀번호를 묻지 않는다면 성공)

## sudo 설정

---

RHEL에서는 기본적으로 /etc/sudoers에서 whell 그룹의 모든 사용자에게 자신의 암호로 sudo를 사용하여 root 권한으로 명령을 실행할 수 있는 기능이 있다.

여기서는 특정 유저가 아예 비밀번호를 쓰지 않고 sudo 명령을 써서 root 권한으로 명령을 실행하도록 설정한다.

(이 부분이 become=true 부분과 연관이 있다.)

![Untitled 3](https://user-images.githubusercontent.com/84123877/185296913-682365cc-71b6-46db-aa7b-191aa52f3fcd.png)

> 해당 유저는 암호를 입력하지 않고 sudo를 사용하여 root 권한으로 명령을 실행할 수 있다.
> 

설정이 정상적으로 완료되었으면, ansible.cfg 파일의 become=true를 설정여 권한상승을 할 때 비밀번호를 물어보지 않는다.

---
