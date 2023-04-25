# proj02-multi-tier-java-web-app
Deploy a Java Web applicatin in aws resources , using the tools like - git, maven, mysql, nginx, memcached, tomcat, rabbitmq &amp; ubuntu

# Workflow Diagram
![](/images/workflow-diag.png)

# Flow of  Execution
1. Login to account 
2. Create key pair
3. Create security group
4. Lunch instane with user data (bash script)
5. Update ip to name mapping in route 53
6. build application from source code 
7. upload to s3 bucket
8. Download the artifact to tomcat ec2 instance
9. setup elb with https [certification from amazon certificate manger]
10. map elb end point to website name Godaddy dns
11. verify all
12. Build autoscaling group for tomcat instance 
