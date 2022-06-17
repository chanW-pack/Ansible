# [Ansible] 앤서블(Ansible) 호스트 명령 내리기

---

서버 여러대를 그룹으로 나누어 따로 따로 명렁어를 내려보겠다.

[](https://github.com/chanwoo9730/Ansible/blob/main/%5BAnsible%5D%20%EC%95%A4%EC%84%9C%EB%B8%94(Ansible)%20%EA%B0%9C%EB%85%90%EA%B3%BC%20%EC%84%A4%EC%B9%98%EC%82%AC%EC%9A%A9%EB%B2%95%20(w%20Amazon%20Linux).md)

위 페이지에서 이어서 진행하므로,

AWS EC2 의 cwCool name의 Controller 서버 1 대,
내 공유기의 ESXi Ubuntu server 1대,
AWS EC2 새로운 인스턴스 3대를 생성해서 진행하겠다.

즉 , cwCool 인스턴스가 4대를 관리하는것

![Untitled](https://user-images.githubusercontent.com/84123877/174318647-42eb24cf-0200-4194-857c-b3ee4087062f.png)

> 기존 ansible이 설치된 cwCool을 제외한 3개의 인스턴스를 추가 생성하였다.
> 

<aside>
🔥 **여기서 잠깐**
원래 ssh-copy-id 명령어를 사용해서 쉽게 공개키를 보내려했는데 aws ec2 인스턴스들
pem파일을 넣어줘야 하는 문제때문에 ssh 명령어에 pem 파일 추가하는것은 찾았지만,
ssh-copy-id 명령어에 pem 파일도 추가해서 보내는것을 할 수가 없음….
그냥 하나하나 공개키를 추가하기로 했음..(나중에 방법을 찾으면 추가함)

</aside>

![Untitled 1](https://user-images.githubusercontent.com/84123877/174318588-8a1b0416-9c3a-4f38-99e4-ffafe993edc6.png)

> Controller 인스턴스 home 디렉터리에 EC2 접속시 필요한 pem 파일을 넣었다.
> 

![Untitled 2](https://user-images.githubusercontent.com/84123877/174318595-e1e9bbcb-cd4f-42ee-85d9-db861bb0a82c.png)

```bash
chmod 600 [해당 파일명]
```

pem키를 사용해서 ssh 접속을 진행하러면 pem파일의 권한을 열어주어야 한다.

![Untitled 3](https://user-images.githubusercontent.com/84123877/174318600-2175ef80-7d97-4fe4-b0c6-a1c652e9f353.png)

```bash
ssh -i [pem 파일 경로] [사용자이름]@[접속할 IP]
```

pem 키 파일을 넣고 접속이 문제없이 진행되었다.

![Untitled](%5BAnsible%5D%20%E1%84%8B%E1%85%A2%E1%86%AB%E1%84%89%E1%85%A5%E1%84%87%E1%85%B3%E1%86%AF(Ansible)%20%E1%84%92%E1%85%A9%E1%84%89%E1%85%B3%E1%84%90%E1%85%B3%20%E1%84%86%E1%85%A7%E1%86%BC%E1%84%85%E1%85%A7%E1%86%BC%20%E1%84%82%E1%85%A2%E1%84%85%E1%85%B5%E1%84%80%E1%85%B5%20ed8562397f25498894104e32772a9a15/Untitled%204.png)

```bash
vi /home/ec2-user/.ssh/authorized_keys
```

호스트 서버로 접속하였으니, 해당 서버의 authorized_keys로 들어가 복사했던
키를 넣어준다.

![Untitled](%5BAnsible%5D%20%E1%84%8B%E1%85%A2%E1%86%AB%E1%84%89%E1%85%A5%E1%84%87%E1%85%B3%E1%86%AF(Ansible)%20%E1%84%92%E1%85%A9%E1%84%89%E1%85%B3%E1%84%90%E1%85%B3%20%E1%84%86%E1%85%A7%E1%86%BC%E1%84%85%E1%85%A7%E1%86%BC%20%E1%84%82%E1%85%A2%E1%84%85%E1%85%B5%E1%84%80%E1%85%B5%20ed8562397f25498894104e32772a9a15/Untitled%205.png)

> 윗 줄은 이미 들어가있는 키값(pem파일) 이고, 
뒤로 이어서 작성해주면 된다.
> 

키 값에는 공백이 있으면 안된다!!!

동일하게 모든 인스턴스에 적용시킨다.

![Untitled](%5BAnsible%5D%20%E1%84%8B%E1%85%A2%E1%86%AB%E1%84%89%E1%85%A5%E1%84%87%E1%85%B3%E1%86%AF(Ansible)%20%E1%84%92%E1%85%A9%E1%84%89%E1%85%B3%E1%84%90%E1%85%B3%20%E1%84%86%E1%85%A7%E1%86%BC%E1%84%85%E1%85%A7%E1%86%BC%20%E1%84%82%E1%85%A2%E1%84%85%E1%85%B5%E1%84%80%E1%85%B5%20ed8562397f25498894104e32772a9a15/Untitled%206.png)

> 인벤토리 파일에 ip 추가 완료
> 

![Untitled](%5BAnsible%5D%20%E1%84%8B%E1%85%A2%E1%86%AB%E1%84%89%E1%85%A5%E1%84%87%E1%85%B3%E1%86%AF(Ansible)%20%E1%84%92%E1%85%A9%E1%84%89%E1%85%B3%E1%84%90%E1%85%B3%20%E1%84%86%E1%85%A7%E1%86%BC%E1%84%85%E1%85%A7%E1%86%BC%20%E1%84%82%E1%85%A2%E1%84%85%E1%85%B5%E1%84%80%E1%85%B5%20ed8562397f25498894104e32772a9a15/Untitled%207.png)

> 오류가 발생했다..unreachable.....failed to connect to the host via ssh: premission denied...
> 

![Untitled](%5BAnsible%5D%20%E1%84%8B%E1%85%A2%E1%86%AB%E1%84%89%E1%85%A5%E1%84%87%E1%85%B3%E1%86%AF(Ansible)%20%E1%84%92%E1%85%A9%E1%84%89%E1%85%B3%E1%84%90%E1%85%B3%20%E1%84%86%E1%85%A7%E1%86%BC%E1%84%85%E1%85%A7%E1%86%BC%20%E1%84%82%E1%85%A2%E1%84%85%E1%85%B5%E1%84%80%E1%85%B5%20ed8562397f25498894104e32772a9a15/Untitled%208.png)

> ssh-copy-id로 키를 전달한 ubuntu는 잘 작동된다..
> 

ec2 인스턴스들에게는 공개키 추가만 했을뿐, 공개키 자체 파일을 보내지 않았기 때문인걸로 예상.

공개키도 전송한뒤 다시 시도해보겠다.

scp 명령어로 공개키를 전송해도 작동되지않는다.

그런데, 

![Untitled](%5BAnsible%5D%20%E1%84%8B%E1%85%A2%E1%86%AB%E1%84%89%E1%85%A5%E1%84%87%E1%85%B3%E1%86%AF(Ansible)%20%E1%84%92%E1%85%A9%E1%84%89%E1%85%B3%E1%84%90%E1%85%B3%20%E1%84%86%E1%85%A7%E1%86%BC%E1%84%85%E1%85%A7%E1%86%BC%20%E1%84%82%E1%85%A2%E1%84%85%E1%85%B5%E1%84%80%E1%85%B5%20ed8562397f25498894104e32772a9a15/Untitled%209.png)

> 혹시 몰라서 공개 키 내용을 다시 넣어봤더니 잘 작동된다….
키를 넣을때 공백 등 내용에 문제가 있어나봄….
> 

<aside>
🛰️ **주의 !**
명령어에 -u ec2-user를 주지 않으면 현재 실행 계정과 동일한 계정명으로 시도한다.

</aside>

## 여러 서버를 그룹으로 나눠 따로 명령어 내보내기

---

### 연결된 호스트 전체 명령내리기

-u 명령어를 더 추기하는 방법을 찾지 못해 ubuntu server도 ec2-user라는 사용자를 추가했다.
<이젠 ec2-user 로 명령을 넣으면 4개의 서버가 반응할것.>

![Untitled](%5BAnsible%5D%20%E1%84%8B%E1%85%A2%E1%86%AB%E1%84%89%E1%85%A5%E1%84%87%E1%85%B3%E1%86%AF(Ansible)%20%E1%84%92%E1%85%A9%E1%84%89%E1%85%B3%E1%84%90%E1%85%B3%20%E1%84%86%E1%85%A7%E1%86%BC%E1%84%85%E1%85%A7%E1%86%BC%20%E1%84%82%E1%85%A2%E1%84%85%E1%85%B5%E1%84%80%E1%85%B5%20ed8562397f25498894104e32772a9a15/Untitled%2010.png)

```bash
$ ansible -m command -a 'ls /' all -u ec2-user
```

이제 따로 명령어를 내리게 해볼 것이다.

vi group.ini을 만들어 내용을 넣어주겠다.

ansible_user=[유저이름] 은 -u [유저이름] 이 명령어를 자동으로 인식되게 만들어준다.

그룹 chan, woo 2개에 서버 2개씩 주소를 넣어주었다.

![Untitled](%5BAnsible%5D%20%E1%84%8B%E1%85%A2%E1%86%AB%E1%84%89%E1%85%A5%E1%84%87%E1%85%B3%E1%86%AF(Ansible)%20%E1%84%92%E1%85%A9%E1%84%89%E1%85%B3%E1%84%90%E1%85%B3%20%E1%84%86%E1%85%A7%E1%86%BC%E1%84%85%E1%85%A7%E1%86%BC%20%E1%84%82%E1%85%A2%E1%84%85%E1%85%B5%E1%84%80%E1%85%B5%20ed8562397f25498894104e32772a9a15/Untitled%2011.png)

> 본인의 경로 : /home/ec2-user/.ssh/test/group.ini
> 

![Untitled](%5BAnsible%5D%20%E1%84%8B%E1%85%A2%E1%86%AB%E1%84%89%E1%85%A5%E1%84%87%E1%85%B3%E1%86%AF(Ansible)%20%E1%84%92%E1%85%A9%E1%84%89%E1%85%B3%E1%84%90%E1%85%B3%20%E1%84%86%E1%85%A7%E1%86%BC%E1%84%85%E1%85%A7%E1%86%BC%20%E1%84%82%E1%85%A2%E1%84%85%E1%85%B5%E1%84%80%E1%85%B5%20ed8562397f25498894104e32772a9a15/Untitled%2012.png)

```bash
ansible --list-hosts web -i group.ini  - web그룹 확인하기

ansible --list-hosts host -i group.ini - host그룹 확인하기

ansible --list-hosts all -i group.ini - 전체 확인하기
```

<aside>
🔥 주의사항 !
ansible 명령어를 ini 파일을 이용해서 사용할 때는 해당 파일이 있는 디렉터리에서
사용하여야 된다.
본인도 계속 먹통이라 찾아봤는데 경로문제였다…

</aside>

각 그룹에 명령어를 내려보겠다.

![Untitled](%5BAnsible%5D%20%E1%84%8B%E1%85%A2%E1%86%AB%E1%84%89%E1%85%A5%E1%84%87%E1%85%B3%E1%86%AF(Ansible)%20%E1%84%92%E1%85%A9%E1%84%89%E1%85%B3%E1%84%90%E1%85%B3%20%E1%84%86%E1%85%A7%E1%86%BC%E1%84%85%E1%85%A7%E1%86%BC%20%E1%84%82%E1%85%A2%E1%84%85%E1%85%B5%E1%84%80%E1%85%B5%20ed8562397f25498894104e32772a9a15/Untitled%2013.png)

> chan 그룹 df -h 명령어
> 

![Untitled](%5BAnsible%5D%20%E1%84%8B%E1%85%A2%E1%86%AB%E1%84%89%E1%85%A5%E1%84%87%E1%85%B3%E1%86%AF(Ansible)%20%E1%84%92%E1%85%A9%E1%84%89%E1%85%B3%E1%84%90%E1%85%B3%20%E1%84%86%E1%85%A7%E1%86%BC%E1%84%85%E1%85%A7%E1%86%BC%20%E1%84%82%E1%85%A2%E1%84%85%E1%85%B5%E1%84%80%E1%85%B5%20ed8562397f25498894104e32772a9a15/Untitled%2014.png)

> woo 그룹 df -h 명령어
> 

위 명령어로 해당 그룹에 대해서만 명령어가 실행되게 하는법을 진행하였고,

이와 같이 나온 내용을 txt 파일로 저장하려고 한다.

![Untitled](%5BAnsible%5D%20%E1%84%8B%E1%85%A2%E1%86%AB%E1%84%89%E1%85%A5%E1%84%87%E1%85%B3%E1%86%AF(Ansible)%20%E1%84%92%E1%85%A9%E1%84%89%E1%85%B3%E1%84%90%E1%85%B3%20%E1%84%86%E1%85%A7%E1%86%BC%E1%84%85%E1%85%A7%E1%86%BC%20%E1%84%82%E1%85%A2%E1%84%85%E1%85%B5%E1%84%80%E1%85%B5%20ed8562397f25498894104e32772a9a15/Untitled%2015.png)

```bash
$ ansible -m command -a 'df -h /' > test.txt chan -i group.ini
```

chan 이라는 그룹 안에 있는 호스트들에 한해서 df -h 명령어를 내리고,
그 결과를 test.txt 라는 이름으로 txt 파일로 저장한다.

만들어진 txt 파일은 명령을 내렸던 ansible 서버에 저장된다.

위 사진으로  내용이 보여지며 저장하고 생성된 것을 확인할 수 있다.

![Untitled](%5BAnsible%5D%20%E1%84%8B%E1%85%A2%E1%86%AB%E1%84%89%E1%85%A5%E1%84%87%E1%85%B3%E1%86%AF(Ansible)%20%E1%84%92%E1%85%A9%E1%84%89%E1%85%B3%E1%84%90%E1%85%B3%20%E1%84%86%E1%85%A7%E1%86%BC%E1%84%85%E1%85%A7%E1%86%BC%20%E1%84%82%E1%85%A2%E1%84%85%E1%85%B5%E1%84%80%E1%85%B5%20ed8562397f25498894104e32772a9a15/Untitled%2016.png)

> cat 명령어로 확인해보면 어떤 서버이고 어떤 내용인지 확인이 가능하다.
> 

![Untitled](%5BAnsible%5D%20%E1%84%8B%E1%85%A2%E1%86%AB%E1%84%89%E1%85%A5%E1%84%87%E1%85%B3%E1%86%AF(Ansible)%20%E1%84%92%E1%85%A9%E1%84%89%E1%85%B3%E1%84%90%E1%85%B3%20%E1%84%86%E1%85%A7%E1%86%BC%E1%84%85%E1%85%A7%E1%86%BC%20%E1%84%82%E1%85%A2%E1%84%85%E1%85%B5%E1%84%80%E1%85%B5%20ed8562397f25498894104e32772a9a15/Untitled%2017.png)

> 만약에 다른 내용을 test.txt로 넣으면 처음에 넣었던 내용들은 사라지고,
새로운 내용들로 저장된다.
> 

### 내용을 더 추가하기

만들었던 test.txt에 내용을 더 추가하려고 한다.
df -h 명령을 내리고 내용을 이어서 더 추가해본다.

![Untitled](%5BAnsible%5D%20%E1%84%8B%E1%85%A2%E1%86%AB%E1%84%89%E1%85%A5%E1%84%87%E1%85%B3%E1%86%AF(Ansible)%20%E1%84%92%E1%85%A9%E1%84%89%E1%85%B3%E1%84%90%E1%85%B3%20%E1%84%86%E1%85%A7%E1%86%BC%E1%84%85%E1%85%A7%E1%86%BC%20%E1%84%82%E1%85%A2%E1%84%85%E1%85%B5%E1%84%80%E1%85%B5%20ed8562397f25498894104e32772a9a15/Untitled%2018.png)

```bash
ansible -m command -a 'df -h /' >> test.txt chan -i /home/ec2-user/.ssh/test/group.ini
```

> woo 그룹 free -m 명령어에 이어 chan 그룹 df -h 명령어도 test.txt안에 포함된것을 볼 수 있다.
> 

ansible 서버의 가동시간을 확인

![Untitled](%5BAnsible%5D%20%E1%84%8B%E1%85%A2%E1%86%AB%E1%84%89%E1%85%A5%E1%84%87%E1%85%B3%E1%86%AF(Ansible)%20%E1%84%92%E1%85%A9%E1%84%89%E1%85%B3%E1%84%90%E1%85%B3%20%E1%84%86%E1%85%A7%E1%86%BC%E1%84%85%E1%85%A7%E1%86%BC%20%E1%84%82%E1%85%A2%E1%84%85%E1%85%B5%E1%84%80%E1%85%B5%20ed8562397f25498894104e32772a9a15/Untitled%2019.png)

```bash
$ ansible localhost -m command -a uptime
```

---
