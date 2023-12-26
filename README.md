# jwt-apigateway-security

Simple microservice app using:

- Spring cloud gateway
- Service discovery using Netflix eureka
- JWT authentication (Not OAuth2, No authorization)

Ref: https://www.youtube.com/watch?v=MWvnmyLRUik

My Postman collection: jwt-apigateway-security

## Regist an user

```
curl --location --request POST 'http://localhost:8080/auth/register' \
--header 'Content-Type: application/json' \
--header 'Cookie: JSESSIONID=7CE91EE75A65277C0DCB6C5736C5DF5D' \
--data-raw '{
    "name":"Basant",
    "password":"Pwd1",
    "email":"basant@gmail.com"
}'

```

## Generate token

```
curl --location --request POST 'http://localhost:9898/auth/token' \
--header 'Content-Type: application/json' \
--header 'Cookie: JSESSIONID=7CE91EE75A65277C0DCB6C5736C5DF5D' \
--data-raw '{
    "username":"Basant",
    "password":"Pwd1"
}'
```

## Access Swiggy-app

```
curl --location --request GET 'http://localhost:8080/swiggy/37jbd832' \
--header 'Authorization: Bearer eyJhbGciOiJIUzI1NiJ9.eyJzdWIiOiJCYXNhbnQiLCJpYXQiOjE2NzkwNTU4MDIsImV4cCI6MTY3OTA1NzYwMn0.Q0bwS5_16q1Z8K-p_flpmyRoJNFCyOhU2AMKSNYh66o' \
--header 'Cookie: JSESSIONID=7CE91EE75A65277C0DCB6C5736C5DF5D'
```

## Access Restaurant-service

```
curl --location --request GET 'http://localhost:8080/restaurant/orders/status/37jbd832' \
--header 'Authorization: Bearer eyJhbGciOiJIUzI1NiJ9.eyJzdWIiOiJCYXNhbnQiLCJpYXQiOjE2NzkwNTU1MDcsImV4cCI6MTY3OTA1NzMwN30.9nNAW1rx8RoTIrhn5Abtzg7RplvT9_d-U5EOwUcJZq8' \
--header 'Cookie: JSESSIONID=7CE91EE75A65277C0DCB6C5736C5DF5D'
```

# IntelliJ project

To open all these services in a single instance of IntelliJ, we need to create an empty project inside this folder, such as: `H:\Documents\MyProjects\Java\jwt-apigateway-security\_jwt-apigateway-security`, notice I use `_` as a prefix of this project name.

After that, add all these services to that project:

![](./intellij-import-module.png)

# App design

- `identity-service`:
  - Là Auth-service trên hình
  - Có thể coi nó như 1 IAM, có vai trò authenticate user (chưa add chức năng authorize user, tức là hệ thống chưa dùng role, permission)
- `swiggy-gateway`:
  - API gateway của hệ thống, mọi request bên ngoài phải gọi qua đây chứ không gọi trực tiếp tới các service khác
  - Vai trò: routing và authenticating user
- Other services:
  - `swiggy-app`: chính là swiggy-service ở trên hình
  - `restaurant-service`: 1 module khác của hệ thống

Để đơn giản thì `swiggy-app` và `restaurant-service` đều có 1 API là check order status. 2 API này return giá trị như nhau :v

- Bên `swiggy-app` sẽ dùng RestTemplate gọi qua `restaurant-service` để lấy kết quả cho API này
- Bên `restaurant-service` sẽ dùng 1 HashMap để lưu các dummy order data cho API này

![](./design.png)
