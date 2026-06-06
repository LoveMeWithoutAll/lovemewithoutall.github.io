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

Goal: Build a local LLM on Apple Silicon
============================

Tools Used
-----

### mistral

[https://mistral.ai/](https://mistral.ai/)

  `Mistral-7B-v0.1`

4-bit quantization

### MLX

[https://ml-explore.github.io/mlx/build/html/index.html](https://ml-explore.github.io/mlx/build/html/index.html)

### M1 Pro 16G

Work
==

Quantization
---

Converting mistral 7B to 4-bit: took less than 30 minutes

Fine-tuning
----

### Lora

A dataset of 1000 entries provided by Apple (data borrowed from WikiSQL [https://github.com/ml-explore/mlx-examples/tree/main/lora/data](https://github.com/ml-explore/mlx-examples/tree/main/lora/data))

Notable point about data preparation: it must strictly follow the predefined jsonl format

Parameters set to the defaults from Apple's example

> _batch size: 4_
> 
> _iteration: 600_
> 
> _lora layer: 16_
> 
> _and so on..._

Time taken: 5 hours

Note: Since I ran the fine-tuning alongside other tasks (development using IntelliJ, Chrome DevTools, etc.), the time could be reduced if you run only the training job

### Results

##### test 1

When I increased max-token from 50 to 100 in Apple's example, it kept repeating the same answer

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

When I asked about content that was in the training dataset, it answered well

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

  

Conclusion
==

A poor man's local LLM

-----

20240107
