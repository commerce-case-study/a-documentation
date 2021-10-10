# <a href="https://github.com/orgs/commerce-case-study/repositories">Commerce Case Study</a>

[![Java](https://img.shields.io/badge/Java-1.8.0-red.svg?style=plastic)](https://www.oracle.com/java/technologies/)
[![Maven](https://img.shields.io/badge/Maven-3.5.3-purple.svg?style=plastic)](https://maven.apache.org)
[![Spring Cloud](https://img.shields.io/badge/Spring%20Cloud-Hoxton.SR7-green.svg?style=plastic)](https://spring.io/projects/spring-cloud)
[![Spring Boot](https://img.shields.io/badge/Spring%20Boot-2.3.2.RELEASE-green.svg?style=plastic)](https://spring.io/projects/spring-boot)
[![My Batis](https://img.shields.io/badge/MyBatis-3.5.1-red.svg?style=plastic)](https://mybatis.org/mybatis-3/)

Commerce Case Study, it's build with considering the following aspect :

- Stateless Application
- Centralized Authentication Service
- Easy to Scale Up
- Support Intuitive/Complex Native Query
- Support Function level Cache using Redis
- Support Multitenancy 
- Support Basic Authorization for All Public Request (Non Login user)
- ...

## Technology Stack
- Spring Cloud Gateway
- Spring Cloud Security
- Spring Cloud Discovery (Eureka)
- Spring Boot
- Spring Data JPA
- Feign Client
- MyBatis

## Architecture Diagram
<img src="https://raw.githubusercontent.com/commerce-case-study/a-documentation/main/diagram/service-architecture-diagram.JPG" alt="drawing" width="900"/>

## Service Relationship
<img src="https://raw.githubusercontent.com/commerce-case-study/a-documentation/main/diagram/service-relationship.JPG" alt="drawing" width="600"/>

## Service Discovery
<img src="https://raw.githubusercontent.com/commerce-case-study/a-documentation/main/diagram/service-discovery.JPG" alt="drawing" width="700"/>

## Project Structure

| Application | Project Name | Port | Notes |
| ------------ | ------------ | ------------ | ------------ |
| Core ||||
| Spring Cloud Gateway | core-gateway | 8101 | Application Server, used as API Gateway |
| Spring Cloud Security | core-security | 8102 | Application Server, used as Central Authentication |
| Spring Cloud Discovery (Eureka) | core-discovery | 8103 | Application Server, used as Service Coordinator |
| Service ||||
| Service Member | service-member | 6101 | Application Server, Service Producer |
| Service Product | service-product | 6102 | Application Server, Service Producer |
| Service Trade | service-trade | 6103 | Application Server, Service Producer |
| Application ||||
| Web Member | web-member | 8001 | Application Server, Service Consumer |
| Web Order | web-order | 8002 | Application Server, Service Consumer |
| Web Payment | web-payment | 8003 | Application Server, Service Consumer |

## Preparation
```
1. Created Database & Assign User:
   ## db_member
   mysql> create database db_member;
   mysql> create user 'ucommerce'@'%' identified with mysql_native_password by '123qwe';
   mysql> grant all privileges on db_member.* to 'ucommerce'@'%' with grant option;
   mysql> flush privileges;
   ## db_product
   mysql> create database db_product;
   mysql> grant all privileges on db_product.* to 'ucommerce'@'%' with grant option;
   mysql> flush privileges;
   ## db_trade
   mysql> create database db_trade;
   mysql> grant all privileges on db_trade.* to 'ucommerce'@'%' with grant option;
   mysql> flush privileges;
   
2. Run the application please see 'Build & Run' section.

3. Run Application and Insert Initialize Client Data :
   INSERT INTO oauth_client_details (client_id,client_secret,scope,authorized_grant_types,authorities,access_token_validity,refresh_token_validity) VALUES
	 ('mobile-apps','$2y$12$qAD6hUSq9FOuvum4XKCBf.5o3/ZtOniJ4pYocfnZoLRvFVtrKRjCu','read,write','password','password,refresh_token,client_credentials,authorization_code',900,2592000);

   Notes
   Client Id     : mobile-apps
   Client Secret : rahasia12345

4. Insert Seller
   INSERT INTO db_member.t_mem_member (email,image,password,phone,status,`type`,username) VALUES
      ('rio.bastian.seller@metranet.co.id','http://here.iam/rio.bastian.jpeg','$2a$10$XsgU9gEPL/eax2Y5X9dj9.SJa5GaEJI6DXR4q.fiYlNLMjnAil6Du','082124334112','ACTIVE','SELLER','rio.bastian.seller');

5. Insert Seller Address
   INSERT INTO db_member.t_mem_member_address (address_line,city,district,member_id,name,notes,postal_code,province) VALUES
   ('Mulia Busniess Park','Jakarta Selatan','Pancoran',6,'Rio Bastian Seller','Di seberang SPBU Pancoran','12701','DKI Jakarta');
   
6. Create Shop for Seller
   INSERT INTO db_trade.t_trd_shop (member_address_id,member_id,shop_name,status) VALUES
   (2,6,'Ribas Online Shop','ACTIVE');

7. Insert Item to Seller Shop
   INSERT INTO db_product.t_prd_item (name,price,quantity,shop_id) VALUES
   ('Iphone 12 Pro',12500000.00,99,1),
   ('Screeen Guard 5.5"',50000.00,99,1),
   ('Samsung Max Pro',2100000.00,99,1);

```

## Build & Run
```
1. Git Clone
$ git clone https://github.com/commerce-case-study/core-discovery-server.git
$ git clone https://github.com/commerce-case-study/core-api-gateway.git
$ git clone https://github.com/commerce-case-study/core-security-server.git
$ git clone https://github.com/commerce-case-study/service-member.git
$ git clone https://github.com/commerce-case-study/service-product.git
$ git clone https://github.com/commerce-case-study/service-trade.git
$ git clone https://github.com/commerce-case-study/web-member.git
$ git clone https://github.com/commerce-case-study/web-order.git
$ git clone https://github.com/commerce-case-study/web-payment.git

2. Run Discovery Server (Eureka)
$ cd ${PROJECT_BASE}/core-discovery-server/
$ mvn -e clean spring-boot:run

3. Run API Gateway Server
$ cd ${PROJECT_BASE}/core-api-gateway/
$ mvn -e clean spring-boot:run

4. Run Security Server
$ cd ${PROJECT_BASE}/core-security-server/
$ mvn -e clean spring-boot:run

5. Run Service : Member
$ cd ${PROJECT_BASE}/service-member/
$ mvn -e clean spring-boot:run

6. Run Service : Product
$ cd ${PROJECT_BASE}/service-product/
$ mvn -e clean spring-boot:run

7. Run Service : Trade
$ cd ${PROJECT_BASE}/service-trade/
$ mvn -e clean spring-boot:run

8. Run Web : Member
$ cd ${PROJECT_BASE}/web-member/
$ mvn -e clean spring-boot:run

9. Run Web : Order
$ cd ${PROJECT_BASE}/web-order/
$ mvn -e clean spring-boot:run

10. Run Web : Payment
$ cd ${PROJECT_BASE}/web-payment/
$ mvn -e clean spring-boot:run
```

## API Documentation
https://documenter.getpostman.com/view/457814/UV5RkzNs
https://github.com/commerce-case-study/a-documentation/blob/main/sample-request-response.txt

## Contributors
| Name | Email | Role |
| ------------ | ------------ | ------------ |
| Rio Bastian | altanovela@gmail.com | Authors |
