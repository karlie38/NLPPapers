# SimCLS: A Simple Framework for Contrastive Learning of Abstractive Summarization

***

## Info
- ACL-IJCNLP 2021 Oral Session
- Author: Yixin Liu, Pengfei Liu
- Paper link: https://arxiv.org/abs/2106.01890
- Github: https://github.com/yixinL7/SimCLS

## Summary
내 요약
- Abstract summarization: Generate then evaluate 2 step framework

## Detail

#### What problem are they trying to solve?
Abstract summarization (생성 요약)


#### What model architecture was used?
![image](https://user-images.githubusercontent.com/56949426/129464478-b1111499-c2a9-429f-a4a3-f47e1d6907f1.png)

- **2 Step Framework**
1. Candidate generation 
	- Seq2Seq generator 로 요약 생성 (pretrained BART, Pegasus 사용)
2. Reference-free evaluation
	- 더 좋은 summary candidate은 요약하고자 하는 source document와의 유사도가 높은 것이라는 전제
	- 여러 후보군들 중에 source docoment와의 코싸인 유사도가 젤 높은 candidate을 택함
	- Contrastive learning (비슷한 클래스 간 비슷하게 represent되도록 인코딩하는 기법) 사용
	- RoBERTa로 인코딩

#### Dataset
	- CNN/DailyMail: large scale news articles dataset
	- XSum: highly abstractive dataset containing online articles from the BBC

#### How did they do training?
	- Pretrained BART, Pegasus 사용
	- Diverse beam search as sampling strategy: 16. 즉 16개의 요약 candidate 생성
	- Evaluation model: Adam optimizer with learning rate scheduling
	- Metric으로 Rouge Score랑 semantic similarity를 측정하기 위해 BS: BertScore, MS: MoverScore 도 사용

#### Result
CNN/Daily Mail dataset 결과
![image](https://user-images.githubusercontent.com/56949426/129464598-136fbb04-5e44-4922-abd6-12dbbc1d0dc9.png)

	- SimCLS 젤 성능 높음
	- Generator로 pretrained BART 사용


XSum 결과
![image](https://user-images.githubusercontent.com/56949426/129464627-286bba70-8050-43df-ad19-e6b44538c0eb.png)

	- SimCLS 젤 성능 높음
	- Generator로 pretrained Pegasus 사용

