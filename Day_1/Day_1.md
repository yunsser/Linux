# Day_1

빅데이터 플랫폼

- 수집, 저장, 처리, 분석(통계분석, Machine Learning)
- Hadoop (Google File System)
- 빅데이터 분산환경 (Distributed) :  Clustering 환경
- 빅데이터 :

1. 슈퍼컴
2. 저사양컴 Clustering

master / slave

Name node / Data node

Clustering (분업)

Hadoop Eco-System

- 수집 시스템
- 저장 시스템
- 처리
- 분석

HDFS (Hadoop Distributed File System) 명령어

- 분산환경에서 작동

Linux 환경에서 작동

- Windows에서 작동하지 않음

윈도우에 가상화 소프트웨어 설치

- VMWare에 리눅스 설치, 리눅스 안에 Hadoop 설치
- Hadoop안에 Eco-System 설치

VMWare 설치

- VMware-player-full-16.2.3-19376536.exe
- 가상의 하드웨어 생성
- 가상의 하드웨어에 OS(Ubuntu Linux) 설치
    - hduer
    

shutdown now

pwd : Present Working Directory

![Untitled](https://user-images.githubusercontent.com/102286605/168568827-b49d3d80-0a4c-4f87-8c5d-90bbe7e611b9.png)

![Untitled 1](https://user-images.githubusercontent.com/102286605/168568839-525bd526-550f-4b2a-9ad3-c3e6900a606e.png)

- whoami : 유저확인
- shutdown now : 주위 필요 중요함
- pwd : 현재 디텍토리 위치 (Present Working Directory)
- ls : 현재 디렉토리 확인
- ls -l : 현재 디렉토리 상세정보
- ls -al : 숨긴파일 확인 (권한 확인)
- cd Documents/ : 해당 디렉토리로 이동
- cd : 홈
- cd ~ : 처음으로 돌아가기
- cd .. : 상위 디렉토리로 가기
- cd ./abc : 현재 디렉토리의 abc로 들어가라
- d rwx r-x --- : 오너는 읽고 쓰고 실행, 그룹은 읽고 실행, 그 외 암것두 안됨
    - owner(rwx), group(r-x), others(—-)
        - r : read (4)
        - w : write (2)
        - x : execute (1)
            - 4 4 4  - 모두에게 읽기
            - 7 7 7  - 모두에게 읽고 쓰고 실행
            - 3 3 3 - 모두에게 쓰고 실행
- ls -R/ : Recursive 모든 목록을 다볼수있다

- 파일작성
    
    cat > test.txt 
    
    (내용작성)
    
    Contol + D
    

![Untitled 2](https://user-images.githubusercontent.com/102286605/168568895-d1536fb4-3003-43fe-b9f7-5500555a9473.png)


cat > test.txt : 키보드 입력 데이터가 sample 파일에 저장

- 표준입력 → 파일에 저장

cat sample : sample 파일의 내용이 화면에 표시

- 파일 데이터 → 표준출력

nano 

- 에디터
    - Control + o 저장

![Untitled 3](https://user-images.githubusercontent.com/102286605/168568909-c6843545-937e-4101-a09e-9f1ec2ce02b1.png)


cat >> test.txt 

- 이어쓰기

cat test.txt > sample.txt

- 내용 새로운 파일에 복사

![Untitled 4](https://user-images.githubusercontent.com/102286605/168568926-90e923d2-f6ff-4a3d-bcec-05cf8fb75e23.png)


test.txt의 내용을 sample.txt 아래에 추가하려면?

- cat test.txt >> sample.txt

- cat file :  파일의 내용을 화면에 표시
- cat > file : 표준입력 내용을 file에 저장
- cat >> file : 표준입력 내용을 file에 추가
- cat file > file2 : 복사 있으면 덮어쓰고 없으면 생성
- cat file >> file2 :  이어쓰기

- mkdir testdata : 디렉토리 생성 mkdir(make directory)
- mkdir -p mydata/image
- cp sample.txt testdata/ : 파일 위치 이동
- cp text.txt testdata/
- ls -l testdata

- rm copy.txt : 삭제
- rm -r testdata : 디렉토리 삭제

- rm - r mydata/image : 마이데이터 안에 있는 이미지데이터가 삭제
- touch names.txt : 내용이 없는 새로운 파일 만들기

- mv sample.txt /acb/image/other.txt : 심플 텍스트 삭제 후 복사된 파일은 다른데로 이동

- find -type f -name mem* : 이름이 mem으로 시작하는 모든 것

- find mydata/.txt : mydata 안에 txt 파일들 검색
- find mydata : mydata 안에 파일검색
- find -type f -name mem* : mydata 안에 mem으로 시작하는 모든 파일 검색

- which python3
- /usr/bin/python3 ( 이렇게 쳐야 파이썬이 나옴)

- 파이썬 간편하게 열게 만들기
    1. nano .profile
    2. export PATH=/user/bin/python3
    3. source .profile
    4. python3 
    5. python3 hello.py

- 경로없이 파이썬 실행
    1. nano hello.py (파일생성)
    2. #! /usr/bin/python3 (파일 안에 맨 윗줄에 적어주기


```python
-- txt -> py
cp members.txt load_mem.py
nano load_mem.py
#! /usr/bin/python3

with open('members.txt', 'r') as fin:
        for line in fin:
                if len(line)<2:
                        break
                num, name, phone = line.strip().split()
                str = "{}\t{}\t{}".format(num, name, phone)
                print(str)

./load_mem.py

# permission denied : 오류는 권한부여 오류
```

>, >> : Redirection

> file

| : 다른프로그램의 표준 출력 입력스트림으로 변환

test1.py → ‘Hello World’ : test1.py가 표준출력

./test1.py | ./test2.py

stdout.py : ‘Hello World’ 출력

stdin.py : 키보드로부터 입력하여 문자의 갯수를 세어서 화면에 표시

./stdout.py | ./stdin.py → 출력되는 문자의 갯수를 화면에 표시

![Untitled 5](https://user-images.githubusercontent.com/102286605/168568953-1529e0a2-a53d-4fd6-8f91-89b4fd19a717.png)

텍스트 붙여넣기 : Shift + Insert 또는 Ctrl + shift + V
텍스트 복사 :  Ctrl + Insert 또는 Ctrl + shift + C