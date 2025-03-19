```shell
# 1. MySQL 최신 이미지 다운로드
docker pull mysql:latest

# 2. MySQL 컨테이너 실행
#    - 컨테이너 이름: mysql-container
#    - root 비밀번호: rootpassword
#    - 호스트와 컨테이너의 포트 3306을 연결
docker run --name mysql-container -e MYSQL_ROOT_PASSWORD=rootpassword -p 3306:3306 -d mysql:latest

# 3. 컨테이너 실행 확인
docker ps

# 4. MySQL 컨테이너 내부의 MySQL 셸 접속 (비밀번호 입력 필요)
docker exec -it mysql-container mysql -u root -p
```


도커 정지
```shell
docker stop mysql-container
```

해당 컨테이너 제거
```shell
docker rm mysql-container
```

컨테이너 시작
```shell
docker start mysql-container
```


Docker 환경에 설치한 MySQL에 계정을 생성할때 도메인을 일반적인 localhost로 지정하면 에러가 발생 합니다.

그렇기 때문에 에러 메세지에 나오는 Docker 가상 IP로 지정을 해서 계정을 생성해야 합니다.
```shell

mysql> CREATE USER 'account_name'@'172.17.0.1' IDENTIFIED BY 'password';

mysql> GRANT ALL PRIVILEGES ON *.* TO 'account_name'@'172.17.0.1' WITH GRANT OPTION;
```



