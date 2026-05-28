---
layout: page
title: "엣지 AI 환경을 위한 YOLO 기반 PM 탐지 경량화 모델 연구"
description: "YOLO 아키텍처 재설계 및 후처리 최적화(Pruning & Quantization)를 통한 PM 탐지 모델 경량화 연구"
img: assets/img/edge_ai_poster.png
importance: 1
category: work
---

<br/>

<div class="row justify-content-center">
    <div class="col-sm-8 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/edge_ai_poster.png" title="Project Poster" class="img-fluid rounded z-depth-1" %}
    </div>
</div>

<br/>

## 관련 자료
- **PDF 문서**: [프로젝트 상세 보고서 보기](/assets/pdf/edge_ai.pdf)

<br/>

## 배경
최근 개인형 이동장치(PM)의 급격한 확산은 도시 내 단거리 이동의 편리성을 높였지만, 동시에 교통 안전 문제와 사고 위험을 증가시키고 있다. 이에 따라 CCTV 기반의 실시간 모니터링 시스템을 통해 PM의 운행을 감시하고 위험 상황을 자동으로 감지하는 기술이 중요해졌다. 하지만 제한된 메모리와 연산 자원을 가진 엣지 디바이스에 모델을 효율적으로 배포하기 위해서는 실시간 대응을 위한 낮은 지연 시간과 경량화 기술이 필수적이다.

<br/>

## 목표
- PM 객체 탐지라는 특정 도메인에 최적화된 경량화 솔루션을 모색
- 성능-효율성 트레이드오프(Accuracy-Efficiency Trade-off) 측면에서 다양한 배포 환경에 적합한 유연한 선택지 제공
- YOLOv5s와 YOLOv8s를 baseline 모델로 활용하여 상호 보완적인 세 가지 경량화 솔루션 제시 및 성능 비교 분석

<br/>

## 핵심 경량화 기법 (Methods)

본 연구에서는 아키텍처 재설계와 후처리 최적화라는 두 가지 상이한 경량화 패러다임을 탐구했다.

### 1. YOLO-TLA (아키텍처 재설계)
- **기반 모델**: YOLOv5s
- **적용 기법**: C3Ghost 모듈과 C3CrossConv 모듈을 적용하여 백본과 헤드 모듈을 경량화하고 작은 객체 탐지 성능을 개선.

### 2. L-YOLO (아키텍처 재설계)
- **기반 모델**: YOLOv8s
- **적용 기법**: 백본을 L-HGNetV2로 교체하고, CStar 모듈, FPIoU² 손실함수, LAMP 프루닝 기법을 적용하여 파라미터 수를 줄이면서 특징 추출 능력을 유지.

### 3. Pruning + Quantization (후처리 최적화)
- **Pruning**: 그룹 기반 희소성 학습을 통해 특정 채널을 제거하여 모델 압축.
- **Quantization**: 모델의 가중치와 활성화 값을 INT8 저정밀도로 변환. Fine-tuning과 양자화 시 head 부분을 제외하여 낮은 정확도 손실을 보완.

<br/>

## 결과 및 성과 (Results)
전동 킥보드 이미지로 구성된 데이터셋을 사용하여 CPU 기반 환경에서 실험한 결과는 다음과 같다.

- **YOLO-TLA**: 
  - 파라미터 수: 7.20M → 1.13M (크게 감소)
  - 연산량: 17.0G → 10.0G
  - 성능: mAP 0.9410으로 baseline(0.6330) 대비 훨씬 높은 정확도를 보임.
- **L-YOLO**: 
  - 파라미터 수: 11.13M → 8.76M (약 21% 절감)
  - 성능: mAP 0.9684로 baseline(0.9737)에 준하는 성능을 유지하였으나, 추론 시간이 늘어나는 한계를 보임.
- **Pruning + Quantization**: 
  - 파라미터 수: 7.20M → 1.49M
  - 추론 시간: 470ms → 113ms (약 4배 가속)
  - 성능: mAP 0.9660 유지

- **결론**: 모델의 크기를 극적으로 줄이고 추론 속도를 가속화하여, 자원 제약적 엣지 디바이스 환경에 가장 적합한 배포 솔루션을 제공함.
