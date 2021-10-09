# Microservice Base Framework

[![Java](https://img.shields.io/badge/Java-1.8.0-red.svg?style=plastic)](https://www.oracle.com/java/technologies/)
[![Maven](https://img.shields.io/badge/Maven-3.5.3-purple.svg?style=plastic)](https://maven.apache.org)
[![Spring Cloud](https://img.shields.io/badge/Spring%20Cloud-Hoxton.SR7-green.svg?style=plastic)](https://spring.io/projects/spring-cloud)
[![Spring Boot](https://img.shields.io/badge/Spring%20Boot-2.3.2.RELEASE-green.svg?style=plastic)](https://spring.io/projects/spring-boot)
[![My Batis](https://img.shields.io/badge/MyBatis-3.5.1-red.svg?style=plastic)](https://mybatis.org/mybatis-3/)

Microservice Base Framework ini di buat sebagai acuan untuk mengembangkan framework aplikasi berbasis Java.
Dibuat dengan mempertimbangkan :

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
- Spring Cloud Discovery Service (Eureka)
- Spring Boot
- Spring Data JPA
- Feign Client
- MyBatis

## Architecture Diagram
![](https://raw.githubusercontent.com/altanovela/microservice-baseframework/master/.res/architecture-diagram.jpg)

## Project Structure

| Application | Project Name | Port | Notes |
| ------------ | ------------ | ------------ | ------------ |
| Core ||||
| Spring Cloud Gateway | core-gateway | 8101 | Application Server, used as API Gateway |
| Spring Cloud Security | core-security | 8102 | Application Server, used as Central Authentication |
| Discovery Service (Eureka) | core-discovery | 8103 | Application Server, used as Service Colaborator |
| Application ||||
| Web Member | web-member | 8001 | Application Server, Service Consumer |
| Service ||||
| Service Member | service-member | 6101 | Application Server, Service Producer |

## Pre Preparation
```
1. Created Database & Assign User:
   mysql> create database db_member;
   mysql> create user 'umember'@'%' identified with mysql_native_password by '123qwe';
   mysql> grant all privileges on db_member.* to 'umember'@'%' with grant option;
   mysql> flush privileges;

2. Run Application and Insert Initialize Client Data :
   INSERT INTO db_member.oauth_client_details (client_id, access_token_validity, additional_information, authorities, autoapprove, client_secret, authorized_grant_types, web_server_redirect_uri, refresh_token_validity, resource_ids, `scope`) VALUES ('mobile-apps', 900, NULL, 'password,refresh_token,client_credentials,authorization_code', NULL, '$2y$12$qAD6hUSq9FOuvum4XKCBf.5o3/ZtOniJ4pYocfnZoLRvFVtrKRjCu', NULL, NULL, 2592000, NULL, 'read,write');

Notes
Client Id     : mobile-apps
Client Secret : rahasia12345
```

## Build & Run
```
1. Git Clone
$ git clone https://github.com/commerce-case-study/core-discovery-server.git
$ git clone https://github.com/commerce-case-study/core-api-gateway.git
$ git clone https://github.com/commerce-case-study/core-security-server.git
$ git clone https://github.com/commerce-case-study/service-member.git
$ git clone https://github.com/commerce-case-study/web-member.git

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

6. Run Web : Member
$ cd ${PROJECT_BASE}/web-member/
$ mvn -e clean spring-boot:run
```

## Sample API
#### Register New Member
cUrl Request :
```
curl --location --request POST "http://localhost:8101/auth/member/register" 
--header "Authorization: Basic bW9iaWxlLWFwcHM6cmFoYXNpYTEyMzQ1" 
--header "Content-Type: application/json" 
--data-raw "{
    \"phone\": \"082124334111\",
    \"email\": \"rio.bastian@metranet.co.id\",
    \"username\": \"rio.bastian\",
    \"password\": \"Password123\",
    \"image\": \"http://here.iam/rio.bastian.jpeg\" 
  \}"
```
*Notes 
```
Authorzation Basic is generated as follows Base64Encoder.encode(client-id:client-secret)
```
Response :
```
{
  "id":6,
  "username":"rio.bastian",
  "email":"rio.bastian@metranet.co.id", 
  "password":"$2a$10$UiMlTv.YTMBFfxnAnSqFwueTCtVsnbmiD.u/Wmao.c6mgBAoZVWqy", 
  "type":"BUYER", 
  "image":"http://here.iam/rio.bastian.jpeg", 
  "status":"ACTIVE", 
  "phone":"082124334111"
}
```

#### Login and Get Token
cUrl Request : 
```
curl --location --request POST "http://localhost:8101/oauth/token" \
--header "Authorization: Basic bW9iaWxlLWFwcHM6cmFoYXNpYTEyMzQ1" \
--header "Content-Type: application/x-www-form-urlencoded" \
--data-urlencode "grant_type=password" \
--data-urlencode "username=082124334111" \
--data-urlencode "password=Password123"
```
*Notes 
```
Authorzation Basic is generated as follows Base64Encoder.encode(client-id:client-secret)
username attribute could be fill by username, email or phone number
```
Response :
```
{
  "access_token":"eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJmaW5ib3gtc2VjIiwiZXhwIjoxNjE4NTgyMjcyLCJ1c2VyX25hbWUiOiJyaW8uYmFzdGlhbiIsImF1dGhvcml0aWVzIjpbIkJVWUVSIl0sImNsaWVudF9pZCI6Im1vYmlsZS1hcHBzIiwic2NvcGUiOlsicmVhZCIsIndyaXRlIl19.QvTsJVICMjp8hlwIgVQemPAv2Y56WWR2Lk6MYYiwGioLEszb3zbqXaZkpZJ4kfYQjNzuKzfwOVqwx55HqiL2dsHhIcFTrFwe273AJgid6G6ECPPZyB8dPkrnB6cuowDKCM6Z4DTt8TMrlPFIsub1Na9TyGPyYmMgM53oLm9D7mIPlzv6-LLZ8Oc_EVdSjhj-mzC13d_UtxlWzNB4yqX9WampsxBwmCztPqeZKeAgTdIAU2INhP8jUi8ilnxl_4ctcQ4T3KJGpX8lfbYOLIgVSPq_x2EtYBC3TODTrsrLiJGod8gc-1AIop7BC493u6-uDV4K2r3t_Cy_IggT60ARMA",
  "token_type":"bearer",
  "refresh_token":"eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJmaW5ib3gtc2VjIiwiZXhwIjoxNjIxMTczMzcyLCJ1c2VyX25hbWUiOiJyaW8uYmFzdGlhbiIsImF1dGhvcml0aWVzIjpbIkJVWUVSIl0sImNsaWVudF9pZCI6Im1vYmlsZS1hcHBzIiwic2NvcGUiOlsicmVhZCIsIndyaXRlIl19.PSU3hLAAhYOYZ2AzmDrgDWLyvn1Le4Swyls8Q-imDbQKBohiNMPKPRzYhFEgBYuKQ3MIgoTw2R0j_BDWpdq7Stf_whjtt4DtDQAswA8NjqRtMBNqXpJPEmYXwK9nbVFQAIqs6cAyLPrwoI9ZYepSAg4MyfpuKnLBLZh5Pa9agVW-cTn-I2hShHhbqVG3bDIfJcx5svTI5eY5FkfDpSXPz8-O13y7z246oHcOs2fl8o2AelEbdTEF4BiUoLwy8YGoU1Xn0kxkhUw3QhSYHAnW98bwfNa6W2TbWcnC7JhLssLW-CGwWeTWivptUodUV_1_tgyypTZyE3bFUU_vForlhA",
  "expires_in":899,
  "scope":"read write",
  "iss":"finbox-sec"
}
```

#### Get Member Info (based on Token)
cUrl Request :
```
curl --location --request GET "http://localhost:8101/api/member/info" \
--header "Authorization: Bearer eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJmaW5ib3gtc2VjIiwiZXhwIjoxNjE4NTgyMjcyLCJ1c2VyX25hbWUiOiJyaW8uYmFzdGlhbiIsImF1dGhvcml0aWVzIjpbIkJVWUVSIl0sImNsaWVudF9pZCI6Im1vYmlsZS1hcHBzIiwic2NvcGUiOlsicmVhZCIsIndyaXRlIl19.QvTsJVICMjp8hlwIgVQemPAv2Y56WWR2Lk6MYYiwGioLEszb3zbqXaZkpZJ4kfYQjNzuKzfwOVqwx55HqiL2dsHhIcFTrFwe273AJgid6G6ECPPZyB8dPkrnB6cuowDKCM6Z4DTt8TMrlPFIsub1Na9TyGPyYmMgM53oLm9D7mIPlzv6-LLZ8Oc_EVdSjhj-mzC13d_UtxlWzNB4yqX9WampsxBwmCztPqeZKeAgTdIAU2INhP8jUi8ilnxl_4ctcQ4T3KJGpX8lfbYOLIgVSPq_x2EtYBC3TODTrsrLiJGod8gc-1AIop7BC493u6-uDV4K2r3t_Cy_IggT60ARMA"
```
Response :
```
{
    "id": 6,
    "username": "rio.bastian",
    "email": "rio.bastian@metranet.co.id",
    "password": "$2a$10$UiMlTv.YTMBFfxnAnSqFwueTCtVsnbmiD.u/Wmao.c6mgBAoZVWqy",
    "type": "BUYER",
    "image": "http://here.iam/rio.bastian.jpeg",
    "status": "ACTIVE",
    "phone": "082124334111"
}
```

#### Update Member Info (based on Token)
cUrl Request :
```
curl --location --request POST "http://localhost:8101/api/member/update" \
--header "Authorization: Bearer eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJmaW5ib3gtc2VjIiwiZXhwIjoxNjE4NTgyMjcyLCJ1c2VyX25hbWUiOiJyaW8uYmFzdGlhbiIsImF1dGhvcml0aWVzIjpbIkJVWUVSIl0sImNsaWVudF9pZCI6Im1vYmlsZS1hcHBzIiwic2NvcGUiOlsicmVhZCIsIndyaXRlIl19.QvTsJVICMjp8hlwIgVQemPAv2Y56WWR2Lk6MYYiwGioLEszb3zbqXaZkpZJ4kfYQjNzuKzfwOVqwx55HqiL2dsHhIcFTrFwe273AJgid6G6ECPPZyB8dPkrnB6cuowDKCM6Z4DTt8TMrlPFIsub1Na9TyGPyYmMgM53oLm9D7mIPlzv6-LLZ8Oc_EVdSjhj-mzC13d_UtxlWzNB4yqX9WampsxBwmCztPqeZKeAgTdIAU2INhP8jUi8ilnxl_4ctcQ4T3KJGpX8lfbYOLIgVSPq_x2EtYBC3TODTrsrLiJGod8gc-1AIop7BC493u6-uDV4K2r3t_Cy_IggT60ARMA" \
--header "Content-Type: application/json" \
--data-raw "{
    \"image\": \"http://here.iam/rio.bastian.2.jpeg\"
}"
```
Response :
```
{
    "msg":"Success Update Member"
}
```

## Contributors
| Name | Email | Role |
| ------------ | ------------ | ------------ |
| Rio Bastian | rio.bastian@metranet.co.id | Authors |
