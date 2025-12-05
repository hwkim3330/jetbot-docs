# JetBot ROS2 Documentation

<p align="center">
  <img src="https://raw.githubusercontent.com/hwkim3330/roii/refs/heads/main/keti.png" alt="KETI Logo" height="80">
</p>

<p align="center">
  <strong>Jetson Orin Nano JetBot ROS2 제작 가이드</strong><br>
  차세대 엣지 AI 로보틱스 플랫폼 구축을 위한 포괄적 기술 문서
</p>

<p align="center">
  <a href="https://hwkim3330.github.io/jetbot-docs/">📖 Documentation</a> •
  <a href="#features">Features</a> •
  <a href="#quick-start">Quick Start</a> •
  <a href="#bom">BOM</a>
</p>

---

## Overview

이 프로젝트는 **NVIDIA Jetson Orin Nano**를 기반으로 한 자율주행 로봇(JetBot)을 **ROS 2 Humble** 환경에서 구축하기 위한 기술 문서입니다.

기존 Jetson Nano 기반 JetBot에서 Orin Nano로의 마이그레이션 시 직면하는 하드웨어/소프트웨어 이슈와 해결책을 상세히 다룹니다.

### Why Orin Nano?

| 항목 | Jetson Nano | Jetson Orin Nano | 향상 |
|-----|-------------|------------------|------|
| AI 성능 | 0.5 TOPS | 40 TOPS | **80x** |
| GPU | 128-core Maxwell | 1024-core Ampere | 8x |
| CPU | 4-core A57 | 6-core A78AE | 1.5x |
| 메모리 | 4GB LPDDR4 | 8GB LPDDR5 | 2x |
| ROS 지원 | Melodic (ROS1) | **Humble (ROS2)** | Native |

## Features

### 📖 문서 구성

| 페이지 | 내용 |
|--------|------|
| [Hardware](https://hwkim3330.github.io/jetbot-docs/hardware.html) | 전원 설계, GPIO, I2C, 카메라 인터페이스, BOM |
| [Software](https://hwkim3330.github.io/jetbot-docs/software.html) | JetPack 6 설치, ROS 2 Humble 환경 구축 |
| [ROS2](https://hwkim3330.github.io/jetbot-docs/ros2.html) | 모터 드라이버, 센서 통합, SLAM, Nav2 |
| [ROS 심화](https://hwkim3330.github.io/jetbot-docs/ros-advanced.html) | DDS, QoS, Lifecycle, Executors, 콜백 그룹 |
| [시각화 도구](https://hwkim3330.github.io/jetbot-docs/visualization.html) | RViz 2, RQt, Gazebo 시뮬레이션 |
| [SLAM](https://hwkim3330.github.io/jetbot-docs/slam.html) | SLAM Toolbox, Cartographer, 지도 관리 |
| [Navigation](https://hwkim3330.github.io/jetbot-docs/navigation.html) | Nav2, Costmap, Path Planning, Controllers |
| [Reference](https://hwkim3330.github.io/jetbot-docs/reference.html) | 트러블슈팅, 핀맵, 명령어 레퍼런스 |
| [Web Tools](https://hwkim3330.github.io/jetbot-docs/web-tools.html) | 웹 기반 로봇 제어 및 모니터링 |

### 주요 내용

- **하드웨어 설계**
  - 3.3V GPIO 전압 레벨 주의사항 및 레벨 시프터 사용법
  - I2C 버스 7번 매핑 (Orin Nano 전용)
  - 22핀 CSI 카메라 어댑터 필요성
  - 전원 분배 네트워크(PDN) 및 브라운아웃 방지

- **소프트웨어 환경**
  - JetPack 6.x (Ubuntu 22.04) 설치
  - ROS 2 Humble 바이너리 설치
  - Isaac ROS GPU 가속

- **ROS 2 구현**
  - PCA9685 기반 모터 드라이버 노드
  - 디퍼렌셜 드라이브 키네마틱스
  - SLAM Toolbox 맵핑
  - Nav2 자율주행 스택

- **웹 인터페이스**
  - rosbridge_suite 웹소켓 통신
  - ROSboard 실시간 모니터링
  - roslibjs/ros3djs 시각화

## Quick Start

### 1. 부품 준비 (~$620-650)

```
- Jetson Orin Nano 8GB Dev Kit ($499)
- TT Motor x2 ($5.90 x2)
- Motor Driver - Adafruit DRV8833 ($19.95)
- IMX219 Camera 175° FoV ($19.99)
- PD Battery 10,000mAh ($12.99)
- 3D Printed Chassis (STL 제공)
```

### 2. JetPack 6 설치

```bash
# SDK Manager로 플래싱 (호스트 PC에서)
sdkmanager

# NVMe SSD 부팅 권장
```

### 3. ROS 2 Humble 설치

```bash
# ROS 2 소스 추가
sudo curl -sSL https://raw.githubusercontent.com/ros/rosdistro/master/ros.key \
    -o /usr/share/keyrings/ros-archive-keyring.gpg

echo "deb [arch=$(dpkg --print-architecture) \
    signed-by=/usr/share/keyrings/ros-archive-keyring.gpg] \
    http://packages.ros.org/ros2/ubuntu $(. /etc/os-release && echo $UBUNTU_CODENAME) main" | \
    sudo tee /etc/apt/sources.list.d/ros2.list > /dev/null

# 설치
sudo apt update
sudo apt install ros-humble-desktop
```

### 4. I2C 확인 (Orin Nano)

```bash
# 버스 7번에서 PCA9685 감지 확인
sudo i2cdetect -y -r 7

# 0x40 주소가 보이면 성공
```

## BOM (Bill of Materials)

[JetBot.org 공식 BOM](https://jetbot.org/master/bill_of_materials_orin.html) 참고

| 카테고리 | 부품 | 가격 |
|---------|------|------|
| **Core** | Jetson Orin Nano 8GB | $499.00 |
| | TT Motor (x2) | $11.80 |
| | Motor Driver | $19.95 |
| | Caster Ball | $10.99 |
| **Power** | PD Battery 10,000mAh | $12.99 |
| | USB-C to DC Cable | $10.99 |
| **Camera** | IMX219 175° FoV | $19.99 |
| **Optional** | RPLIDAR A1 (SLAM용) | ~$99 |
| | | |
| **Total** | | **~$620-650** |

## Project Structure

```
jetbot-docs/
├── index.html          # 홈페이지
├── hardware.html       # 하드웨어 가이드
├── software.html       # 소프트웨어 설치
├── ros2.html           # ROS2 구현
├── ros-advanced.html   # ROS 심화 (핵심 개념)
├── visualization.html  # 시각화 도구 (RViz, RQt, Gazebo)
├── slam.html           # SLAM (Toolbox, Cartographer)
├── navigation.html     # Navigation (Nav2)
├── reference.html      # 참고자료
├── web-tools.html      # 웹 도구
├── css/
│   └── style.css       # KETI 블루 테마
├── js/
└── images/
```

## References

### Official Documentation
- [NVIDIA Jetson Orin Nano](https://developer.nvidia.com/embedded/jetson-orin-nano-developer-kit)
- [ROS 2 Humble](https://docs.ros.org/en/humble/)
- [Nav2 Documentation](https://navigation.ros.org/)
- [JetBot.org](https://jetbot.org/)

### Robot Web Tools
- [rosbridge_suite](https://github.com/RobotWebTools/rosbridge_suite)
- [roslibjs](https://github.com/RobotWebTools/roslibjs)
- [ROSboard](https://github.com/dheera/rosboard)

## License

This documentation is provided for educational purposes.

---

<p align="center">
  <sub>Built with ❤️ by KETI | Powered by NVIDIA Jetson</sub>
</p>
