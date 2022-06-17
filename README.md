# Genetic-Algorithm


### 유전 알고리즘이란(GA)?
~~~
유전 알고리즘은 생물체가 환경에 적응하며 진화하는 모습을 모방하여, 최적해를 찾아내는 검색 방법이다. 
GA는 수학적으로 명확하게 이해 혹은 풀기 힘든 문제에도 적용이 가능하기 때문에, 이 경우 매우 유용하게 사용된다.
~~~

---
* 유전 알고리즘에서의 필요개념


-> 염색체(Chromosome) 는 생물학적으로 유전자들이 뭉쳐져 포장된 것이다. GA에서의 염색체는 하나의 개체 값을 의미한다.


-> 유전자(gene) 는 생물학적으로나 GA적으로나 염색체의 요소로, 유전 정보를 나타낸다.


->자손(offspring, child) 는 부모(Parent) 유전자로부터 생성된 자식 유전자이며, 이전 세대와 비슷한 (완전히 동일하지는 않은) 유전 정보를 갖게 된다.


->적합도(fitness) 어떤 염색체가 최적해에 얼마나 가까운지 나타내는 고유 값으로, 적합도의 값으로 염색체의 개체선별을 하게 된다.

---

* 유전 알고리즘의 구성


(1) 초기 염색체를 생성하는 연산


(2) 적합도를 계산하는 연산


(3) 적합도를 기준으로 염색체를 선택하는 연산


(4) 선택된 염색체들로부터 자손을 생성하는 연산


(5) 돌연변이 연산


** 보통 1번 연산 후, 2~5번 연산을 반복하는 식으로 진행된다. **

---

### 유전알고리즘 - 숫자야구

* 숫자야구 규칙

(1) 임의의 N자리 숫자를 선택한다.
(예 : N은 4, 숫자는 1234)

(2) 플레이어는 숫자를 임의로 추측한다.
(예 : 현국은 숫자를 2354로 추측하였다.)

(3) 숫자와 자릿수가 모두 일치하는 숫자의 수는 스트라이크, 자릿수는 일치하지 않지만 숫자만 일치하는 숫자의 수는 볼로 계산한다. 만약 스트라이크, 볼 모두 0이라면 아웃이다. (어떤 숫자가 스트라이크인지, 볼인지는 알려주지 않는다.)
(예 : 현국은 2354를 추측하였지만 답은 1234로, 1스트라이크 2볼이다.)

(4) 플레이어는 이 정보를 가지고 숫자를 다시 추측한다.
(예 : 현국은 2354로 1스트라이크 2볼을 얻었음으로, 4235를 시도한다.)


~~~
독립변수 : 숫자야구의 자릿수
종속변수 : 정답(최적해)까지 도달하는대에 필요한 투구수
~~~

<p align="center"><img src="https://postfiles.pstatic.net/MjAyMjA2MTdfNyAg/MDAxNjU1NDMxNTE0MzE2.u9udLDC9XYodyJFLIAD8edFnWT_FzGqM2jon4Of5TYwg.V7D5deiemLq8KXEWqImzaUZQyvoZMEvEaXA5VgpD3eMg.PNG.dyddyd4/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7(19).png?type=w773" height="300px" width="500px"></p>
 <br><br>
 
 -> 두 변수의 관계를 수학적 모형대신 그래프로 나타내 보았다.
 

---

## 코드구현

(1)초기 염색체를 생성하는 연산

```

def _generate_parent(length, geneSet, get_fitness):
chromosome_list = []
for i in range(0, 10):
    genes = []
    while len(genes) < length:
        sampleSize = min(length - len(genes), len(geneSet))
        genes.extend(random.sample(geneSet, sampleSize))
    fitness = get_fitness(genes)
    chromosome_list.append(Chromosome(genes, fitness))
return chromosome_list

```

* 정답과 같은 길이의 염색체를 10개정도 생성하여, 리스트에 저장한다. 이때 유전자의 배열은 0부터 9까지 사이 숫자의 랜덤 값이다.


(2) 적합도를 계산하는 연산

```

def get_fitness(guess, target):
fitness = 0
for expected, actual in zip(target, guess):
    if expected == actual:
        fitness += 5
    elif actual in target:
        fitness += 1

return fitness

```
* 숫자와 그 위치까지 동일하다면 스트라이크이므로 5점, 만약 위치는 같지 않고 숫자만 같다면 볼이므로 1점으로 환산하여 계산한다.


(3) 적합도를 기준으로 염색체를 선택하는 연산

```
def _generate_child(parent_list, geneSet, get_fitness):
child_list = []
fitness_percent_list = []
fitness_accum_list = []
fitness_sum = 0
for parent in parent_list:
    fitness_sum += parent.Fitness

for parent in parent_list:
    fitness_percent_list.append(parent.Fitness / fitness_sum)

fitness_sum = 0
for fitness_percent in fitness_percent_list:
    fitness_sum += fitness_percent
    fitness_accum_list.append(fitness_sum)

# Selection
for i in range(0, 10):
    rand = random.random()
    before = 0
    for j in range(0, len(fitness_accum_list)):
        if rand > before and rand <= fitness_accum_list[j]:
            child_list.append(copy.deepcopy(parent_list[j]))
            break
        before = fitness_accum_list[j]

# --- 생략 ---

```
* 부모들로부터 계산된 적합도 값을 모두 더한 후, 백분율로 환산하여 축적하여 저장한다. 이후 난수를 돌려서 걸린 염색체를 자식 염색체로 지정한다. 이게 바로 룰렛 휠 방식이다.


(4) 선택된 염색체들로부터 자손을 생성하는 연산

```
def _generate_child(parent_list, geneSet, get_fitness):
# --- 생략 ---

# Crossover
crossover_rate = 0.20
selected = None
for i in range(0, len(child_list)):
    rand = random.random()
    if rand < crossover_rate:
        if selected is None:
            selected = i
        else:
            child_list[selected].Genes[2:], child_list[i].Genes[2:] = \
                child_list[i].Genes[2:], child_list[selected].Genes[2:]
            selected = None

    # update
    child_list[i].Fitness = get_fitness(child_list[i].Genes)

# --- 생략 ---
```
* 선택한 염색체 중 랜덤으로 선택하여 유전자를 교배 시킨다. Single Point Crossover 방식으로, 유전자, 즉 숫자의 일부를 두 유전자끼리 교환시킨다. Crossover Point는 임의로 지정하였다.



(5) 돌연변이 연산

```
def _generate_child(parent_list, geneSet, get_fitness):
# --- 생략 ---

# mutate
mutate_rate = 0.2
for i in range(0, len(child_list)):
    rand = random.random()
    if rand < mutate_rate:
        child = _mutate(child_list[i], geneSet, get_fitness)
        del child_list[i]
        child_list.append(child)
return child_list
```
* 기존의 염색체들로만 교배를 한다면 지역 최적점, 즉 원하는 값에 도달할 수가 없기 때문에, 광역 최적점에 도달하기 위해서는 돌연변이 연산을 섞어줘야 한다. 일정 확률로 돌연변이를 발생 시킨 후, 유전자의 일부를 치환하였다.

~~~
유전 알고리즘에서 돌연변이는 어떤식으로 발생하는가? -> 유전자의 어떤 부분의 값을 강제적으로 바꾸고, 유전자 집단으로서의 다양성, 결국 흩어짐을 크게 한다. 이렇게 함으로써 보다 좋은 해를 가지는 개체의 발생을 기대하는 것이다.

확률이 너무 높거나 낮으면 어떤 현상이 발생하는가? -> 돌연변이의 비율을 너무 크게 하면 나쁜 방향으로 변이의 확률도 크게 되고, 해를 좀처럼 구할 수 없게 된다.
~~~


<p align="center"><img src="https://blogfiles.pstatic.net/MjAyMjA2MTdfMjI0/MDAxNjU1NDc0ODE3NDQ3.cRxhgdo2jRhcD74j0WNpSWiSIj8n8Hs02DLidoUyoBAg.HUChExa7f44kzhQbiTzuqWkBx03pycAROJx1bo2AT7gg.PNG.dyddyd4/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7(30).png" height="400px" width="700px"></p>
 <br><br>
 * 숫자야구 자릿수 : 3


 <p align="center"><img src="https://blogfiles.pstatic.net/MjAyMjA2MTdfOTgg/MDAxNjU1NDc0ODE3NDU0.YG8KjQ-t4goQsKUUXw64c8WR99sf_K8pAub2qiL4joAg.eQxsXsx6rpp65gXm5z_n3owQLH-8X3e1vDq_ESFmNAYg.PNG.dyddyd4/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7(29).png" height="400px" width="700px"></p>
 <br><br>
 * 숫자야구 자릿수 : 4

 <p align="center"><img src="https://blogfiles.pstatic.net/MjAyMjA2MTdfMjYg/MDAxNjU1NDc0ODE3NDU1.UcnvzrIbgQ6_7J8HFY3YxfeCfB1xzJgZciJ8GyJlqpcg.tKyGoCdLugy9VpXZX06m-37XydGZRqYkoxFzM_z8yzcg.PNG.dyddyd4/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7(28).png" height="400px" width="700px"></p>
 <br><br>
 
 * 숫자야구 자릿수 : 5
 
 <p align="center"><img src="https://postfiles.pstatic.net/MjAyMjA2MTdfMjA0/MDAxNjU1NDc0ODE3NDU1.tPGvZpGJHm9Yf2OK_f7fNN1S1vyFMVj5qco5XAxRGqog.4-0qVRuk7uJxCpaB2fAQZrgS4O80Ws4Jd59lwm424tIg.PNG.dyddyd4/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7(27).png?type=w773" height="400px" width="700px"></p>
 <br><br>
 
 
 <p align="center"><img src="https://postfiles.pstatic.net/MjAyMjA2MTdfMjIy/MDAxNjU1NDc0ODE3NDU4.7jZt58uNrtSO5Jlb6l1ueNXLCpJNuPO0OAN87DZ2GAog.FeYo8qOF2CKbSIYvGHFxEYPsSTtQQaba_3CGI_C67hUg.PNG.dyddyd4/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7(26).png?type=w773" height="400px" width="700px"></p>
 <br><br>
 
 * 숫자야구 자릿수 : 6


 ~~~
 자릿수가 클수록 정답을 위한 투구수도 많아진다.
 ~~~

## 최적화 과정 그래프
<p align="center"><img src="https://postfiles.pstatic.net/MjAyMjA2MTdfMTYx/MDAxNjU1NDc3NjcxNzc2.Ec4oG8hGk3R5Y69XTVnsR__BAZTvTXeUQAEOImVBDUMg.hFefF4cHK34BLHv5m8MqEW27gkpli4j0w3XNxpjP7G0g.PNG.dyddyd4/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7(32).png?type=w773" height="400px" width="700px"></p>
 <br><br>

