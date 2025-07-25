# 🤖 Grippy
물체 인식 + 분류 로봇팔 자동 분류 시스템 (시뮬레이터 환경) 프로젝트

## 팀원
* 3반_05013_박현근: hermes7308@gmail.com
* 3반_05027_김민정: alswjd057@gmail.com
* 3반_05012_이황제: hwangje0327@gmail.com
* 3반_05043_이용우: ericlee3025@gmail.com

---

## 🧩 1. 전체 시스템 구조 (Architecture Overview)

```
[카메라 시뮬레이션] → [물체 인식/분류 AI] → [로봇팔 제어] → [물체 이동 시뮬레이션]
 (Gazebo/Unity)    (YOLOv8/MediaPipe)  (MoveIt + ROS2)   (Rviz/Gazebo)

```

---

## ⚙️ 2. 시스템 구성 요소

### 📷 2.1 카메라 + 물체 환경 시뮬레이션
- **도구**: Gazebo (또는 Unity + ROS bridge)
- **내용**:
  - 테이블 위에 다양한 형태/색상의 물체 배치
  - 물체는 랜덤 위치에 스폰
  - RGB 카메라 센서 시뮬레이션

---

### 🧠 2.2 물체 인식 및 분류
- **도구**: YOLOv8 / MediaPipe / OpenCV
- **내용**:
  - 카메라 이미지를 ROS 토픽으로 전달받아 객체 탐지
  - 물체 중심 좌표 및 클래스(label) 추출
- **기능**:
  - 인식된 객체 위치를 3D 좌표로 변환 (camera frame → world frame)

---

### 🤖 2.3 로봇팔 제어 (시뮬레이션 기반)
- **도구**: MoveIt 2 + ROS 2 + Rviz + Gazebo
- **로봇 모델**: UR5 / xArm6
- **내용**:
  - 목표 물체의 위치로 이동 (Pick)
  - 그리퍼로 물체 잡기
  - 클래스별로 구분된 박스/위치로 이동 후 놓기 (Place)

---

### 🧠 2.4 통합 제어 로직
- **도구**: ROS 2 (foxy/humble), Python or C++
- **ROS2 노드 구성 예시**:
  - `camera_publisher`: 카메라 이미지 발행
  - `object_detector`: YOLO 실행 및 물체 인식
  - `transformer`: 좌표 변환
  - `task_planner`: Pick & Place 목표 설정
  - `motion_executor`: MoveIt API 사용

---

### 📊 2.5 시각화 및 디버깅
- **도구**: Rviz2 + Gazebo
- **내용**:
  - 인식된 물체에 bounding box 표시
  - 로봇 경로, 이동 시뮬레이션
  - Pick/Place 성공 여부 확인

---

## 🛠️ 3. 시스템 요구사항 정리

| 항목 | 내용 |
|------|------|
| 운영 체제 | Ubuntu 22.04 LTS |
| ROS 버전 | ROS 2 Humble |
| 시뮬레이터 | Gazebo (Fortress or Garden) |
| 로봇 모델 | UR5 / xArm6 (URDF + MoveIt config 지원) |
| 인식 모델 | YOLOv8 (Ultralytics), MediaPipe (선택사항) |
| 개발 언어 | Python (ROS2 노드 제어), 일부 C++ 가능 |
| 프레임워크 | OpenCV, PyTorch, MoveIt2, TF2, ROS2 bridge |

---

## 🧪 4. 시뮬레이션 예시 시나리오

1. Gazebo에서 테이블 위에 사과/우유/박스 스폰
2. 카메라 → YOLO → ‘사과’ 객체 인식 → 중심 좌표 추출
3. 좌표 변환 후 Pick 위치 결정
4. MoveIt으로 경로 생성 → UR5가 사과 잡고 이동
5. ‘사과’ 전용 구역에 물체 drop
6. 반복 진행

---

## 🗂️ 5. 폴더 구조 예시
```
robot_sorting_ws/
├── src/
│ ├── camera_simulator/ # Gazebo + 카메라 환경
│ ├── object_detector/ # YOLO/MediaPipe 노드
│ ├── coordinate_transformer/ # tf2 사용한 좌표 변환
│ ├── task_planner/ # Pick/Place 계획
│ ├── motion_controller/ # MoveIt 2 기반 로봇 제어
│ └── gripper_controller/ # 그리퍼 제어 노드
├── models/
│ └── apple, milk, box/ # Gazebo 물체 모델
├── config/
│ └── moveit_config/ # UR5 설정 파일
└── launch/
└── full_simulation.launch.py # 전체 통합 실행
```

---

## 📈 6. 확장 아이디어

- 클래스 별로 자동 분류 통계 수집 (e.g., 오늘 사과 몇 개 분류됨?)
- AI 학습 기반 잘못된 분류 탐지 (강화학습 기반으로 보정)
- 모바일 앱/웹으로 실시간 제어 및 확인

---

## ✨ 7. 추천 개발 순서

1. Gazebo에서 카메라 + 물체 인식 테스트
2. YOLO 모델을 이용해 이미지에서 물체 인식
3. MoveIt2를 사용해 단일 객체 Pick & Place 성공
4. 전체 시스템 ROS2 노드 통합
5. 다양한 객체 추가 및 조건 처리

