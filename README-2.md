# 개발 워크스테이션 구축 미션

> 터미널 · Docker · Git/GitHub로 "누가 해도 똑같이 돌아가는" 개발 환경을 직접 구축하고, 모든 수행 결과를 이 문서에 기록한다.

> **작성 방법**: `<여기에 ...>` 로 표시된 부분을 실제 실행 결과로 교체하세요. 완료 후 이 안내문은 삭제합니다.

---

## 목차

1. [프로젝트 개요](#1-프로젝트-개요)
2. [실행 환경](#2-실행-환경)
3. [수행 체크리스트](#3-수행-체크리스트)
4. [터미널 조작 로그](#4-터미널-조작-로그)
5. [권한 실습](#5-권한-실습)
6. [Docker 설치 및 기본 점검](#6-docker-설치-및-기본-점검)
7. [Docker 기본 운영 명령](#7-docker-기본-운영-명령)
8. [컨테이너 실행 실습](#8-컨테이너-실행-실습)
9. [커스텀 이미지 제작 (Dockerfile)](#9-커스텀-이미지-제작-dockerfile)
10. [포트 매핑 및 접속 증거](#10-포트-매핑-및-접속-증거)
11. [바인드 마운트 반영 검증](#11-바인드-마운트-반영-검증)
12. [Docker 볼륨 영속성 검증](#12-docker-볼륨-영속성-검증)
13. [Git 설정 및 GitHub 연동](#13-git-설정-및-github-연동)
14. [검증 방법 요약](#14-검증-방법-요약)
15. [트러블슈팅](#15-트러블슈팅)
16. [보안 및 개인정보 점검](#16-보안-및-개인정보-점검)
17. [회고](#17-회고)

---

## 1. 프로젝트 개요

### 미션 목표
- "내 컴퓨터에서만 돌아가는" 문제를 줄이고, 누구나 같은 방식으로 실행·배포·디버깅할 수 있는 환경을 만든다.
- 리눅스 CLI(터미널), Docker(컨테이너), Git/GitHub(버전 관리·협업)를 직접 사용해 본다.
- 실행 결과(로그/접속/데이터 유지)로 핵심 흐름을 확인하고, 설계 원칙을 설명 가능한 형태로 정리한다.

### 핵심 원칙
| 원칙 | 설명 |
|---|---|
| 이미지와 컨테이너의 분리 | 이미지는 설계도, 컨테이너는 그 설계도로 만든 실행물 |
| 격리된 실행 환경 | 컨테이너끼리, 그리고 호스트와 서로 영향을 주지 않음 |
| 포트·스토리지 연결 | 외부 접속은 포트 매핑, 데이터 보존은 볼륨/바인드 마운트로 연결 |

### 저장소 구조
```
.
├── README.md
├── Dockerfile
├── index.html          # 커스텀 콘텐츠 (또는 app/ 디렉토리)
└── screenshots/        # 브라우저 접속, VSCode 연동 캡처
```

---

## 2. 실행 환경

| 항목 | 내용 |
|---|---|
| OS | `<예: Windows 11 / macOS 14 / Ubuntu 22.04>` |
| 쉘/터미널 | `<예: bash (WSL2) / zsh / PowerShell>` |
| Docker 버전 | `<docker --version 결과>` |
| Git 버전 | `<git --version 결과>` |
| 에디터 | VSCode `<버전>` |

```bash
$ docker --version
<출력 붙여넣기>

$ git --version
<출력 붙여넣기>

$ echo $SHELL
<출력 붙여넣기>
```

---

## 3. 수행 체크리스트

- [ ] 터미널: 위치 확인 / 목록(숨김 포함) / 이동 / 생성 / 복사 / 이동·이름변경 / 삭제 / 내용 확인 / 빈 파일 생성
- [ ] 권한: 파일 1개 + 디렉토리 1개 변경 전/후 비교
- [ ] Docker: 설치 확인 (`docker --version`) 및 데몬 점검 (`docker info`)
- [ ] Docker 운영: 이미지 다운로드·목록 / 컨테이너 실행·중지·목록 / 로그 / 리소스(`stats`)
- [ ] 컨테이너 실습: `hello-world` / `ubuntu` 내부 진입 / attach·exec 차이 정리
- [ ] Dockerfile: 커스텀 이미지 빌드 및 실행 성공
- [ ] 포트: 포트 매핑 후 접속 증거(브라우저 또는 `curl`)
- [ ] 마운트: 바인드 마운트 변경 반영 확인
- [ ] 볼륨: 컨테이너 삭제 전/후 데이터 유지 확인
- [ ] Git: 사용자 정보·기본 브랜치 설정, `git config --list` 기록
- [ ] GitHub: VSCode 로그인 및 저장소 연동 증거
- [ ] 보안: 토큰/비밀번호/이메일 등 민감정보 마스킹 확인

---

## 4. 터미널 조작 로그

> 작업 디렉토리: `<예: ~/workstation-mission>`

### 4.1 현재 위치 확인
```bash
$ pwd
<출력>
```

### 4.2 목록 확인 (숨김 파일 포함)
```bash
$ ls -la
<출력>
```

### 4.3 디렉토리 생성 및 이동
```bash
$ mkdir practice
$ cd practice
$ pwd
<출력>
```

### 4.4 빈 파일 생성
```bash
$ touch empty.txt
$ ls -l
<출력>
```

### 4.5 파일 내용 작성 및 확인
```bash
$ echo "hello terminal" > hello.txt
$ cat hello.txt
<출력>
```

### 4.6 복사
```bash
$ cp hello.txt hello_copy.txt
$ ls -l
<출력>
```

### 4.7 이동 / 이름 변경
```bash
$ mv hello_copy.txt renamed.txt
$ ls -l
<출력>
```

### 4.8 삭제
```bash
$ rm renamed.txt
$ ls -l
<출력>
```

---

## 5. 권한 실습

### 5.1 파일 권한 변경
```bash
# 변경 전
$ ls -l hello.txt
<출력: 예 -rw-r--r-- ...>

$ chmod 600 hello.txt

# 변경 후
$ ls -l hello.txt
<출력: 예 -rw------- ...>
```

### 5.2 디렉토리 권한 변경
```bash
# 변경 전
$ ls -ld practice
<출력>

$ chmod 700 practice

# 변경 후
$ ls -ld practice
<출력>
```

### 5.3 비교 요약
| 대상 | 변경 전 | 변경 후 | 의미 |
|---|---|---|---|
| `hello.txt` | `<>` | `<>` | `<예: 소유자만 읽기/쓰기>` |
| `practice/` | `<>` | `<>` | `<예: 소유자만 접근 가능>` |

---

## 6. Docker 설치 및 기본 점검

```bash
$ docker --version
<출력>

$ docker info
<출력 (핵심 부분: Server Version, Containers, Images 등)>
```

- 데몬 동작 여부 판단 근거: `<예: docker info에서 Server 항목이 정상 출력됨>`

---

## 7. Docker 기본 운영 명령

### 7.1 이미지
```bash
$ docker pull ubuntu
<출력>

$ docker images
<출력>
```

### 7.2 컨테이너 목록 / 중지
```bash
$ docker ps
<출력>

$ docker ps -a
<출력>

$ docker stop <컨테이너 이름>
<출력>
```

### 7.3 로그
```bash
$ docker logs <컨테이너 이름>
<출력>
```

### 7.4 리소스
```bash
$ docker stats --no-stream
<출력>
```

---

## 8. 컨테이너 실행 실습

### 8.1 hello-world
```bash
$ docker run hello-world
<출력: "Hello from Docker!" 메시지 포함>
```

### 8.2 ubuntu 컨테이너 내부 진입
```bash
$ docker run -it --name ubuntu-test ubuntu bash
root@<컨테이너ID>:/# ls
<출력>
root@<컨테이너ID>:/# echo "hello from container"
<출력>
root@<컨테이너ID>:/# exit
```

### 8.3 attach vs exec 관찰

```bash
# 백그라운드로 컨테이너 실행
$ docker run -dit --name attach-test ubuntu bash

# (1) exec: 새 프로세스를 열어 들어갔다 나오기
$ docker exec -it attach-test bash
root@...:/# exit
$ docker ps
<출력: 컨테이너가 계속 실행 중인지 확인>

# (2) attach: 메인 프로세스에 붙기
$ docker attach attach-test
root@...:/# exit
$ docker ps -a
<출력: 컨테이너 상태 확인>
```

**관찰 결과 (본인 말로 정리)**

| 구분 | `docker exec` | `docker attach` |
|---|---|---|
| 동작 | `<관찰한 내용>` | `<관찰한 내용>` |
| exit 후 컨테이너 | `<유지 / 종료>` | `<유지 / 종료>` |

> 정리: `<예: attach는 메인 프로세스에 직접 붙기 때문에 exit하면 컨테이너가 종료되었고, exec는 별도 프로세스라 컨테이너가 유지되었다.>`

---

## 9. 커스텀 이미지 제작 (Dockerfile)

### 9.1 선택한 방식
- **방식**: (A) 웹 서버 베이스 이미지 활용 + 정적 콘텐츠 교체
- **기존 베이스**: `nginx:alpine` (Docker Hub 공식 이미지)
- **선택 이유**: `<예: 용량이 작고, 정적 파일 교체만으로 결과를 바로 확인할 수 있어서>`

### 9.2 커스텀 포인트

| 커스텀 포인트 | 목적 |
|---|---|
| `index.html` 교체 | `<기본 NGINX 페이지 대신 내가 만든 페이지를 제공>` |
| `<추가한 항목>` | `<목적>` |

### 9.3 소스 코드

**index.html**
```html
<!DOCTYPE html>
<html lang="ko">
<head>
  <meta charset="UTF-8">
  <title>My Workstation</title>
</head>
<body>
  <h1>Hello, Docker!</h1>
  <p>개발 워크스테이션 미션 - 커스텀 NGINX 이미지</p>
</body>
</html>
```

**Dockerfile**
```dockerfile
FROM nginx:alpine
COPY index.html /usr/share/nginx/html/index.html
EXPOSE 80
```

### 9.4 빌드 및 실행
```bash
$ docker build -t my-nginx:1.0 .
<출력: 빌드 성공 로그>

$ docker images | grep my-nginx
<출력>

$ docker run -d --name web -p 8080:80 my-nginx:1.0
<출력: 컨테이너 ID>

$ docker ps
<출력>
```

---

## 10. 포트 매핑 및 접속 증거

- 매핑: 호스트 `8080` → 컨테이너 `80` (`-p 8080:80`)

```bash
$ curl http://localhost:8080
<출력: index.html 내용>
```

**브라우저 접속 화면 (주소창 포함)**

![포트 매핑 접속](screenshots/port-mapping.png)

---

## 11. 바인드 마운트 반영 검증

호스트 파일을 수정하면 컨테이너에 즉시 반영되는지 확인한다.

```bash
# 기존 컨테이너 정리 후 바인드 마운트로 실행
$ docker rm -f web
$ docker run -d --name web-bind -p 8080:80 \
    -v "$(pwd)/index.html":/usr/share/nginx/html/index.html \
    nginx:alpine

# 변경 전
$ curl http://localhost:8080
<출력>

# 호스트에서 파일 수정
$ sed -i 's/Hello, Docker!/Hello, Bind Mount!/' index.html
$ cat index.html
<출력>

# 변경 후 (컨테이너 재시작 없이)
$ curl http://localhost:8080
<출력: 변경된 내용>
```

> Windows PowerShell 사용 시 `$(pwd)` 대신 `${PWD}`를 사용하고, `sed` 대신 에디터로 직접 수정해도 됩니다.

| 구분 | 결과 |
|---|---|
| 변경 전 | `<출력 요약>` |
| 변경 후 | `<출력 요약>` |

---

## 12. Docker 볼륨 영속성 검증

```bash
# 1) 볼륨 생성
$ docker volume create mydata
<출력>

$ docker volume ls
<출력>

# 2) 컨테이너에 연결하고 데이터 기록
$ docker run -d --name vol-test -v mydata:/data ubuntu sleep 3600
$ docker exec vol-test sh -c "echo 'persistent data' > /data/note.txt"
$ docker exec vol-test cat /data/note.txt
<출력: 삭제 전 데이터 확인>

# 3) 컨테이너 삭제
$ docker rm -f vol-test
$ docker ps -a
<출력: vol-test 없음>

# 4) 새 컨테이너로 같은 볼륨 연결 후 확인
$ docker run --rm -v mydata:/data ubuntu cat /data/note.txt
<출력: 삭제 후에도 데이터 유지>

$ docker volume inspect mydata
<출력>
```

| 시점 | 확인 결과 |
|---|---|
| 컨테이너 삭제 전 | `<note.txt 내용>` |
| 컨테이너 삭제 후 | `<note.txt 내용 (동일)>` |

---

## 13. Git 설정 및 GitHub 연동

### 13.1 Git 설정
```bash
$ git config --global user.name "<표시할 이름>"
$ git config --global user.email "<GitHub noreply 이메일 권장>"
$ git config --global init.defaultBranch main

$ git config --list
<출력: 이메일 등 민감 정보는 마스킹>
```

### 13.2 저장소 생성 및 푸시
```bash
$ git init
$ git add .
$ git commit -m "docs: add workstation mission README"
$ git branch -M main
$ git remote add origin https://github.com/<계정>/<저장소>.git
$ git push -u origin main
<출력>
```

### 13.3 VSCode GitHub 연동 증거

![VSCode GitHub 로그인](screenshots/vscode-github-login.png)
![VSCode 저장소 연동](screenshots/vscode-repo.png)

---

## 14. 검증 방법 요약

| 검증 항목 | 사용한 명령 | 확인한 내용 | 결과 위치 |
|---|---|---|---|
| 터미널 조작 | `pwd`, `ls -la`, `cp`, `mv`, `rm`, `cat` | 파일 생성/복사/이동/삭제 동작 | [4장](#4-터미널-조작-로그) |
| 권한 변경 | `ls -l`, `chmod` | 권한 문자열 변화 | [5장](#5-권한-실습) |
| Docker 설치 | `docker --version`, `docker info` | 데몬 정상 동작 | [6장](#6-docker-설치-및-기본-점검) |
| Docker 운영 | `images`, `ps -a`, `logs`, `stats` | 이미지/컨테이너 상태 | [7장](#7-docker-기본-운영-명령) |
| 컨테이너 실습 | `run hello-world`, `exec`, `attach` | 실행 성공, 종료/유지 차이 | [8장](#8-컨테이너-실행-실습) |
| 커스텀 이미지 | `docker build`, `docker run` | 빌드·실행 성공 | [9장](#9-커스텀-이미지-제작-dockerfile) |
| 포트 매핑 | `-p 8080:80`, `curl` | 접속 응답 | [10장](#10-포트-매핑-및-접속-증거) |
| 바인드 마운트 | `-v 호스트:컨테이너`, `curl` | 호스트 변경 즉시 반영 | [11장](#11-바인드-마운트-반영-검증) |
| 볼륨 영속성 | `volume create`, `rm -f`, `cat` | 삭제 후 데이터 유지 | [12장](#12-docker-볼륨-영속성-검증) |
| Git/GitHub | `git config --list`, 스크린샷 | 설정 및 연동 완료 | [13장](#13-git-설정-및-github-연동) |

---

## 15. 트러블슈팅

> 실습 중 실제로 막힌 순간을 기록합니다. 최소 2건.

### 사례 1: `<문제 제목>`
- **문제**: `<어떤 오류/증상이 발생했는가>`
  ```
  <오류 메시지>
  ```
- **원인 가설**: `<왜 그럴 거라고 생각했는가>`
- **확인**: `<가설을 검증한 명령과 결과>`
  ```bash
  $ <명령>
  <출력>
  ```
- **해결/대안**: `<어떻게 해결했는가>`

### 사례 2: `<문제 제목>`
- **문제**: `<>`
- **원인 가설**: `<>`
- **확인**: `<>`
- **해결/대안**: `<>`

<details>
<summary>자주 발생하는 문제 예시 (참고용, 제출 전 삭제)</summary>

- `port is already allocated` → 해당 포트를 쓰는 다른 컨테이너/프로세스 확인 (`docker ps`) 후 다른 포트로 매핑
- `permission denied while trying to connect to the Docker daemon socket` → 사용자를 docker 그룹에 추가 또는 Docker Desktop 실행 여부 확인
- `Cannot connect to the Docker daemon` → Docker Desktop/데몬이 켜져 있는지 확인
- 바인드 마운트 경로 오류 → 절대 경로 사용, Windows 경로 형식 확인
- `container name is already in use` → `docker rm -f <이름>` 후 재실행

</details>

---

## 16. 보안 및 개인정보 점검

제출 전 아래 항목을 확인했다.

- [ ] 로그/스크린샷에 토큰, 비밀번호, 개인키, 인증 코드가 없다.
- [ ] `git config --list` 출력의 이메일이 마스킹되었거나 noreply 주소다.
- [ ] VSCode 로그인 스크린샷에 민감한 정보가 노출되지 않는다.
- [ ] `.env`, 키 파일 등이 저장소에 포함되지 않았다.
- [ ] (노출 발생 시) 해당 토큰을 폐기하고 재발급했으며, 히스토리에서 제거했다.

---

## 17. 회고

- 새로 알게 된 점: `<>`
- 어려웠던 점: `<>`
- 이미지/컨테이너 분리, 격리, 포트·스토리지 연결이 왜 필요한지에 대한 나의 설명: `<>`
