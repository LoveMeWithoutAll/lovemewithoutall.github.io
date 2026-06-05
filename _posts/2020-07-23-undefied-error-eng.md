---
layout: single
title: "Dev Diary - An Error with No Identifiable Cause"
date: 2020-07-23 08:23:30.000000000 +09:00
type: post
header:
    teaser: "https://images2.minutemediacdn.com/image/upload/c_crop,h_1706,w_3036,x_0,y_241/f_auto,q_auto,w_1100/v1554752078/shape/mentalfloss/istock-483749258.jpg"
    image: "https://images2.minutemediacdn.com/image/upload/c_crop,h_1706,w_3036,x_0,y_241/f_auto,q_auto,w_1100/v1554752078/shape/mentalfloss/istock-483749258.jpg"
categories:
- IT
tags: [dev]
---

Today was a truly strange day. As always, a very simple development request came in, and I implemented the feature in just a few minutes. There were no problems at all. But that wasn't the end of it. The feature sitting right next to the one I had just touched stopped working properly. There wasn't even an error message. It just sat there quietly, as if it had never worked in the first place. Yet judging by the way the data had piled up, it was clear that the feature had been working just fine. I had a feeling I wouldn't be able to find the cause of the error anytime soon. In situations like this, the best move is to quickly roll everything back. But even after rolling back, the error in question still wasn't resolved. Had the rollback not gone through properly? I double-checked and rolled it back again several times, but to no avail. If nothing has changed, then there shouldn't be any problem either. This is where hell begins. There shouldn't be a problem, yet there is one. I can't figure out the cause. My faith in myself starts to waver. All I can do is tear my hair out.

Fortunately, I've had this kind of experience a few times. Even when nothing appears to have changed, there are definitely changes that not even a file comparison tool or a version control tool can catch. The file's encoding, whitespace at the end of the file, whatever it may be. In any case, development is an extraordinarily subtle thing, so unexpected and astonishing things can happen at any time. And the solution in these cases is very simple. You just build it again from scratch. Since you've already built it once, it doesn't even take that long. Then the problem gets resolved, and even so, I'm left feeling like a fool, like I've been toyed with.

Today, too, the problem was resolved that way. The newly added feature naturally works just fine. But still, the aftertaste isn't pleasant. The reason I chose software development for a living was that I didn't want situations like today's, where the answer is unknowable. Should I at least count it as fortunate that cases like this don't happen often? Or should I count it as fortunate that there's a solution at all, however inelegant? It leaves a bitter taste.

20200720
