# EXE.Nginx_Load_Balancing
# 1. 로컬에서 동일한 SpringBoot 서버 여러개 만들어서 실행

우선 테스트 해보기 위해 같은 Spring Boot 서버를 다른 포트로 여러 개 만들어서 실행해놓겠습니다!

### 1-1. Edit Configurations 접속

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/0ee43fab-2947-49b1-b42b-cf1eb07e56bc/fd7cb62a-64a6-4f37-9f6d-3b487ea735d3/Untitled.png)

### 1-2. 왼쪽 상단의 Copy Configuration 선택

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/0ee43fab-2947-49b1-b42b-cf1eb07e56bc/53652dbd-e2d2-4bd3-b081-7d65bf5cc977/Untitled.png)

### 1-3. Add Run Options - Add VM options

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/0ee43fab-2947-49b1-b42b-cf1eb07e56bc/f9146c45-0613-4deb-8b5a-428fb3ef3560/Untitled.png)

### 1-4. 새로운 포트 번호 입력

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/0ee43fab-2947-49b1-b42b-cf1eb07e56bc/cf040a75-3968-4eca-92b8-508916e95e59/Untitled.png)

```jsx
-Dserver.port=포트번호 
```

### 1-5. 서버 실행

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/0ee43fab-2947-49b1-b42b-cf1eb07e56bc/1dc5a6cb-62ae-409e-9c44-5f7efddcad7e/Untitled.png)

저는 포트 번호를 기존에 있던 8080 포트와 새롭게 추가한 8081 포트를 사용하였습니다.

# 2. Nginx

다음으로 Nginx를 설치하겠습니다!

저는 mac 환경이기 때문에 HomeBrew를 통해 설치하고 Mac 환경에서 진행하겠습니다!

### 2-1. HomeBrew를 통한 Nginx 설치

```jsx
$ brew install nginx
```

### 2-2. Nginx 설정파일 확인

```jsx
$ brew info nginx
```

이렇게 입력하면 default port가 설정된 설정 파일의 경로가 나오는데 해당 경로를 이용하여 다음 단계의 수정을 진행하면 됩니다!

### 2-3. nginx.conf 수정

```jsx
$ sudo vim nginx.conf의 경로
$ sudo vim /opt/homebrew/etc/nginx/nginx.conf
```

저는 위와 같은 경로였기 때문에 저렇게 사용했습니다!

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/0ee43fab-2947-49b1-b42b-cf1eb07e56bc/9ed02f34-eb28-428e-b731-9e5ee9e9bfeb/Untitled.png)

### 2-4. 로드밸런싱 설정

[Nginx HTTP Load Balancing](https://docs.nginx.com/nginx/admin-guide/load-balancer/http-load-balancer/) 공식 문서를 참고하시면 좋습니다!

경로를 지정해준뒤 upstream 내에서 로드밸런싱에 사용될 알고리즘을 정해야합니다. 사용 가능한 알고리즘은 다음과 같습니다. 저는 기본 설정이 

### **Round Robin**

클라이언트의 요청을 여러 대의 서버에 순차적으로 분배하는 알고리즘, 기본 설정

```jsx
upstream sample {
  ...
}
```

### **Least Connections**

연결된 횟수가 가장 적은 서버로 연결되는 알고리즘

```jsx
upstream sample {
  least_conn;
  ...
}
```

### **IP Hash**

클라이언트 IP를 해싱하여 특정 클라이언트는 특정 서버로 연결, Stick Session 세션 방식처럼 동작 가능

```jsx
upstream sample {
  ip_hash;
  ...
}
```

### **Generic Hash**

사용자가 정의한 다양한 변수를 조합해 트래픽을 분산 가능

```jsx
upstream sample {
  hash $request_uri;
  ...
}
```

### **Random**

트래픽을 무작위로 분배

```jsx
upstream sample {
  random;
  ...
}
```

### 2-5. 설정 파일 적용

```jsx
$ nginx -s reload
```

# 테스트
