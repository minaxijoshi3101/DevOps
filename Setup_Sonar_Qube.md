SonarQube for CentOs - 
youtube link: https://www.youtube.com/watch?v=gH1EQTRbeIw
https://docs.sonarqube.org/latest/analysis/scan/sonarscanner/
https://www.devopshint.com/how-to-install-sonarqube-on-amazon-linux-2/      ----->this works
https://github.com/minaxijoshi3101/DevOps-1/blob/master/sonarqube/Sonarqube_with_database.md.  --> git url
https://sonarqube.inria.fr/sonarqube/documentation/analysis/scan/sonarscanner-for-maven/        --> sonar-integration

Prerequsites:
1. Server should have 2GB RAM minimum
2. java 11 should be there
    yum update -y
    amazon-linux-extras list | grep openjdk-11
    amazon-linux-extras install java-openjdk11
    java -version
3.  Install postgresql
    check the user created by the postgres database
    # cat /etc/passwd i:e: postgres
    set the password for this user: postgres
    ALTER USER sonar WITH ENCRYPTED password 'admin';
    systemctl restart postgresql
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
   
    
    
