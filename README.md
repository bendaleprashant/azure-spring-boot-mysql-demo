# azure-spring-boot-mysql-demo
Spring Boot App Deployment from Azure-CLI

1. create mysql server in azure
az mysql server create --resource-group pb-spring-rg --name pb-mysql-server --location eastus --sku-name B_Gen5_1 --storage-size 5120 --admin-user pb-spring --admin-password XXXXX
#Password needs to match password validation rules (upercase, lowercase chars, number and special symbols)

2. command to reset mysql server password
az mysql server update -n pb-mysql-server -g pb-spring-rg -p <new-password>

3. open mysql server's firewall
az mysql serveaz mysql server firewall-rule create --resource-group pb-spring-rg --name pb-mysql-server-database-allow-local-ip --server-name pb-mysql-server --start-ip-address 223.233.86.81 --end-ip-address 223.233.86.81

4. allow firewall access formazure resources
az mysql server firewall-rule create --resource-group pb-spring-rg --name allAzureIPs --server-name pb-mysql-server --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0

5. create database
az mysql db create --resource-group pb-spring-rg --name pb-spring-jpa-demo --server-name pb-mysql-server

6. generate spring project using spring initializer
curl https://start.spring.io/starter.tgz -d type=maven-project -d dependencies=web,data-jpa,mysql -d baseDir=azure-spring-workshop -d bootVersion=3.1.5.RELEASE -d javaVersion=17 | tar -xzvf -

7. add following config in src/main/resources/application.properties
logging.level.org.hibernate.SQL=DEBUG

8. Spring boot properties configuration
spring.datasource.url=jdbc:mysql://pb-mysql-server.mysql.database.azure.com:3306/demo?serverTimezone=UTC
spring.datasource.username=pb-spring@pb-mysql-server
spring.datasource.password=XXXXX
#Had to add this Dialect to support InnoDB engine to avoid MyIASM errors and engine errors)
spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.MySQL5InnoDBDialect   
spring.jpa.show-sql=true
spring.jpa.hibernate.ddl-auto=create-drop

9. pom.xml changes
#Had to downgrade spring boot version to 2.7.18. The build was failing with 3.1.5 version.
#Not sure about the issue. May be my local mvn settings, compiler settings issue.

10. Added Controller, Repository, Entity code

11. Test the local application
curl --header "Content-Type: application/json" --request POST --data '{"description":"configuration","details":"congratulations, you have set up your Spring Boot application correctly!","done": "true"}' http://127.0.0.1:8080
#curl command failing with weird issue "unmatched } bracket"
#Successful execution of POST request from Postman

12. Deploy spring boot app to Azure App Service

12.1 Configuration
mvn com.microsoft.azure:azure-webapp-maven-plugin:1.12.0:config 
#(Giving NoSuchMethod error - Azure API incompatibility issue)

mvn com.microsoft.azure:azure-webapp-maven-plugin:1.12.0:config 
#Working fine (Had to correct resource group in maven plugin pom.xml)

12.2 Deployment
mvn package com.microsoft.azure:azure-webapp-maven-plugin:1.12.0:deploy 
#Deployment failing with issue - No subscription found in this account. However the subscriptio is present






