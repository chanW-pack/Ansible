# Ansible Inventory 작성방법

---

## Inventory 작성 방법

---

Invnetory는 확장자명이 따로 존재하지 않는다.

따라서 파일 이름은 본인 마음대로 정해도 된다.

(inventory 파일은 기본적인 host 주소들의 묶음이라고 생각하면 된다.)

추가로 .yml 파일로도 inventory 작성이 가능하다.

```
mail.example.com	//이렇게 하면 Ad-hoc이나 Playbook에서 해당 호스트 네임으로 명령어 실행가능

//호스트 네임과 IP주소로 설정 가능

[webservers]
192.168.0.5
192.168.0.6

[dbservers]
one.example.com
two.example.com
three.example.com

[nossh]
nossh.example.com:5050	//기본 ssh포트를 사용하지 않는다면 이런식으로도 설정 가능

[webservers2]
www[01:50].example.com	//저런식으로 for문 처리를 통해 01 ~ 50 번까지의 이름을 묶을 수 있다.

[databases2]
db-[a:f].example.com	//알파벳 또한 가능

//호스트 변수
[atlanta]
host1 http_port=80 maxRequestsPerChild=808	
//해당 호스트 네임에 이런식으로 정의해줄 수 있다.

host2 http_port=303 maxRequestsPerChild=909 
//이렇게 정의한 호스트들을 atlanta라는 묶음으로 사용한다는 뜻

//그룹 변수
[atlanta]
host1
host2

[atlanta:vars]	//그룹 자체를 기준으로 변수를 부여한다.
ntp_server=ntp.atlanta.example.com
proxy=proxy.atlanta.example.com

//그룹 그룹 묶기
[atlanta]
host1
host2

[raleigh]
host2
host3

[southeast:children] // :children 키워드를 이용해 그룹끼리 묶을 수 있다.
atlanta
raleigh
```

이런식으로 작성한 inventory를 playbook을 통해 실행하려면 ansible.cfg 라는 파일에 등록해야 한다.
(하지만 본인은 지금까지 -i 명령어로 사용했었다.)

### ansible.cfg 등록

---

```
[defaults]		//기본 셋팅
inventory = ./inventory	//현재 디렉토리에 inventory라는 파일을 읽어온다.
remote_user = hwan		//접속하려는 계정 이름
ask_pass = false		//접근할때 패스워드를 입력할 것인지

[privilege_escalation]		//root로 접근해야할 경우 설정
become = true				//sudo = true 라고 생각하시면 됩니다.
become_method = sudo
become_user = root
become_ask_pass = false
```

이런식으로 ansible.cfg 파일을 등록해주면 playbook 작성시 알아서 inventory 파일을 읽어오게 된다.

ansible.cfg 작성 말고도 본인이 자주 사용하는 AD-hoc 방식으로 참조도 가능하다.

## AD-Hoc

```
ansible-playbook playbook.yml -i inventory
```

## Inventory 변수 기능 및 설명

---

ansible_connetcion

해당 호스트에 대한 연결 설정을 정하는 것이다.

기본적으로 ssh 또는 smart로 설정되어 있으며, 

주로 ssh통신을 사용하지 않을 때 설정해준다.

```
ansible-doc -t connection -l
```

해당 명령어를 통해 Plugin List를 확인해 볼 수 있다.

```
kubectl      Execute tasks in pods running on Kubernetes
napalm       Provides persistent connection using NAPALM
qubes        Interact with an existing QubesOS AppVM
libvirt_lxc  Run tasks in lxc containers via libvirt
funcd        Use funcd to connect to target
chroot       Interact with local chroot
psrp         Run tasks over Microsoft PowerShell Remoting Protocol
zone         Run tasks in a zone instance
winrm        Run tasks over Microsoft's WinRM
paramiko_ssh Run tasks via python ssh (paramiko)
local        execute on controller
network_cli  Use network_cli to run command on network appliances
netconf      Provides a persistent connection using the netconf protocol
vmware_tools Execute tasks inside a VM via VMware Tools
podman       Interact with an existing podman container
lxd          Run tasks in lxc containers via lxc CLI
lxc          Run tasks in lxc containers via lxc python library
ssh          connect via ssh client binary
httpapi      Use httpapi to run command on network appliances
iocage       Run tasks in iocage jails
oc           Execute tasks in pods running on OpenShift
persistent   Use a persistent unix socket for connection
jail         Run tasks in jails
saltstack    Allow ansible to piggyback on salt minions
docker       Run tasks in docker containers
buildah      Interact with an existing buildah container
```

- ansible_host

호스트 변수 설정시 해당 호스트의 IP주소를 설정하는 변수

- ansible_port

호스트 변수 설정시 해당 호스트의 port를 설정하는 변수

- ansible_user

호스트 변수 설정시 해당 호스트의 계정을 설정하는 변수

- ansible_password

해당 호스트의 서버에 접근 시 비밀번호를 입력받는 변수

### 예시

```yaml
[all:vars]		//전역 변수식으로 모든 host들에게 적용할 때
ansible_user: admin
ansible_password: password
ansible_connection: httpapi  # REST API connection method

[webserver]		//하나의 호스트에만 적용시킬 때
ansible_host: 10.0.0.1
ansible_user: admin
ansible_password: password
ansible_connection: network_cli
```

> inventory에서 저런식으로 설정할 수 있다.
> 

또한 ssh 통신시 사용하는 변수들과 docker에서 사용하는 변수들도 있다.

---
