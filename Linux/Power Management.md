# 전력 사용량 줄이기
대부분의 리눅스 배포판은 세부적인 절전 설정을 사용자가 따로 해주어야 한다.  DELL 7275 태블릿에 다양한 리눅스 배포판을 설치해보고 절전 설정과 씨름하면서 알게된 그나마 괜찮은 것 같은 절전 설정 방법을 기록한다.
### 절전 설정으로 얻게되는 효과
DELL 7275의 슬립모드 기준으로 `설정 전 2.4W` -> `설정 후 0.9W`으로 절감된다.
## 1. `powertop`과 `tlp`를 이용한 방법
조금은 귀찮은 방법이지만 세밀하게 자신에게 맞는 설정을 찾아낼 수 있고, 문제가 발생해도 바로바로 수정할 수 있다.
### 설정 절차
#### 1. `powertop`과 `tlp`, `tlpui`를 설치한다.
```bash
$ sudo add-apt-repository ppa:linuxuprising/apps # tlpui를 설치하기 위한 저장소 추가
$ sudo apt-get update
$ sudo apt-get install powertop tlp tlpui
```
#### 2. powertop으로 `Tunables` 목록 확인
* 터미널로 확인하는 방법

  **powertop 실행**
  ```bash
  $ sudo powertop
  PowerTOP v2.9     Overview   Idle stats   Frequency stats   Device stats   Tunables

  The battery reports a discharge rate of 8.30 mW
  The power consumed was 164 mJ
  The estimated remaining time is 3008 hours, 59 minutes

  ...

  <ESC> Exit | <TAB> / <Shift + TAB> Navigate |
  ```
  **`Shift + TAB`을 눌러 Tunables 탭으로 이동하여 `Bad` 항목 확인**
  ```bash
  PowerTOP v2.9     Overview   Idle stats   Frequency stats   Device stats   Tunables

  >> Bad           Autosuspend for USB device CORSAIR K63 Wireless Mechanical Gaming Keyboard [CORSAIR]
     Bad           Autosuspend for USB device USB PnP Sound Device [C-Media Electronics Inc.      ]
     Bad           Autosuspend for USB device USB Receiver [Logitech]
     Bad           Wake-on-lan status for device enx00e04c68d597
     Good          NMI watchdog should be turned off
  ...
  ```
* 웹으로 확인하는 방법

  **powertop 결과를 html 파일로 내보내기**
  ```bash
  $ cd
  $ sudo powertop --html
  ```
  **파일 관리자를 열어 홈으로 이동해서 powertop.html 파일 실행**
  ![powertop html](../assets/img/powertop_html_1.png)
  **`Tuning` 탭으로 이동하여 `Software Settings in Need of Tuning`의 항목 확인**
  ![powertop html](../assets/img/powertop_html_2.png)
#### 3. `tlpui`로 절전 설정 번경
1. `tlpui` 실행
```
$ sudo tlpui
```
2. `PCIe` > `RUNTIME_PM` > `RUNTIME_PM_ON_AC` 활성화 상태로 변경
![RUNTIME_PM_ON_AC 활성화](../assets/img/tlpui_1.png)
3. 상단 메뉴에서 File > Save 버튼을 눌러 변경 사항 저장
#### 4. `powertop`의 `Tuntables` 목록이 어떻게 바뀌었는지 확인해보자
#### 5. 이렇게 tlp로 하나 바꾸고 powertop으로 확인해보면서 맞는 설정을 찾아가보자.
**참고: `Tuntables`의 모든 항목을 `Good`으로 만들 필요 없다.**
### **추천하는 옵션값(특히 DELL 7275 / 9250 사용자일 경우)**
`DEFAULT` 설정 상태에서 아래의 추천 옵션들을 설정해보자. DELL 7275의 슬립모드 기준으로 `설정 전 2.4W` -> `설정 후 0.9W`으로 절감된다.
*  `Audio` > `SOUND_POWER_SAVE_ON_AC` 활성화

    시스템에서 아무 소리도 재생되지 않을 때에도 스피커가 활성화되어 있어서 전력(7275 기준 500mW)을 계속 잡아먹고 있는다. 이 옵션을 활성화하면 소리가 재생되지 않을 때에는 스피커를 꺼서 전기를 절약한다.
*  `Disks` > `SATA_LINKPWR_ON_AC`에서 `min_power`와 `max_performance`를 활성화한다.

    Disk를 사용중일 때에는 `max_performance`로 동작하고 슬립모드에서는 `min_power`로 동작한다. 이 옵션을 활성화하면 슬립모드의 전기 소모량이 줄어든다. `DELL 7275는 사실상 이 옵션만 설정해도 슬립모드 시 전력 소모량이 0.9W로 줄어든다.`
  * `PCIe` > `PCIE_ASPM_ON_AC`를 default로 설정
  * `PCIe` > `RUNTIME_PM_ON_AC` 활성화
  * `Processor` > `SCHED_POWERSAVE_ON_AC` 활성화

    CPU 부하량을 감당할 수 있는 최소의 쓰레드만 사용하여 전력소모를 줄이는 옵션이다. 경우의 따라 지연이 발생하는 등의 성능 하강을 체감할 수 있으니 기호에 맞게 설정하자.
  * `Processor` > `ENERGY_PERF_POLICY_ON_AC`를 `balance-performance`로 설정

    OS 레벨의 CPU 제어 규칙을 설정하는 옵션
  * `USB` > `USB_BLACKLIST_BTUSB` 활성화 혹은 `USB_BLACKLIST`에서 `(btusb)`로 끝나는 항목 활성화

    DELL 7275에서 블루투스를 블랙리스트에 등록하지 않으면 슬립모드에서 깨어났을 때 블루투스가 먹통이 되는 문제가 있다. 만약 7275에서 블루투스가 먹통이 되었는데 시스템을 재시작해도 블루투스가 살아나지 않는 경우, 바이오스에 한번 진입한 뒤 다시 부팅해보면 살아난다.
  * (참고) `Processor`의 `CPU_SCALING_GOVERNOR`와 `CPU_HWP`의 차이점

    두 옵션은 CPU의 연산량에 따라 CPU의 전력을 조절하는 옵션이다. `CPU_HWP`는 p-state 드라이버를 통해 동작 상태를 제어한다. 인텔 2세대 CPU(샌디브릿지) 이상에서만 적용되는 옵션이니 그 이전 CPU를 사용중이라면 `CPU_SCALING_GOVERNOR`를 설정하면 된다. `sudo tlp stat -p` 명령어를 통해 CPU의 `scaling_driver`를 확인할 수 있다.
## 2. `powertop --auto-tune`을 이용한 방법
간단한 방법이지만 부작용이 많다. 예를 들어 마우스가 버벅인다던가.. 블루투스가 먹통이 된다던가.. 간혹가다 프리징이 걸려서 강종해야 한다던가..
### 설정 절차
#### 1. `powertop`을 설치한다.
```bash
$ sudo apt-get update
$ sudo apt-get install powertop
```
#### 2. (선택) powertop의 절전 제어 오차를 줄이기 위해 캘리브레이션을 수행한다.
```bash
$ sudo powertop --calibrate
```
#### 3. powertop이 자동으로 절전 문제를 해결하도록 한다.
```bash
$ powertop --auto-tune
```
#### 4. (선택) powertop을 서비스로 등록

`powertop --auto-tune` 명령어는 시스템을 재시작하면 효력이 사라진다. 원한다면 서비스를 등록하여 시스템이 시작될 때 `powertop --auto-tune` 명령을 수행하도록 설정할 수 있다.
```bash
# 서비스 파일 생성
$ cat << EOF | sudo tee /etc/systemd/system/powertop.service
[Unit]
Description=PowerTOP auto tune

[Service]
Type=idle
Environment="TERM=dumb"
ExecStart=/usr/sbin/powertop --auto-tune

[Install]
WantedBy=multi-user.target
EOF

# 생성한 서비스 사용 등록
$ systemctl enable powertop.service

# 서비스 시작
$ systemctl start powertop.service
```
# gnome Suspend Timeout 15분 미만으로 설정
그놈 데스크탑 환경에서 `자동 대기 모드 지연 시간`을 설정하기 위해서 설정화면으로 들어가면 아래처럼 최소 15분으로 밖에 설정을 못한다. 요걸 Command Line을 통해 15분 미만으로 설정하는 방법을 기록한다.
![그놈 쉐이키](../assets/img/그놈절전_1.png)
## 설정 방법
### 1. `gsettings`를 사용해서 설정 절전 시간 설정 값의 Key 찾기
```bash
# 15분으로 설정해놓았다면 900으로 설정되어 있는 Key가 2개 보일 것이다.
$ gsettings list-recursively | grep -i "sleep\|suspend"
org.gnome.desktop.screensaver ubuntu-lock-on-suspend true
org.gnome.settings-daemon.plugins.power sleep-inactive-ac-timeout 900 # 전원 사용시
org.gnome.settings-daemon.plugins.power critical-battery-action 'suspend'
org.gnome.settings-daemon.plugins.power lid-close-suspend-with-external-monitor false
org.gnome.settings-daemon.plugins.power sleep-inactive-ac-type 'suspend'
org.gnome.settings-daemon.plugins.power button-suspend 'suspend'
org.gnome.settings-daemon.plugins.power button-sleep 'suspend'
org.gnome.settings-daemon.plugins.power sleep-inactive-battery-timeout 900 # 배터리 사용시
org.gnome.settings-daemon.plugins.power lid-close-ac-action 'suspend'
org.gnome.settings-daemon.plugins.power sleep-inactive-battery-type 'suspend'
org.gnome.settings-daemon.plugins.power lid-close-battery-action 'suspend'
```
### 2. 조회된 절전 시간 Key에 원하는 시간(초)를 설정
```bash
# AC와 배터리 모두 5분(300)으로 설정하였다.
$ gsettings set org.gnome.settings-daemon.plugins.power sleep-inactive-ac-timeout 300
$ gsettings set org.gnome.settings-daemon.plugins.power sleep-inactive-battery-timeout 300

# 잘 변경되었는지 확인
$ gsettings list-recursively | grep -i "sleep-inactive"
org.gnome.settings-daemon.plugins.power sleep-inactive-ac-timeout 300
org.gnome.settings-daemon.plugins.power sleep-inactive-ac-type 'suspend'
org.gnome.settings-daemon.plugins.power sleep-inactive-battery-timeout 300
org.gnome.settings-daemon.plugins.power sleep-inactive-battery-type 'suspend'
```