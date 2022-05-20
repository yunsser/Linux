# Day_05

Hadoop Eco-System

- Storage(HDFS), Process(MapReduce)

- Hive : SQL을 사용하여 분산환경에서 HDFS, MapReduce 활용
    - Enterprise Data Warehouse로 적합
    - Data Warehouse : 의사결정에 도움을 주기위한 전사적인 통합 DB
    - HiveQL
    - SELECT AVG (salary) FROM emp
    - 데이터는 csv에 저장, 테이블은 메타데이터로 존재
        
        ![Untitled](https://user-images.githubusercontent.com/102286605/169500201-7ad391de-3d4a-4b84-ab61-002f8b9199bd.png)

    
    ### Hive 설치
    
    1. [https://archive.apache.org/dist/hive/hive-3.1.2/](https://archive.apache.org/dist/hive/hive-3.1.2/) : 웹에 접속
        1. `[apache-hive-3.1.2-bin.tar.gz](https://archive.apache.org/dist/hive/hive-3.1.2/apache-hive-3.1.2-bin.tar.gz)` 다운로드하기``
        2. wget https://archive.apache.org/dist/hive/hive-3.1.2/apache-hive-3.1.2-bin.tar.gz
            
            ![Untitled 1](https://user-images.githubusercontent.com/102286605/169500235-69be7c76-81e9-441e-9206-1f891c570a43.png)

            
    2. ls (파일 왔는지 확인)
        1. apache-hive-3.1.2-bin.tar.gz
    3. tar -xvzf apache-hive-3.1.2-bin.tar.gz
        1. 압축풀기
    4. mv apache-hive-3.1.2-bin hive-3.1.2
        1. 파일이름 변경
    
    ### hdfs 모듈을 사용하여 파이썬에 hdfs 사용하기
    
    - 윈도우 쥬피터 +리눅스 하둡 같이 돌리기
    1. ip addr ( Ubuntu에서 ip 주소찾기 )
        
        ```python
        import pandas as pd
        from hdfs import InsecureClient
        
        client_hdfs = InsecureClient('http://나의 아이피:9870')  # namenode의 웹 인터페이스
        with client_hdfs.read('/user/csv/sample.csv') as reader:
            df = pd.read_csv(reader,index_col=0)
        print( df )
        ```

        
    2. 호스트 컴의 Anaconda Prompt 에서 pip install hdfs 명령으로 설치
    3. 실행
        
        ![Untitled 3](https://user-images.githubusercontent.com/102286605/169500268-d8355034-02f2-4336-9bcb-d69845f4735e.png)

    
    VM : Linux, hadoop, hdfs
    
    Host : Windows, Jupyter notebook
    

### Hive 설치법

1. ls -al
2. nano .bashrc
3. 코드
    
    ```python
    export HIVE_HOME=/home/hduser/hive-3.1.2
    export PATH=$PATH:$HIVE_HOME/bin/
    ```
    
4. source .bashrc
5. echo $HIVE_HOME
6. echo $PATH
7. cd hive-3.1.2/
8. cd bin
9. ls
10. nano hive-config.sh
11. 코드 
    
    ```python
    export HADOOP_HOME=/home/hduser/hadoop
    ```
    
12. source hive-config.sh
13. echo $HADOOP_HOME
14. cd
15. hdfs dfs -mkdir /tmp
16. hdfs dfs -chmod g+w /tmp
17. hdfs dfs -ls -R /
18. hdfs dfs -mkdir -p /user/hive/warehouse
19. hdfs dfs -ls -R /user/
20. hdfs dfs -chmod g+w /user/hive/warehouse
21. hdfs dfs -ls -R /user/
22. cd hive-3.1.2/
23. cd conf
24. cp hive-default.xml.template hive-site.xml
25. cd ..
26. cd bin
27. schematool -dbType derby -initSchema
    1. 이렇게 하면 에러뜸
28. cd ../lib/
29. rm guava-19.0.jar
30. find $HADOOP_HOME -type f | grep ‘guava’ 
    1. 구아바 파일 위치찾기
31. cp /home/hduser/hadoop/share/hadoop/common/lib/guava-27.0-jre.jar ./
    
    ![Untitled 4](https://user-images.githubusercontent.com/102286605/169500324-88d572f3-a33e-480b-be0f-832f366fac91.png)

    
32. cd ../bin
33. schematool -dbType derby -initSchema
    1. 오류
34. nano ../conf/hive-site.xml
35. 코드 
    1. 컨트롤 + w : transactional  : 찾기
    2. 알트 w : 다음 찾기
    3. 빨간 글씨 부분 지워주고 for 과 transactional  사이에 공백 넣어주기
    
    ![Untitled 5](https://user-images.githubusercontent.com/102286605/169500356-d7d38eff-e709-4aba-a871-a20337a351bb.png)

    
    ![Untitled 6](https://user-images.githubusercontent.com/102286605/169500373-d6573d2e-455e-4a1b-9f93-62e05c32b3c0.png)

    
36. schematool -dbType derby -initSchema
    1. 성공
        
        ![Untitled 7](https://user-images.githubusercontent.com/102286605/169500408-9f6ff465-663f-474b-900f-f8e5a6d2deac.png)

        

### Hive 돌려보기

1. nano ../conf/hive-site.xml
2. 코드 
    1. 코드 
3. hive
    
    ![Untitled 8](https://user-images.githubusercontent.com/102286605/169500433-b620bd5a-0577-4b17-bf8e-0a98cf3fb6f2.png)

    
4. /hive-3.1.2/bin에서 schematool -dbType derby -initSchema

### Hive 테이블 생성 및 돌려보기

1. cd hive-3.1.2/bin
2. hive (실행), /// 컨트롤 + c (종료)
3. show databases ;
    1. s ; 사이 스페이스바 필수
4. show tables ;
    
    ![Untitled 9](https://user-images.githubusercontent.com/102286605/169500459-84c24e89-42c9-4695-8b71-8e6b53063d29.png)

    
5. create database userdb ;
6. show databases ;
    
    ![Untitled 10](https://user-images.githubusercontent.com/102286605/169500478-ca8fe73c-9f6f-4e00-b64f-9811a9b7b78d.png)

    
7. use userdb;
8. 코드
    
    ```python
    CREATE TABLE IF NOT EXISTS employee ( 
    eid int, name String,
    salary String, destination String )
    COMMENT 'Employee details'
    ROW FORMAT DELIMITED
    FIELDS TERMINATED BY ','
    LINES TERMINATED BY '\n'
    STORED AS TEXTFILE
    TBLPROPERTIES ('NO_AUTO_COMPACTION' = 'true');
    ```
    
    ![Untitled 11](https://user-images.githubusercontent.com/102286605/169500508-f778b255-afd5-421f-812d-6dd55f58e84b.png)

    
9. show tables;
10. 로컬 시스템에 employee.csv 생성 
    
    ```python
    1201,Gopal,45000,Technical manager
    1202,Manisha,45000,Proof reader
    1203,Masthanvali,40000,Technical writer
    1204,Kiran,40000,Hr Admin
    1205,Kranthi,30000,Op Admin
    ```
    
11. hive>
    1. 데이터 파일을 로드하여 employee 테이블과 연결한다
        
        ```python
        LOAD DATA LOCAL INPATH '/home/hduser/employee.csv' OVERWRITE INTO TABLE employee;
        ```
        
12. hive>
    
    ```python
    SELECT * FROM employee;
    ```
    
    ![Untitled 12](https://user-images.githubusercontent.com/102286605/169500525-c9298496-2c73-4294-a3a4-c3ebc2fb8fe9.png)

    
13. SELECT AVG(salary) FROM employee;
    1. 자바를 이용한 통계14. 
        
        ![Untitled 13](https://user-images.githubusercontent.com/102286605/169500533-f6067f66-a575-4e35-8c9d-e977a35b41b4.png)

        
14. exit;
    1. Hive 종료

- Metadata
    - <div id=’empinfo’>xxxxxxxx</div>
    - 

### hiveserver2

- mySQL 같은 존재
- 외부접속 가능
1. hiveserver2
2. 새로운 터미널
3. cd hive-3.1.2/bin
4. beeline
5. !connect jdbc:hive2://localhost:10000/userdb;
6. hduser 2번 치기
7. show tables;
8. SELECT * FROM employee;
9. SELECT AVG(salary) FROM employee;
10. exit;

### hiveserver2오류 해결법 (설정)

1. cd
2. cd hadoop/etc/hadoop/
3. nano core-site.xml
    1. $HADOOP_HOME/etc/hadoop/ core-site.xml 하단에 아래 내용 추가
        
        ```python
        <property>
             <name>hadoop.proxyuser.계정이름.hosts</name>
             <value>*</value>
        </property>
        <property>
             <name>hadoop.proxyuser.계정이름.groups</name>
             <value>*</value>
        </property>
        ```
        
4. cd hive-3.1.2/conf
5. nano hive-site.xml
    1. $HIVE_HOME/conf/ hive-site.xml 하단에 아래의 내용 추가
        
        ```python
        <property>
            <name>hive.server2.active.passive.ha.enable</name>
            <value>true</value>
          </property>
          <property>
            <name>hive.metastore.event.db.notification.api.auth</name>
            <value>false</value>
          </property>
        ```
        
6. stop-all.sh
7. start-all.sh
8. cd hive-3.1.2/bin
9. hiveserver2 (터미널 1)
10. beeline (터미널 2)
    
    ```python
    !connect jdbc:hive2://localhost:10000/userdb;
    	username: hduser
    	userpass: hduser
    userdb > show tables;
    userdb > SELECT * FROM employee;
    userdb > SELECT AVG(salary) FROM employee;
    ```
    

### hiveserver2에 외부에서 접속

- beeline CLI 로 확인

Java 프로그램

Python 접속

1. 이클립스에 자바 프로젝트 생성
    1. Java-Hive
    2. 패키지 com.tjorun.hadoop
    3. 클래스 HiveConn
    4. 아래 코드 복붙
    
    ```python
    package com.tjoeun.hadoop;
    
    import java.sql.SQLException;
    import java.sql.Connection;
    import java.sql.ResultSet;
    import java.sql.Statement;
    import java.sql.DriverManager;
    
    public class HiveConn 
    {
    ****   private static String driverName = "org.apache.hive.jdbc.HiveDriver";
       
       public static void main(String[] args) throws Exception {
       
          // Register driver and create driver instance
          Class.forName(driverName);
          
          // get connection, 데이터베이스 이름 : userdb
          // VMWare의 Ubuntu에 Hadoop, Hive가 실행되고, 호스트 컴의 Eclipse에서 이 클래스 실행
          Connection con = DriverManager.getConnection("jdbc:hive2://192.168.45.128:10000/userdb", "hduser", "hduser");
          
          // create statement
          Statement stmt = con.createStatement();
          
          // execute statement.   default 데이터베이스에 employee 테이블 생성 후 테스트
          ResultSet res = stmt.executeQuery("SELECT * FROM employee WHERE salary>30000");
          
          System.out.println("Result:");
          System.out.println(" ID \t Name \t Salary \t Designation ");
          
          while (res.next()) {
             System.out.println(res.getInt(1) + " " + res.getString(2) + " " + res.getDouble(3) + " " + res.getString(4));
          }
          con.close();
       }
    }
    ``` 
    
    ![Untitled 14](https://user-images.githubusercontent.com/102286605/169500629-9fde5dc3-f9b7-4eb0-abf7-415228bb719f.png)

    
    ![Untitled 15](https://user-images.githubusercontent.com/102286605/169500640-d7f808c6-7295-4227-8827-49ace6b11318.png)

    
    ![Untitled 16](https://user-images.githubusercontent.com/102286605/169500642-1079dc8a-b548-4639-8033-d1c66e6878a2.png)

    
    ![Untitled 17](https://user-images.githubusercontent.com/102286605/169500654-28d8f3f6-7ada-4fad-ab58-cdff21c49ce9.png)

    
- hive를 연결하는 java 프로그램을 작성할 때 요구되는 라이브러리
1. ~/hive-3.1.2/lib
2. ls
3. hive-jdbc-3.1.2.jar
    1. [https://mvnrepository.com/artifact/org.apache.hive/hive-jdbc/3.1.2](https://mvnrepository.com/artifact/org.apache.hive/hive-jdbc/3.1.2)
