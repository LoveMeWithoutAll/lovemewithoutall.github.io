---
layout: single
title: Learning Korean with MLX feat. Mistral 7B
date: 2024-01-30 09:50:30.000000000 +09:00
type: post
header:
    teaser: "https://ml-explore.github.io/mlx/build/html/_static/mlx_logo.png"
    image: "https://ml-explore.github.io/mlx/build/html/_static/mlx_logo.png"
categories:
- IT
tags: [LLM]
---

Goal
==

1.  Train on Korean data with MLX
    
2.  Build a text-generating AI that writes wuxia novels in the style of Jin Yong
    

Result: Failure
======

But it did get a little better at Korean

Training Process
-----

*   Machine: M1 Pro
    
*   Framework: MLX
    
*   Training model: Mistral 7B
    
*   Training data: The Return of the Condor Heroes, converted into JSONL format myself
    
*   Time spent: 1 hour
    
*   Training method: LoRa
*   lora script: _pythyon lora.py --model ./mlx\_model --train --iters 600_
    

  

Training Results
-----

### Mistral 7B base model: barely speaks Korean
![fail](/assets/images/2024-01-30-korean-kim-yong-mlx%20copy/fail.png)

### Lora: it does respond
![half](/assets/images/2024-01-30-korean-kim-yong-mlx%20copy/half-success.png)
> python lora.py --model ./mlx\_model --adapter-file ./adapters.npz --max-tokens 50 --prompt "서독 구양봉이 사용하는 무공의 위력은 "

Presumed Cause of Failure
--------

Insufficient Korean training of the model: a model trained on the original English text ([Will Durant's The Story of Civilization, Volume 13, Greece-Rome model training](https://huggingface.co/mlx-community/mistral-7b-v0.1-GreeceRome-v0.1)) works well

Lack of data: 8 books' worth of data seems insufficient

  

Future Tasks
-----

Collect high-quality data for Korean training

Gain a deeper understanding of MLX

Review successful Korean training examples

20240130
