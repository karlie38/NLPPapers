# Synthesizer: Rethinking Self-Attention for Transformer Models

***

## Info
- ICML 2021 Spotlight
- Author: Yi Tay, Dara Bahri, Don Metzler, Da-Cheng Juan, Zhe Zhao, Che Zheng
- Paper link: https://arxiv.org/abs/2005.00743
- Github: https://github.com/leaderj1001/Synthesizer-Rethinking-Self-Attention-Transformer-Models

## Summary
내 요약
- self-attention 내 dot product가 유의미하지 않을 수도 있다. 이를 대체하는 Synthesizer

## Detail

#### What problem are they trying to solve?
self-attention 내 dot product(quadratic interaction)를 대체할 수 있을까


#### What model architecture was used?

#####  Synthesizer Model

- attention의 query-key-value 없애고 aglignment matrix로 바로 합성한 것

![image](https://user-images.githubusercontent.com/56949426/134122885-ec5d18b5-1684-4f3d-94ee-4d8c85b69ea6.png)


1. Dense Synthesizer

![image](https://user-images.githubusercontent.com/56949426/134158169-9f12068d-a04f-4737-aa2d-e1828706ac30.png)


- 위의 그림처럼 L(sequence length)가 5일 때, 각 토큰마다 F를 거쳐 vector (초록색 라인)를 만들어 냄으로 B(L x L matrix) 생성

![image](https://user-images.githubusercontent.com/56949426/134158136-3b55d473-840b-43ed-ac73-3b85085b3b3f.png)

- B matrix에서 각 토큰으로 부터 생성된 column 끼리는 서로 independent (독립적)

![image](https://user-images.githubusercontent.com/56949426/134158835-51950149-18b3-4335-ba8b-a7236e11ecde.png)

- F는 2 layered feed-forward layer with ReLU activations
- 각 토큰은 input sequence의 각 토큰의 weight을 예측(즉, input에 의존적임)

2. Random Synthesizer
- Dense Synthesize랑 구조는 비슷함
- 다만 attention weights이 random 값으로 초기화되고(즉, input에 의존적이지 않음) 그 값들은 학습 가능하거나 고정도 가능

3. Factorized Model
- 기본 Transformer에서의 Query, Key projection 과정을 생략하기 때문에 Synthesizer는 파라미터 절약
- 그럼에도 L(sequence length)가 너무 길 때,  모델이 무거우므로 factorized variation을 통해 모든 Synthesizer에 적용하여 이 문제를 해결

	A)  Factorized Dense Synthesizer
	- Factorized output은 파라미터 cost도 줄일 수 있고 overfitting 방지에도 도움을 줌
	![image](https://user-images.githubusercontent.com/56949426/134125018-73857372-2a0c-4ad6-a45c-c6820fc91f18.png)

	B) Factorized Random Synthesizer
	- 각 head는 파라미터 saving: N**2 -> 2 (Nk) (k << N) 감소, overfitting 방지도 가능 (논문에서 k=8로 잡음)
	![image](https://user-images.githubusercontent.com/56949426/134125340-795e143c-ed8c-4ada-89e3-20665f22e240.png)

	C) Mixture of Synthesizers


- **위에 설명한 Synthesizer와 dot-product 특징 정리 요약 표**
![image](https://user-images.githubusercontent.com/56949426/134125869-66e336f4-ad7e-4ead-aa26-5984731f6cad.png)


#### Task & Dataset
	-  (1) machine translation (EnDe, EnFr) 
	-  (2) autoregressive language modeling (LM1B) 
	-  (3) text generation (summarization and dialogue modeling)
	-  (4) multi-task natural language processing (GLUE/SuperGLUE)


#### Result
R: Random

D: Dense

V: vanilla dot product attention

Fix: Fixed Random

FR: Factorized Random

FD: Factorized Dense

+: Mixture Synthesizers

**Text Generation task**

![image](https://user-images.githubusercontent.com/56949426/134126584-3e948ae0-20b7-4e4c-b670-b7cc650a6a1d.png)


- CNN/Dailymail dataset에 대해 abstractive summarization task에선 D+V 가 성능 최고
- Dialogue generation task 에서는 D가 성능 최고

**Machine translation (EnDe, EnFr)**

![image](https://user-images.githubusercontent.com/56949426/134282608-44ac2229-fb0e-4b00-849f-a2717ae2940a.png)


- A. 노란색: Transformer (자기내들이 돌린 거) -> Synthesizer (Dense)
	a) Parameter 수 감소: 67 M -> 62 M 
	b) EnDe BLEU 점수 27.67 -> 27.43 ( 꽤나 근사)
- B. 초록색: Synthesizer (Random + Vanilla) 성능 최고
	a) 즉, 원래 Transformer 에 Random을 붙여서 실험. 하지만 파라미터 수는 73 M으로 크게 증가했음
	b) Synthesizer (Dense + Vanilla) 의 파라미터 수는 74 M 으로 더 크지만 성능은 감소했음
	
**Multi-task language understanding benchmark (GLUE and SuperGLUE)**

![image](https://user-images.githubusercontent.com/56949426/134283403-c85c60bb-c027-4c18-bbb2-d8d0472dc69f.png)


- R, D Synthesizer 는 크게 성능 향상을 이루지 x
- 논문에서는 이 이유를 T5의 인코더 self-attention이 문장 간 어텐션 역할(cross-sentence attention)을 하기 때문일꺼라 추측 ( 더 문맥적 의미를 잘 파악해서)
- T5(Base)나 Synthesizer(R + V) 가 각 task에서 높은 성능을 대체적으로 보임


**Reference**
- https://www.youtube.com/watch?v=q7QP_lfqnQM&t=1281s (paper review done by Yannic Kilcher)
- https://seewoo5.tistory.com/2 (paper review done by seewoo5)