# LKAS_BirdEye
---
이 레포지토리는 MORAI simulator 환경에서 LKAS(차선 유지 시스템)을 구현한 프로젝트입니다.<br>
LKAS 구현하기까지 1 ~ 9단계로 나눠 정리 

### 사용 운영체제
- Ubuntu 18.04LTS
- ROS melodic
### 사용 시뮬레이션 환경
- MORAI simulator
    - scout-mini 모델 사용
### 사용 기술
- OpenCV
- BirdEye view
- sliding_window

### 실행 전 작업
- MORAI simulator 실행
    - map 선택 및 scout-mini 모델 선택
    - 카메라 설치
- ros-bridge 실행
```
$ roslaunch rosbridge_server rosbridge_websocket.launch
```

### 1. sub_camera
MORAI simulator에서 publish하는 카메라 이미지 토픽을 subscribe하여 cv2모듈의 imshow로 이미지 확인
```
$ rosrun scout_ros 1.sub_camera.py
```

![1_sub_camera](https://github.com/Choi0914/LKAS_Sliding_Window/assets/121415776/0607e11c-c123-4c7b-b65e-693c20a955f0)

### 2. pub_camera
카메라 이미지 토픽을 subscribe한 것을 topic으로 publish하여 rqt_image_show로 확인
```
$ rosrun scout_ros 2.pub_camera.py
```

![2_pub_camera](https://github.com/Choi0914/LKAS_Sliding_Window/assets/121415776/d555ba0a-f59b-4de1-9c3f-21a981334533)

### 3. bird_eye_view
cv2모듈의 warpPerspective함수를 통해 정면에서 보고 있는 이미지를 탑뷰로 보이도록 전환
- 이미지의 좌표는 좌상단부터 (0,0)으로 시작
- 원근으로 인해 실제 모습에서 왜곡된 것을 직선 차선을 기준으로 차선의 4개의 모서리 좌표를 지정해 bird_eye_view로 변환
```
$ rosrun scout_ros 3.bird_eye_view.py
```

![3_bird_eye_view_2](https://github.com/Choi0914/LKAS_Sliding_Window/assets/121415776/11bdf115-859d-409b-8d39-5b39bd7b2439)

### 4. white_line_detect
HSV색영역을 사용해 bird_eye_view 상에서 하얀색만 추출해 cv2모듈의 bitwise_and함수로 원본과 겹치는 부분만 출력
```
$ rosrun scout_ros 4.white_line_detect.py
```

![4_white_line](https://github.com/Choi0914/LKAS_Sliding_Window/assets/121415776/62ca7f29-cb54-4fb1-b59e-a908040c4779)

### 5. yellow_line_detect
HSV색영역을 사용해 bird_eye_view 상에서 노란색만 추출해 cv2모듈의 bitwise_and함수로 원본과 겹치는 부분만 출력
```
$ rosrun scout_ros 5.yellow_line_detect.py
```

![5_yellow_line](https://github.com/Choi0914/LKAS_Sliding_Window/assets/121415776/b4925159-b140-4b89-ad1f-31c9f4c20838)

### 6. blend_line
cv2모듈의 bitwise_or함수로 하얀색, 노란색 추출한 것을 합쳐 출력
```
$ rosrun scout_ros 6.blend_line.py
```

![6_blend_line](https://github.com/Choi0914/LKAS_Sliding_Window/assets/121415776/7eff0932-60b5-4043-a42e-6913ac202287)

### 7. binary_line
추출한 이미지를 더 확실히 하기 위해 이진화작업 후 픽셀 값이 일정 이상이면 다 최대치로 전환
```
$ rosrun scout_ros 7.binary_line.py
```

![7_binary_line](https://github.com/Choi0914/LKAS_Sliding_Window/assets/121415776/228b7827-bacc-4efe-8183-648b7be58e2e)

### 8. sliding_window
차선을 인식하기 위해 이미지를 양옆으로 2등분하고 세로 크기가 이미지의 10분의 1인 window안에 차선 픽셀이 존재하는지 찾아 차선 인식
```
$ rosrun scout_ros 8.sliding_window.py
```

![8 sliding_window_render](https://github.com/Choi0914/LKAS_Sliding_Window/assets/121415776/411bf965-d36d-4201-8642-a7135504a084)

### 9. LKAS
양 차선의 중앙 좌표의 x값을 사용해 z축 회전 속도값을 계산해 scout_mini에 publish하여 자동으로 움직임
```
$ rosrun scout_ros 9.LKAS.py
```
![9 LKAS_render](https://github.com/Choi0914/LKAS_Sliding_Window/assets/121415776/01b56ec5-3b2a-4d45-8336-e4b509db8ad0)
