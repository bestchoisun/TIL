# 1-15 AWS-EC2 접속하기

https://ap-northeast-2.console.aws.amazon.com/ec2/v2/home?region=ap-northeast-2

로그인후 '지역:서울', '언어:한국어' 확인

EC2 -> 인스턴스 -> Ubuntu Server 18.04(1년간 무료) 선택
 -> 검토 및 시작  -> 시작하기 
 -> 새 키페어 생성 : 키 페어 이름 작성후 저장
 -> 인스턴스 시작 -> 보기

우분투(리눅스) 컴터에 원격접속을 한다. 


1. window > git bash 프로그램켜기 

1-2. 
(mac > terminal
 $ sudo chmod 400 ${키페어 끌어오기} 
 [enter]
 mac 비번 입력0 
 [enter]
)


2. 
 $ ssh -i {키페어 끌어오기} ubuntu@{퍼블릭 IPv4 주소} 
 [enter]
 [yes]


3. 간단한 명령어 입력

ls 
 ? 지금 위치에서 모든 폴더나 파일을 조회 

mkdir {name}
 ? 폴더 만들기 

cd {name}/
 ? 폴더 안으로 들어가기 

cd .. 
 ? 상위 위치로 나가기


4. 포트를 열어줘야 함 

aws 사이트 -> 인스턴스 -> 보안 tap메뉴 
 -> 보안그룹 link -> 인바운드 규칙 추가

포트범위 : 5000, 사용자 위치 무관
 ? flask 
포트범위 : 80, 사용자 위치 무관
 ? http의 기본 포트 주소
포트범위 : 27017, 사용자 위치 무관
 ? mongo db의 포트


# 1-16 AWS EC2세팅

5. filezilla
 protocol : SFTP
 HOST : {내 아이피}
 Port : 22 
 Logon type :  key file
 User : ubuntu
 key file 열기

6. ubuntu 서버의 sparta 폴더에 세팅파일(initial_ec2.sh) 갖다 붙이기 
  (세팅파일은 스파르타쌤 제공)
 -> git bash 에서 cd sparta
 -> sudo chmod 755 initial_ec2.sh
     ? 이 파일의 읽기, 쓰기 권한 바꿔주는거, 무반응 정상
 -> ./initial_ec2.sh

# 1-17 AWS 배포하기

7. mongodb 연결

8. app.py 파일에 db설정
'
client = MongoClient('mongodb://test:test@localhost', 27017)
'
한줄 넣기 

9. static, templates, app.py 를 ubuntu 컴터에 올리기 

10. git bash 에서  먼저 flask, pymongo 를 깔아야 함
 sparta 폴더에서 
 pip install flask 
 pip install pymongo

11. 실행 
 python app.py

12. 주소창에 {IP주소}:5000
 
