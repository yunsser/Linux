# Day_06

- Hive
    - Data Warehouse
    - 분산환경
    - hdfs, MapReduce
    - HiveQL
    - 외부 시스템에서 Java를 사용하여 Hive에 접근하기
    - Java 웹사이트
    - VM(Hadoop, Hive), Host(Jaava)
    - VM(Hadoop, Hive), Host(Python)
    - hiveserver2 : IP주소, 포트번호(10000)
    
- 인공지능
    - ML(Machine Learning), 인공신경망(Deep Learning)
    - Tensorflow(Keras), PyTorch

- Spark
    - 하둡 Eco-system
    - 분석 (통계, Machine Learning)
    - MapReduce 대체를 위한 방법
    - SQL 지원
    - In-Memory : 100배 속도
    

1. start-all.sh
2. cd hive-3.1.2/bin
3. hiveserver2

1. 새로운 창에 > jps
2. cd hive-3.1.2/bin
3. beeline
4. !connect jdbc:hive2://localhost:10000/userdb;
5. show databases;
6. use userdb;
7. show tables;
8. SELECT * FROM employee;
9. SELECT MIN(salary) FROM employee;
10. 창닫기

### Hive 연결하는법

1. Anaconda Prompt
2. pip install pure-sasl
3. pip install thrift-sasl==0.2.1 --no-deps
4. pip install thrift==0.9.3
    1. conda install thrift==0.9.3
    2. pip install thrift
    3. 에러
5. pip install thriftpy==0.3.9
    1. 에러
6. pip install impyla

### 환경변수 바꿔주기 (위 실행시 오류뜰때)

1. hdfs dfs -chmod 777 /tmp

### 쥬피터

1.  쥬피터 노트북
2. 코드
    
    ```python
    from impala.dbapi import connect
    
    conn = connect(host='내 아이피', port=10000, database="userdb", auth_mechanism='PLAIN')
    cursor = conn.cursor()
    cursor.execute('select * from employee')
    print(cursor.fetchall())
    ```
    

### 스파크 설치

- hadoop-3.2.2, Spark-3.1.2 두 개가 잘 맞음]
1. 터미널 켜서 설치하기
2. wget https://dlcdn.apache.org/spark/spark-3.1.3/spark-3.1.3-bin-hadoop3.2.tgz
3. tar -xvzf spark-3.1.3-bin-hadoop3.2.tgz
4. mv spark-3.1.3-bin-hadoop3.2 spark-3.1.3
5. nano .bashrc (컨트롤 end = 맨뒤로감)
    
    ```python
    export SPARK_HOME=/home/hduser/spark-3.1.3
    export PATH=$PATH:$SPARK_HOME/bin:$SPARK_HOME/sbin
    export SPARK_CONF_DIR=$SPARK_HOME/conf
    export SPARK_MASTER_HOST=localhost
    export PYSPARK_PYTHON=python3
    export PYTHONPATH=$SPARK_HOME/python:$SPARK_HOME/python/lib/py4j-0.10.9-src.zip:$PYTHONPATH
    export PATH=$SPARK_HOME/bin:$SPARK_HOME/python:$PATH
    ```
    
6. source .bashrc
7. echo $SPARK_HOME
8. cd spark-3.1.3/bin
9. spark-shell (컨트롤 d,c 로 나갈수있다)

### 파이썬 스파크 실행

1. pyspark
2. 다른 터미널에
    1. dfs dfs -copyFromLocal employee.csv /user/mydata/
3. spark.read.csv('/home/hduser/employee.csv').show()
4. spark.read.csv('hdfs://localhost:9000/user/mydata/employee.csv').show()

### find spark 모듈설치

1. pip3 install findspark

### 압축파일 삭제

1. rm *.tgz
2. rm *.gz

scala, python, java, R

계정 루트 디렉토리에 pyspark_test 디렉토리 생성

1. pyspark_test
2. nano local_file_load.py
3. 코드
    
    ```python
    #! /usr/bin/python3
    import findspark
    findspark.init()
    from pyspark.sql import SparkSession
    
    sparkSession = SparkSession.builder.appName("pyspark-hdfs1").getOrCreate()
    
    # 로컬에서 파일 로드
    df1 = sparkSession.read.csv('/home/hduser/employee.csv')  # csv 내의 헤더가 없는 경우
    df1 = df1.toDF('id','name','salary','job')        #  컬럼명 추가
    df1.show()
    
    # csv 파일에 헤더가 포함된 경우
    #df2 = sparkSession.read.csv('emp_with_header.csv', header=True)
    #df2.show()
    
    # hdfs 영역으로부터 파일 읽어오기
    df = sparkSession.read.csv('hdfs://localhost:9000/user/mydata/employee.csv')
    df = df.toDF('id','name','salary','job') 
    df.show()
    
    df = df.withColumn('salary', df.salary+1000)
    df.show()
    ```
    
4. python3 local_file_load.py
5. nano local_file_load2.py
6.  코드 
    
    ```python
    #! /usr/bin/python3
    import findspark
    findspark.init()
    from pyspark.sql import SparkSession
    
    sparkSession = SparkSession.builder.appName("pyspark-hdfs2").getOrCreate()
    
    # read.load()는 다양한 옵션을 설정할 수 있다
    df2 = sparkSession.read.load('emp_with_header.csv',
        format='csv', sep=',', inferSchema='true', header='true')
    
    df2.show()
    ```
    
    ```python
    #! /usr/bin/python3
    
    import findspark
    findspark.init()
    from pyspark.sql import SparkSession
    
    sparkSession = SparkSession.builder.appName("pyspark-hdfs2").getOrCreate()
    
    # read.load()는 다양한 옵션을 설정할 수 있다
    df2 = sparkSession.read.load('/home/hduser/employee.csv',
        format='csv', sep=',', inferSchema='true', header='true')
    
    df2.show()
    ```
    
7. chmod 765 local_file_load2.py
8. ./local_file_load2.py

### 맨위 컬럼 추가해보기

1. 코드
    
    ```python
    #! /usr/bin/python3
    
    import findspark
    findspark.init()
    from pyspark.sql import SparkSession
    
    sparkSession = SparkSession.builder.appName("pyspark-hdfs2").getOrCreate()
    
    # read.load()는 다양한 옵션을 설정할 수 있다
    df2 = sparkSession.read.load('/home/hduser/employee.csv',
        format='csv', sep=',', inferSchema='true', header='false')
    df2 = df2.toDF('id','name','salary','jpb')
    
    df2.show()
    ```
    

### 로컬에 있는 employee.csv에 헤더를 추가

(num, name, salary,job)

컬럼명이 포함된 csv 파일을 로컬에서 읽어서 화면에 표시

1. 코드
    
    ```python
    #! /usr/bin/python3
    
    import findspark
    findspark.init()
    from pyspark.sql import SparkSession
    
    sparkSession = SparkSession.builder.appName("pyspark-hdfs2").getOrCreate()
    
    # read.load()는 다양한 옵션을 설정할 수 있다
    df2 = sparkSession.read.load('/home/hduser/employee_header.csv',
        format='csv', sep=',', inferSchema='true', header='true')
    # df2 = df2.toDF('id','name','salary','jpb')
    
    df2.show()
    ```
    

### hdfs 에서 employee.csv를 읽어와서

컬럼명을 추가하고 (num,name,salary,job)

salary에 5000을 추가하여 로컬에 employee_increase.csv로 저장해보세요

저장된 파일의 내용을 다시 읽어서 화면에 표시

```python
예시1
df = sparkSession.read.csv()
df.write.csv(’filename’, mode=’overwrite’)

예시2
df2.write.format('csv').option('header',True).mode('overwrite').
.option('sep',',').save('local_data')
```

1. 코드
    
    ```python
    #! /user/bin/python3
    
    import findspark
    findspark.init()
    from pyspark.sql import SparkSession
    
    sparkSession = SparkSession,builder.appName("pyspark-hdfs1").getOrCreate()
    
    df1 = sparkSession.read.csv('hdfs://localhost:9000/user/mydata/employee.csv',
    inferSchema=True, header=False)
    df1.show()
    
    df2 = df1.toDF('num','name','salary', 'job')
    df2 df2.withColumn('salary',df2.salary+5000)
    df2.write.csv('employee_increase.csv', mode='overwrite', header=True)
    
    df3 = sparkSession.read.csv('employee_increase.csv', header=True)
    df3.show()
    ```
    
2. chmod 765 local_file_load3.py
3. python3 local_file_load3.py

- DataFrame
    - Spark
    - pandas.DataFrame
    
- VM(Hadoop, Spark) ← Host(Jupyter notebook)
    - spark-master.sh
    - spark-worker.sh

### Windows 에 Spark설치, Jupyter notebook 사용

1. C:/spark/스파크 압축파일 다운로드 (압축해제 까지)
    1. [https://www.apache.org/dyn/closer.lua/spark/spark-3.1.3/spark-3.1.3-bin-hadoop3.2.tgz](https://www.apache.org/dyn/closer.lua/spark/spark-3.1.3/spark-3.1.3-bin-hadoop3.2.tgz)
2. c:/spark/스파크 압축해제 디렉토리/bin/winutils.exe 복사
    1. winutils.exe 설치 [https://github.com/steveloughran/winutils/tree/master/hadoop-3.0.0/bin](https://github.com/steveloughran/winutils/tree/master/hadoop-3.0.0/bin)
3. findspark 설치 : Anaconda Prompt 이용
    1.  python -m pip install findspark
4. 환경변수 설정 : 윈도우 검색기에서 시스템 환경변수 편집
    1. SPARK_HOME : D:\spark\spark-3.1.3-bin-hadoop3.2
    2. HADOOP_HOME = D:\spark\spark-3.1.3-bin-hadoop3.2
    3. PYSPARK_DRIVER_PYTHON = jupyter
    4. PYSPARK_DRIVER_PYTHON_OPTS = notebook
    5. PATH = %HADOOP_HOME%\bin
    6. JAVA_HOME = 자바가 설치된 루트 경로(bin 디렉토리 상위까지)
5. 테스트 코드 실행해보기 
    
    ```python
    import findspark
    findspark.init()
    
    import pyspark              # only run after findspark.init()
    from pyspark.sql import SparkSession
    
    spark = SparkSession.builder.getOrCreate()
    
    df = spark.sql('''select 'spark' as hello ''')
    df.show()
    ```
    
6. start-master.sh
7. start-worker.sh spark://localhost:7077