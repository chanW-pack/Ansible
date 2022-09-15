# Ansible_lineinfile 모듈 (파일 수정)

---

## lineinfile 모듈

---

ansible의 lineinfie 모듈은 regular expression을 사용하여 파일의 내용 변경 작업을 하는 모듈이다.

- Ansible 가이드 페이지
[docs.ansible.com/ansible/2.5/modules/lineinfile_module.html](https://docs.ansible.com/ansible/2.5/modules/lineinfile_module.html)

### 파일에 내용 추가

```yaml
tasks:
  - name: insert String in file
    lineinfile:
      path: /path/to/file
      line: "삽입할 내용 입력"
```

- path : 내뇽을 추가할 파일 위치
- line : 삽입 내용 입력

### 파일 내용 수정

```yaml
tasks:
  - name: Modify file
    lineinfile:
      path: /path/to/file
      regexp: 'regular expression 입력'
      line: "수정할 내용 입력"
```

- path : 수정할 파일의 위치
- regexp : 정규표현식으로 수정할 파일 내용 탐색
- line : 정규표현식으로 찾은 내용을 line에 입력한 값으로 변경

### 파일의 내용 찾아 다음 line에 삽입

```yaml
tasks:
  - name: insertafter String in file
    lineinfile:
      path: /path/to/file
      insertafter: 'regular expression 입력'
      line: "삽입할 내용 입력"
```

- path : 수정할 파일의 위치
- insertafter : 정규표현식으로 내용 탐색
- line : 정규표현식으로 찾은 행의 다음 line에 입력 값 삽입

### 파일에 해당 line 삭제

```yaml
tasks:
  - name: Modify file
    lineinfile:
      path: /path/to/file
      regexp: 'regular expression 입력'
      state: absent
```

- path : 수정할 파일의 위치
- regexp : 정규표현식으로 내용 찾기
- state : absent 옵션을 설정하면 정규표현식과 일치하는 행을 삭제

---