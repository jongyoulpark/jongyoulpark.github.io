
# jekyll 이란
- 정정 웹 사이트 생성기 (md -> html 변환)
- github 무료 호스팅.
- ruby 기반 (rbenv, gem 등 버젼/패키지 관리 툴)
- serving : `bundle exec jekyll serve`


# 아래는 기본 markdown 확인 (w/ _config.yml)
- [x] 기본 markdown 테스트.
  - [ ] 아직 LaTex 수식이 표현 안됨. (MD conversion을 손봐야하는 듯..)
- [x] side, header, footer 정리
- [x] fontsize 고정
- [x] M1 줄간격 조절

----

&nbsp;

# Markdown 1

## Markdown 2

### Markdown 3

#### Markdown 4

##### Markdown 5

###### Markdown 6

&nbsp;

# blockquotes

> This is blockquotes 1<br>
> This is blockquotes 2

> This is blockquotes 3
> 
> This is blockquotes 4

> This is blockquotes 5
>> This is blockquotes 6
&nbsp;


# orderd list

1. aaa
2. bbb
   1. ccc
   2. ddd
3. eee

2024\. years

&nbsp;

# unorderd list

- aaa
- bbb
  - ccc
  - ddd
- eee

    new line in 'eee'

- ff
    > new blockquotes line in 'ff'
- gg

    new line in 'gg'
- hh
- ii

&nbsp;

# bold, italic

normal  <!-- comment -->

**bold**

*italic*

&nbsp;

# check box

- [X] qwer
- [ ] asdf

&nbsp;

# line

---

***

&nbsp;

# LaTex 

## 1
$$
1+1=2
$$

$$
y = ax^2 + bx + c
$$

## 2

$$
x = \frac{-b \pm \sqrt{b^2 - 4ac}}{2a}
$$


$$
\frac{\partial E}{\partial w_{ij}} = \frac{\partial E}{\partial z_j} \frac{\partial z_j}{\partial w_{ij}}
$$



## 3

$$
\begin{gather*}
y = ax^2 + bx + c \\
\\ % 빈 줄 삽입

x = \frac{-b \pm \sqrt{b^2 - 4ac}}{2a}
\end{gather*}
$$

## 4
$$
\begin{align*}
y &= ax^2 + bx + c \\
\\ % 빈 줄 삽입
x &= \frac{-b \pm \sqrt{b^2 - 4ac}}{2a}
\end{align*}
$$

&nbsp;

# links


🦜️🔗 is [Langchain](https://github.com/langchain-ai/langchain "LangChain is a framework for developing applications powered by large language models (LLMs).")

[langchain_v0.2](https://python.langchain.com/v0.2/docs/introduction/)


&nbsp;

# code


``` python
import numpy as np

print('hello')
```


# end