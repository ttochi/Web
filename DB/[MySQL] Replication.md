# Master/Slave Replication

MySQL Replicaton 은 DB 서버를 여러대 두고 웹서버로 부터의 접속(쿼리, select)을 분산 시켜 DB서버의 부하를 줄이는 방법입니다.
slave 서버에서 master 서버의 바이너리 로그파일을 참조하여 업데이트 하므로, slave 서버에 로그인하여 DB를 update 하면 동기화가 되지 않으니 slave 서버는 select 용도로만 사용을 해야 합니다.

http://hys9958.tistory.com/entry/mysql-%EC%9D%B4%EC%A4%91%ED%99%94-master-slave%EA%B5%AC%EC%84%B1%ED%95%98%EA%B8%B0

## master slave 연결

master에서 dump뜬 것을 slave에 넣어줌
dump 파일에는 현재 덤프한 순간의 `로그파일`과 `위치`정보가 있음

slave에서는 master 정보를 설정해주고
start slave
show slave status


## 프로젝트 설정
gem 'ar-octopus' 을 사용
database.yml에서 master로 설정
octopus 에서 shards.yml을 생성 —> slave들을 설정
