## 개요
Centos 8 에 NVIDIA 그래픽 드라이버 설치하는 과정을 작성 한다.

## 설치 과정
### OS graphic driver 버전 확인
```shell
$ lspci -vnn | grep VGA
```
### 위에서 확인한 버전으로 NVIDIA 홈페이지 에서 버전에 맞는 드라이버 설치

nvidia 드라이버 설치 진행 시에 현재 그랙픽 카드에 맞는 버전인 nvidia 440.X 를 설치하여도, 390.xx 버전으로 설치하라고 나오기 때문에 처음부터 390.X 버전을 설치

### 드라이버 설치 전 필요 패키지 설치
```shell
$ dnf groupinstall “Development Tools”
$ dnf install libglvnd-devel elfutils-libelf-devel
```

### 기존 드라이버 nouveau 비활성화
```shell
$ grub2-editenv — set “$(grub2-editenv — list | grep kernelopts) nouveau.modeset=0”
```

### nouveau 비활성화 시 cui 환경으로 전환

cui 환경으로 전환해야 블랙스크린을 피하면서 작업을 이어갈 수 있다.
```shell
$ systemctl set-default multi-user.target
```
### 재시작 (reboot)
```shell
$ reboot
```
### 드라이버 다운받은 경로롤 진입 후 설치 진행
```
$ bash NVIDIA-Linux-x86_64-*
```
드라이버 설치 시 kernel level 관련 에러가 나오면, 커널 업그레이드 혹은 kernel-heeaders / kernel-devel 간의 동기화 작업이 필요

해결 방법은 [링크](https://linuxconfig.org/error-unable-to-find-the-kernel-source-tree-for-the-currently-running-kernel-centos-rhel) 참고

### 드라이버 설치 완료 후 gui 모드로 변경
$ systemctl set-default graphical.target

### 로그인 후 블랙 스크린 뜨는 현상은 아래와 같이 처리(물론, CUI 환경에서)
```shell
$ systemctl restart systemd-logind
```

위와 같이 입력 후에도 동일 현상이 일어난다면 아래와 같이 진행
```shell
$ vi /etc/default/grub
```

GRUB_CMDLINE_LINUX 라인에 있는 rhgb 부분을 삭제한다.

### 이 후 적용을 위한 아래 커맨드 입력
```shell
$ grub2-mkconfig
$ reboot
```

### 설치 확인
```shell
$ nvidia-smi
```

## 참고
https://linuxconfig.org/how-to-install-the-nvidia-drivers-on-centos-8
