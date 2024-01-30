---
layout: single
title: MLX로 한국어 학습 feat. Mistral 7B
date: 2024-01-30 09:50:30.000000000 +09:00
type: post
header:
    teaser: "https://ml-explore.github.io/mlx/build/html/_static/mlx_logo.png"
    image: "https://ml-explore.github.io/mlx/build/html/_static/mlx_logo.png"
categories:
- IT
tags: [LLM]
---

목표
==

1.  MLX로 한국어 데이터를 학습한다
    
2.  김용 스타일의 무협지를 쓰는 text generating AI를 만든다
    

결과: 실패
======

지만 한국어는 조금 더 잘 하게 되었다

학습 과정
-----

*   머신: M1 pro
    
*   프레임워크: MLX
    
*   학습 모델: Mistral 7B
    
*   학습 데이터: 신조협려를 JSONL 포맷으로 자체 변환
    
*   소요시간: 1시간
    
*   학습 방식: LoRa
*   lora script: _pythyon lora.py --model ./mlx\_model --train --iters 600_
    

  

학습 결과
-----

### Mistral 7B 기본 모델: 한국어를 거의 못한다
![fail](/assets/images/2024-01-30-korean-kim-yong-mlx%20copy/fail.png)

### Lora: 대답은 한다
![half](/assets/images/2024-01-30-korean-kim-yong-mlx%20copy/half-success.png)
> python lora.py --model ./mlx\_model --adapter-file ./adapters.npz --max-tokens 50 --prompt "서독 구양봉이 사용하는 무공의 위력은 "

실패 원인 추정
--------

모델의 한국어 학습 미비: 영어 원서 학습 모델([윌 듀란트 문명이야기 13권 그리스-로마  모델학습](https://huggingface.co/mlx-community/mistral-7b-v0.1-GreeceRome-v0.1))은 잘 동작함

데이터 부족: 책 8권 분량의 데이터로는 부족해보임

  

추후 과제
-----

한국어 학습을 위한 양질의 데이터 수집

MLX에 대한 보다 깊은 이해

한국어 학습 성공 사례 참고

20240130
