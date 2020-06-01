## [Vagrant]

- vagrant up
- vagrant status



- ![image-20200522131051800](C:\Users\HPE\AppData\Roaming\Typora\typora-user-images\image-20200522131051800.png)



- ![image-20200522131029960](C:\Users\HPE\AppData\Roaming\Typora\typora-user-images\image-20200522131029960.png)

- vagrant ssh -> login

- vagrant ssh jenkins-server

  ![image-20200522134644254](C:\Users\HPE\AppData\Roaming\Typora\typora-user-images\image-20200522134644254.png)

- ip addr = ifconfig

#### [Jenkins Installation]

- Install Java

  1. sudo yum install java-1.8*

  2. sudo yum -y install java-1.8.0-openjdk-devel

  3. java —version

     ![image-20200522135333038](C:\Users\HPE\AppData\Roaming\Typora\typora-user-images\image-20200522135333038.png)

  4. [jenkins-server] $ find /usr/lib/jvm/java-1.8* | head -n 3

     ![image-20200522135352338](C:\Users\HPE\AppData\Roaming\Typora\typora-user-images\image-20200522135352338.png)

  5. [jenkins-server] $ cat .bash_profile

     ![image-20200522140557471](C:\Users\HPE\AppData\Roaming\Typora\typora-user-images\image-20200522140557471.png)

  6. JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.252.b09-2.el7_8.x86_64/
     export JAVA_HOME
     PATH=$PATH:$JAVA_HOME

  ###### To set it perment update your .bash_profile
  [jenkins-server] $vi /.bash_profile

  ![image-20200522135919705](C:\Users\HPE\AppData\Roaming\Typora\typora-user-images\image-20200522135919705.png)

  ![image-20200522135900623](C:\Users\HPE\AppData\Roaming\Typora\typora-user-images\image-20200522135900623.png)

  [jenkins-server] $source .bash_profile

![image-20200522140616526](C:\Users\HPE\AppData\Roaming\Typora\typora-user-images\image-20200522140616526.png)

- http://mirrors.jenkins-ci.org/redhat/ => 설치.

  1. yum -y install wget
  2. sudo wget -O /etc/yum.repos.d/jenkins.repo https://pkg.jenkins.io/redhat-stable/jenkins.repo
  3. sudo rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io.key
  4. yum -y install jenkins

- Start Jenkins

  #Start jenkins service

  service jenkins start

  #Setup Jenkins to start at boot,

  chkconfig jenkins on

- public_network -> private network 설정.

![image-20200522141341552](C:\Users\HPE\AppData\Roaming\Typora\typora-user-images\image-20200522141341552.png)

- vagrant halt

- vagrant up

  ![image-20200522141830046](C:\Users\HPE\AppData\Roaming\Typora\typora-user-images\image-20200522141830046.png)

  - localhost:18080으로 접속.

  

  - vagrant status

  - vagrant ssh jenkins-server

    id : vagrant password : vagrant

  - ping 192.168.56. 11 -> 13

  - ping 192.168.56.11 -> 12

  

[jenkins-sever] $ sudo cat /var/lib/jenkins/secrets/initialAdminPassword

=> 숫자 copy => 로그인 할 때 사용.

- [jenkins-server ~] $ sudo cat /etc/hosts

![image-20200522144303077](C:\Users\HPE\AppData\Roaming\Typora\typora-user-images\image-20200522144303077.png)

- [jenkins-server ~] $ sudo vi /etc/ssh/sshd_config

![image-20200522144440503](C:\Users\HPE\AppData\Roaming\Typora\typora-user-images\image-20200522144440503.png)

![image-20200522144813386](C:\Users\HPE\AppData\Roaming\Typora\typora-user-images\image-20200522144813386.png)

- sudo systemctl restart sshd ( tomcat에서 jenkins 접속 가능.)

-> jenkins, docker, tomcat. ssh 192.168.56.13

![image-20200522150914351](C:\Users\HPE\AppData\Roaming\Typora\typora-user-images\image-20200522150914351.png)

![image-20200522150858598](C:\Users\HPE\AppData\Roaming\Typora\typora-user-images\image-20200522150858598.png)

echo $JAVA_HOME

![image-20200522151124592](C:\Users\HPE\AppData\Roaming\Typora\typora-user-images\image-20200522151124592.png)



![image-20200522151601555](C:\Users\HPE\AppData\Roaming\Typora\typora-user-images\image-20200522151601555.png)



##### [Tomcat-server에 설치할 것]

1. java 설치 - 완료.
2. apache-tomcat-9 설치- https://blog.naver.com/PostView.nhn?blogId=tawoo0&logNo=221584906693
3. (windows) https://localhost:28080
4. management 클릭 후, error 확인. 

* sudo wget https://downloads.apache.org/tomcat/tomcat-9/v9.0.34/bin/apache-tomcat-9.0.34.tar.gz 
* ![image-20200522165859085](C:\Users\HPE\AppData\Roaming\Typora\typora-user-images\image-20200522165859085.png)



- ![image-20200522165914334](C:\Users\HPE\AppData\Roaming\Typora\typora-user-images\image-20200522165914334.png)

- https://victorydntmd.tistory.com/224

- ![image-20200522174411815](C:\Users\HPE\AppData\Roaming\Typora\typora-user-images\image-20200522174411815.png)

- vagrant ssh-config tomcat-server

  => 

- ![image-20200525142447380](C:\Users\HPE\AppData\Roaming\Typora\typora-user-images\image-20200525142447380.png)

- ![image-20200525142537471](C:\Users\HPE\AppData\Roaming\Typora\typora-user-images\image-20200525142537471.png)

- ![image-20200525142734429](C:\Users\HPE\AppData\Roaming\Typora\typora-user-images\image-20200525142734429.png)

- ![image-20200525145651606](C:\Users\HPE\AppData\Roaming\Typora\typora-user-images\image-20200525145651606.png)



![image-20200527094029898](C:\Users\HPE\AppData\Roaming\Typora\typora-user-images\image-20200527094029898.png)