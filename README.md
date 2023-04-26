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
     
 
   
    [Back to Top](#flow-of--execution)

