# Ansible_ Sysnchronize 모듈(Remote to Remote 파일전송)

---

## Synchronize 모듈

ansible의 Synchrinize 모듈은 linux의 rysnc 명령어를 사용할 수 있도록 만들어 놓은 모듈이다.

## Remote to Remote로 데이터 전송

ansible에서 파일을 옮기는 방법은 copy, fetch 등의 방법도 있지만, synchronize(rsync)를 사용하여 두 host 간에 데이터를 전송하는 방법에 대해 설명하겠다.
(단일 데이터 혹은 2개, 3개 데이터를 복사할 경우는 copy, fetch를 주로 사용, 대용량의 파일 or 디렉터리를 복사할 때는 synchronie를 사용을 한다고 함)

## Sysnchrinize default 모드 (push)

![%EB%8B%A4%EC%9A%B4%EB%A1%9C%EB%93%9C](https://user-images.githubusercontent.com/84123877/188073186-523a27da-ca0f-4f95-971d-26899cffa50c.png)

> sysnchrionize default 동작
> 

Sysnchronize는 default로 push mode로 동작한다.
SourceServer 파일 or 디렉터리를 DestinationServer로 보내는 동작이 default다.

rsycn.yaml 파일

```yaml
- name: Test rsync
  hosts: {{ DestinationServer }}
  remote_user: {{ remoteUser }}
  tasks:
    - name: 
      synchronize:
        src: "/path/to/SourceServer"
        dest: "/path/to/DestinationServer"
      delegate_to: {{ SourceServer }}
```

여기서 keyword 옵션은 delegate_to 옵션이다.
delegate_to 옵션은 “실행하는 localhost의 위치를 위임하겠다.”라는 의미인데 delegate_to 옵션을 사용하면 옵션에 설정한 SourceServer가 localhost가 된다.

- src 옵션은 source의 줄임말로 옮길 대상
- dest 옵션은 destination의 줄임말로 옮길 목적지

push mode는 보내는 LocalServer가 주체가 되므로 “SourceServer의 src를 destionationServer의 dest로 보내겠다”로 생각할 수 있다.

- vvv 옵션을 주고 rsync.yaml 파일을 실행 후 “cmd” 부분을 확인하면 다음과 같이 rsync 명령이 실행된다.

`/usr/bin/rsync ~~~(여러가지 옵션들)~~~ {{ src }} {{remote_user@hosts}}:{{ dest }}`

rsync의 명령이 실행되는 위치가 delegate_to옵션으로 설정한 SourceServer가 되므로 SourceServer(local)의 src 디렉터리의 내용을 Destination의 dest디렉터리로 복사(push)를 하겠다는 의미이다.

delegate_to 옵션이 없으면 ansible-playbook을 실행한 Control Server가 local이 되고 src는 Control Server의 파일 or 디렉터리가 된다.

## Synchronize pull 모드

![%EB%8B%A4%EC%9A%B4%EB%A1%9C%EB%93%9C_(1)](https://user-images.githubusercontent.com/84123877/188073190-c117d08e-6252-4b60-91c0-894eaf51497e.png)

> synchronize pull 동작
> 

SourceServer에서 DestinationServer의 파일 or 디렉터리를 가져오는(당겨오는) 동작이 pull mode이다.

rsycn.yaml 파일

```yaml
- name: Test rsync
  hosts: {{ DestinationServer }}
  remote_user: {{ remoteUser }}
  tasks:
    - name: 
      synchronize:
        src: "/path/to/DestinationServer"
        dest: "/path/to/SourceServer"
        mode: pull
      delegate_to: {{ SourceServer }}
```

mode를 pull로 변경하는 ‘mode: pull’ 옵션을 추가하고 src 옵션과 dest 옵션을 바꿔줘야 한다.

(pull 모드의 동작방식이 src(옮길 대상)을 dest(목적지)로 가져오는 것이기 때문)

---
