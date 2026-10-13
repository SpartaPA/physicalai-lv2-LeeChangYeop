# LV2 Module 3

## 1. 코드 출처

- **저장소:** https://github.com/cowsjh/Lv2_Horus_Assignment

## 2. 실행 환경

| 항목 | 내용 |
|---|---|
| 운영체제 |Ubuntu 24.04.5 LTS |
| ROS 2 | lyrical |
| Python 버전 | Python 3.12.3 |
| RealSense SDK 버전 | 2.59.1 |
| 카메라 모델 | RealSense D435 |
| 입력 영상 해상도 | 640x480 |

## 3. 실행 방법

### 3.1. 환경 설정

```bash
source /opt/ros/lyrical/setup.bash
source ~/ros2_lyrical/install/setup.bash
source ~/realsense_ws/install/setup.bash
source ~/rqt_image_ws/install/setup.bash
source ~/git/pj/Lv2_Horus_Assignment/lv2_module5/ros2_ws/install/setup.bash
```

### 3.2. 비전 노드 실행

```bash
ros2 launch realsense2_camera rs_launch.py \rgb_camera.color_profile:=640,480,30 \enable_depth:=false
ros2 launch bringup bringup.launch.py
```

### 3.3. 실행 확인

```bash
ros2 node list
ros2 topic list
```

## 4 설정값 변경 및 적용 방법

### 5.1. HSV 설정

- **설정 파일 또는 코드:** 
  
  `perception/config/perception.yaml`
  
- **변경할 파라미터:** 
  
  `detector.hsv_ranges`

- **값을 변경하는 방법:** 
  

  `lower`와 `upper`의 HSV 범위를 수정한다.수정한 설정 파일을 명시적으로 지정하려면 다음과 같이 실행한다.

  ```bash
  ros2 run perception perception_node --ros-args \
    -p config_path:=/절대경로/perception.yaml \
    -p publish_debug_mask:=true \
    -p publish_debug_image:=true
  ```
  설정 파일을 수정한 뒤 `perception_node`를 재실행한다. 수정한 설정 파일을 명시적으로 지정하려면 다음과 같이 실행한다.

  ```bash
  ros2 run perception perception_node --ros-args \
    -p config_path:=/절대경로/perception.yaml \
    -p publish_debug_mask:=true \
    -p publish_debug_image:=true
  ```

### 5.2. 후보 영역 필터 설정

- **설정 파일 또는 코드:** 
  `perception/config/perception.yaml`

- **변경할 파라미터:**
 `detector.min_area_px`, `detector.min_fill_ratio`, `detector.max_distance_m`

- **값을 변경하는 방법:**
  

 `min_area_px`: 검출 후보로 인정할 최소 윤곽선 면적을 조절한다. 현재 값은 `300.0`이다.

 `min_fill_ratio`: 후보 윤곽선 면적과 회전 바운딩 박스 면적의 비율에 대한 최소 기준을 조절한다. 현재 값은 `0.5`이다.

 `max_distance_m`: 깊이 정보를 이용해 검출 후보의 최대 거리를 제한한다. 현재 값은 `1.5` m이다. `null`로 설정하면 거리 제한을 비활성화한다.

- **변경 사항을 적용하는 방법:** 
  

설정 파일에서 파라미터 값을 수정한 뒤 `perception_node`를 재실행한다. 
HSV 설정과 동일하게 수정한 설정 파일을 지정하고, `/target/debug_mask`, `/target/debug_image`, `/target` 토픽을 확인해 후보 영역과 최종 검출 결과를 비교한다. 거리 필터를 실험할 때는 깊이 토픽이 사용 가능한지도 확인한다.


## 6. 결과 기록 위치

실험별 적용값, ROS 2 토픽 정보, 수신 결과 및 검출 로그는 다음 문서에 기록한다.
- [실험 결과 및 수신 기록](results/results.md)

실험의 가설, A/B 비교 분석 및 최종 설정값 선정 근거는 다음 문서에 정리한다.
- [실험 분석 보고서](report.md)
