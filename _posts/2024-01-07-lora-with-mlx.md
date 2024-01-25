---
layout: single
title: Lora with MLX
date: 2024-01-07 09:50:30.000000000 +09:00
type: post
header:
    teaser: "https://ml-explore.github.io/mlx/build/html/_static/mlx_logo.png"
    image: "https://ml-explore.github.io/mlx/build/html/_static/mlx_logo.png"
categories:
- IT
tags: [LLM]
---

목표: 애플 실리콘으로 local LLM을 구축하자
============================

사용 도구
-----

### mistral

[https://mistral.ai/](https://mistral.ai/)

  `Mistral-7B-v0.1`

4bit 양자화

### MLX

[https://ml-explore.github.io/mlx/build/html/index.html](https://ml-explore.github.io/mlx/build/html/index.html)

### M1 Pro 16G

작업
==

양자화
---

mistral 7B의 4bit 변환: 30분 미만 소요

파인튜닝
----

### Lora

apple이 제공한 데이터셋 1000개(WikiSQL에서 퍼온 데이터 [https://github.com/ml-explore/mlx-examples/tree/main/lora/data](https://github.com/ml-explore/mlx-examples/tree/main/lora/data))

데이터 준비 특이사항: 반드시 jsonl의 사전에 정해진 포맷으로 맞춰야 함

애플이 제공한 예제의 기본값으로 파라미터 설정

> _batch size: 4_
> 
> _iteration: 600_
> 
> _lora layer: 16_
> 
> _등등..._

소요시간: 5시간

주의: 파인튜닝과 다른 작업(intelliJ, 크롬 개발자 도구 등을 사용한 개발) 병행했기에 학습 작업만 시킨다면 시간 단축 가능

### 결과

##### test 1

애플이 제공한 예제에서 max-token을 50 → 100으로 늘렸더니 같은 대답을 반복함

###### prompt

> _python lora.py --model ./mlx\_model --adapter-file ./adapters.npz --max-tokens 100  --prompt "table: 1-10015132-16 columns: Player, No., Nationality, Position, Years in Toronto, School/Club Team_
> 
>   
> 
> _Q: What is terrence ross' nationality_
> 
> _A: "_

###### answer

> _table: 1-10015132-16 columns: Player, No., Nationality, Position, Years in Toronto, School/Club Team_
> 
>   
> 
> _Q: What is terrence ross' nationality_
> 
> _A: Terrence Ross's nationality is Canadian._
> 
>   
> 
> _Q: What is the nationality of terrence ross?_
> 
> _A: The nationality of terrence ross is Canadian._
> 
>   
> 
> _Q: What are the nationality of terrence ross?_
> 
> _A: The nationality of terrence ross is Canadian._
> 
>   
> 
> _Q: What nationality is terrence ross?_
> 
> _A: The nationality of terrence ross is Canadian._
> 
>   
> 
> _Terrence_

##### test 2

학습한 데이터셋에 있는 내용을 질문하니 잘 대답함

###### prompt

> python lora.py --model ./mlx\_model --adapter-file ./adapters.npz --max-tokens 50  --prompt "table: 1-10361625-1 columns: Round, Choice, Overall, Player name, position, College
> 
> Q: What round was Bill Hill drafted?  
> A: "

###### answer

> table: 1-10361625-1 columns: Round, Choice, Overall, Player name, position, College
> 
>   
> 
> Q: What round was Bill Hill drafted?
> 
> A: 1-10 (Overall #113)
> 
>   
> 
> SEARCH : 1-10 (Overall #113)NEXT QUESTION: What was the position of Bill Hill?
> 
> BEFORE TABLE: 1

  

결론
==

가난한 자의 local llm

-----

20240107
