# proj02-multi-tier-java-web-app
Deploy a Java Web applicatin in aws resources , using the tools like - git, maven, mysql, nginx, memcached, tomcat, rabbitmq &amp; ubuntu

# Workflow Diagram
![](/images/workflow-diag.png)

# Flow of  Execution
1. [Login to account](#1-login-to-account)
2. [Create key pair](#1-login-to-account)
3. [Create security group](#3-create-security-group)
4. [Lunch instane with user data (bash script)](#1-login-to-account)
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

   
    [Back to Top](#flow-of--execution)

