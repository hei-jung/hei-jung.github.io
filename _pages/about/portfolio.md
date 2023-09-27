---
layout: single
permalink: /portfolio/
title: "장혜정"
use_mermaid: true
---

> 안녕하세요, 주어진 일에 책임을 다하는 연구원 장혜정입니다.<br>
> 저는 주변 사람들로부터 차분하다는 평가를 받고 있습니다. 또한 맡은 일은 끝까지 해보자는 마인드를 가지고 있습니다.<br>
> 이러한 제 가치관과 차분한 성격을 바탕으로 주어진 일에서 어려움을 겪더라도, 일단 몰두해서 책임을 다하고자 노력하는 편입니다.

[![GitHub](http://img.shields.io/badge/GitHub-black?style=flat-square&logo=github)](https://github.com/hei-jung)
[![Blog](https://img.shields.io/badge/Blog-badge?style=flat-square&logo=Naver&logoColor=white)](http://blog.naver.com/jungs-note)
[e-mail📩](mailto:wkdgpwjd007@naver.com)

<!-- ```mermaid
timeline
        title 학력사항
        section 2016
          서초고등학교 졸업<br>(2013 - 2016)
        section 2020
          한국외국어대학교 전자공학과 졸업<br>(2016 - 2020) : C/C++을 이용한 프로그래밍을 배웠으며 주로 임베디드 소프트웨어 개발을 수행하였습니다. : 동아리 활동을 통해 Java를 사용해서 Android App을 개발하였습니다. : 졸업 프로젝트로 Python을 사용해서 강화학습 알고리즘을 구현하였습니다.
        section 2023
          이화여자대학교 컴퓨터의학협동과정 졸업<br>(2021 - 2023) : Medical Imaging and Learning (MIL) Lab<br>지도교수 신태훈 교수님 : 3D MRI 영상을 활용한 컴퓨터 비전 연구를 수행하였습니다. : Python 언어와 PyTorch 프레임워크를 사용하였습니다. : 딥러닝 모델 개발 역량과 XAI 기술 구현 스킬을 습득하였습니다.
``` -->

## 학력
- 2013.03 ~ 2016.02 서초고등학교 졸업
- 2016.02 ~ 2020.02 한국외국어대학교 공과대학 전자공학과 졸업
- 2021.09 ~ 2023.08 이화여자대학교 컴퓨터의학협동과정 졸업
  - Medical Imaging and Learning (MIL) Lab (엘텍공과대학 휴먼기계바이오공학부 신태훈 교수님 연구실)

## 학술대회 발표

- 2023.06 대한전자공학회(IEIE) 포스터 발표
- 2022.10 대한의료인공지능학회(KoSAIM) 구두 발표

## 프로젝트 경험

### 대학원 연구

|삼차원 경동맥 TOF 자기공명 혈관조영 영상을 활용한 딥러닝 전이학습 기반 뇌백질변성 부피 예측|
|------------------------------------|
|딥러닝 기술을 활용하여 목 MRI 영상으로부터 뇌혈관 질환의 중증도를 예측하는 연구를 하였습니다.<br>뇌혈관 질환은 일반적으로 의사들이 뇌 MRI 영상을 통해 눈으로 진단합니다. 그런데 뇌 MRI 영상은 목 MRI 영상보다 촬영 시간이 길고 촬영 비용이 많이 듭니다.<br>뇌혈관 질환의 중증도와 목 동맥(경동맥) 질환이 상관관계가 있다는 임상 연구가 있어, 이 가설을 활용하여 목 MRI 영상을 통해 뇌혈관 질환의 중증도를 예측하고자 하였습니다.<br>문제를 간단하게 만들기 위해, 딥러닝 모델이 먼저 목 MRI 영상으로부터 경동맥을 찾아내도록 pre-training을 하고, 그런 다음 뇌혈관 질환의 중증도를 예측하도록 transfer learning 하는 방법을 적용하였습니다.<br>연구 결과, 뇌혈관 질환 중증도의 예측 값과 실제 값 간 Pearson 상관계수가 0.52로 딥러닝 모델을 활용하여 목 MRI 영상으로부터 뇌혈관 질환의 중증도를 예측하는 것이 가능함을 보였습니다.|
|`Python` `PyTorch` `XAI` `컴퓨터 비전` `딥러닝` `segmentation` `regression` `classification` `Linux`|

### 전공 프로젝트

|수업명|주제|기간|내용|관련 기술|
|----|---|---|---|-------|
|전자공학종합설계프로젝트|강화학습을 이용한 5G Network Slicing 트래픽 제어|2019.03.04 ~ 2019.11.27|학부 졸업 프로젝트의 주제로 5G Network Slicing 기술에 강화학습 알고리즘을 적용하여, 사용자 수요에 맞춰 대역폭을 할당하는 방법을 제안하였습니다. Python 언어를 사용했고, 강화학습의 environment(네트워크 환경)와 agent(통신사)를 직접 구현하였습니다. 강화학습에서 가장 대표적인 알고리즘인 Monte-Carlo update를 활용하여 데이터 패킷의 수가 많은 서비스에 대해 더 큰 보상을 부여하는 방식을 고안하였습니다. 이를 통해 강화학습 알고리즘으로 5G Network Slicing 기술 구현이 가능함을 보였습니다.|`Python` `TensorFlow` `강화학습`|
|프로세서응용종합설계|스마트폰으로 조작이 가능한 LED 디지털 시계|2018.10.30 ~ 2018.12.11|실생활에서 사용할 수 있는 전자제품을 설계하는 팀 프로젝트였습니다. 3인 팀을 구성하여 시계 하드웨어 구현, LED 동작 코드 작성, Android App 개발의 파트로 나누었습니다. 저는 앱 개발을 담당하였고 LED 동작 코드 작성에 기여하였습니다. Java 언어를 사용해서 앱을 개발했고 Bluetooth 모듈을 활용하여 앱과 시계 간 통신을 구현하였습니다. 프로젝트 활동을 바탕으로 LED 임베딩 경험을 할 수 있었고 데이터 통신 역량을 키울 수 있었습니다.|`IoT` `Java` `C++` `Arduino` `Android`|

### 기타 프로젝트

|주제|기간|활동내용|관련 기술|
|---|---|------|------|
|데이터톤(심전도 데이터셋을 이용한 부정맥 진단 AI 모델 개발)|2021.11.26 ~ 2021.12.12|2인 팀을 구성하여 심전도 데이터셋을 이용한 부정맥 진단 AI 모델을 개발하였습니다. PyTorch 기반으로 ResNet 모델을 사용한 결과, 예측 정확도가 약 99%로 모델의 성능이 매우 우수하였으며, 참가팀 중에서 5위를 기록하였습니다. 심전도 신호는 시계열 데이터이기 때문에 시계열 데이터를 활용하는 경험을 쌓을 수 있었습니다.|`Python` `PyTorch` `딥러닝`|
|시선 프로젝트(시각장애인을 위한 애플리케이션)|2020.08.10 ~ 2020.09.11|4인 팀을 구성하여 시각장애인의 안전한 보행을 위해 횡단보도와 신호등을 탐지하는 애플리케이션을 개발하였습니다. 횡단보도 및 신호등 사진 데이터 수집 및 라벨링 작업과 신호등 탐지 모델 구현을 수행하였습니다. Tensorflow 기반의 Darknet 모델을 tuning 한 결과, 탐지 정확도를 91%까지 낼 수 있었습니다. Object Detection 분야를 배울 수 있었고, 딥러닝 모델 구현 역량을 키울 수 있었습니다. 또한 GitHub 사용을 통해 협업 스킬을 습득하였습니다.|`Python` `TensorFlow` `Java` `컴퓨터 비전` `딥러닝` `object detection` `Android` `GitHub`|
|훕밥 프로젝트(교내 학생식당 애플리케이션)|2019.09.29 ~ 2019.12.21|교내 학생식당에서 기존에 사용하던 종이 식권을 바코드 혹은 QR 코드로 대체할 수 있는 Android App을 제안하였습니다. 앱 개발팀인 저희 팀에서는 팀원 5명의 역할을 각각 Front-end 파트와 Back-end 파트로 나누었습니다. 저는 팀원 한 명과 함께 Back-end 개발을 담당하였습니다. Firebase 데이터베이스를 활용하여 NoSQL 기반의 DB 설계 스킬을 습득하였습니다. |`Java` `Android` `NoSQL`|
|딥러닝 기반 Breast Cancer Prediction|2019.03.28 ~ 2019.06.04|Kaggle 플랫폼의 Breast Cancer Dataset을 활용하여 딥러닝 예측 모델을 구현하였습니다. Keras 기반의 baseline 코드를 참고해서 모델의 hyper-parameter를 tuning 한 결과, 예측 정확도가 98%로 모델의 성능이 매우 우수했습니다. 직접 딥러닝 모델을 구현해 보는 활동을 통해 딥러닝 메서드를 이해할 수 있게 되었고, 딥러닝 분야에 관심을 가지는 계기가 되었습니다.|`Python` `Keras` `딥러닝` `classification`|

## 기술 스택

|   |Name|
|---|----|
|OS|![Linux](https://img.shields.io/badge/Linux-FCC624?style=flat-square&logo=linux&logoColor=black) ![Ubuntu](https://img.shields.io/badge/Ubuntu-E95420?style=flat-square&logo=ubuntu&logoColor=white)|
|Languages|![C](https://img.shields.io/badge/c-%2300599C.svg?style=flat-square&logo=c&logoColor=white)/![C++](https://img.shields.io/badge/c++-%2300599C.svg?style=flat-square&logo=c%2B%2B&logoColor=white) ![Java](https://img.shields.io/badge/java-%23ED8B00.svg?style=flat-square&logo=java&logoColor=white) ![Python](https://img.shields.io/badge/python-3670A0?style=flat-square&logo=python&logoColor=ffdd54)|
|Databases|![OracleSQL](https://img.shields.io/badge/OracleSQL-F80000?style=flat-square&logo=oracle&logoColor=white)|
|ML/DL Frameworks|![PyTorch](https://img.shields.io/badge/PyTorch-%23EE4C2C.svg?style=flat-square&logo=PyTorch&logoColor=white) ![TensorFlow](https://img.shields.io/badge/TensorFlow-%23FF6F00.svg?style=flat-square&logo=TensorFlow&logoColor=white)|
|Backend|![SpringBoot](https://img.shields.io/badge/SpringBoot-%236DB33F.svg?style=flat-square&logo=SpringBoot&logoColor=white)|
|Frontend|![React](https://img.shields.io/badge/react-%2320232a.svg?style=flat-square&logo=react&logoColor=%2361DAFB)|
|Other|![Arduino](https://img.shields.io/badge/-Arduino-00979D?style=flat-square&logo=Arduino&logoColor=white) ![Verilog](http://img.shields.io/badge/Verilog-black?style=flat-square)|


|   |자격증|인증기관|
|---|-----|------|
|2021.06.02|정보처리기사|한국산업인력공단|

## 외국어 능력

|취득일|공인어학성적|점수/등급|
|---|---------------------------------|-----|
|2023.03|TOEIC Speaking|160(AL)|
|2022.08|TOEIC|915|
|2018.08|JLPT|N2|


## 학내외 활동

- 2020.09 ~ 2020.12 비카누스 소프트웨어 엔지니어 인턴
- 2020.04 ~ 2020.09 엔코아 플레이데이터 자율주행을 위한 임베디드 및 AI 영상분석 컨버전스 SW 개발자 양성과정
- 2019.03 ~ 2019.12 한국외대 소프트웨어 동아리 GnuVill(그누빌)
- 2018.04 ~ 2018.08 컨버전스형 IT 연합동아리 CADI(카디) 6기
- 2016.03 ~ 2016.12 한국외대 전자공학과 집행부
