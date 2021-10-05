# Pretrained Transformers As Universal Computation Engines

***

## Info
- ICML 2021 Spotlight
- Author: Kevin Lu, Aditya Grover, Pieter Abbeel, Igor Mordatch
- Paper link: https://arxiv.org/pdf/2103.05247.pdf
- Github: -
- Google Brain, UC Berkeley, FAIR 공동 발표

## Summary
내 요약
- 언어를 잘하는 애는 그림도 잘 이해하네.  modality transfer 가능성을 봤다!

## Detail

#### What problem are they trying to solve?
자연어로 프리트레인된 transformer는 최소의 파인튜닝으로 다른 modality에 일반화할 수 있을까?

(capability of a transformer pretrainedon natural language to generalize to other modalities with minimal finetuning)


#### What model architecture was used?

1. 배경
transformer
- NLP 모델링의 large model의 backbone 역할을 해옴
- modality transfer에 대한 가능성 탐구하고자 함(NLP -> Image, NLP -> protein )

2. 실험을 위한 환경 세팅

![image](https://user-images.githubusercontent.com/56949426/135735998-f7cf2c7c-2dc8-4b1a-9b40-924e509eac6e.png)


A. 사용 모델: **Frozen Pretrained Transformer(FPT)**
- Pretrained base GPT-2 (embedding size/hgidden dimension = 768, number of layers = 12)
- linear input, output layers만 파인튜닝 함
	- output layer: 
		- simplest possisible instantiation of an output network. 대부분의 연산이 frozen transformer에서 이루어지도록 함
		- 예를 들어 CIFAR-10의 경우 768 * 10 (output dim) = 7680 파라미터 for the weight matrix
	- input layer
		- transformer 밖의 연산량을 줄이기 위해 linear layer 사용
		- 예를 들어 CIFAR-10의 경우 768 * 16 (4 x 4 image patch) = 7680 파라미터 for the weight matrix
	- layer norm parameters
		- affine lyaer norm parameters (scale and bias) 파인튜닝함
		- GPT-2에는 블록 당 2번 layer norm이 적용되므로 총 4 x 768 (n dim) x 12 (n layers) 파라미터 나옴
	- positional embedding
		- l (sequence length) x n dim 에 대한 포지셔닝 임베딩 파인튜닝해서 작은 benefit 얻음
		- 예를 들어 CIFAR-10의 경우, 64 x 768 = 49512 파라미터 수 생성
- 모델 내 토큰 간의 모든 communication은 frozen 됨 (학습 X)
- self-attention layer가 효율적인 universal computation을 가능하게 하는 결과 얻음

( **참고**
universal computation이 가능하다는 건 다양한 modality을 넘나들며 represenation 학습이 가능하다)

#### Task & Dataset
1. Task

- 토큰을 마스킹하고 원래 토큰을 복구하는 task
	- Bit memory, Bit XOR
- 수식을 계산하는 task라는데 잘 모르겠음..
	- ListOps
- Image classificaiton
	- MNIST, CIFAR-10, CIFAR-10 LRA
- Protein sequence prediction
	- Remote homology detection

#### Result

1. **다른 modality**에 대해 pretrained language model이 **transfer**가 가능할까?

![image](https://user-images.githubusercontent.com/56949426/135736042-15fe97ec-747f-4b76-b5f6-f38503a24d1b.png)

- YES, 가능하다!
- 어떤 task에 대해서는 FPT가 LSTM에 비해 월등히 outperfrom 함
- 작은 dataset에 대해 12 layer의 transformer를 푼련시키는 건 어렵다는 걸 발견 (발산하거나 과적합 됨)
	- CIFAR-10, ListOps, CIFAR-10 LRA 의 경우 3 layer transformer 로 훈련시킴

- 최소의 spatial bias 사용함으로 inductive error로 줄인 듯


2. pretraining modality의 중요함이란?

**<pretraining mothod 3가지>**

![image](https://user-images.githubusercontent.com/56949426/135737147-1e2bdd17-2ad0-4470-991e-29c2d817389b.png)


![image](https://user-images.githubusercontent.com/56949426/135737400-912d0d9b-d842-4381-a192-952af8143213.png)
- 성능: random < bit memory< FPT
- random에 비해 모든 모델이 더 빨리 수렴함
- ViT는 Homology(protein classification task)에서는 성능이 안좋아져서 아무래도 이 테스크가 nlp랑 성격이 비슷해서 언어에서 protein으로의 transfer 가 더 자연스런 성능향상으로 이루어지지 않나 생각한다함


3. LSTM과 비교했을 때 **transformer architecture**는 얼마나 중요한가?

비슷한 아키텍쳐인 lstm을 갖고 비교

![image](https://user-images.githubusercontent.com/56949426/135737495-dea7edff-fed7-4e5c-bb9b-02f16aefd25e.png)

- 대부분 **FPT > LSTM frozen, LSTM fintuned**
- **self-attention이 universal computation을 위한 inductive bias를 효과적으로 잡는 것**으로 보임



![image](https://user-images.githubusercontent.com/56949426/135737595-fa9a2fb0-fb2d-4788-8f35-ef54cd3d6bcd.png)

- 12 layer lstm에 residual connection을 추가했을 때, 엄청한 성능 향상 보임 (residual connection이 LSTM의 정보 손실을 잘 채우는 거로 보임)
- positional embeeding 추가 시, 성능이 향상되긴 했지만 그래도 transformer에 비해 한참 성능 아래


4. language pretraining 은 random initilization에 비해 **연산 효율**을 향상시킬까?

![image](https://user-images.githubusercontent.com/56949426/135738657-786d1229-3d67-4f68-b106-f265cdced2be.png)

- YES. language pretraining은 random에 비해 **연산 효율 향상**시킴


5. frozen attention layer는 **modality 특징을 잘 반영**하는 토큰 역할을 하는가? 

![image](https://user-images.githubusercontent.com/56949426/135738807-b0a91692-e62c-4b0a-bded-0746e0f3b333.png)

- bit task에 대해서는 FPT 가 interpretable attention pattern을 보이지만 다른 task에 대해서는 그러지 않음
- 즉, **애매**하다..


6. transformer를 freezing 하는 것이 **과적합이나 과소적합**을 막을 수 있을까?

![image](https://user-images.githubusercontent.com/56949426/135738847-e475494b-0a5e-4065-8b31-4c903f996246.png)

- CIFAR-10에 대한 결과: transformer을 freezing하는 것이 **과적합 및 과소적합 막는 것**으로 보임


7. **모델 사이즈**가 커지면 성능이 향상되는가?

![image](https://user-images.githubusercontent.com/56949426/135738884-233d08cd-0073-417b-a9f5-44e5ed04193e.png)

- 모델 사이즈 커지면 성능 향상


8. 성능이 initialization 에 의한 효과는 아닐까?

![image](https://user-images.githubusercontent.com/56949426/135997250-cd8c0855-afee-4666-b6fa-a427c45e828f.png)

![image](https://user-images.githubusercontent.com/56949426/135997398-5365be67-815c-4570-a5be-652cb6def751.png)


- Pretrained: 원래 갖고 있는 GPT2 원본?
- Statiscs only: random하게 초기화안하고 oracle(fully trained transformer)분포 statistics로 초기화함
- Default: random initialization
- 결과보면 Statiscs only 일때 그리 결과가 좋지 않아서 그닥 initialization의 효과는 크지 않은 거로 보임


9. output layer만 파인튜닝해서 transformer를 훈련시킬 수 있을까?

![image](https://user-images.githubusercontent.com/56949426/135998343-01533c1f-a33e-46c7-a84c-20ea6e98041c.png)

- output layer만 학습하면 연산 속도는 빨라지지만, 성능은 떨어짐
- 별로 적절하지 않아보임

10. 더 많은 파라미터를 훈련시키는 게 성능향상에 좋을까?

![image](https://user-images.githubusercontent.com/56949426/135783690-9843a684-d4e0-4c3d-a6c1-1da151dedcad.png)

- NO!
- Feedforward 추가하는 건 성능향상에 도움이 되지만 attention layer를 추가하는 건 도움이 되지 않는 듯
- Feedforward, attention layer 다 추가하면 오히려 성능 떨어지고 있음


11. 어떤 파라미터를 파인튜닝하는 게 중요할까?

![image](https://user-images.githubusercontent.com/56949426/135783546-17b520e4-3421-4c2b-b9ab-a59113412d84.png)

- layer norm을 파인튜닝하는 게 가장 중요한 것으로 보임


12. FPT에서 **layer norm 을 파인튜닝**하는 게 성능 향상에 꼭 필요할까?

![image](https://user-images.githubusercontent.com/56949426/135782501-b56fbcdc-4421-4d42-9325-ef6555cadb19.png)

- 응, FPT 에서 layer norm을 파인튜닝하는 게 성능 향상에 도움이 됨


13. 다른 transformer 모델들은 어떨까?

![image](https://user-images.githubusercontent.com/56949426/135782758-2094b24f-4668-4ce3-97bf-c861c03428d5.png)

- transformer 계열 다른 모델들에 적용 시, T5가 다른 애들에 비해 성능이 좀 낮긴 하지만, FPT가 보였던 경향 비슷하게 보임
- ListOps task에서는 성능이 낮지만 CIFAR-10에 대해서 어느정도 성능을 보임

**Reference**

https://www.youtube.com/watch?v=2rB5aTdRTJM

https://seewoo5.tistory.com/19?category=852389

https://bair.berkeley.edu/blog/2021/03/23/universal-computation/

https://www.youtube.com/watch?v=Elxn8rS88bI
