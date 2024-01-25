---
layout: single
title: stable diffusion with mlx
date: 2024-01-07 09:50:30.000000000 +09:00
type: post
header:
    teaser: "https://ml-explore.github.io/mlx/build/html/_static/mlx_logo.png"
    image: "https://ml-explore.github.io/mlx/build/html/_static/mlx_logo.png"
categories:
- IT
tags: [stable diffusion]
---

목표: 애플 실리콘으로 stable diffusion 2를 돌려보자
=====================================

사용 도구
-----

### MLX

[https://ml-explore.github.io/mlx/build/html/index.html](https://ml-explore.github.io/mlx/build/html/index.html)

### M1 Pro 16G

  

Test
----

커스터마이징 없이 apple이 제공한 순정 그대로 구동

### 1

> python txt2image.py "A logo of an shy baby bird who have yellow color." --n\_images 4 --n\_rows 2

![bird](/assets/images/2024-01-07-stable-diffusion-with-mlx/out_bird.png)
  

### 2

> python txt2image.py "A logo of an shy baby bird who have yellow color with cartool style." --n\_images 1

![logo](/assets/images/2024-01-07-stable-diffusion-with-mlx/out_logo.png)

### 3

> python txt2image.py "cartoon character of a person with a hoodie , in style of cytus and deemo, ork, gold chains, realistic anime cat, dripping black goo, lineage revolution style, thug life, cute anthropomorphic bunny, balrog, arknights, aliased, very buff, black and red and yellow paint" --n\_images 1

![cartoon](/assets/images/2024-01-07-stable-diffusion-with-mlx/out_cartoon.png)

### 4

> python txt2image.py "realistic futuristic city-downtown with short buildings, sunset" --n\_images 1

![city](/assets/images/2024-01-07-stable-diffusion-with-mlx/out_city.png)


결론
--

프롬프트 장인의 도움이 필요합니다

---
20240107
