SonarQube for CentOs - 
youtube link: https://www.youtube.com/watch?v=gH1EQTRbeIw               <br/>
https://docs.sonarqube.org/latest/analysis/scan/sonarscanner/       <br/>
https://www.devopshint.com/how-to-install-sonarqube-on-amazon-linux-2/      ----->this works <br/>
https://www.fosstechnix.com/how-to-install-sonarqube-on-centos-8/.    <br/>
https://github.com/minaxijoshi3101/DevOps-1/blob/master/sonarqube/Sonarqube_with_database.md.  --> git url <br/>
https://sonarqube.inria.fr/sonarqube/documentation/analysis/scan/sonarscanner-for-maven/        --> sonar-integration <br/>

Prerequsites:
1. Server should have 2GB RAM minimum
2. java 11 should be there
    yum update -y </br>
    yum list | grep openjdk <br/>
    yum install java-11-openjdk-devel.x86_64 -y <br/>
    amazon-linux-extras list | grep openjdk-11 
    amazon-linux-extras install java-openjdk11
    java -version
3.  Install postgresql
    check the user created by the postgres database
    # cat /etc/passwd i:e: postgres
    set the password for this user: postgres
    ALTER USER sonar WITH ENCRYPTED password 'admin';
    systemctl restart postgresql
    service postgresql-13.service restart
    check whether running on 5432 port or not
    - netstat -tunlp | grep 5432
4. Do some OS level modifications in vim /etc/sysctl.conf
    - vm.max_map_count=262144
    - fs.file-max=65536
    - ulimit -n 65536
    - ulimit -u 4096
5. Setup sonarcube
   download sonarcube
   
   
    tail /opt/sonarqube/logs/sonar.log
    http://13.233.97.45:9000/
    admin admin1
  
  On jenkins server, download sonar-scanner - https://docs.sonarqube.org/latest/analysis/scan/sonarscanner/
  wget https://binaries.sonarsource.com/Distribution/sonar-scanner-cli/sonar-scanner-cli-4.6.2.2472-linux.zip
  unzip
  In Jenkins, download sonar plugins (Sonar Quality Gates Plugin,SonarQube Scanner for Jenkins,Quality Gates Plugin) .
  Go to "Manage Jenkins" --> "global tool configuration" --> SonarQube Scanner --> sonarqube = /path of sonarscanner(/opt/jenkins/sonar-scanner-4.6.2.2472-linux)
  Go to "Manage Jenkins" --> "configure system" --> Name: sonarqube --> server url: http://13.233.38.32:9000/ for token
  go to sonar server-->Administrator-->My Account -->Security --> generate token --> name 
  copy this token and paste in jenkins unser token
  ============================-
  postgresql:
  yum list installed | grep postgres </br>
  yum remove postgresql* -y </br>
  rm -rf /var/lib/pgsql </br>
  rpm -qa | grep postgres </br>
  rpm -qa | grep post* </br>
  sudo amazon-linux-extras install postgresql10 -y </br>
  yum install -y postgresql-server.x86_64 postgresql-contrib.x86_64 postgresql-devel.x86_64 -y </br>
  /usr/bin/postgresql-setup --initdb </br>
  sudo systemctl enable --now postgresql </br>
  sudo systemctl status postgresql </br>
  adduser postgresql </br>
  passwd postgresql </br>
  su - postgres </br>
  createuser sonar </br>
  psql </br>
  ALTER USER sonar WITH ENCRYPTED password 'sonar'; </br>
  CREATE DATABASE sonarqube OWNER sonar; </br>
  grant all privileges on DATABASE sonarqube to sonar; </br>
  \q </br>
  exit </br>
  ####install java
  sudo amazon-linux-extras install java-openjdk11 </br>
  java -version </br>
  https://www.devopshint.com/how-to-install-sonarqube-on-amazon-linux-2/ </br>
  https://www.youtube.com/watch?v=BuGaeDoiCcs&list=PLUltco2RCw43iSpp9B-sOtHuBQInkmnYA
  
