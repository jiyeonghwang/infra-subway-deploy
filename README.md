<p align="center">
    <img width="200px;" src="https://raw.githubusercontent.com/woowacourse/atdd-subway-admin-frontend/master/images/main_logo.png"/>
</p>
<p align="center">
  <img alt="npm" src="https://img.shields.io/badge/npm-%3E%3D%205.5.0-blue">
  <img alt="node" src="https://img.shields.io/badge/node-%3E%3D%209.3.0-blue">
  <a href="https://edu.nextstep.camp/c/R89PYi5H" alt="nextstep atdd">
    <img alt="Website" src="https://img.shields.io/website?url=https%3A%2F%2Fedu.nextstep.camp%2Fc%2FR89PYi5H">
  </a>
  <img alt="GitHub" src="https://img.shields.io/github/license/next-step/atdd-subway-service">
</p>

<br>

# 인프라공방 샘플 서비스 - 지하철 노선도

<br>

## 🚀 Getting Started

### Install
#### npm 설치
```
cd frontend
npm install
```
> `frontend` 디렉토리에서 수행해야 합니다.

### Usage
#### webpack server 구동
```
npm run dev
```
#### application 구동
```
./gradlew clean build
```
<br>

## 미션

* 미션 진행 후에 아래 질문의 답을 README.md 파일에 작성하여 PR을 보내주세요.

### 0단계 - pem 키 생성하기

1. 서버에 접속을 위한 pem키를 [구글드라이브](https://drive.google.com/drive/folders/1dZiCUwNeH1LMglp8dyTqqsL1b2yBnzd1?usp=sharing)에 업로드해주세요

2. 업로드한 pem키는 무엇인가요.: jiyeonghwang-pem-key.pem

### 1단계 - 망 구성하기
1. 구성한 망의 서브넷 대역을 알려주세요
- 대역 : 192.168.25.0

2. 배포한 서비스의 공인 IP(혹은 URL)를 알려주세요

- URL : http://jiyeonghwang-infra.kro.kr



---

### 2단계 - 배포하기
1. TLS가 적용된 URL을 알려주세요

- URL : https://jiyeonghwang-infra.kro.kr

---

### 3단계 - 배포 스크립트 작성하기

1. 작성한 배포 스크립트를 공유해주세요.

<details>
<summary>배포 스크립트</summary>

경로: /home/ubuntu/bin/deploy.sh
```shell

#!/bin/bash

## 변수 설정

txtrst='\033[1;37m' # White
txtred='\033[1;31m' # Red
txtylw='\033[1;33m' # Yellow
txtpur='\033[1;35m' # Purple
txtgrn='\033[1;32m' # Green
txtgra='\033[1;30m' # Gray

REPOSITORY=/home/ubuntu/nextstep/infra-subway-deploy/

echo -e "${txtylw}=======================================${txtrst}"
echo -e "${txtgrn}  << 스크립트 🧐 >>${txtrst}"
echo -e "${txtylw}=======================================${txtrst}"

function pull() {

	## 저장소 pull
	echo -e "${txtylw}=======================================${txtrst}"
	echo -e "${txtgrn}<< Git Pull >>${txtrst}"
	echo -e "${txtylw}=======================================${txtrst}"

	cd $REPOSITORY
	git pull
}


function build() {

	## gradle build
	echo -e "${txtylw}=======================================${txtrst}"
	echo -e "${txtgrn}<< build >>${txtrst}"
	echo -e "${txtylw}=======================================${txtrst}"
	./gradlew clean build

}

function search_pid() {

	## 프로세스 pid를 찾는 명령어
	CURRENT_PID=$(pgrep -f subway-0.0.1-SNAPSHOT.jar)

	echo -e "${txtylw}=======================================${txtrst}"
	echo -e "pid: $CURRENT_PID"
	echo -e "${txtylw}=======================================${txtrst}"

}

function process_kill() {

	## 프로세스를 종료하는 명령어
	if [ -z $CURRENT_PID ]; then
			echo -e "${txthlw} 현재 구동중인 어플리케이션이 없으므로 종료하지 않습니다."
	else
			echo -e "${txtred} kill -2 $CURRENT_PID ${txtrst}"
			kill -2 $CURRENT_PID
			sleep 5
	fi

}


function process_start() {

	## 프로세스를 실행하는 명령어
	cd $REPOSITORY/build/libs

	echo -e "${txtylw}=======================================${txtrst}"
	echo -e "프로세스 실행"
	echo -e "${txtylw}=======================================${txtrst}"

	nohup java -jar -Dspring.profiles.active=prod subway-0.0.1-SNAPSHOT.jar 1> log.log 2>&1 &

	java -jar -Dspring.profiles.active=prod subway-0.0.1-SNAPSHOT.jar &

	echo -e "${txtylw}=======================================${txtrst}"
	echo -e "프로세스 실행 완료"
	echo -e "${txtylw}=======================================${txtrst}"

}

pull;
build;
search_pid;
process_kill;
process_start;
```
</details>

<details>
<summary>변경 사항 check</summary>

경로: /home/ubuntu/bin/batch_deploy.sh
```shell
function check_df() {
  git fetch
  master=$(git rev-parse $BRANCH)
  remote=$(git rev-parse origin/$BRANCH)

  if [[ $master == $remote ]]; then
    echo -e "[$(date)] Nothing to do!!! 😫"
    exit 0
  else
	echo -e "[$(date)] BATCH START!!! 😫"
	./deploy.sh
  fi
}

check_df;
```
</details>

