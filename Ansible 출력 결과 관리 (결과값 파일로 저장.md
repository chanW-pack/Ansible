# Ansible 출력 결과 관리 (결과값 파일로 저장, 리턴)

---

오늘 모든 RHEL OS 서비스(서버) 호스트들의 패스워드 만료일을 파악해달라는 요청이 왔다.
서버 수가 상당했지만 수작업으로 하나하나 진행하고 있었다..만, 5분의 1도 못해서 포기하고 ansible로 일괄 작업 및 자동화를 시키기로 결심했다.

`chage`명령어를 사용해 나타나는 계정의 정보들중에서 패스워드 만기일을 나타내는 password expires 부분의 데이터만 grep 해와서 보면될듯싶은데..

ansible이 playbook 형식으로도 사용할 수 있지만, 이미 기능들이 생성되어있는 모듈들로도 충분히 진행가능한 기능인듯 싶어 모듈로 진행하였다.

![Untitled](https://user-images.githubusercontent.com/84123877/176133370-99954f55-0f90-411e-989e-8ade0515c904.png)

> Inventory를 작성하여 host ec2를 명시하였다.
기능 테스트만 진행할하므로 간단히 1개만 설치했다.
> 

![Untitled 1](https://user-images.githubusercontent.com/84123877/176133336-1886e1c8-85ff-4d8b-8e4e-670c112f9350.png)

> ping 테스트를 진행했다. 잘 가는것을 보니 inventory에는 문제가 없다.
> 

![Untitled 2](https://user-images.githubusercontent.com/84123877/176133341-af939fc4-6a0a-4faf-a1a0-e523f90da6bd.png)

> playbook 아닌 모듈로 간단하게 원하는 값만 리턴받을 수 있었다.
> 

모듈로 진행하던 도중, 지속적으로 사용하려면 playbook이 효율적이라 playbook으로 전향했다.

하지만 모듈에서는 결과가 바로 나타나지만, playbook에서는 ansible의 success, fail만 나타내지 결과를 가져오지 않았다. (똑같은 명령임에도 … )

![Untitled 3](https://user-images.githubusercontent.com/84123877/176133348-b48c9df7-b269-483d-a94e-517f27b4f853.png)

> 그래서 생각한 대책이 값을 파일로 저장하여 읽어오는것.
(chage 명령은 유저의 정보를 모두 생성하는데, 그 중에서 패스워드 만료일만 grep으로 가져왔다.)
> 

![Untitled 4](https://user-images.githubusercontent.com/84123877/176133352-ba5c6da5-d63f-404c-bbd9-51d4e8c37a20.png)

![Untitled 5](https://user-images.githubusercontent.com/84123877/176133354-25b224c0-01b6-4f14-a569-5aadf179d807.png)

> 상단 = ansible 서버 / 하단 = host 서버
password expires(패스워드 만료일) 이 never(무한) 으로 설정되어있는것을 확인가능하다.
> 

이렇게 결과를 파일로 저장하여 이제는 파일을 읽어들이는 playbook을 생성하여 진행하면(아니면 tasks를 하나 추가하던가) 하나하나의 서버를 만질 필요없이 ansible이 설치된 서버 하나로 host 서버들을 모두 관리할 수 있다.

그런데 요청자가 파일로 저장하여 불러오는것보다는 ansible 서버에서 바로 텍스트로 직접 확인하고 싶다고 한다…

조사하다 보니 플레이북 말고 애드혹(모듈)로 패스워드 만료 정보를 저장하지 않고 바로 나타내게 하는 테스트는 완료했는데, playbook이 없으면 결국 소용이 없다… (한 두번 할것이 아니니..)

게다가 본인은 일반 계정인데 root 계정의 정보를 빼오는 기능도 찾아야한다.

### .

### .

### .

### 적용 완료하였다!!

일단 애드혹(모듈)로 구현은 가능하다. 하지만 root 권한을 이용해야 하고, 지속성을 위해 playbook으로 기능을 완성해야한다. (근데 playbook에서는 기본적으로 결과값이 리턴되지 않는다.)

관련 자료들을 조사하니 tasks값을 register 변수로 저장 가능하고, 이를 debug 창에 띄울 수 있다고 한다.
게다가 shell 에서 패스워드 정보를 찾는 명령어 앞에 sudo를 붙이니 root 계정의 정보도 가져올 수 있었다.

![Untitled 6](https://user-images.githubusercontent.com/84123877/176133360-f7722ecf-7591-4972-b960-4d78c15f15fd.png)

> shell 까지는 동일하나, 이에 대한 리턴값을 register ‘command_output’ 에 저장하고 이를 debug 콘솔에 띄우는 방식으로 진행했다.
> 

![Untitled 7](https://user-images.githubusercontent.com/84123877/176133363-9ff8012b-8cf2-4ca3-a904-354bd62a721e.png)

> 원래 debug 창에는 해당 명령의 대한 정보들이 나타나는데, 나는 debug 정보? 응~ 안받아~
리턴값으로 채워~
> 

![Untitled 8](https://user-images.githubusercontent.com/84123877/176133366-d2461d26-e3a4-4c7c-b780-59cfdc35c191.png)

> 실제 적용샷. 일일이 찾아봐야하는 유저 패스워드 만료일을 1초만에 볼 수 있다.
매우 만족^^ 이래서 앤서블 하는구나~
> 

---
