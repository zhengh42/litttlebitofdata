---
title: "生活中的贝叶斯：谈医院检查的必要性"
date: '2017-08-16'
slug: bayesian_in_life_series1
categories: ["data at fingertips"]
tags: ["Bayesian","statistics","生活里的统计学"]
---

不少人抱怨去医院看病各种检查太多，但事实上，仅通过一次检查常常是确诊不了疾病的，通常至少需要两三次检查。为什么呢？本文从Bayes' theorem谈谈这个问题。

假设你去医院做了某种疾病的检测，该病在人群里的发病率为0.001，即1000人里只有一人患病。这种检测手段的灵敏度高达99%，也就是说，100个患者，99人的检测结果是阳性的，只有一例被漏掉。另外，检测有5%的假阳性率，也就是说，100个健康人里，5个人的检测结果是阳性。

如果你得知你的检测结果是阳性——不要太过恐慌，你实际患病的概率并不是99%，不是95%，甚至连5%都不到——在这个例子里，只有1.9%。基于这样的概率，贸然开展治疗并不明智，通常会继续进行检查直至确诊。

为什么检测结果是阳性，患病的概率只有1.9%呢？因为要考虑到

- 该病在普通人群中只有0.001的发病率
- 该检测高达5%的假阳性率

也就是说，检测结果是阳性的人群里，其实大部分都是假阳性，并未患病。


|                   | disease                   | healthy              |                  |
|    ---------      | ---------                 |   ---------              |  ---------    |
|__test positive__  | __True positive__ 9,900   | __False positive__ 499,500  | 509,400          |
|__test negative__  | __False negative__ 100    | __True negative__ 9,490,500 | 9,490,600        |
|                   | 10,000                    | 9,900,000                   | 10,000,000       |



假设有10,000,000人接受了检测，按照0.001的发病率，0.99的检出率（sensitivity）和0.05%的假阳性率，可以得到四格的数字。其中， 

- True positive (TP) 患病，检查阳性
- False positive (FP) 未患病，检查阳性
- False negative (FN) 患病，检查阴性
- True negative (TN) 未患病，检查阴性

所以，一个人的检查结果是阳性，这个人患病的概率是多少呢？

统计学上这叫做precision. Precision=TP/(TP+FP)=1.9%.

----------------------------------------------------------------------------------------------------------

这个问题也可以用Bayes' theorem来解释。用`$θ=disease$`和`$θ=healthy$`表示患病与否，用 `$D= + $`和 `$D= - $`表示检测结果阳性或者阴性。

- `$p(θ=disease) = 0.001$` 发病率
- `$p(D= + | θ=disease) = 0.99$` 如果患病，检测阳性的概率。相应地；
- `$p(D= - | θ=disease) = 1- p(D= + | θ=disease) = 0.01$` 如果患病，检测阴性的概率
- `$p(D= + | θ=healthy) = 0.05$` 如果未患病，检测阳性的概率。相应地；
- `$p(D= - | θ=healthy) = 1- p(D= + | θ=healthy) = 0.95$` 如果未患病，检测阴性的概率。

根据Bayes' theorem，在检测阳性的情况下，患病的概率

`$p( θ=disease | D= +) = $`
`$$\frac {p(D= + | θ=disease) * p(θ=disease)} {p(D= +)} $$`

`$p(D= +)$`怎么计算呢？在患病和未患病的人群里都会出现检测阳性的情况，于是`$p(D=+)$`等于两种情况的概率相加：

`$p(D= +)$`  
`$= p(D= +, θ=disease) + p(D= +, θ=healthy)  $`  
`$= p(D= + | θ=disease) * p(θ=disease) + p(D= + | θ=healthy) * p(θ=healthy)  $`  
`$= 0.99 * 0.001+0.05*0.999  $`

所以, `$p( θ=disease | D= +) = 0.99*0.001/ (0.99*0.001 + 0.05*0.999 ) = 0.019$`

如果将这里的Bayes公式写完整：


`$p( θ=disease | D=+) =$`
`$$\frac {p(D= + | θ=disease) * p(θ=disease)}{p(D= + | θ=disease) * p(θ=disease) + p(D= + | θ=healthy) * p(θ=healthy)} $$`

和四格表对比，其实分子表示的就是TP，分母表示的就是就是TP+FP。

Bayes' theorem，简而言之，就是我们事先对一件事情有一个看法（prior），然后我们看到了一些数据（data），这些数据会更新我们的看法（posterior）。

在这个例子中，prior的看法是，人群中随机一个人，患上这种病的概率是0.001。接着我们看到了这个人的检测结果是阳性，基于这样的数据，我们认为这个人患病的概率增加了一些，到0.019。

----------------------------------------------------------------------------------------------------------

0.019的患病概率并不足以确诊，怎么办呢？再进行一次或多次检测（通常是不同的检测）。

那么问题来了。假设你的第一次检测是阳性，我们现在知道了患病概率由普通人群的0.001变成了0.019。为了确诊你又去做了第二次检测，如果第二次检测的结果是阴性，那么患病概率是多少？如果第二次检测的结果是阳性呢？这里假设第二次检测的检出率和假阳性率和第一次检测相同。

这个问题同样可以用类似四格表的方式列出：

第一次检测阳性，第二次检测阴性：

|                     |  Disease                    | Healthy                     |
|    ---------        | ---------                   |   ---------                 |  
|                     |  p(θ=disease) = 0.001       | p(θ=healthy) = 0.999        |
| Total number        | 10,000                      | 9,990,000                   |          
|                     | p(D= + \| θ=disease) = 0.99 | p(D= + \| θ=healthy) = 0.05 | 
| First test positive | 9,900                       | 499,500                     |
|                     | p(D= - \| θ=disease) = 0.01 | p(D= - \| θ=healthy) = 0.95 | 
|Second test negative | 99                          | 474,525                     |

第一次检测阳性，第二次检测阴性的人群里，患病的概率看最后一栏就可以了：`$99/(99+474525)=0.0002$`。这个数字甚至低于普通人群的发病率。

第一次检测阳性，第二次检测阳性：

|                     |  Disease                    | Healthy                     |
|    ---------        | ---------                   |   ---------                 |  
|                     |  p(θ=disease) = 0.001       | p(θ=healthy) = 0.999        |
| Total number        | 10,000                      | 9,990,000                   |          
|                     | p(D= + \| θ=disease) = 0.99 | p(D= + \| θ=healthy) = 0.05 | 
| First test positive | 9,900                       | 499,500                     |
|                     | p(D= + \| θ=disease) = 0.99 | p(D= + \| θ=healthy) = 0.05 | 
|Second test positive | 9,801                       | 24,975                      |


第一次检测阳性，第二次检测也是阳性的人群里，真正患病的概率上升到了`$9801/(9891+24975)=0.28$`.

也就是说，第一次检测阳性，患病概率从普通人群的0.1%更新到1.9%；第二次检测阳性，患病概率更新到28%。类似的计算可得出，如果第三次检测仍是阳性，患病概率才会更新到88%以上。所以，为了确诊疾病，多种检查通常是必要的。

同样地，Bayes公式也能用于计算第一次检测阳性，第二次检测也是阳性的人群里，真正患病的概率。

`$p( θ=disease | D2=+) =$`
`$$\frac {p(D2= + | θ=disease) * p(θ=disease)}{p(D2= + | θ=disease) * p(θ=disease) + p(D2= + | θ=healthy) * p(θ=healthy)} $$`

只不过这里面的`$p(θ=disease)$`要用经过第一次检测阳性更新过的患病概率（0.019），而不是普通人群的患病概率（0.001）。也就是第一次检测后的posterior要作为第二次检测的prior。公式中其他的概率`$p(D2= + | θ=disease)$`和`$p(D2= + | θ=healthy)$` 描述的是这个检测自身的性质，不受发病率变化的影响。

`$p( θ=disease | D2= +) = 0.99*0.019/ (0.99*0.019+0.05*(1-0.019)) =~ 0.28$`

当然，如果第二次检测的假阳性率低一些，只有0.01，那么

`$p( θ=disease | D2= +) = 0.99*0.019/ (0.99*0.019+0.01*(1-0.019)) =~ 0.66$`

----------------------------------------------------------------------------------------------------------

所以，如果正规医院里医生让你去做多种检查，不要再有那么多抱怨啦。只有确诊了疾病，才好开展治疗，不然要是没病的话反而要经受治疗，不仅浪费医疗资源，自身也担惊受怕承受痛苦。多种检查并非过度医疗，反而是为了避免过度医疗。
