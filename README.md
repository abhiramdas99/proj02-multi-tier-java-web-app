# proj02-multi-tier-java-web-app
Deploy a Java Web applicatin in aws resources , using the tools like - git, maven, mysql, nginx, memcached, tomcat, rabbitmq &amp; ubuntu

# Workflow Diagram
![](/images/workflow-diag.png)

# Flow of  Execution
1. [Login to account](#1-login-to-account)
2. [Create key pair](#2-create-key-pair)
3. [Create security group](#3-create-security-group)
4. [Lunch instance with user data (bash script)](#1-login-to-account)
5. [Update ip to name mapping in route 53](#1-login-to-account)
6. [build application from source code](#1-login-to-account) 
7. [upload to s3 bucket](#1-login-to-account)
8. [Download the artifact to tomcat ec2 instance](#1-login-to-account)
9. [setup elb with https - certification from amazon certificate manger](#1-login-to-account)
10. [map elb end point to website name Godaddy dns](#1-login-to-account)
11. [verify all](#1-login-to-account)
12. [Build autoscaling group for tomcat instance](#1-login-to-account) 

# Execution Detail 
# 1. Login to account
![image](https://user-images.githubusercontent.com/62290469/234351298-6f5cd22c-aa7f-420e-bd52-4a9038f0444f.png)
![image](https://user-images.githubusercontent.com/62290469/234352144-8baca2a6-0c07-43be-83fb-4073326aa5a7.png)
[Back to Top](#flow-of--execution)

# 2. Create key pair
![image](https://user-images.githubusercontent.com/62290469/234464613-6a90d906-de8f-4127-9b47-3e9a9dee745d.png)

[Back to Top](#flow-of--execution)

# 3. Create security group
  * Security group for ELB( AWS Elastic Load Balancer)
  1.  create new security group for elasti load balancer named  as - PROJ02-SG-ELB
  2.  allow port no 80,443 for both ip 4 and ip 6
   ![image](https://user-images.githubusercontent.com/62290469/234356887-95b6ee0c-367a-429a-9d2a-bf5596cc5559.png)

  * Security group for tomcat web application server
    1. Security group name - PROJ02-SG-WEB-APP
    2. allow elb traffic to access web application by portno 8080
    ![image](https://user-images.githubusercontent.com/62290469/234359270-afc6d9e7-3bd9-4014-bbec-c4ed67cd17de.png)
    
  * Security group for backnd server (memcached,rabbit mq  & mysql)
   1. Security group name - PROJ02-SG-BACKEND
   2. allow traffic from tomact abbplicaton server to  port no 3306(mysql) , 11211(memcached) and rabbitmq(5672)
      ![image](https://user-images.githubusercontent.com/62290469/234366477-5fe383e3-c75d-496e-9d74-dddfedee646a.png)
   
  * Modify the security bakend security group again, to allow the own traffic 
  ![image](https://user-images.githubusercontent.com/62290469/234463963-4b4a1c5d-f002-494c-afbb-b6f952a49cdd.png)
   
# 4. Lunch instance with user data (bash script)
  * Lunch Backend - mysql 
    1. instance name : proj02-mysqldb-instance
    2. Amazon Machine Image (AMI) : CentOS-7-2111-20220825_1.x86_64- ( from the market place)
    3. Instance type : t2.micro
    4. keypair : attached earlier creaate 
    5. security group : attached as per earlier create ie. PROJ02-SG-BACKEND
    5. vpc :default 
    6. subnet : default 
    7. auto-asigned public IP : enabled
    8. storage : default as per ami
    9. advance detail :
       1. user data : copy the script from /userdata/mysql.sh and paste here
    10. then final lunch it   
    11. take the ssh to ec2 and run the following command to validate 
        1. ps -ef // to check if any process is running related sql
        2. systemctl status mariadb
        ![image](https://user-images.githubusercontent.com/62290469/234491543-7b48458b-0f71-47d8-a5ea-1267fad158bc.png)

  * Lunch Backend - memcached
    1. instance name : proj02-memcached-instance ( its your choice whatever you require you can give)
    2. Amazon Machine Image (AMI) : CentOS-7-2111-20220825_1.x86_64- ( from the market place)
    3. Instance type : t2.micro
    4. keypair : attached earlier create 
    5. security group : attached as per earlier create ie. PROJ02-SG-BACKEND
    5. vpc :default 
    6. subnet : default 
    7. auto-asigned public IP : enabled
    8. storage : default as per ami
    9. advance detail :
       1. user data : copy the script from /userdata/memcache.sh and paste here
    10. then final lunch it     
        ![image](https://user-images.githubusercontent.com/62290469/234559832-91a0a039-fa62-4554-b52b-0d1c40b99390.png)
       
     * Lunch Backend - rabbit mq
    1. instance name : proj02-rabbitmq-instance ( its your choice whatever you require you can give)
    2. Amazon Machine Image (AMI) : CentOS-7-2111-20220825_1.x86_64- ( from the market place)
    3. Instance type : t2.micro
    4. keypair : attached earlier create 
    5. security group : attached as per earlier create ie. PROJ02-SG-BACKEND
    5. vpc :default 
    6. subnet : default 
    7. auto-asigned public IP : enabled
    8. storage : default as per ami
    9. advance detail :
       1. user data : copy the script from /userdata/rabbitmq.sh and paste here
    10. then final lunch it  
    ![image](https://user-images.githubusercontent.com/62290469/234565793-c2b8c033-2375-4851-9f66-3e9736cc2fa9.png)
    
    * Lunch EC2 instance for tomcat web application server
    1. Name : proj02-webapp-instance <br>
    2. ami : ubuntu 20 LTS  <br>
    3. type : t2.micro <br>
    4. key pair : attached keypaire recently created <br>
    5. security group :  PROJ02-SG-WEB-APP <br>
    6. vpc : default 
    7. subnet : default 
    8. User data : copy the script from userdata/tomcat_ubuntu.sh and paste here
    9. Lunch the EC2 instance 
    10. make sure that, ssh should be allow in corresponnding security group
    11. take the remote also make sure the that, its a ubuntu machine and user should be ubuntu
     
    
# Update ip to name mapping in route 53
   * Add all three machine private ip address  in route 53 
    1. created new hosted zone<br>
    2. domain name : abhiamdas99.xyz<br>
    3. descritpion(optional) : hosted zone for backend server<br>
    4. type : private hosted zone<br>
    5. region : asia paciic (mumabai ) <br>
    6. vpc : default  ( as per your ec2 machine running<br>
    7. finally create it<br>
    8. after successfully created, add new record [create record]<br>
    ![image](https://user-images.githubusercontent.com/62290469/234607346-ccf09c06-0830-487d-808b-2136ad26eb87.png)
    
    9. Kindly note that, the host name entered in dns zone should be match with application.properties in  src folder
    10. In application.properties, following thing should be updated
      For mysql
      jdbc.url=jdbc:mysql://db01.abhiramdas99.xyz:3306/accounts?useUnicode=true&characterEncoding=UTF-8&zeroDateTimeBehavior=convertToNull
      memcached.active.host=mc01.abhiramdas99.xyz ( in your case you link our domain )
      rabbitmq.address=rmq01.abhiramdas99.xyz
    
     
# 6. build application from source code
    1. Now you are in your local laptop or pc 
    2. pull the project to your local laptop  i.e git pull 
    3. make sure that, you have with all following file and folder expect target folder
    ![image](https://user-images.githubusercontent.com/62290469/234846244-ee25d0d6-0341-4e66-9427-8e1f23e138d6.png)
    4. run the "mvn install" in that folder , where  the pom.xml file are present
    5. once installed, you can able to see a target folder 
    ![image](https://user-images.githubusercontent.com/62290469/234849331-6e8d4ffc-c830-4514-b017-096952a383f1.png)
    
# 7. upload to s3 bucket
   1. got aws console and then iam and create a user 
   2. give the "admininstraotrAccess" policy to this user 
   3. generate a acces key and  token
   4. open bash in your laptop 
   5. configure aws cli
   6. create s3 bucket. The command is : aws s3 mb s3://proj02-artifact-storage    
   7. copy the artifact to s3 bucket. The command is : aws s3 cp vprofile-v2.war s3://proj02-artifact-storage
   8. ![image](https://user-images.githubusercontent.com/62290469/234903262-cc5094a5-0329-4fc2-90c5-d4af3a6537fe.png)

   9. verify its done or not . The command is : aws s3 ls proj02-artifact-storage
   10. ![image](https://user-images.githubusercontent.com/62290469/234904753-14ab4e70-3a15-4278-9f31-4b055062062e.png)

# 8. Download the artifact to tomcat ec2 instance
  1. open iam console 
  2. create new role 
  3. trusted entity type : aws services
  4. common use cases : ec2
  5. next
  6. Add permission : s3FullAccess
  7. give some role name 
  8. ![image](https://user-images.githubusercontent.com/62290469/234910561-7c4b5a4b-363a-422d-9c66-9d459eed3367.png)

  9. select ec2-webapp-server  in aws ec2 dashboard
  10.action > security > modify iam role 
  ![image](https://user-images.githubusercontent.com/62290469/234912636-739cae80-7f73-4da9-9e40-66b48c67eedb.png)

  11. take ssh the ec2-webapp-server
  12. check tomcat is running or not . The command is : systemctl status tomcat9
  13. check the installation folder . the command is : cd /var/lib/tomcat9
  14. remove the default ROOT directory (command)
    1. systemctl stop tomcat9
    2. rm -rf ROOT
  15. install aws cli. The command is : apt install awscli
  16. check the bucket. The commad is : aws s3 ls s3://proj02-artifact-storage
  17. ![image](https://user-images.githubusercontent.com/62290469/234920765-1432c750-15b5-46d9-81df-6d9393b3a470.png)
  
  19. now need to copy the artifact to tomcat9


    

   
   
   
   
    [Back to Top](#flow-of--execution)

