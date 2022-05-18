# Day_03

### Hadoop

- 빅데이터 분산 플랫폼
- Storage : 데이터를 분산환경에 쓰고 읽는 기능 (HDFS)
- Process : 분산환경에서 처리, 통계분석, ML분석 (MapReduce)

### MapReduce

Mapper : 데이터를 key, value로 나눔 (순서없이 생김) ‘국가-1’, ‘복지-1’, .....

- shuffle :

Reducer

Hive : Data Warehouse 

Client Server : 독립적으로 실행

### HDFS 명령을 사용하여 names.dat 파일을 분산환경에 복사해보세요

1. start-all.sh
2. jps
3. hdfs dfs -ls -R /
4. ls
5. nano names.dat 
    1. 안에 리스트 작성
        
        ```python
        11 Ward 010-2541-8541
        12 Scott 010-5780-3214
        13 King 010-3641-5209
        ```
        
6. ls -l names.dat
7. hdfs dfs -copyFromLocal names.dat /user/mydata/
8. hdfs dfs  -cat /user/mydata/names.dat
    1. 분산환경
    
    ![Untitled](Day_03%20a47ea37cb8524831b9a79b3f4799aa08/Untitled.png)
    

### 기능추가

1. hdfs dfs -appendToFile localfile destfile
    1. 어팬드 명령어 names.dat 아래 한 행 추가
    2. localfile : 내가 지정한 파일이름
2. cat > localdile
3. cat localfile
4. hdfs dfs -appendToFile localfile /user/mydata/names.dat
5. hdfs dfs -cat /user/mydata/names.dat
    1. 샘플사진
        
        ![Untitled](Day_03%20a47ea37cb8524831b9a79b3f4799aa08/Untitled%201.png)
        

### /user/mydata 디렉토리의 권한 설정을 rwxrwxr-x 으로 변경

- chmod을 이용 (권한설정)
1. hdfs dfs -ls -R /
2. hdfs dfs -chmod 775 /user/mydata
3. hdfs dfs -ls /user/mydata
4. hdfs dfs -ls /user

![Untitled](Day_03%20a47ea37cb8524831b9a79b3f4799aa08/Untitled%202.png)

### 하둡 : 분산환경에 있는 파일을 로컬로 가져다놓고 분석가능

명령어 앞 : hdfs dfs

끝쪽에 디렉토리 주소쓰기 :  /user/mydata/ ....

- cp를 이용한 dfs 내에서의 파일복사
    - hdfs dfs -cp /user/mydata/names.dat /user/
        
        ![Untitled](Day_03%20a47ea37cb8524831b9a79b3f4799aa08/Untitled%203.png)
        
- -copyFromLocal
    - 복사, a→b
    - hdfs dfs -copyFromLocal 파일명 /user/디렉토리명
    - 예시
        
        ![Untitled](Day_03%20a47ea37cb8524831b9a79b3f4799aa08/Untitled%204.png)
        
- -copyToLocal
    - 복사, b→a
- -rm -r mydir
    - dfs의 디렉토리 삭제
- -rm myfile
    - dfs의 파일 삭제
    - 예시사진
        
        ![Untitled](Day_03%20a47ea37cb8524831b9a79b3f4799aa08/Untitled%205.png)
        
- -get dfsfile localfile
    - dfs에 있는 sample.txt를 로컬로 복사
- -put localfile dfsfile
    - localfile을 dfsfile로 집어 넣는다
- -moveFromLocal localfile dfsfile
    - localfile을 삭제하고 복사한걸 dfsfile안에 넣는다
- -mv
    - dfs 내에서 파일의 이동 a → b
- -head dfsfile
    - 파일의 앞부분을 확인
- -tail dfsfile
    - 파일의 뒷부분을 확인
- -test -e/z/d/f/s
    - e : exist, 파일이 존재하냐?
    - z : zero, 파일내용이 비었냐? (길이), 파일의 내용 (zero냐?)
    - d : directory, 디렉토리냐?
    - f : file, 파일이냐?
    - s : size, 사이즈가 있느냐 (zero가 아니냐?)

### 로컬 파일 testdata.txt에 20행 정도를 입력하고 그 파일을 dfs의 sample.txt 파일의 아래에 추가해보세요

1. nano testdata.txt
2. hdfs dfs -appendToFile testdata.txt /user/mydata/sample.txt
3. hdfs dfs -cat /user/mydata/sample.txt
    1. 샘플사진
    
    ![Untitled](Day_03%20a47ea37cb8524831b9a79b3f4799aa08/Untitled%206.png)
    

### -Test 명령어 사용법

1. hdfs dfs -test -e /user/mydata/sample.txt
    1. sample.txt 이 파일이 있는지 테스트해라
2. echo $?
    1. 이거까지 써야 응답이 나온다
        1. 0 : 있다
        2. 1 : 없다

### HDFS 파이썬

HDFS 명령으로 Hadoop Storage 관련작업

HDFS를 지원하는 파이썬 모듈

HDFS를 지원하는 HBase, Hive, ....

- HBase : 컬럼기반 hdfs지원하는 컬럼기반 DB
- Hive : SQL과 유사한 HiveQL을 사용하여 HDFS 기반 데이터 웨어하우스

pypi.org

### HDFS 지원하는 파이썬 모듈 설치

1. sudo apt-get install python3-pip
    1. pip3 설치
2. 비밀번호 치기
3. y
4. pip3 install pandas hdfs
5. pip3 install pyarrow

### 파이썬 파일 작성, 불러오기

1. nano python_hdfs_demo.py
2. 코드 작성
    1. 코드
    
    ```python
    import pandas as pd
    import numpy as np
    from hdfs import InsecureClient
    
    client_hdfs = InsecureClient('http://localhost:9870')
    with client_hdfs.read('/user/mydata/names.dat', encoding='utf-8') as reader:
    	contents = reader.read()
    
    print(contents)
    ```
    
3. ls -l
    1. 파일 확인
4. chmod 764 python_hdfs_demo.py
    1. 실행권한주기
5. python3 python_hdfs_demo.py

- Linnx local : hdfs dfs -copyToLocal /user/xxx/my.txt ~/
    - 리눅스 로컬에서 돌아가게해두고 hdfs에서도 돌아가게함

### client.read

- hdfs_client.read()
    - 코드
        
        ```python
        from hdfs import InsecureClient
        
        hdfs_client = InsecureClient('http://localhost:9870')
        with hdfs_client.write('/user/filename.txt', overwrite=True, encoding='utf-8') as writer:
                writer.write('data')
        
        print('End of write')
        ```
        

### 로컬 파일에 있는 내용을 위의 방법으로 dfs 파일에 저장해보기

1. nano hdfs_file_write.py
2. 코드 
    
    ```python
    from hdfs import InsecureClient
    
    with open('names.dat') as fin:
    	contents = fin.read()
    
    #print(contents)
    
    hdfs_client = InsecureClient('http://localhost:9870')
    with hdfs_client.write('/user/mem.txt', overwrite=True, encoding='utf-8') as writer:
            writer.write('contents')
    
    print('End of write')
    ```
    
3. hdfs dfs -copyFromLocal mem.txt /user/
4. python3 hdfs_file_write.py
5. hdfs dfs -cat /user/member.txt

### 로컬에서 dfs로 파일 올리기

- hdfs_client.upload(’로컬파일’, ‘dfs파일’) # -copyFromLOcal
    - 로컬파일 → dfs파일 복사
    - hdfs_client_up.py
        
        ```python
        from hdfs import InsecureClient
        
        client = InsecureClient('http://localhost:9870')
        client.uplod('/user/up.txt', 'mem.txt')
        ```
        
- hdfs_client.download(’dfs파일’, ‘로컬파일’) # -copyToLocal
    - dfs파일 → 로컬파일 복사
    - hdfs_client_dw.py
        
        ```python
        from hdfs import InsecureClient
        
        client = InsecureClient('http://localhost:9870')
        client.download('/user/mem.txt', 'mem.txt')
        ```
        
- content 기능 : 경로에 있는지 알고 싶을때 (기능실행)
    - hdfs_client_content.py
        
        ```python
        from hdfs import InsecureClien
        
        print(client.content('/user/up.txt'))
        ```
        

![Untitled](Day_03%20a47ea37cb8524831b9a79b3f4799aa08/Untitled%207.png)

1. nano hdfs_client.py
2. python3 hdfs_client.py
3. cat mem.txt

- Hadoop(Srorage, Process)

### 파이썬 데이터 분석

- 통계
    - pandas, numpy
- Machine Learning (AI)
    - sci-learn, Tensorflow(인공신경망)

### pandas.DataFrame & CSV 파일 입출력

```python
import pandas as pd

data = {
	'A':[20,14,30],
	'B':[30,45,20]
}

df = pd.DataFrame(data)
print(df)
```

- CSV (Comma Separated Values)
    - 표 형태의 데이터를 저장하는 파일형식
    - 한줄이 한개의 행에 해당하며, 열 사이에는 쉼표로 구분
- DataFrame → CSV
- SV → DataFrame
- 표를 파일에 저장한다
    
    ```python
    df.to_csv('mydata.csv')
    ```
    
- 파일을 읽어서 표를 만든다
    
    ```python
    df2 = pd.read_csv('mydata.csv')
    print(df2)
    ```
    

1. nano data_frame_demo.py
2. 코드
    
    ```python
    import pandas as pd
    
    data = {
    	'A':[20,14,30],
    	'B':[30,45,20]
    }
    
    df = pd.DataFrame(data)
    print(df)
    df.to_csv('mydata.csv')
    ```
    
3. python3 data_frame_demo.py
4. cat sample.csv
5. nano data_frame_demo.py
6. 코드 (불러오기)
    
    ```python
    import pandas as pd
    
    df2 = pd.read_csv('sample.csv')
    print(df2)
    ```
    
    ![Untitled](Day_03%20a47ea37cb8524831b9a79b3f4799aa08/Untitled%208.png)
    
7. nano data_frame_demo.py
8. 코드
    
    ```python
    import pandas as pd
    
    df2 = pd.read_csv('sample.csv', index_col=0) 
    print(df2) 
    
    #index_col=0 : 맨 첫줄 0123 이 인덱스라는 걸 알려준다7.
    ```
    
    ![Untitled](Day_03%20a47ea37cb8524831b9a79b3f4799aa08/Untitled%209.png)
    
9. nano data_frame_demo.py
10. 코드
    1.  df2.describc() :기술통계
        
        ```python
        import pandas as pd
        
        df2 = pd.read_csv('sample.csv',index_col=0)
        print(df2)
        
        df2.describe()
        print(df2.describe())
        ```
        
        ![Untitled](Day_03%20a47ea37cb8524831b9a79b3f4799aa08/Untitled%2010.png)
        

### 로컬에서 생성된 DataFrame 객체를 dfs에 csv로 저장하기

```python
from hdfs import InsecureClient

df2 = pd.read_csv('sample.csv',index_col=0)
print(df2)

df2.describe()
print(df2.describe())

client_hdfs = InsecureClient('http://localhost:9870')
with client_hdfs.write('/user/sample.csv',overwrite=True,encoding='utf-8') as writer: 
        df2.to_csv(writer)
```

### dfs에 저장된 csv 파일을 읽어서 DataFrame 객체를 생성하고 통계 함수(sum)를 실행한다

1. 예시 (1)
    
    ```python
    from hdfs import InsecureClient
    
    df2 = pd.read_csv('sample.csv',index_col=0)
    print(df2)
    
    df2.describe()
    print(df2.describe())
    
    hdfs_client = InsecureClient('http://localhost:9870')
    with hdfs_client.read('/user/sample.csv') as reader:
    	df2 = pd.read_csv(reader, index_col=0)
    
    print(df2.sum(axis=0))[
    ```
    
    ![Untitled](Day_03%20a47ea37cb8524831b9a79b3f4799aa08/Untitled%2011.png)
    
2. 예시 (2)
    
    ```python
    import pandas as pd
    from hdfs import InsecureClient
    
    df = pd.read_csv('sample.csv', index_col=0)
    
    print('sum')
    print(df.sum(axis=0))
    
    hdfs_client = InsecureClient('http://localhost:9870')
    with hdfs_client.write('/user/mydata/sample.csv',overwrite=True,encoding='utf-8') as writer:
            df.to_csv(writer)
    print('csv saved!')
    
    with hdfs_client.read('/user/mydata/sample.csv') as reader:
            df2 = pd.read_csv(reader, index_col=0)
            print(type(df2))
            df2.sum(axis=0).to_csv('axis0_sum.csv', encoding='utf-8')
            print('results of sum saved!')
    ```
    
    ![Untitled](Day_03%20a47ea37cb8524831b9a79b3f4799aa08/Untitled%2012.png)
    
    ![Untitled](Day_03%20a47ea37cb8524831b9a79b3f4799aa08/Untitled%2013.png)