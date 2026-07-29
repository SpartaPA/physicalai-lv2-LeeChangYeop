# 문제 1. 목표 입력과 응답 확인

## 1) 라즈베리 파이 환경

### OS

```bash
pa3@pa3rpi:~/git/lv2_module1$ cat /etc/os-release
PRETTY_NAME="Ubuntu 22.04.5 LTS"
NAME="Ubuntu"
VERSION_ID="22.04"
VERSION="22.04.5 LTS (Jammy Jellyfish)"
VERSION_CODENAME=jammy
ID=ubuntu
ID_LIKE=debian
HOME_URL="https://www.ubuntu.com/"
SUPPORT_URL="https://help.ubuntu.com/"
BUG_REPORT_URL="https://bugs.launchpad.net/ubuntu/"
PRIVACY_POLICY_URL="https://www.ubuntu.com/legal/terms-and-policies/privacy-policy"
UBUNTU_CODENAME=jammy

pa3@pa3rpi:~/git/lv2_module1$ uname -m
aarch64
```

### SSH 접속 확인
```bash
pa3@pa3rpi:~/git/lv2_module1$ echo $SSH_CONNECTION
10.2.16.227 33508 10.2.16.196 22
```

### OpenCR USB 인식 확인
```bash
pa3@pa3rpi:~/git/lv2_module1$ lsusb
Bus 003 Device 001: ID 1d6b:0002 Linux Foundation 2.0 root hub
Bus 002 Device 001: ID 1d6b:0003 Linux Foundation 3.0 root hub
Bus 001 Device 016: ID 0483:5740 STMicroelectronics Virtual COM Port
Bus 001 Device 002: ID 2109:3431 VIA Labs, Inc. Hub
Bus 001 Device 001: ID 1d6b:0002 Linux Foundation 2.0 root hub

pa3@pa3rpi:~/git/lv2_module1$ ls /dev/ttyACM*
/dev/ttyACM0
```
### 포트 권한 확인
```bash
pa3@pa3rpi:~/git/lv2_module1$ ls -l /dev/ttyACM0
crw-rw---- 1 root dialout 166, 0 Sep 22 12:58 /dev/ttyACM0
```

## 2) 업로드 확인
```bash
pa3@pa3rpi:~$ "$UPLOADER" "$PORT" 115200 \
  "$BASE/output/opencr_position_p.ino.bin" 1 \
  2>&1 | tee "$BASE/upload.log"
opencr_ld ver 1.0.4
opencr_ld_main 
>>
file name : /home/pa3/pa-opencr-build/output/opencr_position_p.ino.bin 
file size : 102 KB
Open port OK
Clear Buffer Start
Clear Buffer End
Board Name : OpenCR R1.0
Board Ver  : 0x17020800
Board Rev  : 0x00000000
>>
flash_erase : 0 : 0.990000 sec
flash_write : 0 : 1.175000 sec 
CRC OK 9BEFA0 9BEFA0 0.004000 sec
[OK] Download 
jump_to_fw 
jump finished

pa3@pa3rpi:~$ grep -E "CRC OK|\[OK\] Download" "$BASE/upload.log"
CRC OK 9BEFA0 9BEFA0 0.004000 sec
[OK] Download 
```

---

## 3) 설정

```bash
pa3@pa3rpi:~$ PORT=/dev/ttyACM0
udevadm info --query=property --name="$PORT"
test -r "$PORT" && test -w "$PORT" && echo 'Port access OK'
DEVPATH=/devices/platform/scb/fd500000.pcie/pci0000:00/0000:00:00.0/0000:01:00.0/usb1/1-1/1-1.3/1-1.3:1.0/tty/ttyACM0
DEVNAME=/dev/ttyACM0
MAJOR=166
MINOR=0
SUBSYSTEM=tty
USEC_INITIALIZED=14136285793
ID_BUS=usb
ID_VENDOR_ID=0483
ID_MODEL_ID=5740
ID_PCI_CLASS_FROM_DATABASE=Serial bus controller
ID_PCI_SUBCLASS_FROM_DATABASE=USB controller
ID_PCI_INTERFACE_FROM_DATABASE=XHCI
ID_VENDOR_FROM_DATABASE=STMicroelectronics
ID_MODEL_FROM_DATABASE=Virtual COM Port
ID_VENDOR=ROBOTIS
ID_VENDOR_ENC=ROBOTIS
ID_MODEL=OpenCR_Virtual_ComPort_in_FS_Mode
ID_MODEL_ENC=OpenCR\x20Virtual\x20ComPort\x20in\x20FS\x20Mode
ID_REVISION=0200
ID_SERIAL=ROBOTIS_OpenCR_Virtual_ComPort_in_FS_Mode_FFFFFFFEFFFF
ID_SERIAL_SHORT=FFFFFFFEFFFF
ID_TYPE=generic
ID_USB_INTERFACES=:020201:0a0000:
ID_USB_INTERFACE_NUM=00
ID_USB_DRIVER=cdc_acm
ID_USB_CLASS_FROM_DATABASE=Communications
ID_PATH=platform-fd500000.pcie-pci-0000:01:00.0-usb-0:1.3:1.0
ID_PATH_TAG=platform-fd500000_pcie-pci-0000_01_00_0-usb-0_1_3_1_0
ID_MM_CANDIDATE=1
DEVLINKS=/dev/serial/by-id/usb-ROBOTIS_OpenCR_Virtual_ComPort_in_FS_Mode_FFFFFFFEFFFF-if00 /dev/serial/by-path/platform-fd500000.pcie-pci-0000:01:00.0-usb-0:1.3:1.0
TAGS=:systemd:
CURRENT_TAGS=:systemd:
Port access OK

pa3@pa3rpi:~$ UPLOADER="$BASE/uploader-src/arduino/opencr_develop/opencr_ld/opencr_ld"
set -o pipefail
"$UPLOADER" "$PORT" 115200 \
  "$BASE/output/opencr_position_p.ino.bin" 1 \
  2>&1 | tee "$BASE/upload.log"
opencr_ld ver 1.0.4
opencr_ld_main 
>>
file name : /home/pa3/pa-opencr-build/output/opencr_position_p.ino.bin 
file size : 102 KB
Open port OK
Clear Buffer Start
Clear Buffer End
Board Name : OpenCR R1.0
Board Ver  : 0x17020800
Board Rev  : 0x00000000
>>
flash_erase : 0 : 0.992000 sec
flash_write : 0 : 1.173000 sec 
CRC OK 9BEFA0 9BEFA0 0.004000 sec
[OK] Download 
jump_to_fw 
jump finished

pa3@pa3rpi:~$  ls -l /dev/ttyACM*
PORT=/dev/ttyACM0
python3 -m serial.tools.miniterm "$PORT" 115200 --eol LF -e
crw-rw---- 1 root dialout 166, 0 Sep 22 14:39 /dev/ttyACM0
--- Miniterm on /dev/ttyACM0  115200,8,N,1 ---
--- Quit: Ctrl+] | Menu: Ctrl+T | Help: Ctrl+T followed by Ctrl+H ---
Invalid setting. Finite Kp, positive speed or max, angle -90..90. Use k/v/a or s <Kp> <speed_deg_s|max> <angle_deg>.

```
## 4) 관찰 설명
목표각을 +10 deg로 변경하자 모터가 정방향으로 회전하였다.

목표각이 변경된 시점인 t=2.0s에서 현재각은 0.000 deg이므로 초기 오차는 10.000 deg였다.

이후 현재각이 1.230 deg, 3.604 deg, 6.152 deg, 8.613 deg 등으로 증가하면서 목표각에 점차 접근하였고, 이에 따라 위치 오차도 감소하였다.

제어출력인 u_deg_s 역시 초기에는 9.618 deg/s 수준이었으나 현재각이 목표각에 가까워질수록 감소하였다.

실험 후반에는 현재 위치가 약 9.49~9.58 deg 부근에서 유지되었으며, 목표각 10 deg​에 근접한 상태로 안정화되었다.

실험은 약 60초 후 STOP: time limit reached; torque off 메시지와 함께 자동 정지하였다.


# 문제 2. 목표 입력과 응답 확인

## 1) 오차 계산 표

| 시점 (t_s) | 목표각 (deg) | 현재각 (deg) | 오차 = 목표각 - 현재각 (deg) |
| --- | --- | --- | --- |
| 2.0 | 10.000 | 0.000 | 10.000 |
| 2.1 | 10.000 | 0.176 | 9.824 |
| 4.0 | 10.000 | 8.613 | 1.387 |

## 2) 오차 변화 설명
- 초기 시점인 t=2.0s에 목표각이 **10°**로 변경되었지만 현재각은 **0°**이므로 오차는 **10°**이다.
- t=2.1s에는 현재각이 **0.176°**까지 증가하여 오차가 **9.824°**로 감소하였다.
- t=4.0s에는 현재각이 **8.613°**까지 도달하여 오차가 **1.387°**로 더욱 감소하였다.
- 따라서 목표각이 10°로 변경된 이후 현재각이 목표각에 가까워지면서 오차의 크기가 감소하는 것을 확인할 수 있다.

또한 실험 후반에는 현재각이 약 9.49~9.58° 부근에서 유지되어 목표각 10°에 근접하였다.

## 3) 위치 측정 센서 및 통신 경로
위치 측정값은 Dynamixel XM430-W350 내부 엔코더에서 얻는다.
엔코더로 측정된 위치는 Dynamixel 내부 제어기를 거쳐 Protocol 2.0 통신으로 OpenCR에 전달된다.

## 4) 목표가 +30°이고 현재각이 +35°인 경우의 오차 부호와 양의 Kp에 의한 보정 방향
오차 = 목표각 - 현재각 이므로 -5°임에 따라,
오차의 부호는 음수(-)이며, 현재각을 목표각 방향으로 줄이기 위해서 음의 방향(반대 방향)으로 회전하는 보정 명령을 받는다.

# 문제 3. P 게인 변경에 따른 응답 비교

 ## 1) A·B 설정 비교
| 항목    | 실행 A        | 실행 B        |
| ----- | ----------- | ----------- |
| 실행 명령 | `s 1 30 10` | `s 6 30 10` |
| Kp    | 1.0         | 6.0         |
| Ki    | 0           | 0           |
| Kd    | 0           | 0           |
| 목표각   | 10°         | 10°         |
| 속도 상한 | 30 deg/s    | 30 deg/s    |
| 시작 위치 | 0°          | 0°          |
| 측정 주기 | 약 10 ms     | 약 10 ms     |


## 2) 같은 경과 시간의 현재각 비교

| 경과 시간 | A 현재각 (Kp=1) | B 현재각 (Kp=6) | A 목표 초과 여부 | B 목표 초과 여부 |
| ----- | -----------: | -----------: | ---------- | ---------- |
| 2.0 s |       0.000° |       0.000° | 없음         | 없음         |
| 2.1 s |       0.176° |       0.264° | 없음         | 없음         |
| 2.5 s |       3.604° |       9.141° | 없음         | 없음         |
| 2.6 s |       4.219° |      10.459° | 없음         | 초과         |
| 2.7 s |       4.746° |      10.635° | 없음         | 초과         |
| 3.0 s |       6.152° |      10.195° | 없음         | 초과         |
| 4.0 s |       8.613° |      10.195° | 없음         | 초과         |
| 5.0 s |       9.316° |      10.195° | 없음         | 초과         |


## 3) 응답 비교 및 해석

A에서는 목표각이 10°로 변경된 후 현재각이 3.604°(2.5 s), 6.152°(3.0 s), 8.613°(4.0 s), 9.316°(5.0 s)로 점진적으로 증가하여 목표각에 접근하였다. 실험 중 10°를 초과하지 않았다.

B에서는 목표각이 10°로 변경된 후 현재각이 9.141°(2.5 s)까지 빠르게 증가하였고, 2.6 s에서 10.459°로 목표각을 초과하였다. 이후 2.7 s에서 10.635°까지 증가하여 목표각을 0.635° 초과하는 오버슈트가 발생하였다.

B의 오버슈트는

[
10.635-10.000=0.635^\circ
]

이며, 목표각 기준 오버슈트율은 약 6.35%이다.

Kp를 1.0에서 6.0으로 증가시키자 동일한 10°의 위치 오차에 대한 P 제어 출력이 증가하였다. 실제 로그에서도 A는 목표 변경 순간 p_deg_s=10.000, u_deg_s=9.618인 반면 B는 p_deg_s=60.000, u_deg_s=28.854로 나타났다. 그 결과 B가 A보다 더 빠르게 목표각에 접근하였고, 목표각을 초과하는 응답이 나타났다.

## 4) 변경한 게인의 위치

이번 실험에서 변경한 Kp는 Dynamixel 내부 Position PID 게인이 아니라 OpenCR에서 구현한 위치 제어 Kp이다.

OpenCR에서 목표각과 현재 엔코더 위치의 오차를 계산하여 P 제어를 수행하고, 그 결과를 Dynamixel의 Goal Velocity 명령으로 전달한다. 제공 코드에서도 OpenCR의 위치 제어 게인을 사용하며 Dynamixel 내부 위치 PID는 사용하지 않는 구조이다.

## 5) I항과 D항의 역할

I항: 위치 오차를 시간에 따라 누적하여 지속적으로 남는 정상상태 오차를 보정하는 역할을 하지만, 이번 실험에서는 Ki=0이므로 동작하지 않는다.

D항: 측정 속도 또는 오차 변화에 반응하여 급격한 움직임을 억제하는 역할을 하지만, 이번 실험에서는 Kd=0이므로 동작하지 않는다.



# 문제 4. 제어와 통신의 역할 해석

## 1) 제어·통신 구조와 데이터 흐름

PC의 ROS2 노드는 `/motor/target` 토픽으로 목표각을 발행한다. 이 목표값은 micro-ROS Agent를 통해 OpenCR로 전달된다. OpenCR은 전달받은 목표각과 다이나믹셀에서 측정한 현재각을 이용하여 제어 계산을 수행하고, 제어 명령을 다이나믹셀에 전달한다.

다이나믹셀은 현재 위치 등의 상태값을 OpenCR에 전달하고, OpenCR은 측정한 현재각을 `/motor/state`로 발행한다. 이 상태값은 micro-ROS Agent를 거쳐 PC의 ROS2 노드가 수신한다.

즉, 전체 흐름은 다음과 같다.

`PC(ROS2 노드) → /motor/target → micro-ROS Agent → OpenCR → 다이나믹셀`

`다이나믹셀 → OpenCR → /motor/state → micro-ROS Agent → PC(ROS2 노드)`

목표값은 **PC가 보내고 OpenCR이 받으며**, 상태값은 **OpenCR이 보내고 PC가 받는다.**

## 2) 주어진 기록 해석

가상 기록에서는 0.0초에 PC가 `/motor/target`으로 목표각 30.0°를 발행하였다.

이후 PC는 `/motor/state`를 통해 다음과 같이 현재각을 수신한다.

| 시간    | 기록                                |
| ----- | --------------------------------- |
| 0.0 s | PC가 `/motor/target`에 목표각 30.0° 발행 |
| 0.1 s | PC가 `/motor/state`에서 현재각 2.0° 수신  |
| 0.5 s | PC가 `/motor/state`에서 현재각 12.0° 수신 |
| 1.0 s | PC가 `/motor/state`에서 현재각 22.0° 수신 |
| 2.0 s | PC가 `/motor/state`에서 현재각 29.0° 수신 |

따라서 현재각은 2.0° → 12.0° → 22.0° → 29.0°로 증가하며 목표각 30.0°에 점점 가까워지는 것으로 해석할 수 있다.

## 3) 제어 계산 주기

제어 계산은 **10 ms마다 1회**, 상태 발행은 **100 ms마다 1회** 수행된다.

따라서 상태 발행 1주기 동안 수행되는 제어 계산 횟수는

**100 ms ÷ 10 ms = 10회**

이다.

즉, 상태값을 한 번 발행하는 100 ms 동안 OpenCR은 제어 계산을 **10번 수행한다.**

## 4) 통신 단절 시 오래된 명령 처리 정책

통신이 단절되면 마지막으로 수신한 목표각을 무한정 계속 사용하는 방식은 사용하지 않는다. 일정 시간 동안 새로운 목표값이 들어오지 않으면 해당 목표값을 오래된 명령으로 판단하고 모터를 안전하게 정지시키는 정책을 적용한다.

예를 들어 **500 ms 동안 새로운 목표값이 수신되지 않으면 통신 단절로 판단하여 모터를 정지하거나 감속 정지**하도록 할 수 있다.

이 정책을 사용하는 이유는 통신이 끊긴 상태에서 오래된 목표각을 계속 실행하면 사용자가 더 이상 의도하지 않은 동작이 지속될 수 있기 때문이다. 따라서 일정 시간 이상 새로운 명령이 없으면 제어를 중단하여 예상하지 못한 움직임을 방지하는 것이 안전하다.

## 5) 정리

이 구조에서 **PC ROS2 노드는 목표값을 전달하고 상태값을 수신하는 역할**, **micro-ROS Agent는 PC와 OpenCR 사이의 ROS2 통신을 중계하는 역할**, **OpenCR은 목표각과 현재각을 이용해 주기적으로 제어 계산을 수행하는 역할**, **다이나믹셀은 실제 모터 동작과 현재 위치 측정을 담당하는 역할**을 한다.

제어 계산 주기는 10 ms이고 상태 발행 주기는 100 ms이므로, 상태 발행 1회 동안 제어 계산은 **10회** 수행된다.

