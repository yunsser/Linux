# Day_02

스트림(Stream)

- 표준출력 스트림 : 표준출력장치 모니터에 표시
- 표준입력 스트림 : 표준입력장치 키보드로부터 입력

cat sample.txt : sample.txt 내용을 표준출력

cat > sample.txt

cat >> sample.txt

a | b : a에서 표준출력 → b의 표준입력으로 전환

a에서는 1~5까지 5회 표준출력한다

b에서는 a의 출력값을 모두 받아서 합산한 결과를 화면에 표시

![Untitled](Day_02%20df90d2be834c488e94a852ac2942e83f/Untitled.png)

Hadoop

- Linux

---

## Hadoop 설치

sudo apt update

비번치기 ( 안보이는게 정상)

sudo apt install openjdk-8-jdk -y

- java -version 을 쳐서 버전 확인하기
- javac -version 을 쳐서 버전 확인하기

## OpenSSH 설치

sudo apt install openssh-server openssh-client -y

- sudo cat /etc/sudoers : 권한부여
- hdoop ALL=(ALL:ALL) ALL (root 아래 써주기, 지금은 안함)

## SSH 설정

ssh-keygen -t rsa -P '' -f ~/.ssh/id_rsa

![Untitled](Day_02%20df90d2be834c488e94a852ac2942e83f/Untitled%201.png)

물결 뒤 내용은 나의 디렉토리

1. cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys : 앞에 파일을 뒷쪽 파일로 붙여넣는다
2. sudo cat .ssh/authorized_keys
3. chmod 600 .ssh/authorized_keys : 소유자만 읽고 쓰게 함
    1. chmod 0600 ~/.ssh/authorized_keys ( 0600 이라 써두됨)
4. ls -al .ssh/authorized_keys
5. ssh localhost
6. yes (설정하시겠습니까아?)
7. wget https://archive.apache.org/dist/hadoop/common/hadoop-3.2.2/hadoop-3.2.2.tar.gz
    1. https://hadoop.apache.org/release/3.2.2.html > 초록색 버튼 위 오른쪽 마우스 누르면 링크복사
8. tar -xvzf hadoop-3.2.2.tar.gz : 압축파일 푸는 명령어
9. mv hadoop-3.2.2 hadoop

### Hadoop설정파일 편집

1. .bashrc
2. nano .bashrc
3. 맨아래에 붙여넣고 저장
    1. 코드
        
        ```python
        #Hadoop Related Options
        export HADOOP_HOME=/home/hduser/hadoop
        export HADOOP_INSTALL=$HADOOP_HOME
        export HADOOP_MAPRED_HOME=$HADOOP_HOME
        export HADOOP_COMMON_HOME=$HADOOP_HOME
        export HADOOP_HDFS_HOME=$HADOOP_HOME
        export YARN_HOME=$HADOOP_HOME
        export HADOOP_COMMON_LIB_NATIVE_DIR=$HADOOP_HOME/lib/native
        export PATH=$PATH:$HADOOP_HOME/sbin:$HADOOP_HOME/bin
        export HADOOP_OPTS="-Djava.library.path=$HADOOP_HOME/lib/native"
        ```
        
4. source .bashrc
5. which java 
    1. /usr/bin/java
6.  ls -al /etc/alternatives/java 
    1. /usr/lib/jvm/java-8-openjdk-amd64/jre/bin/java (자바경로)
    2. /usr/lib/jvm/java-8-openjdk-amd64

### hadoop-env.sh

1. nano $HADOOP_HOME/etc/hadoop/hadoop-env.sh
2. 맨 아래 이 글 저장
    1. export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
3. cd hadoop/etc/hadoop/
4. echo $JAVA_HOME
5. source $HADOOP_HOME/etc/hadoop/hadoop-env.sh (실행, 적용)
6. echo $JAVA_HOME
    1. /user/lib/jvm/java-8-openjdk-amd64
7. nano $HADOOP_HOME/etc/hadoop/core-site.xml
8. <configuration></configuration> 사이에 명령어를 넣어준다
    1. 코드
    
    ```python
    <property>
      <name>hadoop.tmp.dir</name>
      <value>/home/hduser/tmpdata</value>
    </property>
    <property>
      <name>fs.default.name</name>
      <value>hdfs://127.0.0.1:9000</value>
    </property>
    ```
    
    ![Untitled](Day_02%20df90d2be834c488e94a852ac2942e83f/Untitled%202.png)
    
9. mkdir tmpdata
10. nano $HADOOP_HOME/etc/hadoop/hdfs-site.xml
11. <configuration></configuration>사이에 코드 붙여넣기
    1. 
    
    ```python
    <property>
      <name>dfs.data.dir</name>
      <value>/home/hduser/dfsdata/namenode</value>
    </property>
    <property>
      <name>dfs.data.dir</name>
      <value>/home/hduser/dfsdata/datanode</value>
    </property>
    <property>
      <name>dfs.replication</name>
      <value>1</value>
    </property>
    ```
    
12. mkdir dfsdata
13. cd dfsdata
14. mkdir namenode
15. mkdir datanode
16. nano $HADOOP_HOME/etc/hadoop/mapred-site.xml
17. <configuration></configuration> 코드 복붙
    1. 코드
    
    ```python
    <property> 
      <name>mapreduce.framework.name</name> 
      <value>yarn</value> 
    </property> 
    ```
    
18. nano $HADOOP_HOME/etc/hadoop/yarn-site.xml
19. <configuration></configuration> 코드 복붙
    1. 코드
    
    ```python
    <property>
      <name>yarn.nodemanager.aux-services</name>
      <value>mapreduce_shuffle</value>
    </property>
    <property>
      <name>yarn.nodemanager.aux-services.mapreduce.shuffle.class</name>
      <value>org.apache.hadoop.mapred.ShuffleHandler</value>
    </property>
    <property>
      <name>yarn.resourcemanager.hostname</name>
      <value>127.0.0.1</value>
    </property>
    <property>
      <name>yarn.acl.enable</name>
      <value>0</value>
    </property>
    <property>
      <name>yarn.nodemanager.env-whitelist</name>   
      <value>JAVA_HOME,HADOOP_COMMON_HOME,HADOOP_HDFS_HOME,HADOOP_CONF_DIR,CLASSPATH_PERPEND_DISTCACHE,HADOOP_YARN_HOME,HADOOP_MAPRED_HOME</value>
    </property>
    ```
    

---

### 설치 테스트

1. cd
2. pwd
3. hdfs namenode -format
4. cd hadoop
5. cd sbin
6. ls
7. start-dfs.sh
8. start-yarn.sh
9. jps (자바가 돌아가는지 체크)
10. stop-all.sh
    1. 실행하고 싶으면 start-dfs.sh

![Untitled](Day_02%20df90d2be834c488e94a852ac2942e83f/Untitled%203.png)

![Untitled](Day_02%20df90d2be834c488e94a852ac2942e83f/Untitled%204.png)

1. http://localhost:9870
2. http://localhost:9864
3. http://localhost:8088

---

start-all.sh : 실행

stop-all.sh : 종료

sudo shutdown now : 프로그램 종료

shutdown now : 프로그램 종료

---

### 하둡의 기능

분산처리 병행처리로 이루어져 있는게 하둡

Storage : 분산환경에 데이터 저장/읽어오기, HDFS 명령어 사용 (Hadoop File System 하둡분산환경)

Process : 분산처리, 통계분석, ML분석, 

- Eco-System : HBase, Hive, Spark (SQL, ML)
    - 보통 스파크로 통일

MapReduce > Spark (In-Memory)

- Spark가 100배 빠름

1. start-all.sh
2. jps
    1. 5개가 돌아가야함
        
        ![Untitled](Day_02%20df90d2be834c488e94a852ac2942e83f/Untitled%205.png)
        
    2. 분산처리
        
        2308 DataNode
        2186 NameNode
        2510 SecondaryNameNode
        
    3. 데이터처리
        
        2821 NodeManager
        2693 ResourceManager
        
3. hdfs dfs -ls /
    1. 분산파일 시스템에 대한 ls
    2. / 최상위 루트를 표현
4. ls /
    1. 로컬 리눅스 명령 (최상위 디렉토리)
5. hdfs dfs -mkdir -p /user/mydata 
    1. -P (parent) 부모부터 만든다 (user안에 mydata를 만들고 싶을 때 사용)
6. hdfs dfs -ls /
7. hdfs dfs -ls /user/
8. hdfs dfs -ls -R / 
    1. 반복해서 만들려면 -R ( Recursive) 을 붙여준다

![Untitled](Day_02%20df90d2be834c488e94a852ac2942e83f/Untitled%206.png)

1. hdfs dfs -copyFromLocal sample.txt /user/mydata/
    1. 분산환경으로 집어넣어준다 (분산파일에 있는 시스템)
    2. 복사해서 mydata로 넣어준다
2. hdfs dfs -ls -R /
3. hdfs dfs -cat /user/mydata/sample.txt

![Untitled](Day_02%20df90d2be834c488e94a852ac2942e83f/Untitled%207.png)