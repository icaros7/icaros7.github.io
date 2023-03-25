---
title:  "[K-Shield Jr.] 가상 인프라 구축"
categories:

    - K-Shield
tags:
        - k-shield-jr
            - 케이쉴드
        toc: true
        published: true
---
요구사항에 알맞은 가상 인프라를 구축하자

# 1. 서론
 다음과 같은 요구 사항이 주어진다. 이 IT 기업에 맞는 가상 환경을 구축해보자. Apache, Tomcat 그리고 MariaDB 의 조합이 너무 오랜만이라 애를 좀 먹었다. 시간이 끌어진 포인트는 결론에서 서술하겠다.

- OS: Ubuntu Server 18.04.6 LTS amd64
- RAM: 2GB
- Storage: 20GB
- 각 서버 별 분리 및 내부 망 구성
    1. Gateway: `192.168.xxx.2`
    1. Web Service Server: `192.168.xxx.151`
    2. DataBase Server: `192.168.xxx.152`
    3. Development Server: `192.168.xxx.153`
    4. Log Backup Server: `192.168.xxx.154`
    5. DB Backup Server: `192.168.xxx.155`
- Application
    - Web Server: Apache@2.4.29
    - Web Application: Tomcat@9.0.16
    - DataBase: MariaDB@10.1.48
- DataBase
    - Account: `root` / `<주어진 PW>`
    - Database: `<주어진 데이터베이스 명>`
        - Table: `login`
            - `id` varchar(20), Primary key
            - `pw` varchar(20)
    

# 2. 가상 환경 구성
 가상 인프라 구축을 위해 `VMWare Player` 등 가상 환경을 구축한다. Docker가 아닌 실 환경을 구축하려 한다.

<table>
    <tbody>
        <tr>
          <td><b>Web Service</b><br>- Apache<br>- Tomcat</td>
            <td rowsp>⬅️ vsftp via Port.21</td>
            <td><b>Development</b><br>- Apache<br>- Tomcat<br>- MariaDB</td>
          	<td>➡️ ssh via Port.22</td>
          	<td><b>Dev PC</b></td>
        </tr>
        <tr>
            <td>↕️ Web Sock with Port.3306</td>
            <td>↘️ rsyslog with TCP Port.514</td>
            <td rowspan="4"><b>Log</b><br>- Per Host dir</td>
        </tr>
        <tr>
            <td><b>Database</b><br>- MariaDB</td>
            <td>➡️ rsyslog with TCP Port.514</td>
        </tr>
        <tr>
            <td>⬇️ rsync with TCP Port.22 + cron</td>
          	<td rowspan="2"></td>
        </tr>
        <tr>
            <td><b>DB-Backup</b></td>
        </tr>
    </tbody>
</table>

 간략한 관계도를 그린다면 위와 같다. 또한 각 End-Point 간 주고 받을때 사용되는 서비스는 업체의 요구 사항이다. 화살표는 데이터의 흐름 혹은 개방된 포트를 의미 한다.

## 2-1. 공통 Virtual Machine 설치 및 구성

 우선 5대의 서버 모두 동일 하게 `Ubuntu Server 18.04.6 LTS amd64`를 사용하므로 기본적으로 모두 설치 한다. 설치 과정에서 별도로 수정 한 것은 다음과 같다.

- Default APT Source: https://mirror.kakao.com/ubuntu
- Hostname: `<각 용도>-server`

 그 외 모두 조건에 부합되게 구성 하였다.

### Additional Package

```shell
$ sudo -s apt update
$ sudo -s apt install net-tools tmux -y
```

 내게 필요한 `net-tools`와 `tmux` 정도만 공통으로 부가 설치 하였다.

### Static IP

```shell
$ sudo -s vim /etc/netplan/00-installer-config.yaml
```

```yaml
# File: /etc/netplan/00-installer-config.yaml

network:
  ethernets:
        <NIC>:
                dhcp4: no
                addresses: <192.168.xxx.15n/24>
                gateway4: <192.168.xxx.2>
                nameservers:
                        addresses:
                        	- 8.8.8.8

  version: 2
```

```shell
$ sudo -s netplan apply
```

## 2-2. Apache 설치 및 설정

> 이는 Web Service Server와 Development Server에 해당되는 내용이다.

 `apache` 와 `apache-mod-jk` 를 통해 `tomcat` 과 통신할 예정이다. 이는 결국 `Web Application Server` (WAS)와 AJP 프로토콜을 사용하겠다는 의미이다.

```shell
$ sudo -s apt install apache2 libapache2-mod-jk -y
$ sudo -s vim /etc/apache2/sites-available/000-default.conf
```

```properties
# File: /etc/apache2/sites-available/000-default.conf
	
	# ... Skip ...
			DocumentRoot /var/www/html
#+ 	New line
			JkMount /* tomcat

	# ... Skip ...
```

 다음으로는 새롭게 사용할 Tomcat의 worker 설정 정의 파일을 만들어준다. 

```shell
sudo -s touch /etc/apache2/workers.properties
sudo -s vim /etc/apache2/workers.properties
```

```properties
# File: /etc/apache2/workers.properties

worker.list=tomcat
workers.tomcat_home=/usr/share/tomcat9
workers.java_home=/usr/lib/jvm/java-11-openjdk-amd64
worker.tomcat.port=8009
worker.tomcat.host=localhost
worker.tomcat.type=ajp13
worker.tomcat.lbfactor=1
```

 이후 Tomcat 에서 우리가 새롭게 만든 `worker.properties`를 읽을 수 있도록 한다.

```shell
$ sudo -s vim /etc/apache2/mods-available/jk.conf
```

```properties
# File: /etc/apache2/mods-available/jk.conf

	# ... Skip ...
	
	#- Del line
		JkWorkersFile /etc/libapache2-mod-jk/workers.properties
	#+ New line
   	JkWorkersFile /etc/apache2/workers.properties
```

```shell
$ sudo -s systemctl restart apache2
```

 이후 제작한 war 파일 혹은 사전 제공되는 `ROOT.war` 를 `/var/lib/tomcat9/webapps/` 내에 넣으면 된다. 단, 제공되는 `ROOT.war`의 경우 JSP  내 DB 타겟 IP가 hard coding 되어있으므로 변경을 요한다.

## 2-3. Tomcat 설치 및 설정

> 이는 Web Service Server와 Development Server에 해당되는 내용이다.

 이제 Apache 에서 Tomcat과 통신할 설정을 마쳤으므로 Tomcat 을 설정해보자. 위에서 우리는 AJP 프로토콜을 통해 WAS와 통신하기로 하였으므로 이에 맞게 해당 포트를 열어주어야 한다.

```shell
$ sudo -s apt install default-jdk tomcat9 libmysql-java -y
$ sudo -s vim /etc/tomcat9/server.xml
```

```xml
<!-- File: /etc/tomcat9/server.xml -->

<!-- ... Skip ... ->

	<!-- Define an AJP 1.3 Connector on port 8009 -->
	<Connector port="8009" protocol="AJP/1.3" redirectPort="8443"/>

<!-- ... Skip ... ->
```

```shell
$ sudo -s cp /usr/share/java/mysql-connector-java-5.1.45.jar /usr/share/tomcat9/lib/
$ sudo -s systemctl restart tomcat9
```

## 2-4. MariaDB 설치 및 설정

> 이는 DataBase Server와 Development Server에 해당되는 내용이다.

  다음으로는 MariaDB 를 설치하고 각 상황에 맞게 데이터베이스와 테이블을 업데이트 하겠다.

```shell
$ sudo -s apt install mariadb-server -y
$ sudo -s mysql_secure_installation

# 생략
Enter current password for root (enter for none):	# Passwork를 설정 한적이 없으므로 none
OK, successfully used password, moving on...

Set root password? [Y/n] y 	# Set MariaDB root pw
New password:
Re-enter new password:

Remove anonymous users? [Y/n] y # Anonymous 계정 삭제

Disallow root login remotely? [Y/n] n # External access 허용

Remove test database and access to it? [Y/n] y # Temp database 삭제

Reload privilege tables now? [Y/n] y # flush privileges;

$ mysql -u root -p # Sock access denied 발생 시 sudo 를 통해 실행

MariaDB[(none)]> create database <데이터베이스명>;
MariaDB[(none)]> use <데이터베이스명>;
MariaDB[(<데이터베이스명>)]> create table login (id varchar(20) primary key, pw varchar(20));
MariaDB[(<데이터베이스명>)]> show tables;	# 생성된 table 확인
MariaDB[(<데이터베이스명>)]> desc login;		# 간략한 table 정보 확인
MariaDB[(<데이터베이스명>)]> use mysql;

MariaDB[(mysql)]> grant all privileges on <데이터베이스명>.* to root@'192.168.xxx.{151, 153}' identified by '<root 비밀번호>'
MariaDB[(mysql)]> select host, user, password from user;	# Password 값이 일치해야함
MariaDB[(mysql)]> flush privileges;		# 권한 reload
MariaDB[(mysql)]> exit

$ sudo -s vim /etc/mysql/mariadb.conf.d/50-server.cnf
```

 이제 MariaDB가 어느 IP로 부터 접근을 허용할지 말지를 결정해야 한다. `0.0.0.0`을 통해 모든 IP로부터 허용 시킨다.

```
# File: /etc/mysql/mariadb.conf.d/50-server.cnf

- bind-address = 127.0.0.1
+	bind-address = 0.0.0.0
```

```shell
$ sudo -s systemctl restart mysqld.service
```

## 2-5. vsFTP 를 통한 Dev to Web 배포

> 이는 Development Server 및 Web Server에 해당되는 내용이다.

 vsFTP를 통해 개발 서버 내에서 사용하는 수정 사항을 Web Service Server로 배포할 예정이다.

```shell
$ sudo -s apt install vsftpd -y
$ sudo -s vim /etc/vsftpd.conf
```

```
# File: /etc/vsftpd.conf

- #write_enable=YES
+ write_enable=YES

- #local_umask=022
+ local_umask=022

- #chroot_local_user=YES
+ chroot_local_user=NO
```

> 하기 내용은 Development Server에 해당 되는 내용이다.

```shell
$ sudo -s systemctl restart vsftpd
$ sudo -s chmod o+w /var/lib/tomcat9/webapps/
$ su root # `root` 계정 switch

ftp <192.168.xxx.151>
ftp> mput /var/lib/tomcat9/webapps/ROOT.war
ftp> exit
```

 이후 Web Service Server 에서 `ROOT.war` 내 JSP 파일의 타겟 IP를 수정하고 `tomcat9` 서비스를 재시작 하면 된다.

## 2-6. `rsyslog`를 통한 Log Backup 설정

> 이는 Web Service Server와 DataBase Server에 해당되는 내용이다.

 우리는 `rsyslog`를 통해 로그 파일을 계속 Log Backup Server로 매 1분 마다 보낼 것 이다. 이는 추후 공격 또는 문제 해결에 단서가 되는 중요한 기록이므로 백업을 한다.

 또한 통신으로 TCP를 사용 할 것 이므로 `@`가 아닌 `@@`를 사용한다. 반대로 UDP를 사용 할 경우 전자를 사용하면 된다.

```shell
$ sudo -s vim /etc/rsyslog.conf
```

```
# File: /etc/rsyslog.conf

# ... Skip ...
*.* @@<192.168.xxx.154>:514
```

 이후 `rsyslog` 서비스를 재시작 하면 된다.

## 2-7. `rsyslog`를 통한 Log Server 설정

 바로 위에서 `rsyslog`를 통해 log 파일을 보내므로 우리는 받을 서버를 구축해야한다.

```shell
$ sudo -s vim /etc/rsyslog.conf
```

```
# File: /etc/rsyslog.conf

# TCP 를 통해 전송을 받으므로 imtcp
-		#module(load="imtcp")
-		#input(type="imtcp" port="514")

+		module(load="imtcp")
+		input(type="imtcp" port="514")
+		$template LogName, "/var/log/rsyslog/%fromhost-ip%/%PROGRAMNAME%.log"		# %$YEAR% 등으로 날짜 추가도 가능
+		*.* ?LogName
```

 이후 `rsyslog` 서비스를 재시작 하면 `/var/log/rsyslog/<각 서버 IP>/` 에서 전송된 로그 확인이 가능하다.

## 2-8. Database Backup 설정

### DataBase Server

 `rsync`와 `xinet`를 이용해 DB Backup Server로 DB를 전송한다.

```shell
$ sudo -s apt install rsync xinetd -y
$ sudo -s touch /etc/rsyncd.conf
$ sudo -s touch /etc/xinetd.d/rsync
$ sudo -s touch /backup/backup.sh
```

```shell
# File: /etc/rsyncd.conf

[DBS]
path = /backup
comment = DB server
uid = 65534
gid = 65534
use chroot = yes
read only = yes
hosts allow = <192.168.xxx.155>
max connections = 3
timeout 600
```

```shell
# File: /etc/xinetd.d/rsync

service rsync
{
  disable = no
  socket_type = stream
  wait = no
  user = root
  server = /usr/bin/rsync
  server_args = --daemon
  log_on_failure += USERID
}
```

```shell
# File: /backup/backup.sh

#!/bin/bash

mysqldump -uroot -p<MariaDB root 비밀번호> <데이터베이스 명> > /backup/mysql_dump_$(date +%Y%m%d).sql
```

```shell
$ sudo -s mkdir /backup
$ su root		# root 계정 switch
chmod u+x /backup/backup.sh
crontab -e	# Editor 선택. vim의 경우 2번

* * * * * /backup/backup.sh > /backup/backup.log 2>&1
```

 이후 `xinetd` 서비스를 재시작 한다.

### DB-Backup Server

 `rsync`와 `xinet`를 이용해 DataBase Server로부터 DB를 수신한다.

```shell
$ sudo -s apt install rsync xinetd -y
$ sudo -s touch /etc/xinetd.d/rsync
$ sudo -s touch /backup/backup.sh
```

```shell
# File: /etc/xinetd.d/rsync

service rsync
{
  disable = no
  socket_type = stream
  wait = no
  user = root
  server = /usr/bin/rsync
  server_args = --daemon
  log_on_failure += USERID
}
```

```shell
# File: /backup/backup.sh

#!/bin/bash

rsync -avz <192.168.xxx.152>::DBS/mysql_dump_$(date +%Y%m%d).sql /backup/
```

```shell
$ sudo -s mkdir /backup
$ su root		# root 계정 switch
chmod u+x /backup/backup.sh
crontab -e	# Editor 선택. vim의 경우 2번

* * * * * /backup/backup.sh > /backup/backup.log 2>&1
```

 이후 `xinetd` 서비스를 재시작 한다.

# 3. 결론

 위와 같은 과정을 통해 요구사항에 알맞는 가상 인프라를 성공적으로 구축이 가능 하다. 실제 구축 당시 어려움을 겪은 문제점이 있었다.

- `apache2` (httpd) 패키지 설치 및 enable 시 `/var/run/mysqld/mysqld.sock` 파일이 생성 불가

  - 검색 시 `3306` 포트가 사용 중일 경우 발생
  - 추론: `mariadb`를 동시 설치 하면 증상 발생
  - 결론: 실제 `mariadb-server`를 추후 설치하거나 포트를 바꾸니 해결

- Web Serive 시 DB 연동 불가

  1. JSP 파일 내 hard coding 된 서버 IP를 잘못 바꿈
  2. DB 내 외부 접근 허용을 `no` 로 set

  - 위 두 사항 해결 이후 정상 접근

가장 오래 시간을 끈 첫번째 경우 당시엔 `3306` 포트가 잘 사용 중인데 왜 되지 않는가 의문이었다. 이를 해결하는데 상당한 시간이 소요되어 참 허탈 하였다.