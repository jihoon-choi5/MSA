# 200528 MSA Day15

- docker

  - cpu 2, memory 2048로 변환

  - port 38080

  - docker ce 설치

    > https://docs.docker.com/engine/install/centos/

    ```zsh
    $ sudo yum install -y yum-utils
    
    $ sudo yum-config-manager \
        --add-repo \
        https://download.docker.com/linux/centos/docker-ce.repo
        
    $ sudo yum install docker-ce docker-ce-cli containerd.io
    ```

  - docker 시작

    ```zsh
    systemctl start docker
    docker run hello-world
    ```

  - docker admin 계정 생성 (password : fkjjli0719)

    ```zsh
    sudo useradd dockeradmin
    sudo passwd dockeradmin
    ```

  - dockeradmin에 docker 그룹 추가 

    ```zsh
    sudo usermod -aG docker dockeradmin
    ```

    - docker ps 하기 전에 dockeradmin으로 접속

      ```zsh
      [vagrant@docker-server ~]$ su - dockeradmin
      Password:
      [dockeradmin@docker-server vagrant]$ docker ps
      CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
      ```

  - 1,2,3 모두 docker 설치 (ansible, jenkins, tomcat)

  - docker-server에 tomcat 설치

    ```zsh
    docker pull tomcat
    [dockeradmin@docker-server vagrant]$ docker run -d -p8080:8080 --name tomcat tomcat
    ```

    - container 내부의 webapp의 내용을 copy

      ```bash
      [dockeradmin@docker-server vagrant]$ docker exec -it 25e204877f0b bash
      root@25e204877f0b:/usr/local/tomcat# cp -R ./webapps.dist/ROOT ./webapps
      root@25e204877f0b:/usr/local/tomcat# exit
      exit
      [dockeradmin@docker-server vagrant]$ docker stop tomcat
      tomcat
      [dockeradmin@docker-server vagrant]$ docker start tomcat
      tomcat
      ```

      

- ansible 서버

  - vagrant에 똑같은 내용 하나 만들어 주면 됨
  - ip는 10번
  - host port는 9211

  ```vagrant
    # Ansible
    config.vm.define:"ansible-server" do |cfg|
      cfg.vm.box = "centos/7"
      cfg.vm.provider:virtualbox do |vb|
          vb.name="ansible-server"
          vb.customize ["modifyvm", :id, "--cpus", 2]
          vb.customize ["modifyvm", :id, "--memory", 2048]
      end
      cfg.vm.host_name="ansible-server"
      cfg.vm.network "private_network", ip: "192.168.56.10"
      cfg.vm.network "forwarded_port", guest: 22, host: 9211, auto_correct: false, id: "ssh"
    end
  ```

- vagrant reload 

  - 현재 올라가 있는 설정을 이용하여 다시 시작함

- vagrant ssh 서버이름 : 콘솔에서 접속할때 사용

  - 다른 곳에서 사용하려면 `vagrant ssh-config` 명령으로 private key 값 확인

  - ssh 뒤에 id와 password 필요

    ```zsh
    ssh -p 22 -i 키값파일주소 vagrant@ip주소
    # 또는
    ssh id@ip주소 -p 설정파일port번호
    password : "private_key 값"
    ```

    ![image-20200528122041741](C:\Users\HPE\AppData\Roaming\Typora\typora-user-images\image-20200528122041741.png)

--------------

1. **jenkins- 관리 page**

   -> 플러그인 -> 설치된 플러그인 목록 -> publish over ssh

   ![image-20200528104430661](C:\Users\HPE\AppData\Roaming\Typora\typora-user-images\image-20200528104430661.png)

2. **SSH server**

   ![image-20200528104543818](C:\Users\HPE\AppData\Roaming\Typora\typora-user-images\image-20200528104543818.png)

   name: docker-host

   username: dockeradmin

   Hostname: 192.168.56.13

   체크 해야만 password 나옴. 

   password / Password : 

   11번 port에서 13번 port로 갈 때 사용할 password.

   

3. **docker에서 sshd-config**

   

4. **new item**

   ![image-20200528104720418](C:\Users\HPE\AppData\Roaming\Typora\typora-user-images\image-20200528104720418.png)

5. ![image-20200528104902585](C:\Users\HPE\AppData\Roaming\Typora\typora-user-images\image-20200528104902585.png)

6. **jenkins-server ->  cd /var/lib/jenkins/workspace/Deploy_to_Tomcat_Server**

   -> ls -al -> cd webapp -> target -> **webapp.war이 필요함.**

   

7. **경로 찾은 후 build 후 조치 설정으로. -> build 실행.** 

   ![image-20200528105705013](C:\Users\HPE\AppData\Roaming\Typora\typora-user-images\image-20200528105705013.png)

   

8. **docker-server 에서 전달 받은 war 파일을 tomcat-container에서 배포.**

   

9. **docker-server에서 dockerfile만들고, bulid -> docker images**

   - ![image-20200528115607173](C:\Users\HPE\AppData\Roaming\Typora\typora-user-images\image-20200528115607173.png)

     

   - docker build -t hello-project .

     ![image-20200528115642227](C:\Users\HPE\AppData\Roaming\Typora\typora-user-images\image-20200528115642227.png)

     

   - docker run -d --name hello-container -p 8080:8080 hello-project

   - docker ps -a

     ![image-20200528115701616](C:\Users\HPE\AppData\Roaming\Typora\typora-user-images\image-20200528115701616.png)

   

10. **localhost:38080/webapp/**

    ![image-20200528115733844](C:\Users\HPE\AppData\Roaming\Typora\typora-user-images\image-20200528115733844.png)

    -----------

    - **tomcat images**

      FROM tomcat:latest

      COPY ./webapp.war /usr/local/tomcat/webapps

      

    - maven+jdk(openjdk 8)

    - **maven images**

      FROM maven:3-openjdk-8-slim

      COPY ./webapp.jar /usr/src/webapp.jar

      ENTRYPOINT ["java", "-jar", "webapp.jar"]

      ENTRYPOINT ["mvn", "spring-boot:run"]

    -------

    

11. Jenkins에서 환경구성 -> 빌드 후 조치의 excute-command에 복사.(;로 붙이면 한줄로 쭉 사용가능.)

    ![image-20200528120447178](C:\Users\HPE\AppData\Roaming\Typora\typora-user-images\image-20200528120447178.png)

    - cd /home/dockeradmin;

    - docker build -t hello-project .;

    - docker run -d --name hello-container -p 8080:8080 hello-project

    - 커맨드 수정 후 build

      ![image-20200528121205650](C:\Users\HPE\AppData\Roaming\Typora\typora-user-images\image-20200528121205650.png)

      ![image-20200528120517197](C:\Users\HPE\AppData\Roaming\Typora\typora-user-images\image-20200528120517197.png)

    - docker-server에서 docker images 잘 만들어졌는지 확인.

    - docker ps -a 컨테이너 잘 만들어져있는지 확인.

      ![image-20200528120542861](C:\Users\HPE\AppData\Roaming\Typora\typora-user-images\image-20200528120542861.png)

    

12. Web-brower에서 localhost:38080/webapp 에서 확인.

![image-20200528120634240](C:\Users\HPE\AppData\Roaming\Typora\typora-user-images\image-20200528120634240.png)

