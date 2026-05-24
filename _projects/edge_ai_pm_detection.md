---
layout: page
title: "엣지 AI 경량화 기반 PM 실시간 탐지 시스템"
description: "모델 최적화 및 후처리 경량화 파이프라인(Pruning & Quantization) 설계 및 실험"
img: assets/img/edge_ai_poster.png
importance: 1
category: work
---

## 프로젝트 개요
<br/>


### 배경
최근 개인형 이동장치(PM)의 급격한 확산으로 교통사고 위험이 증가하고 있습니다. 저사양 엣지 디바이스(예: 라즈베리파이)에서도 실시간 객체 탐지와 위험도 평가가 가능하도록 경량화된 AI 시스템이 필요합니다.

<br/>

### 목표
- YOLOv5s, YOLOv8s 기반 객체 탐지 모델을 경량화하여 연산량과 메모리 사용량을 크게 감소
- Pruning 및 Quantization 기법을 활용한 모델 최적화 파이프라인 설계
- 저지연 실시간 탐지 및 거리 기반 위험도 평가 시스템 구현

<br/>

### 담당 역할
- **모델 최적화**: Pruning, Quantization, Knowledge Distillation 적용
- **후처리 경량화**: 비최대 억제(NMS) 및 후처리 연산 최적화
- **실험 및 평가**: 라즈베리파이 등 엣지 디바이스에서의 추론 속도 및 정확도 측정

<br/>

## 주요 구현 내용
- **Pruning**: 구조적 채널 프루닝을 통해 모델 파라미터 40% 감소
- **Quantization**: INT8 양자화를 적용해 추론 속도 2.5배 향상
- **Edge Deployment**: TensorRT와 ONNX Runtime을 이용한 라즈베리파이 배포
- **실시간 위험도 평가**: 객체와 카메라 간 거리 추정 및 ROI 기반 위험도 단계 구분

<br/>

## 결과 및 성과
- 모델 크기: 27 MB → 16 MB (≈40% 감소)
- 추론 지연: 45 ms → 18 ms (≈2.5배 가속)
- 정확도 손실: mAP 0.48 → 0.46 (≈4% 감소, 허용 범위 내)
- 라즈베리파이 4B에서 30 FPS 실시간 탐지 달성

<br/>

## 관련 자료
- **PDF 문서**: [프로젝트 상세 보고서 보기](/assets/pdf/edge_ai.pdf)



