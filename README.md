# Ansible

<!-- "사업에 쓰이는 그 어떤 기술에도 적용되는 첫번째 규칙은, </br>
효율적인 작업을 위해 적용된 자동화 방식이 효율화를 극대화시킬 것이라는 점이다.</br> </br>
두번째 규칙은, 비효율적인 작업을 위해 적용된 자동화 방식은 비효율화를 극대화시킬 것이라는 점이다."
- `William Henry "Bill" Gates III`
Microsoft CEO -->

## Ansible 개념 (기초 기능)

- [Ansible 개념과 설치/사용법](https://github.com/chanW-pack/Ansible/blob/main/Ansible_%EA%B8%B0%EC%B4%88_%EA%B0%9C%EB%85%90/1.%20%5BAnsible%5D%20%EC%95%A4%EC%84%9C%EB%B8%94(Ansible)%20%EA%B0%9C%EB%85%90%EA%B3%BC%20%EC%84%A4%EC%B9%98%EC%82%AC%EC%9A%A9%EB%B2%95%20(w%20Amazon%20Linux).md)
- [Ansible 호스트 명령 내리기](https://github.com/chanW-pack/Ansible/blob/main/Ansible_%EA%B8%B0%EC%B4%88_%EA%B0%9C%EB%85%90/2.%20%5BAnsible%5D%20%EC%95%A4%EC%84%9C%EB%B8%94(Ansible)%20%ED%98%B8%EC%8A%A4%ED%8A%B8%20%EB%AA%85%EB%A0%B9%20%EB%82%B4%EB%A6%AC%EA%B8%B0.md)
- [Ansible Playbook 만들기 - update, shutdown, 파일 복사하기](https://github.com/chanW-pack/Ansible/blob/main/Ansible_%EA%B8%B0%EC%B4%88_%EA%B0%9C%EB%85%90/3.%20%5BAnsible%5D%20%EC%95%A4%EC%84%9C%EB%B8%94(Ansible)%20Playbook%20%EB%A7%8C%EB%93%A4%EA%B8%B0%20-%20up%207d2d7ecdb3fd46c6a566465aa4e02b1e.md)

## Ansible Funtion

- [작업 제어 구현 - 오류처리](https://github.com/chanW-pack/Ansible/blob/main/Ansible_%EC%9E%91%EC%97%85%20%EC%A0%9C%EC%96%B4%20%EA%B5%AC%ED%98%84(%EC%98%A4%EB%A5%98%20%EC%B2%98%EB%A6%AC).md)
- [When 조건문](https://github.com/chanW-pack/Ansible/blob/main/Ansible_When%20%EC%A1%B0%EA%B1%B4%EB%AC%B8.md)
- [When 조건문 트리구조 (includ_tasks, 여러 조건 사용)](https://github.com/chanW-pack/Ansible/blob/main/Ansible_%ED%8A%B8%EB%A6%AC%EA%B5%AC%EC%A1%B0%20%EC%A1%B0%EA%B1%B4%EB%AC%B8.md)
- [모듈 활용 (shell, user, yum, copy, service, file 등)](https://github.com/chanW-pack/Ansible/blob/main/Ansible_%EB%AA%A8%EB%93%88.md)
- [Inventory 작성방법 (ansible.cfg 등록, inventory 변수 기능)](https://github.com/chanW-pack/Ansible/blob/main/Ansible_Inventory.md)
- [구성파일 (cfg)](https://github.com/chanW-pack/Ansible/blob/main/Ansible_%EA%B5%AC%EC%84%B1%20%ED%8C%8C%EC%9D%BC(cfg).md)
- [반복문 (loop, with_item)](https://github.com/chanW-pack/Ansible/blob/main/Ansible_%EB%B0%98%EB%B3%B5%EB%AC%B8.md)
- [Sysnchronize 모듈 (Remote to Remote 파일전송)](https://github.com/chanW-pack/Ansible/blob/main/Ansible_Sysnchronize%20%EB%AA%A8%EB%93%88(Remote%20to%20Remote%20%ED%8C%8C%EC%9D%BC%EC%A0%84%EC%86%A1).md)

## Ansible PlayBook (기능 구현)

- [출력 결과 관리 (결과값 파일로 저장)](https://github.com/chanW-pack/Ansible/blob/main/Playbook/Ansible_%EC%B6%9C%EB%A0%A5%20%EA%B2%B0%EA%B3%BC%20%EA%B4%80%EB%A6%AC_(%EA%B2%B0%EA%B3%BC%EA%B0%92%20%ED%8C%8C%EC%9D%BC%EB%A1%9C%20%EC%A0%80%EC%9E%A5).md)
- [여러 서비스들 계정 삭제](https://github.com/chanW-pack/Ansible/blob/main/Playbook/Ansible_%EC%97%AC%EB%9F%AC%20%EC%84%9C%EB%B9%84%EC%8A%A4%EB%93%A4%20%EA%B3%84%EC%A0%95%20%EC%82%AD%EC%A0%9C.md)
- [원격 LVM 증설](https://github.com/chanW-pack/Ansible/blob/main/Playbook/Ansible_%EC%9B%90%EA%B2%A9%20LVM%20%EC%A6%9D%EC%84%A4.md)
- [Ansible_sysstat 수집 주기 변경](https://github.com/chanW-pack/Ansible/blob/main/Playbook/Ansible_sysstat%20%EC%88%98%EC%A7%91%20%EC%A3%BC%EA%B8%B0%20%EB%B3%80%EA%B2%BD.md)
- [인프라 취약점 점검/분석](https://github.com/chanW-pack/Ansible/tree/main/Playbook/%EC%B7%A8%EC%95%BD%EC%A0%90%20%EB%B6%84%EC%84%9D)
- [nmon 수집주기 자동 생성](https://github.com/chanW-pack/Ansible/tree/main/Playbook/nmon%20%EC%9E%90%EB%8F%99%20%EC%84%A4%EC%A0%95)
