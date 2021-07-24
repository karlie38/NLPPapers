
***

## Info
ICML 2021 Oral Session
Author: Yujia Bao, Shiyu Chang, Regina Barzilay
Paper link: https://arxiv.org/pdf/2105.12628.pdf

## Summary
내 요약
- Robust한 모델을 위한 데이터 구성 방법론: Predict -> Interpolate
- DRO (Distributionally robust optimization)

## Detail


> Biased data = Biased model

예를 들어 사막에 있는 낙타 사진으로 계속 학습을 했다면, 초원에 있는 낙타 사진에 대해 추론한다면 오분류할 것임.

이런 문제를 해결하기 위해 **Learning stable features across several training environments (본 논문의 Goal)** 을 위해
아래 2가지에 집중함
1. 안정적 feature를 찾는 것
2. Robust model를 찾는 거

아래 질문에서 실험을 시작함
> What happens if we apply a classifier f1 trained on environment E1 to a different environment E2?
(E1 환경에 학습시킨 classifier 모델을 다른 환경에 적용한다면?)

직관적으로 생각했을 때, classifier f1이 잘못 예측하는 실수는 사실 feature와 label간의 correlations이 unstable하기 때문

>The unstable correlation within the subset of wrong prediction is **opposite** of that within the subset of correct prediction.
(잘 예측한 subset 내 feature과 라벨 간에 높은 correlation이 존재하고, 틀리게 예측한 subset에서는 feature과 라벨 간의 correlation이 낮다 (불안정))

그래서 이 2가지 subset을 잘 interpolate 하면 (섞으면) Oracle distribution을 찾을 수 있다.