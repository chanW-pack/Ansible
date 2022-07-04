# ansible 여러 서비스들 계정 삭제

---

ansible 여러 서비스들 계정 삭제

linux os server들의 불필요한 계정들을 일괄삭제하는 기능을 살펴보겠다.

테스트를 위해 ec2를 생성하여 진행하였다.

![Untitled](https://user-images.githubusercontent.com/84123877/177087150-925a4742-faad-4b31-a30f-72b96f55b3ef.png)

> 계졍 유무를 확인하는 check.yaml, 계정삭제를 진행하는 userDel.yaml을 생성하였다.
> 

if문을 활용하듯, 한 playbook에 두 기능을 넣고 진행할 수 있겠으나,(if ~~계정이 있다면 = 삭제 진행) 시간이 좀 걸릴거같아 나중에 실습해보기로 한다.

![Untitled 1](https://user-images.githubusercontent.com/84123877/177087156-ce08f045-46b1-475f-850e-b40c439337a4.png)

> user name을 체크하는 playbook이다.
> 

cat /etc/passwd 로 유저 목록을 확인하고, grep으로 원하는 유저의 정보만 가져와 확인하는 기능이다. 결과값은 레지스터 command_output 에 변수로 저장하여 debug 창에 보여지도록 하였다.

![Untitled 2](https://user-images.githubusercontent.com/84123877/177087158-f565cff6-55e6-4219-9cbf-0e80382aea14.png)

> userdel을 진행하는 playbook이다.
> 

관리자 권한으로 userdel을 진행하고, (-r 옵션으로 홈 디렉터리까지 삭제한다)
shell 명령에 | 옵션을 추가하여 여러 줄의 명령을 한번에 진행하도록 생성했다.

![Untitled 3](https://user-images.githubusercontent.com/84123877/177087160-4deab6e3-fddc-40d0-aedb-2b8f50f608bf.png)

> host server에 cwcw 계정들이 생성되어있는 모습.
이를 ansible을 사용하여 한번에 삭제하겠다.
> 

![Untitled 4](https://user-images.githubusercontent.com/84123877/177087161-879f1a11-32c0-4657-ae69-e339b99c602f.png)

```bash
ansible-playbook userDel.yaml -i list
```

`ansible-playbook [생성한 playbook] -i [생성한 inventory]`

ansible server에서 명령으로 ansible을 실행시킨다.

![Untitled 5](https://user-images.githubusercontent.com/84123877/177087164-4a8cc343-60b4-408b-9102-072e68d9027c.png)

> host server의 cwcw, cwcw2, cwcw3, user들이 정상적으로 삭제되었다.
> 

---
