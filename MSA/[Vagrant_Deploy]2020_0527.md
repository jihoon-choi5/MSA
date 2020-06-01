## git으로부터 프로젝트 배포하기

1. 내 git repository `msa-jenkins-basic` 준비
   - 이 프로젝트를 배포할 것임
2. Jenkins 브라우저 접속
3. 플러그인 설치하기 
   - Jenkins 관리 (메뉴 클릭)
   - 플러그인 관리
   - 설치 가능
     - `github` 검색
       - `github` , `github integration` 클릭 후 `재시작 없이 설치하기` 클릭
     - `maven` 검색
       - `maven invoker` , `maven integration` 클릭 후 `재시작 없이 설치하기` 클릭

![image](https://user-images.githubusercontent.com/20036670/82784370-5ae07380-9e9b-11ea-952c-c3a9406908be.png)



## git 배포 기초

1. jenkins-server , tomcat-server 에 명령어 실행 필요

``` shell
$ sudo yum install git -y
$ sudo yum install maven -y

# 설치 확인 명령어
$ whereis git
```

2. maven 경로 확인하기

``` shell
$ mvn --version
/usr/share/maven 
```

3. jenkins 브라우저에서 maven 설정 추가해주기
   - jenkins 관리
   - Global Tool Configuration
   - Maven

![image](https://user-images.githubusercontent.com/20036670/82853079-a2b8d680-9f3f-11ea-8c0f-92ccddb92a3c.png)

4. 새로운 프로젝트 추가하기
   - 새로운 ITEM
   - 이름
     - `My_maven_Build`
   - Maven Project 선택
   - ok

- 소스 관리 부분
  - 배포할 git repository(hello-world) 주소 지정

![image](https://user-images.githubusercontent.com/20036670/82962991-afe7cb00-9ffc-11ea-9864-5b3d3d3251a5.png)

- Build 부분

![image](https://user-images.githubusercontent.com/20036670/82963002-b70ed900-9ffc-11ea-8a89-71b863015f24.png)

- Apply
- 저장

5. 빌드하기
   - Build Now

![image](https://user-images.githubusercontent.com/20036670/82853238-2bd00d80-9f40-11ea-9366-324feeb5ca0d.png)

6. 성공 시 jenkins-server에서 프로젝트 폴더 생성 확인 가능

``` shell
$ cd /var/lib/jenkins/workspace
$ ls -al
```

![image](https://user-images.githubusercontent.com/20036670/82853287-528e4400-9f40-11ea-818d-6c1d044c806f.png)

7. 프로젝트 폴더 안 `webapp/target` 에 최종 `war` 파일 생성되어 존재

![image](https://user-images.githubusercontent.com/20036670/82853968-152ab600-9f42-11ea-8fd5-678251c33b63.png)





## git 배포 - 커맨드로 배포하기

> 커맨드로 배포할 것이기 때문에 tomcat-server에서 설정했던 유저 중 deployer 유저를 사용할 것임

1. jenkins 플러그인 필요
   - jenkins 브라우저에서 `Deploy to container` 설치하기
2. 새로운 프로젝트 추가하기
   - 새로운 ITEM
   - 이름
     - `Deploy_to_Tomcat_Server`
   - Maven Project 선택
   - ok

- 소스 관리 부분
  - 배포할 git repository 주소 지정

![image](https://user-images.githubusercontent.com/20036670/82962991-afe7cb00-9ffc-11ea-9864-5b3d3d3251a5.png)

- Build 부분

![image](https://user-images.githubusercontent.com/20036670/82963002-b70ed900-9ffc-11ea-8a89-71b863015f24.png)

- 빌드 후 조치 부분
  - jenkins-server의 war 파일을 tomcat-server로 보내는 작업해주는 부분 (복사 개념)
  - 빌드 후 조치 추가
    - `Deploy war/ear to a container` 클릭
  - Containers
    - Add Container
      - Tomcat 9.x Remote
      - Add - Jenkins
        - Username : `deployer`
        - Password : `deployer`

![image](https://user-images.githubusercontent.com/20036670/82851700-8f0b7100-9f3b-11ea-9634-58dc3c58e80b.png)

- Container
- s 추가 후 Credentials 선택 및 연결 Tomcat URL 지정해주기
  - tomcat-server ip 주소 : tomcat 포트

![image](https://user-images.githubusercontent.com/20036670/82851961-6f287d00-9f3c-11ea-8425-d5a5da806d7b.png)

- Apply
- 저장

2. 빌드하기
   - Build Now

3. 빌드 성공 후 tomcat 브라우저에 가서 war 올라갔는지 확인하기
   - `localhost:28080`

![image](https://user-images.githubusercontent.com/20036670/82854955-e6620f00-9f44-11ea-965e-f76a4e2090bb.png)





## crontab 이용한 자동 빌드

1. 프로젝트 `msa-jenkins-basic.git` (hello-world)
   - `webapp/index.jsp` 아무렇게나 수정
2. 저장 후 `add - commit` 까지만 해놓기

3. jenkins 브라우저에 올렸던 `Deploy to container` 프로젝트 - 구성
   - 빌드 유발 부분
     - 언제 빌드 시킬건지 지정해주는 것
     - `Poll SCM` 체크
       - git에 변경된게 있으면 빌드시키는 것
       - `Schedule`에 아래처럼 작업 (`crontab` 작업)
         - 사이사이에 스페이스바 필요
           - 1분마다 계속 빌드하겠다는 의미

![image](https://user-images.githubusercontent.com/20036670/82853677-553d6900-9f41-11ea-8e8c-36d43953b20f.png)

- apply
- 저장

4. 아까 안했던 git `push` 하기
   - git 변경사항을 주는 것
5. 1분 뒤 빌드 자동 실행 되는 것 확인
   - git 내용이 변경되었으므로

![image](https://user-images.githubusercontent.com/20036670/82963143-0523dc80-9ffd-11ea-8b7d-95428039ea7a.png)

6. 배포된 웹 접속해서 수정 확인
   - `localhost:28080/webapp`

![image](https://user-images.githubusercontent.com/20036670/82855262-ced75600-9f45-11ea-9a81-02b5ee3f49ab.png)

7. 확인했으니까 이제 다시 `Poll SCM` 체크 해제 해주기

   -------------

   