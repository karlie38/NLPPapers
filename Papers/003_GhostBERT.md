# GhostBERT: Generate More Features with Cheap Operations for BERT

***

## Info
- ACL-IJCNLP 2021 Oral Session
- Author: Zhiqi Huang , Lu Hou , Lifeng Shang, Xin Jiang, Xiao Chen, Qun Liu
- Paper link: https://www.aclanthology.org/2021.acl-long.509
- Github: soon..

## Summary
내 요약
- Redundant feature만드는 Ghost Module (1D Depthwise Separable Convolution + Softmax normalization)

## Detail

#### What problem are they trying to solve?
Over-Parameterized of Neural Networks


#### What model architecture was used?

#####  배경

1. Computer vision 분야에서 CNN에서 redundant feature가 성능 향상에 도움주었다는 연구 결과
2. Pre-trained language model에서 attention map이 Convolution layer의 feature map과 비슷한 패턴을 보인다는 연구 결과
=> 그래서 CNN 기반 (어텐션 역할을 한다고 생각하고) redundant feature(모델 학습에 있어 redundant feature는 중요)을 생성하는 Ghost Module

- **Model architecture**

![image](https://user-images.githubusercontent.com/56949426/129709418-ed6ac927-d593-4e8f-ba0f-9cb2069790ec.png)


- (pruned)란 의미는 pruned bert에 적용할 수도 있고 original bert에 적용할 수 있음을 뜻함
- 보는 것처럼 MHA 혹은 FFN 에서 나온 original feature과 그걸 Ghost Module로 태운 값을 concat해서 사용함


- **Ghost module function**

![image](https://user-images.githubusercontent.com/56949426/129709590-b942b68b-c904-46e4-8443-ca7126689957.png)

Nonlinear: ReLU 

![image](https://user-images.githubusercontent.com/56949426/129709783-f0edfbe9-1b99-49bb-b5c8-2f8043d4d3f4.png)

![image](https://user-images.githubusercontent.com/56949426/129709813-c394ef11-cfde-44bf-bac2-1918452b5d1c.png)


- **Ghost module**

![image](https://user-images.githubusercontent.com/56949426/129710071-f58f4031-c224-4ddb-8c37-541255e70fbb.png)


- 1 Dimensional Depthwise Separable Convolution (DWConv) 사용
	- 기본 Conv에 비해 연산량 감소시키는 효과 있음
	- 각 단일 채널에 대해서만 독립적으로 conv 연산 수행.
	- 또 결과물에 각 채널을 1개의 채널로 압축할 수 있는 추가적인 convolution을 진행해서 결과물이 간략하게 나오게 함. (파라미터 수 1/d 배 줄음, d=dimension)
- Normalization: softmax로 각 convolution kernel를 normalize 함

#### Dataset
	- GLUE

#### How did they do training?
	- Loss: Cross Entropy
	- Pruning
	- Distillation

#### Result
**Unpruned model 에 적용**

![image](https://user-images.githubusercontent.com/56949426/129710660-3c8f3c20-6766-41ba-9eac-1a2519789db7.png)


- 오리지널 모델에 고스트 모듈 적용 시, 성능이 조금이라도 오름



**Pruned model 에 적용**
![image](https://user-images.githubusercontent.com/56949426/129711016-f10d9d53-b96e-4500-a172-eec0804152ec.png)

- Pruned 해도 오리지널 모델과 비교했을 때, 성능 감소폭이 크지 않음


#### Ablation study
![image](https://user-images.githubusercontent.com/56949426/129715428-0d45a34e-8afd-4d04-8ff9-b61e6d4beec1.png)

- Data augmentation(DA), knowledge distillation (KD) 성능 저하에 큰 영향 줌
- Normalization: Softmax > ReLU
- Convolution kernel size: 3일 때 best
- 다른 Conv랑 비교해도, DWConv가 제일 성능 좋음

![image](https://user-images.githubusercontent.com/56949426/129715703-18905ee9-a958-4152-86ea-863306a15f25.png)
![image](https://user-images.githubusercontent.com/56949426/129715735-d8869de7-771c-4c9b-8523-4e94747c210f.png)

- 고스트 모듈 두는 위치는 여러 실험 진행
- 메모리 사용(효율성)과 성능 모두 고려해서 ‘After O’랑 ‘After FFN2’의 위치에 두기로 함.
