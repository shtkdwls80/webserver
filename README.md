# webserver

## 프로젝트 소개
Docker를 이용한 nginx 웹서버 구축 실습 프로젝트입니다.

## 사용 기술
- Docker
- nginx
- HTML

## 파일 구조
webserver/
├── Dockerfile
└── index.html

## 실행 방법

### 1. 이미지 빌드
docker build -t my-webserver:1.0 .

### 2. 컨테이너 실행
docker run -d -p 8080:80 my-webserver:1.0

### 3. 브라우저 접속
http://localhost:8080
