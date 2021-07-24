# Predict then Interpolate: A Simple Algorithm to Learn Stable Classifier

***

## Info
- ICML 2021 Oral Session
- Author: Yujia Bao, Shiyu Chang, Regina Barzilay
- Paper link: https://arxiv.org/pdf/2105.12628.pdf

## Summary
내 요약
- Robust한 모델을 위한 데이터 구성 방법론: Predict -> Interpolate
- DRO (Distributionally robust optimization)

## Detail

#### 배경

> Biased data = Biased model (편향된 데이터는 편향된 모델을 낳는다)

예를 들어 사막에 있는 낙타 사진으로 계속 학습을 했다면, 초원에 있는 낙타 사진에 대해 추론한다면 오분류할 것임.

이런 문제를 해결하기 위해 **Learning stable features across several training environments (본 논문의 Goal)** 을 위해
아래 2가지에 집중함
1. 안정적 feature를 찾는 것
2. Robust model를 찾는 거


아래 질문에서 실험을 시작함
> What happens if we apply a classifier f1 trained on environment E1 to a different environment E2?
> E1 환경에 학습시킨 classifier 모델을 다른 환경에 적용한다면?

직관적으로 생각했을 때, classifier f1이 잘못 예측하는 실수는 사실 feature와 label간의 correlations이 unstable하기 때문

> The unstable correlation within the subset of wrong prediction is **opposite** of that within the subset of correct prediction.
> 잘 예측한 subset 내 feature과 라벨 간에 높은 correlation이 존재하고, 틀리게 예측한 subset에서는 feature과 라벨 간의 correlation이 낮다 (불안정)

그래서 이 **2가지 subset을 잘 interpolate 하면 (섞으면) Oracle distribution**을 찾을 수 있다.


#### 본 논문에서 제안하는 Predict and Interpolate 방법론 (PI)

![1](https://user-images.githubusercontent.com/56949426/126861732-f3a4eba2-d0ee-4d26-92ad-6de63e43f41f.png)

**<Step 3>**
1. 각 환경 Ei에 대해 specific한 classifier fi 훈련시키기
2. 각 환경을 Ei, Ej 이렇게 pair로 짝지어서, Ei로 학습한 fi 모델을 사용해서 Ej를 2가지 partition으로 나누기 (interpolation 과정)
- Ej = (fi 모델로 Ej에서 추론했을 때 옳게 예측된 부분) ∪ (fi 모델로 Ej에서 추론했을 때 틀리게 예측된 부분)
3. 모든 interpolation 셋에 대해 worst-case risk를 최소화하는 방향으로 최종 model를 학습시키기


#### 결과
![image](https://user-images.githubusercontent.com/56949426/126861251-81b0529c-6d3b-42f8-a890-570e38182cda.png)

이미지 분류 테스크에 적용 시, PI가 젤 좋은 성능을 나타냄

![image](https://user-images.githubusercontent.com/56949426/126861275-3b05190e-2dc0-41eb-9a91-67f7bb28e483.png)

ASK2ME(의학 도메인 Text Classification Task)에 적용했을 때, ERM 기준으로 Attribute별로 정확도 낮은 순으로 6개 뽑은 표
PI가 젤 성능 좋음

![image](https://user-images.githubusercontent.com/56949426/126861293-4db26f07-d2dc-4a58-8a28-196b802a2d71.png)

Y축이 LABEL, X축이 ASK2ME의 Attribute이고, 이들 간의 피어슨 상관관계 계수 시각화함.
보면 인풋 과 Y값 간의 상관관계가 굉장히 다양함을 알 수 있음. 즉 쏠리거나 Biased되지 않았음.



