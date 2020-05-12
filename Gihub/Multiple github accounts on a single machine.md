# 개요  
단일 머신에서 여러 Github 계정을 사용해야 할 경우 SSH 를 활용하여 관리하는 방법이다.

* 기존에 SSH 키가 있는지 확인한다.
```bash
$ ls -al ~/.ssh
```

* SSH 키를 생성한다.  
기본 SSH 키가 없을 경우 아래 명령어를 통하여 생성한다.
```bash
$ ssh-keygen -t rsa
```

* 추가적으로 사용할 SSH 키를 생성한다.
```bash
$ ssh-keygen -t rsa -C "work@mail.com" -f "id_rsa_work"
```

* 키가 정상적으로 생성 되었는지 확인한다.
```bash
$ ls -al ~/.ssh
```

* Github 계정에 SSH 키를 등록한다.  
GitHub 계정에 생성 한 키를 신뢰하도록 요청하며 git push 시에 사용자 정보를 입력할 필요가 없습니다.  
공개 키 복사 후에 Github 계정에 SSH 키를 등록한다.  
Github Profile > Settings > SSH and GPG keys > 우측 상단 New SSH Key  
```bash
$ pbcopy < ~/.ssh/id_rsa.pub
```

* 추가할 다른 Github 계정도 위와 동일하게 진행한다.

* ssh-agent 에 키 등록  
키 사용을 위해서는 ssh-agent 에 등록해야한다.
```bash
$ ssh-add ~/.ssh/id_rsa
$ ssh-add ~/.ssh/id_rsa_work
```  
ssh-agent 가 실행 중인지 확인하기 위해서는 eval “$(ssh-agent -s)” 명령어를 입력한다.  
  
아래 커맨드를 입력하면 등록된 키가 모두 삭제 된다.
```bash
$ ssh-add -D
```

* SSH 설정 파일 작성  
여러 도메인에 사용할 ID 파일을 나타내는 서로 다른 호스트에 대하여 SSH 설정을 한다.  
SSH 설정 파일은 ~/.ssh/config 에서 사용 가능하며, 파일이 존재하지 않다면 생성하여 진행한다.
```bash
$ vi ~/.ssh/config
# 개인 계정
Host github.com
   HostName github.com
   User git
   IdentityFile ~/.ssh/id_rsa
   
# 업무 계정
Host github.com-work    
   HostName github.com
   User git
   IdentityFile ~/.ssh/id_rsa_work
```

* Github 레파지토리 복사  
레파지토리 복사 시 HTTPS 가 아닌, SSH 로 한다.
```bash
$ git clone git@github.com:userName/userRepository.git
```

기본 계정이 아닌, 업무용 계정으로 복사 시에는 전에 ~/.ssh/config 에서 설정하였던 github.com-work 로 변경하여 복사한다.  
```bash
$ git clone git@github.com-work:workerName/workerRepository.git
```  
단, 이미 레파지토리를 HTTPS 로 복사하여 사용 중인 주소를 변경하고 싶을 때는 아래 명령어를 실행한다.
  
remote 주소 확인
```bash
$ git remote -v
```
  
remote 주소 변경
```bash
$ git remote set-url origin git@github.com-work:workerName/workerRepository.git
```

* 로컬에서 새 저장소를 추가하는 경우
```bash
$ git remote add origin git@github.com-work:workerName/workerRepository.git
```

* 프로젝트별 gitconfig 설정  
git 관련 전역 설정에서 프로젝트 별로 설정을 관리하고 싶다면 아래와 같이 ~/.gitconfig 에서 includeif 를 활용하면 된다.
```bash
$ vi ~/.gitconfig
[user]
 name = user
 email = user@mail.com
 [includeIf “gitdir:~/workspace/project1”]
 path = ~/workspace/project1/.git/config
 [includeIf “gitdir:~/workspace/project2”]
 path = ~/workspace/project2/.git/config
```

## 마치며  
개인 계정과 업무용 계정을 하나의 pc 에서 작업할 때 위와 같은 방법으로 github 여러 계정을 효율적으로 관리하며 사용할 수 있다.
