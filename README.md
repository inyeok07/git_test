import cv2 as cv

from datetime import datetime

import ftplib

import os

files_Path = "C:/Users/choin/OneDrive/바탕 화면/invisible/" # 파일들이 들어있는 폴더

file_name_and_time_lst = []

session = ftplib.FTP()

session.connect('172.30.1.37', 21)

session.login("inyeok1", "1234")

cap = cv.VideoCapture(0)

if not cap.isOpened():

    print('카메라 열기 실패')
    
    exit()

while True:

    ret, img = cap.read()

    if not ret:
    
        print("카메라에서 읽을 수 없음")
        
        break

    now = datetime.now()


    img_fileName = now.strftime("%Y-%m-%d%H%M%S") + '.png'

    img_captured = cv.imwrite(img_fileName, img)

    uploadfile = open(img_fileName, mode='rb')

    session.encoding = 'utf-8'

    session.storbinary('STOR ' + img_fileName, uploadfile)

    uploadfile.close()

    print('전송 완료')
    
    for f_name in os.listdir(f"{files_Path}"):
    
        written_time = os.path.getctime(f"{files_Path}{f_name}")
        
        file_name_and_time_lst.append((f_name, written_time))
        
    sorted_file_lst = sorted(file_name_and_time_lst, key=lambda x: x[1], reverse=True)
    
    recent_file = sorted_file_lst[0]
    
    recent_file_name = recent_file[0]
    
    print("C:/Users/choin/OneDrive/바탕 화면/invisible/"+recent_file_name)

session.close()

cap.release()

cv.destroyAllWindows()
