# 컴퓨터 리소스 야금야금 먹는 오라클 서버/인스턴스 제어

## 1. 서버(Listener)
### 리스너 시작
```
lsnrctl start
```
### 리스너 중지
```
lsnrctl stop
```

## 2. 인스턴스(Instance)
### 인스턴스 시작
```
sqlplus "/as sysdba"
startup
```
### 인스턴스 중지
```
shutdown immediate
exit
```

### 3. 리스너와 인스턴스
 - 리스너는 서버 사이드에서 클라이언트 연결 요청을 기다리고 있는 데이터베이스의 프로세스임
 - 서비스 이름(service name)은 논리적으로 클라이언트 연결(connection)을 말한다(representation).
 - 클라이언트가 리스너(Listener)에 연결(connect)되면, 이 요청은 서비스에 연결되고, 데이터베이스 인스턴스(instance)가 시작되면 스스로  하나 또는 그 이상 서비스들의 제공을 리스너와 함께 이름으로 등록(register)한다.
 