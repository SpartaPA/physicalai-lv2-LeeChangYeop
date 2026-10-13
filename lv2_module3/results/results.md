# 실험 결과 및 수신 기록

## 1. ROS 2 토픽 정보

| 구분 | 토픽 이름 | 메시지 타입 |
|---|---|---|
| Raw Image | /camera/camera/color/image_raw | sensor_msgs/msg/Image |
| Mask | /target/debug_mask | sensor_msgs/msg/Image |
| 검출 결과 | /target | geometry_msgs/msg/PointStamped |

## 2. 실험 1 — HSV 설정 변경

### 2.1. 적용 설정값

| 항목 | A 설정 | B 설정 |
|---|---|---|
| 변경 파라미터 | HSV 범위 (H, S, V 하한/상한) | HSV 범위 (H, S, V 하한/상한) |
| 적용값 | Lower `[98, 190, 25]` ~ `Upper [118, 255, 230]` | Lower `[98, 20, 25]` ~ `Upper [118, 255, 230]`  |

> 공통 고정값: blur 5, morph 5 (open 1, close 2), min_area 300px, bbox rotated, resize 원본, max_distance 1.5m, min_fill 0.5


### 2.2. A 설정 수신 기록
- **Raw Image Debug:** ![A_raw](images/exp1_A_raw.png)  
  `/camera/camera/color/image_raw` (640x480, RGB8, 30 FPS) 바운더리 포함 수신 화면
- **Raw Image:** ![A_raw](images/exp1_A_raw.png)  
  `/camera/camera/color/image_raw` (640x480, RGB8, 30 FPS) 수신 화면
- **Mask:** ![A_mask](images/exp1_A_mask.png)  
  `/target/debug_mask`: 파란색 목표물 영역이 흰색으로 분리됨
- **검출 결과:** `/target` (PointStamped) 수신값  
  x(ex) = -0.242, y(ey) = 0.304, z(area) = 0.0269  
  → 목표물이 화면 중심 기준 좌측 하단에 있으며, 측정 동안 위치가 안정적으로 유지됨 (ex `-0.241`~`-0.244`, area `0.0267`~`0.0270`)
- **검출 로그:**
[perception_node] 검출 설정: HSV `[98,190,25]`~`[118,255,230]`, blur 5, morph 5 (open 1, close 2), min_area 300px, max_distance 1.5m, min_fill 0.5
[perception_node] 처리 39프레임 (7.8 FPS), 검출 35, 발행 안 함 0, depth 없음 29
[center_node] TARGET: ex=-0.242 ey=0.304 area=0.0269
  → 약 35초 동안 186프레임 중 160프레임 검출 (검출률 86.0%, `4.0`~`7.8` FPS)

### 2.3. B 설정 수신 기록
- **Raw Image Debug:** ![B_debug](images/exp1_B_debug.png)  
  `/target/debug_image` (640x480) 바운더리 포함 수신 화면
- **Raw Image:** ![B_raw](images/exp1_B_raw.png)  
  `/camera/camera/color/image_raw` (640x480, RGB8, 30 FPS) 수신 화면
- **Mask:** ![B_mask](images/exp1_B_mask.png)  
  `/target/debug_mask`: S 하한을 190에서 20으로 낮추자 채도가 낮은 목표물 가장자리와 그림자까지 흰색으로 남음. 화면 상단 중앙의 배경 일부도 흰색으로 남음
- **검출 결과:** `/target` (PointStamped) 수신값  
  x(ex) = -0.247, y(ey) = 0.290, z(area) = 0.0443  
  → 위치는 A와 비슷하지만 area가 0.027에서 `0.041`~`0.047`로 약 1.6배 커짐. 위치 흔들림도 커짐 (ex `-0.206`~`-0.271`, ey `0.275`~`0.350`)  
  → 오검출 발생: 목표물이 아닌 화면 상단 영역이 잡힘 (ex ≈ -0.02, ey ≈ -0.52, area ≈ 0.0015)
- **검출 로그:**
[perception_node] 검출 설정: HSV `[98,20,25]`~`[118,255,230]`, blur 5, morph 5 (open 1, close 2), min_area 300px, max_distance 1.5m, min_fill 0.5
[perception_node] 처리 38프레임 (7.6 FPS), 검출 31, 발행 안 함 0, depth 없음 33
[center_node] TARGET: ex=-0.247 ey=0.290 area=0.0443
[center_node] TARGET: ex=-0.021 ey=-0.538 area=0.0018   ← 오검출
  → 약 40초 동안 199프레임 중 168프레임 검출 (검출률 84.4%, `3.2`~`7.6` FPS)


## 3. 실험 2 — 후보 영역 필터 설정 변경


### 3.1. 적용 설정값

| 항목 | A 설정 | B 설정 |
|---|---|---|
| 변경 파라미터 | `min_fill_ratio` (채움률 = 컨투어 면적 / 회전 bbox 면적) | `min_fill_ratio` (채움률 = 컨투어 면적 / 회전 bbox 면적) |
| 적용값 | 0.5 | 0.95 |

> 공통 고정값: HSV `[98,190,25]`~`[118,255,230]`, blur 5, morph 5 (open 1, close 2), min_area 300px, bbox rotated, resize 원본, max_distance 1.5m

### 3.2. A 설정 수신 기록

### 3.2. A 설정 수신 기록
- **Raw Image Debug:** ![A_debug](images/exp1_A_debug.png)  
  `/target/debug_image` (640x480): 목표물에 바운더리가 표시됨
- **Raw Image:** ![A_raw](images/exp1_A_raw.png)  
  `/camera/camera/color/image_raw` (640x480, RGB8, 30 FPS) 수신 화면
- **Mask:** ![A_mask](images/exp1_A_mask.png)  
  `/target/debug_mask`: 파란색 목표물 영역이 흰색으로 분리됨
- **검출 결과:** `/target` (PointStamped) 수신값  
  x(ex) = -0.242, y(ey) = 0.304, z(area) = 0.0269  
  → 목표물 컨투어의 채움률이 0.5 이상이라 필터를 통과해 정상 검출됨
- **검출 로그:**
[perception_node] 검출 설정: HSV [98,190,25]~[118,255,230], blur 5, morph 5 (open 1, close 2), min_area 300px, max_distance 1.5m, min_fill 0.5
[perception_node] 처리 39프레임 (7.8 FPS), 검출 35, 발행 안 함 0, depth 없음 29
[center_node] TARGET: ex=-0.242 ey=0.304 area=0.0269
  → 약 35초 동안 186프레임 중 160프레임 검출 (검출률 86.0%, 4.0~7.8 FPS)

### 3.3. B 설정 수신 기록
- **Raw Image Debug:** ![B_debug](images/exp2_B_debug.png)  
  `/target/debug_image` (640x480): 바운더리가 표시되지 않음
- **Raw Image:** ![B_raw](images/exp2_B_raw.png)  
  `/camera/camera/color/image_raw` (640x480, RGB8, 30 FPS) 수신 화면 (A와 같은 장면)
- **Mask:** ![B_mask](images/exp2_B_mask.png)  
  `/target/debug_mask`: HSV가 같아서 A와 동일하게 목표물 영역이 흰색으로 남음
- **검출 결과:** `/target` (PointStamped) 수신값  
  x(ex) = 0.000, y(ey) = 0.000, z(area) = 0.0000  
  → Mask에는 목표물이 남아 있지만, 채움률이 0.95보다 낮아 후보에서 탈락함
- **검출 로그:**
[perception_node] 검출 설정: HSV [98,190,25]~[118,255,230], blur 5, morph 5 (open 1, close 2), min_area 300px, max_distance 1.5m, min_fill 0.95
[perception_node] 처리 15프레임 (3.0 FPS), 검출 0, 발행 안 함 0, depth 없음 8
[center_node] TARGET: ex=0.000 ey=0.000 area=0.0000
  → 약 15초 동안 36프레임 중 0프레임 검출 (검출률 0%, 1.6~3.0 FPS)

## 4. 목표물이 없는 입력 테스트

### 4.1. 테스트 조건

- **입력 장면:** 실험 1·2와 같은 카메라 위치와 배경에서 파란색 목표물만 치운 장면
- **적용한 설정값:** 기본 설정 (실험 1·2의 A와 동일)  
  HSV [98,190,25]~[118,255,230], blur 5, morph 5 (open 1, close 2), min_area 300px, bbox rotated, resize 원본, max_distance 1.5m, min_fill 0.5

### 4.2. 수신 기록

- **Raw Image Debug:** ![none_debug](images/exp3_none_debug.png)  
  `/target/debug_image` (640x480): 바운더리가 표시되지 않음
- **Raw Image:** ![none_raw](images/exp3_none_raw.png)  
  `/camera/camera/color/image_raw` (640x480, RGB8, 30 FPS) 수신 화면 (목표물 없음)
- **Mask:** ![none_mask](images/exp3_none_mask.png)  
  `/target/debug_mask`: HSV 범위에 해당하는 영역이 없어 전체가 검은색
- **검출 결과:** `/target` (PointStamped) 수신값  
  x(ex) = 0.000, y(ey) = 0.000, z(area) = 0.0000  
  → 측정 동안 0이 아닌 값은 한 번도 수신되지 않음
- **검출 로그:**
[perception_node] 검출 설정: HSV [98,190,25]~[118,255,230], blur 5, morph 5 (open 1, close 2), min_area 300px, max_distance 1.5m, min_fill 0.5
[perception_node] 처리 30프레임 (6.0 FPS), 검출 0, 발행 안 함 0, depth 없음 25
[center_node] TARGET: ex=0.000 ey=0.000 area=0.0000
  → 약 25초 동안 112프레임 중 0프레임 검출 (검출률 0%, 3.6~6.0 FPS)
- **목표물 미검출 확인 결과:** 목표물이 없는 장면에서는 검출 0건, `/target` = (0, 0, 0)이 계속 수신됨. center_node도 TRACKING으로 넘어가지 않고 `IDLE` 상태를 유지함. 배경을 목표물로 잘못 잡는 오검출은 없었음
