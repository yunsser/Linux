# Day_04

- Storage : 분산환경에서 데이터 입출력 (HDFS)
    - 분산파일 시스템 (DFS)
- Process : 분산환경에서 처리, 분석 (통계,ML)
    - MapReduce, Spark
    

### MapReduce를 사용하여 분석하는 예

텍스트 파일로부터 각 단어의 빈도수를 계산하는 예

- Mapper : 텍스트 파일에서 한 행을 읽어서 단어별로 쪼갠 후 다음 단계로 전달한다
    - Shuffle : 섞어준다
    - Sort : 동일 단어는 인접한 순서로 배치됨
- Reducer : Mapper에서 전달된 단어들의 빈도수를 계산하여 다음 단계로 전달한다
    - 리듀서의 출력을 파일에 저장

hadoop-streaming-xxx.jar

1. cd map_red
2. nano Mapper.py
    
    ```python
    #!/usr/bin/python3
    
    # mapper.py
    
    import sys
    
    for line in sys.stdin:
        line = line.strip()
        words = line.split()  # 한개의 공백이나 연속된 공백을 한개의 구분자로 간주함
    
        for word in words:
            print( '%s\t%s' % (word, 1) )     # 단어와 숫자 1을 매핑하여 출력한다
    ```
    
3. chmod 765 Mapper.py
4. ./Mapper.py
    1. 코드
        
        ```python
        Hello World Bye
        ```
        
    2. 단어를 쪼개서 알려준다
        
        ![Untitled](https://user-images.githubusercontent.com/102286605/169261695-c48c1eaa-6692-4380-a132-f0876de4cb88.png)

        
5. cat putin.txt | ./Mapper.py
    1. 예시
        
        ![Untitled 1](https://user-images.githubusercontent.com/102286605/169261725-e946040d-5d5e-4cf5-ac20-0235e5a36324.png)

        
6. cat putin.txt | ./Mapper.py | sort -k 1
    1. sort 정렬
        
        ![Untitled 2](https://user-images.githubusercontent.com/102286605/169261744-530269cd-b9c3-4c53-90b4-a5a2f2f56c46.png)

        
7. reducer.py
    1. 카운터 되게
        
        ```python
        #!/usr/bin/python3
        
        # reducer.py
        import sys
        
        current_word = None
        current_count = 0
        word = None
        
        for line in sys.stdin:
            line = line.strip()
            word, count = line.split('\t', 1)  # split('separator', max_split=-1)
        
            try:
                count = int(count)
            except ValueError:
                # count 가 숫자가 아니면 아래 코드를 실행하지 않고 지나감
                continue
        
            if current_word == word:            # 동일 단어가 반복될 시
                current_count += count
            else:
                if current_word:                # 동일 단어가 반복 종료될 시, 지금까지 카운트 출력
                    print( '%s\t%s' % (current_word, current_count) )
                current_count = count           # 새로 나온 단어가 시작될 시
                current_word = word
        
        if current_word == word:                # 마지막 단어는 위의 루프 안에서 출력하지 못하므로 루프 종료 후 아래에서 출력
            print( '%s\t%s' % (current_word, current_count))
        ```
        
8. chmod 765 Reducer.py
9. cat putin.txt | ./Mapper.py | sort -k 1 | ./Reducer.py | sort -k 2
    1. sort -k 1,2 차이는 첫번째칸 두번째칸이라는 의미
    2. 정렬 (오름차순, 숫자가  적은거부터 큰 순으로 , 문자열로 정렬이라 10과 13로 1로 쳐진다)
        
        ![Untitled 3](https://user-images.githubusercontent.com/102286605/169261814-70444643-3048-466d-8029-43a8fd166d58.png)

        
        
    3. 정렬 (내림차순, -r 을 추가해준다)
        1. cat putin.txt | ./Mapper.py | sort -k 1 | ./Reducer.py | sort -r -k 2
    4. 정렬 (숫자정렬)
        1. cat putin.txt | ./Mapper.py | sort -k 1 | ./Reducer.py | sort -r -n -k 2
            
            ![Untitled 4](https://user-images.githubusercontent.com/102286605/169261858-a8b2ac9a-56c3-43b1-91da-859fd6aa2f11.png)

            
    5. 옵션 종류
        1. sort [-옵션] [-o 저장될 파일명] 정렬할 파일명 [병합할 파일명]
            
            
            | 종류 | 내용 |
            | --- | --- |
            | -n | 라인의 각 필드를 비교하는 대상을 숫자로 한정 |
            | -f | 영어로 정렬할 때, 대소문자 구별안함 |
            | -r | 출력 순서를 역순 |
            | -b | 앞에 붙는 공백 무시 |
            | -t | 필드 구분자 지정 |
            | -m | 정렬된 파일을 병합 |
            | -u | 정렬후, 중복행 제거 |
            | -o | 저장할 파일명을 명시, 명시하지 않으면 화면에 출력 |
    6. 두번째 필드를 기준으로 하고 싶다면
        1. sort + 1 [파일명]
    7. 마지막으로 파일에 저장하고 싶다면
        1. sort [파일명] -o [저장할 파일명]

### 하둡 명령어

1. hdfs dfs -mkdir /user/hdoop/input
    1. hdfs dfs -mkdir /user/hdoop
    2. hdfs dfs -mkdir /user/hdoop/input
2. hdfs dfs -copyFromLocal Mapper.py /user/hdoop/input
3. hdfs dfs -copyFromLocal Reducer.py /user/hdoop/input
4. 손으로 치기 그래야 오류안남.. 한줄로쓰기
    
    ```python
    hadoop jar $HADOOP_HOME/share/hadoop/tools/lib/hadoop-streaming-3.2.2.jar -files Mapper.py,Reducer.py -input /user/hdoop/input/* -hadoop jar $HADOOP_HOME/share/hadoop/tools/lib/hadoop-streaming-3.2.2.jar -files Mapper.py,Reducer.py -input /user/hdoop/input/* -output /user/hdoop/output -mapper Mapper.py -reducer Reducer.pyoutput /user/hdoop/output -mapper Mapper.py -reducer Reducer.py
    ```
    
    ![Untitled 5](https://user-images.githubusercontent.com/102286605/169261943-165117c3-6dad-4744-ad28-80f1c097ab4f.png)


    
    ![Untitled 6](https://user-images.githubusercontent.com/102286605/169261923-d84b7b3e-90e9-4061-8714-bf4d31f0a0b3.png)

    
5. hdfs dfs -ls -R /user/ouput
6. hdfs dfs -cat /user/hdoop/output/part-00000

### 통계

- 위의 파일에서 각 연도별 평균 승객수를 구해보세요
- Hadoop의 처리 프레임워크인 MapReduce를 활용하며 Python으로 작성해보세요
1. nano airtravel.csv
    
    ```python
    "Month", "1958", "1959", "1960"
    "JAN",  340,  360,  417
    "FEB",  318,  342,  391
    "MAR",  362,  406,  419
    "APR",  348,  396,  461
    "MAY",  363,  420,  472
    "JUN",  435,  472,  535
    "JUL",  491,  548,  622
    "AUG",  505,  559,  606
    "SEP",  404,  463,  508
    "OCT",  359,  407,  461
    "NOV",  310,  362,  390
    "DEC",  337,  405,  432
    ```
    
2. nano AT_Mapper.py
    1. 설명
        
        ```python
        #! /user/bin/python3
        
        import sys
        for line in sys.stdin:
        	line = line.strip()
        	values = line.split(',')
        	for i in range(0,len(values)):
        		try:		
        			num = int(values[i])
        		except:
        			continue
        		# 값에 연도를 매핑하여 다음단계로 출력
        		print("{}\t{}".format(valuse[i].strip(), 1958+i-1) # 340   1958
        ```
        
    2. 코드
        
        ```python
        #! /usr/bin/python3
        
        import sys
        
        for line in sys.stdin:
        
                line = line.strip()
                values = line.split(',')
                for n in range(0,len(values)):
                        try:
                                num = int(values[n])
                        except:
                                continue
                        print('{}\t{}'.format(num, 1958+n-1))
        ```
        
        ![Untitled 7](https://user-images.githubusercontent.com/102286605/169262027-117b0036-2a24-42d0-8e1e-36de7372bcd9.png)

        
3.  AT_Reducer py
    1. 설명
        
        ```python
        passengers = {}
        passengers['1958'] = []
        passengers['1959'] = []
        passengers['1960'] = []
        
        #
        passengers['1958'].append(int(value.strip()))
        
        avg_58 = sum(passengers['1958']) // len(passengers['1958'])
        
        avg = {}
        avg['1958'] = avg_58
        avg['1959'] = avg_59
        avg['1960'] = avg_60
        print(avg)
        # avg['1958':avg_58, '1959':avg_59, '1960':avg_60]
        ```
        
    2. 코드
        
        ```python
        #! /usr/bin/python3
        
        import sys
        
        passengers = {'1958':[], '1959':[], '1960':[]}
        
        for line in sys.stdin:
                line = line.strip()
                v, y = line.split('\t', 1)
                passengers[y.strip()].append( int(v.strip()))
        
        avg = {}
        avg['1958'] = sum( passengers['1958'])// len(passengers['1958'])
        avg['1959'] = sum( passengers['1959'])// len(passengers['1959'])
        avg['1960'] = sum( passengers['1960'])// len(passengers['1960'])
        
        print(avg)
        ```
        
        ![Untitled 8](https://user-images.githubusercontent.com/102286605/169262054-0991dba9-0f0c-4dbe-a86a-dcf7c8bef995.png)

        
4. output/part-00000
    
    ```python
    {'1958':341, '1959':420, '1960':520 } # 이렇게 나오게끔 하기
    ```
