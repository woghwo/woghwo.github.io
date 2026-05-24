---
layout: page
title: "병원용 자율주행 로봇의 다층 이동을 위한 엘리베이터 승하차 및 경로 제어 시스템"
description: "엘리베이터 승하차 알고리즘 설계 및 CSV 기반 경로 추종 로직 최적화"
img: assets/img/hospital_robot.png
importance: 2
category: work
---

## 프로젝트 개요

<br/>

### 배경 및 목적
- **문제 정의**: 병원 내 의료진의 단순 반복 업무(물품 운반 등) 과부하 및 대면 접촉으로 인한 감염 위험이 존재했다.
- **솔루션**: 층간 이동이 가능한 자율주행 로봇을 도입하여 다층 구역 순찰 및 환자 상태(낙상 등) 실시간 모니터링을 수행한다.
- **핵심 도전 과제**: 엘리베이터 내부의 좁은 공간 및 센서 노이즈 환경에서 안정적인 승하차와 복귀 경로 좌표 정합을 달성하고자 했다.

---

<br/>

## 핵심 기술 구현 (Key Contributions)

<br/>

### 1. Unified Path Node: 상대 좌표 변환 기반의 정밀 경로 추종
단순히 저장된 좌표를 따라가는 것이 아니라, 시작 시점의 환경 변화에 대응하기 위해 2D 회전 변환 행렬을 활용한 좌표 정합 로직을 구현했다.
- **좌표계 보정**: 로봇의 현재 Heading과 CSV 데이터의 초기 Heading 차이($yaw\_offset$)를 계산하여 전체 경로를 실시간 회전 변환했다.
- **수학적 모델**:
$$\begin{bmatrix} x_{goal} \\ y_{goal} \end{bmatrix} = \begin{bmatrix} \cos(\theta) & -\sin(\theta) \\ \sin(\theta) & \cos(\theta) \end{bmatrix} \begin{bmatrix} x_{csv} - x_{orig} \\ y_{csv} - y_{orig} \end{bmatrix} + \begin{bmatrix} x_{start} \\ y_{start} \end{bmatrix}$$

- **안정성 최적화**: 엘리베이터 하차 후(STATE_BACKWARD) 발생하는 오차를 최소화하기 위해, '현재 위치에서 가장 가까운 웨이포인트(Nearest Waypoint) 탐색' 알고리즘을 추가하여 복귀 안정성을 강화했다.

<br/>

### 2. Auto Elevator Node: 비전-라이다 센서 퓨전 승하차 로직
엘리베이터라는 특수 환경에서의 인지 판단을 위해 다중 센서를 활용한 상태 머신(FSM)을 설계했다.
- **비전 기반 문 개폐 판단 (MobileNet_V3_small)**:
  - **효율성**: 병원 환경의 저사양 임베디드 시스템에서도 실시간 추론이 가능하도록 경량 모델을 채택했다.
  - **성능**: Data Augmentation(회전, 노이즈 등)을 통해 Test Accuracy 100%, 평균 신뢰도 98%를 달성했다.
- **라이다 기반 공간 인식**:
  - 엘리베이터 내부 진입 시 4면이 밀폐되는 물리적 특징을 라이다 포인트 클라우드 데이터로 분석하여 진입 완료 및 회전 시점을 정확히 판정했다.

---

<br/>

## 시스템 아키텍처 및 데이터 흐름

| 구분 | 주요 스택 / 토픽 | 상세 역할 |
| :--- | :--- | :--- |
| **Localization** | /odom, /imu | 로봇 위치 추정 및 Filtered Yaw 기반 방향 제어 |
| **Perception** | /scan_raw, MobileNet V3 | 장애물 감지 및 비전 기반 엘리베이터 상태 인식 |
| **Decision** | Finite State Machine | IDLE &rarr; FORWARD &rarr; ELEVATOR &rarr; BACKWARD 상태 제어 |
| **Control** | /cmd_vel | 최종 속도 명령 발행 및 경로 추종 |

---

<br/>

## 주요 성과 및 결과
- **정밀도 향상**: 복귀 주행 시 엘리베이터 근처의 센서 노이즈를 극복하고 원점 복귀 오차 범위를 최소화했다.
- **높은 신뢰성**: 다양한 조도와 각도에서도 비전 기반 문 개폐 인지 성공률 100%를 기록했다.
- **유연한 확장성**: CSV 경로 데이터를 기준으로 하므로, 병원 내 이동 경로 변경 시 별도의 코드 수정 없이 데이터 교체만으로 즉시 대응할 수 있도록 설계했다.
