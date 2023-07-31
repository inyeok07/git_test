<img width="133" alt="image" src="https://github.com/inyeok07/git_test/assets/134765214/2404994b-7421-4f6a-847e-500324ac7fe4">import cv2 as cv
from datetime import datetime
import ftplib
import os

files_Path = "C:/Users/choin/OneDrive/바탕 화면/invisible/" # 파일들이 들어있는 폴더
file_name_and_time_lst = []
# FTP 세션 생성
session = ftplib.FTP()
session.connect('172.30.1.37', 21)
session.login("inyeok1", "1234")

# 카메라 열기

cap = cv.VideoCapture(0)

# 카메라 열기에 실패한 경우 처리
if not cap.isOpened():
    print('카메라 열기 실패')
    exit()

while True:
    # 카메라에서 프레임 읽기
    ret, img = cap.read()

    # 프레임 읽기에 실패한 경우 처리
    if not ret:
        print("카메라에서 읽을 수 없음")
        break

    # 현재 시간 가져오기
    now = datetime.now()

    # 프레임 출력
    #   cv.imshow("PC 카메라", img)

    # 현재 시간을 기반으로 고유한 파일명 생성
    img_fileName = now.strftime("%Y-%m-%d%H%M%S") + '.png'

    # 캡처한 프레임을 이미지 파일로 저장
    img_captured = cv.imwrite(img_fileName, img)

    # 업로드할 파일을 이진 모드로 열기
    uploadfile = open(img_fileName, mode='rb')

    # FTP 세션의 인코딩 설정
    session.encoding = 'utf-8'

    # 이미지 파일을 FTP 서버에 업로드
    session.storbinary('STOR ' + img_fileName, uploadfile)

    # 파일 닫기
    uploadfile.close()

    # 업로드 완료 메시지 출력
    print('전송 완료')
    for f_name in os.listdir(f"{files_Path}"):
        written_time = os.path.getctime(f"{files_Path}{f_name}")
        file_name_and_time_lst.append((f_name, written_time))
    sorted_file_lst = sorted(file_name_and_time_lst, key=lambda x: x[1], reverse=True)
    # 가장 앞에 이는 놈을 넣어준다.
    recent_file = sorted_file_lst[0]
    recent_file_name = recent_file[0]
    print("C:/Users/choin/OneDrive/바탕 화면/invisible/"+recent_file_name)
    # 'q' 키를 눌러 종료할 때까지 대기
    #if cv.waitKey(1) == ord('q'):
     #   break

# FTP 세션 종료
session.close()

# 카메라 해제
cap.release()

# 모든 창 닫기
cv.destroyAllWindows()
