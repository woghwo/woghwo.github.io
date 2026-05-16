---
layout: post
title: "UniAD"
date: 2026-05-14 17:30:00 +0900
categories: paper-review
tags: [paper-review, E2E]
description: "Planning-oriented Autonomous Driving"
---



<br/>

## 0. Abstract 

자율주행 시스템의 주요 태스크(Task)는 크게 **인지(Perception)**, **예측(Prediction)**, **계획(Planning)** 세 가지로 구성됩니다. 

자율주행의 궁극적인 목적은 안전하고 효율적인 **계획(Planning)**을 수립하는 것입니다. (기존 방식들에 대한 한계점은 Introduction에서 자세히 다룹니다.)

본 논문에서는 이러한 3개의 태스크를 하나의 네트워크로 통합하여 End-to-End 방식으로 처리하는 **Unified Autonomous Driving (UniAD)** 구조를 제안합니다.


<br/>


## 1. Introduction 

{% include figure.liquid loading="eager" path="assets/img/uniad_pic1.png" title="Comparison on the various designs" class="img-fluid rounded z-depth-1" %}

자율주행 모델에 대한 기존의 접근 방식들은 크게 다음과 같이 분류할 수 있습니다.

### (a) Standalone Models
각 태스크(Perception, Prediction, Planning)마다 개별적인 모델(Network)을 독립적으로 사용하는 방식입니다. 파이프라인 상에서 순차적(Sequential)으로 각 모델의 결과를 전달하며 진행됩니다. 하지만 이러한 방식은 다음과 같은 단점을 가집니다.

- **정보 손실 (Information Loss):** 이전 모듈의 최종 출력(Output)만을 다음 모듈의 입력(Input)으로 사용하므로, 이전 모듈이 추출한 방대한 양의 특징(Feature) 정보들이 전달 과정에서 손실됩니다.
- **오류 누적 (Error Accumulation):** 각 모듈의 결과가 연쇄적으로 전달되기 때문에, 앞단에서 발생한 오류가 뒷단으로 갈수록 점진적으로 누적되고 증폭됩니다.

### (b) Multi-Task Learning (MTL)
하나의 네트워크를 공유하면서 여러 개의 Head를 추가하여 다양한 태스크를 동시에 학습하는 방식입니다.

- **장점:** 새로운 태스크나 모듈을 쉽게 확장하여 추가할 수 있습니다.
- **단점:** 특정 태스크를 학습하는 과정이 오히려 다른 태스크의 성능을 저하시키는 **Negative Transfer** 현상이 발생할 수 있습니다.

### (c.1) Vanilla Solution
가장 초기의 End-to-End 네트워크 구조로, 센서 데이터로부터 직접적으로 Planning 결과를 예측합니다.

- **단점:** 중간 과정인 Perception과 Prediction에 대한 명시적인 추론 과정이 없기 때문에, 모델의 판단 근거를 알 수 없어 해석 가능성(Interpretability)이 떨어지고 안전성 보장(Safety Guarantee)이 어렵습니다.

### (c.2) Explicit Design

{% include figure.liquid loading="eager" path="assets/img/uniad_pic2.png" title="Tasks comparison and taxonomy" class="img-fluid rounded z-depth-1" %}

Perception과 Prediction 태스크를 명시적으로 고려한 구조이지만, 위 테이블에서 확인할 수 있듯이 각 태스크 내에서의 세밀한(Detailed) 상호작용 및 연결성에 대한 고려가 여전히 부족합니다.

<br/>

**👉 결론적으로 본 논문에서는,** 
최종 목표인 **Planning**을 중심에 두는 **Planning-Oriented** 관점에서, 각 태스크들의 세부적인 상호작용을 통합적으로 반영한 **UniAD**를 제안합니다. 

이 네트워크의 가장 핵심적인 포인트는 **"모든 요소(Node)를 Query를 통해 연결하여 지속적으로 정보를 교환한다"**는 것입니다. 구체적인 동작 방식과 구조는 *Methodology* 섹션에서 자세히 다루겠습니다.

<br/>

## 2. Methodology 

논문에서 제시한 해결 방법이나 모델 구조 등을 작성합니다.

<br/>

## 3. Results (결과)

실험 결과 및 기존 모델과의 성능 비교 등을 작성합니다.

<br/>

## 4. Conclusion (결론 및 배운 점)

이 논문을 읽고 느낀 점이나 추후 연구 방향 등을 작성합니다.
