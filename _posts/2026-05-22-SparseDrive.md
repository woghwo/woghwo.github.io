---
layout: post
title: "SparseDrive"
date: 2026-05-18 17:30:00 +0900
categories: paper-review
tags: [paper-review, E2E]
description: "End-to-End Autonomous Driving via Sparse Scene Representation"
thumbnail: assets/img/sparsedrive_fig4.png
---



<br/>

## 0. Abstract 


UniAD에서 언급했듯이, Standalone 모델의 한계점은 (1) 정보 손실, (2) 오류 누적 이었습니다.

이에 Planning-Oriented한 End-to-Way 방식의 network들이 등장을 했지만, 기존의 방식에는 성능과 효율성 측면에서 한계점이 있습니다.
<br/>
본 논문에서는 이러한 두 한계점을 극복하기 위해 아래의 두 가지 방식을 포함한 **SparseDrive**를 제안합니다.

1. **Symetric Sparse Perception**
2. **Parallel Motion Planning**

<br/>

이를 통해, 기존 SOTA 모델 대비 training, inference 그리고 performance 측면에서 우수한 성능을 달성했습니다.

<br/>


## 1. Introduction 

UniAD에서 다뤘던 것처럼 기존의 **Standalone 방식**은 다음 두 가지 한계점이 있었습니다.

1. **Information loss** (정보 손실)
2. **Error accumulation** (오류 누적)

또한, 기존의 **End-to-End 방식**은 아래의 한계점들이 존재합니다.

1. **Performance** (성능)
2. **Efficiency** (효율성)

<img src="{{ '/assets/img/sparsedrive_fig1.png' | relative_url }}" class="img-fluid rounded z-depth-1" alt="Comparison on end-to-end paradigm">
<br/>

이는 주로 아래의 **구조적 한계**에 의해서 발생했습니다. (a)

1. **Straightforward design** (단순한 설계 방식)
2. **Dense한 BEV feature** 사용

---

본 논문에서는 성능을 높이기 위해서 아래의 핵심적인 내용들을 간과해서는 안 된다고 주장합니다.

1. **Ego-agents 간의 상호작용 부재**
2. **Multi-modal problem** (여러 경로 후보군 존재)

이를 고려하여 본 논문에서는 다음과 같은 구조(b)를 지닌 **SparseDrive**를 제안합니다.

1. **Symmetric Sparse Perception**
2. **Parallel Motion Planner**


이는 3. Method에서 자세히 다루도록 하겠습니다.



## 2. Methodology 

논문에서 제시한 해결 방법이나 모델 구조 등을 작성합니다.

<br/>

## 3. Results (결과)

실험 결과 및 기존 모델과의 성능 비교 등을 작성합니다.

<br/>

## 4. Conclusion (결론 및 배운 점)

이 논문을 읽고 느낀 점이나 추후 연구 방향 등을 작성합니다.
