# Synthesizer: Rethinking Self-Attention for Transformer Models

***

## Info
- ICML 2021 Spotlight
- Author: Yi Tay, Dara Bahri, Don Metzler, Da-Cheng Juan, Zhe Zhao, Che Zheng
- Paper link: https://arxiv.org/abs/2005.00743
- Github: https://github.com/leaderj1001/Synthesizer-Rethinking-Self-Attention-Transformer-Models

## Summary
내 요약
- self-attention 내 dot product는 필요하지 않을 수 있다.

## Detail

#### What problem are they trying to solve?
self-attention 내 dot product를 대체할 수 있을까


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

- 기본 R,D는 Transformer보다 더 좋은 성능이 나오지 않았음
- Mixture들이 좋은 성능을 보임


#### Conclusion



