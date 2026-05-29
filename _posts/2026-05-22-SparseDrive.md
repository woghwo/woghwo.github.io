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
<br/>
UniAD에서 다뤘던 것처럼 기존의 **Standalone 방식**은 다음 두 가지 한계점이 있었습니다.

1. **Information loss** (정보 손실)
2. **Error accumulation** (오류 누적)
<br/>
또한, 기존의 **End-to-End 방식**은 아래의 한계점들이 존재합니다.

1. **Performance** (성능)
2. **Efficiency** (효율성)

<img src="{{ '/assets/img/sparsedrive_fig1.png' | relative_url }}" class="img-fluid rounded z-depth-1" alt="Comparison on end-to-end paradigm">
<br/>
<br/>
이는 주로 아래의 **구조적 한계**에 의해서 발생했습니다. (a)

1. **Straightforward design** (단순한 설계 방식)
2. **Dense한 BEV feature** 사용

---

본 논문에서는 성능을 높이기 위해서 아래의 핵심적인 내용들을 간과해서는 안 된다고 주장합니다.

1. **Ego-agents 간의 상호작용 부재**
2. **Multi-modal problem** (여러 경로 후보군 존재)
<br/>


이를 고려하여 본 논문에서는 다음과 같은 구조(b)를 지닌 **SparseDrive**를 제안합니다.

1. **Symmetric Sparse Perception**
2. **Parallel Motion Planner**


이는 3. Method에서 자세히 다루도록 하겠습니다.



## 2. Related Work

### 2.1 Multi-view 3D Detection (멀티뷰 3D 검출)

* **목적**: 여러 카메라 이미지에서 3차원 객체(차량, 보행자 등)를 안전하게 검출하는 것이 자율주행의 기초.
* **주요 접근**:
  * **LSS 계열**: 이미지를 depth(깊이) 추정으로 3D로 올리고 BEV(Bird’s-Eye View) 평면으로 투사하는 "lift-splat" 방식.
  * **BEV queries 방식**: BEV 상의 쿼리(토큰)를 다시 투영해 이미지에서 피쳐를 샘플링.
  * **BEV-free(희소·쿼리 기반) 방식**:
    * **PETR 계열**: 3D 위치 임베딩과 전역 어텐션으로 뷰 변환을 암묵적으로 학습.
    * **Sparse4D 계열**: 3D 공간에 명시적인 앵커(anchors)를 두고 이를 이미지로 투영해 지역 피쳐를 모아 반복적으로 앵커를 갱신.
    
즉, BEV(밀집 표현)는 성능은 좋지만 비용이 크므로, 최근엔 쿼리·희소 앵커 기반으로 효율성·성능을 개선하려는 흐름이 있습니다.

<br/>

### 2.2 End-to-End Tracking (엔드투엔드 다중 객체 추적)

* **전통적 방식**: tracking-by-detection — 먼저 검출하고, 후처리(데이터 연관)를 통해 추적 ID를 연결. 이 방식은 신경망의 능력을 온전히 활용하지 못함.
* **새로운 아이디어**: object queries를 차용해 'track queries'로 스트리밍 방식으로 인스턴스를 모델링. (MOTR 등)
* **문제점 및 발전**: 일부 방법은 tracklet-aware label assignment로 검출과 연관 간 충돌이 생김. Sparse4Dv3는 시간적으로 전파된 인스턴스 자체가 ID 일관성을 갖는다는 점을 보여 간단한 ID 할당으로도 SOTA 성능 달성.

<br/>

### 2.3 Online Mapping (온라인 맵 생성)

* **배경**: HD 맵(full high-definition map)은 만들기 비용이 매우 높아, 실시간으로 주변 환경 벡터화(map elements)를 생성하려는 연구가 활발.
* **대표적 방법**:
  * **HDMapNet**: BEV 세그멘테이션 + 후처리로 벡터화.
  * **VectorMapNet**: 오토리그레시브 트랜스포머 기반.
  * **MapTR**: 맵 요소를 점(point) 집합의 순열 동치성으로 모델링해 정의의 모호성 제거.
  * **기타**: BeMapNet, StreamMapNet 등은 베지어 곡선이나 시계열(temporal) 퓨전, 쿼리 전파로 향상시킴.

즉, 온라인 맵핑은 다양한 표현과 시계열 처리를 통해 HD 맵을 대체하려는 시도들이 있습니다.

<br/>

### 2.4 End-to-End Motion Prediction (엔드투엔드 모션 예측)

* **목적**: 전통 파이프라인에서 발생하는 누적 오류를 줄이고자, 감지·추적 등과 통합된 단일 모델에서 미래 궤적을 예측.
* **대표적 접근**:
  * **FaF**: 단일 CNN으로 현재·미래 바운딩박스 예측.
  * **IntentNet**: 고수준 행동(intent)과 장기 궤적을 함께 추론.
  * **PnPNet**: 온라인 트래킹을 포함해 궤적 수준의 피쳐 집계.
  * **ViP3D, PIP**: 에이전트 쿼리, 혹은 로컬 벡터맵으로 HD 맵을 대체.
* **핵심**: 예측도 단일 모델에 통합하면 안정성과 효율성 면에서 장점이 있음.

<br/>

### 2.5 End-to-End Planning (엔드투엔드 주행 계획)

* **역사**: 초기부터 엔드투엔드 주행 연구 존재(예: ALVINN). 초기 접근은 중간단계(감지·예측)를 생략해 해석성 부족.
* **중간 접근**: 일부 연구는 명시적 cost map을 만들어 해석성을 확보하면서도 수작 규칙에 의존.
* **최신 연구**: UniAD(통합 쿼리 디자인)·VAD(벡터화 표현)·GraphAD(그래프 기반 상호작용)·FusionAD(다중 센서) 등은 장면 학습을 중점적으로 다루지만, 이들 대부분은 장면(인식) 학습에 치중하고, 모션 예측과 계획을 단순하게 처리하는 경향이 있어 두 작업의 유사성(상호작용 고려, 에고(ego) 정보 필요성, 다중 모달성 등)을 충분히 활용하지 못함.
* **SparseDrive의 포인트**: 예측(prediction)과 계획(planning)의 유사성을 적극 반영해 병렬 설계와 충돌 인식 재점수화(collision-aware rescore)를 제안함.

<br/>

## 3. Methodology 

<br/>

### 3.1 Overview

<br/>
<img src="{{ '/assets/img/sparsedrive_fig2.png' | relative_url }}" class="img-fluid rounded z-depth-1" alt="SparseDrive Overview">
<br/>

전체 구조는 위 그림과 같으며, 데이터 처리 흐름은 다음과 같은 단계로 진행됩니다:

1. **Feature Extraction**: 입력된 Multi-view 이미지가 **Image Encoder**를 거쳐 multi-view, multi-scale의 **Image Feature Map ($I$)**으로 변환됩니다.
2. **Symmetric Sparse Perception**: 앞서 추출된 Feature Map ($I$)은 두 갈래로 나뉘어, 주변 에이전트(Surrounding Agents)와 맵 요소(Map Elements)를 각각 탐지합니다.
3. **Parallel Motion Planner**: 인식된 에이전트 및 맵 정보는 최종적으로 **Parallel Motion Planner**로 전달되어 자율주행 차량(Ego)의 최적 궤적(Trajectory)을 결정합니다.

<br/>

### 3.2 Symmetric Sparse Perception 

<br/>
<img src="{{ '/assets/img/sparsedrive_fig3.png' | relative_url }}" class="img-fluid rounded z-depth-1" alt="Symmetric Sparse Perception">
<br/>

위 그림에서 볼 수 있듯이, 주변 에이전트(Agents)와 맵(Map)에 대한 탐지는 **대칭적인(Symmetric) 동일한 구조**로 처리됩니다. 각 모듈의 세부적인 작동 방식은 다음과 같습니다.
<br/>

#### 1) Sparse Detection

주변 에이전트는 다음 두 가지 요소로 표현됩니다:
* **$F_d \in \mathbb{R}^{N_d \times C}$** : 인스턴스 피처 (Instance Feature)
* **$B_d \in \mathbb{R}^{N_d \times 11}$** : 앵커 박스 (Anchor Box)
  *(여기서 $N_d$는 앵커 박스의 개수, $C$는 피처의 차원을 의미합니다.)*

각 앵커 박스($B_d$)는 다음 11개의 파라미터로 구성됩니다:
$$
\{x, y, z, \ln w, \ln h, \ln l, \sin yaw, \cos yaw, v_x, v_y, v_z\}
$$

Fig 3의 구조를 살펴보면, 각 브랜치(Branch)는 **두 종류의 디코더(Decoder)**로 구성되어 있습니다:
* **Temporal Decoder**: $(n-1)$개 
* **Non-temporal Decoder**: $1$개

모든 디코더는 공통적으로 다음과 같은 입출력을 가집니다:
* **입력 (Input)**: Feature Maps ($I$), Anchor Boxes ($B_d$), Instance Features ($F_d$)
* **출력 (Output)**: Updated Instance Features, Refined Anchor Boxes

<br/>

**[Non-temporal Decoder]**

공통적으로 사용되는 Non-temporal Decoder는 다음 **3가지 핵심 모듈**로 구성됩니다:
1. Deformable Aggregation
2. FFN (FeedForward Network)
3. Refinement & Classification

<br/>
<img src="{{ '/assets/img/sparsedrive_add.png' | relative_url }}" class="img-fluid rounded z-depth-1" alt="Non-temporal Decoder 구조">
<br/>

이 디코더의 가장 큰 특징은 **랜덤하게 초기화된(Randomly Initialized) 인스턴스**를 입력으로 사용한다는 점입니다.

<br/>

**[Temporal Decoder]**

반면, Temporal Decoder는 **이전 디코더의 결과값을 입력**으로 사용하며, 앞선 구조에 **Multi-Head Attention**이 추가된 형태입니다:
1. **Cross Attention**: 이전 프레임(Previous Frame)과 현재 프레임(Current Frame)의 인스턴스 간 어텐션을 수행합니다.
2. **Self Attention**: 현재 프레임 내 인스턴스들 간의 어텐션을 수행합니다.

<br/>

#### 2) Sparse Online Mapping

맵 요소에 대한 인스턴스 정의는 다음과 같이 이루어집니다:
* **$F_m \in \mathbb{R}^{N_m \times C}$** : 인스턴스 피처 (Instance Feature)
* **$L_m \in \mathbb{R}^{N_m \times N_p \times 2}$** : 앵커 폴리라인 (Anchor Polyline)
  *(여기서 $N_m$은 앵커 폴리라인의 개수, $N_p$는 폴리라인을 구성하는 점의 개수입니다.)*

각 앵커 폴리라인($L_m$)은 다음과 같은 점들의 집합으로 구성됩니다:
$$
\{x_0, y_0, x_1, y_1, \ldots, x_{N_p - 1}, y_{N_p - 1}\}
$$

이러한 형태적 차이를 제외한 나머지 작동 원리는 앞서 설명한 Sparse Detection과 동일합니다.

<br/>

#### 3) Sparse Tracking

앞선 과정에서 산출된 **Detection Confidence**가 특정 임계값(Threshold)을 넘어서면 이를 유효한 타겟으로 간주하고, 고유 ID를 부여하여 **추적(Tracking)**을 시작합니다. 


<br/>

### 3.3 Parallel Motion Planner

<br/>
<img src="{{ '/assets/img/sparsedrive_fig4.png' | relative_url }}" class="img-fluid rounded z-depth-1" alt="Parallel Motion Planner">
<br/>

**Parallel Motion Planner**는 크게 다음의 세 가지 과정으로 구성됩니다.

<br/>

#### 1) Ego Instance Initialization

이전의 주변 에이전트(Agents)에서 정의된 것과 유사하게, Ego 인스턴스 역시 다음과 같이 정의됩니다

* **$F_e \in \mathbb{R}^{1 \times C}$** : Ego 인스턴스 피처 (Ego Instance Feature)
* **$B_e \in \mathbb{R}^{1 \times 11}$** : Ego 앵커 박스 (Ego Anchor Box)

기존의 방식에서는 Ego 피처를 랜덤(Random)하게 초기화했습니다. 하지만 이러한 방법은 의미론적(Semantic)이거나 기하학적(Geometric)인 정보를 얻기 어렵다는 단점이 존재합니다.

$$
F_e = \operatorname{AveragePool}(I_{\text{front}}, S)
$$

따라서 본 논문에서는 Perception 단계에서 얻은 이미지 피처 맵($I$) 중 **전면 카메라(Front Camera)의 가장 작은 스케일(Scale)의 피처 맵**을 사용하여 Ego 피처를 초기화합니다. *(실제 카메라 이미지 상에서는 Ego 차량이 보이지 않기 때문에 이와 같은 풀링(Pooling) 방식을 사용합니다.)*

앵커 박스($B_e$)의 경우 위치(Location), 크기(Dimension), 요 각도(Yaw angle)는 알 수 있지만, 속도(Velocity)는 알 수 없습니다. 이를 해결하기 위해 **$ES_T$라는 보조 태스크(Auxiliary Task)**를 추가해 속도를 디코딩(Decode)하며, 이전 프레임의 결과값을 현재 프레임의 초기화 값으로 활용합니다.

<br/>

#### 2) Spatial-Temporal Interaction

먼저 모든 에이전트 간의 상호작용(Interaction)을 모델링하기 위해, Ego 차량과 주변 에이전트를 합쳐 다음과 같이 **에이전트 수준의 인스턴스(Agent-level Instance)**로 정의합니다

$$
F_a = \operatorname{Concat}(F_d, F_e), \quad B_a = \operatorname{Concat}(B_d, B_e)
$$

Ego 인스턴스를 처음 초기화할 때는 시간적(Temporal) 정보가 고려되지 않았으므로, 시간적 모델링을 위해 **$(N_d + 1) \times H$** 크기의 인스턴스 메모리 큐(Instance Memory Queue)를 사용합니다. *(여기서 $H$는 저장된 프레임 수를 의미합니다.)*

이후 공간적-시간적 문맥(Spatial-Temporal Context)을 얻기 위해 다음 **3가지 상호작용(Interaction)**을 수행합니다:

1. **Agent-Temporal Cross-Attention**: 에이전트와 시간 프레임 간의 어텐션
2. **Agent Self-Attention**: 현재 에이전트들 간의 어텐션
3. **Agent-Map Cross-Attention**: 에이전트와 맵 요소 간의 어텐션

여기서 사용되는 Temporal Cross-Attention은 앞선 Sparse Perception의 Scene-level Interaction(모든 시간적 프레임의 인스턴스와 현재 프레임 인스턴스 간의 상호작용)과 달리, **각 인스턴스 간의 상호작용만을 수행하는 Instance-level Interaction**이라는 점이 특징입니다.

이를 통해 각 에이전트(주변 차량 및 Ego 차량)에 대해 모션 예측(Motion Prediction)과 주행 계획(Planning)을 위한 궤적과 점수(Score)를 다음과 같이 산출합니다:
* **모션 예측 (Motion Prediction)**: 
  * 궤적: $\tau_m \in \mathbb{R}^{N_d \times K_m \times T_m \times 2}$
  * 점수: $s_m \in \mathbb{R}^{N_d \times K_m}$
* **주행 계획 (Planning)**: 
  * 궤적: $\tau_p \in \mathbb{R}^{N_{cmd} \times K_p \times T_p \times 2}$
  * 점수: $s_p \in \mathbb{R}^{N_{cmd} \times K_p}$

*(여기서 $K_m, K_p$는 각각 모션 예측과 주행 계획을 위한 궤적 후보(Modes)의 수이며, $T_m, T_p$는 예측 및 계획할 미래의 타임스텝 수입니다. $N_{cmd}$는 주행 명령(Turn left, Turn right, Go straight 등 3가지)의 수를 의미합니다.)*

<br/>

#### 3) Hierarchical Planning Selection

앞서 얻은 멀티 모달(Multi-modal) 형태의 여러 주행 계획 궤적 중에서, **상위 수준 명령(High-level Command, $cmd$)에 해당하는 궤적**만 우선적으로 선택합니다:
$$
\tau_{p,cmd} \in \mathbb{R}^{K_p \times T_p \times 2}
$$

이후, **충돌 인지 재점수화 모듈(Collision-aware Rescore Module)**을 통해 모션 예측 단계에서 얻은 다른 에이전트들의 궤적($\tau_m$)과 선택된 Ego 궤적($\tau_{p,cmd}$) 사이의 충돌 여부를 판단하여, 최종적으로 가장 안전하고 최적화된 궤적을 선택하게 됩니다.




## 4. Results (결과)

실험 결과 및 기존 모델과의 성능 비교 등을 작성합니다.

<br/>

## 5. Conclusion (결론 및 배운 점)

이 논문을 읽고 느낀 점이나 추후 연구 방향 등을 작성합니다.
