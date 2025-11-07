Algoritmo de Rabin-Karp 
======

## O Problema da Busca em Texto

Vamos começar definindo o problema: 
* Temos um Texto (uma longa sequência de caracteres).
* E um Padrão (uma sequência menor). 

Queremos encontrar a Posição (o índice)
onde o padrão aparece pela primeira vez no texto.

??? Problema da Busca

Só para garantir que estamos na mesma página, vamos usar um exemplo.

Texto = "o rato roeu a roupa do reide roma" **padrão** = "roupa"

Se o primeiro caractere 'o' está no índice 0, qual é a saída (o índice da primeira letra do **padrão**) esperada?

::: Gabarito

Resposta = 14

:::

???

## A Abordagem "Força Bruta"

A primeira solução que vem à mente é a "força bruta". A ideia é simples: "deslizar" o padrão pelo texto, 
uma posição de cada vez, e comparar caractere por caractere.

Vamos ver isso em ação com nosso exemplo. O **padrão** é "roupa" (tamanho 5).

:rato

??? Checkpoint

Vamos pensar no custo disso. Considere um Texto com n caracteres e um Padrão com **M** caracteres.

No pior caso possível (ex: Texto = "AAAAAAAAAAAAAAAAAB" e Padrão = "AAB"), quantas comparações de caracteres o algoritmo de força bruta teria que fazer? 

::: Gabarito

O algoritmo teria que "deslizar" a janela N−M+1 vezes. Em cada uma dessas posições, ele poderia ter que comparar até **M** caracteres.

Isso nos dá uma complexidade de $O((N−M+1)×M)$, que simplificamos para $O(N×M)$.

:::
???

??? Checkpoint

Imagine um cenário real, como um sistema de verificação de plágio.

    Texto (base de dados inteira) com **N = 1.000.000.000** caracteres.

    Padrão (frase do aluno) com **M = 100** caracteres.

Uma complexidade de $O(N×M)$ parece eficiente? 

::: Gabarito

Definitivamente não. É um custo computacional altíssimo. Precisamos de uma forma mais rápida de descartar as posições que não dão match.

:::
???

---------
## A Ideia Principal: Hashing

**Hashing** é uma ideia matemática que transforma o texto em números.  A melhor forma de pensar nisso é como se fosse uma “impressão” digital” de um texto. O algoritmo pega uma string, que pode ser longa, e converte em um único numero.
A regra de ouro do hashing é: se duas strings são idênticas, seus hashes têm que ser o mesmo. Isso nos leva a uma conclusão muito importante, que é o segredo da velocidade do algoritmo, de que se dois hashes são diferentes, temos 100% de certeza que as strings também são diferentes.

Em vez de comparar cada caractere do texto com o padrão, o algoritmo compara apenas os **valores hash**,  
o que é muito mais rápido.

---
??? Para os exercicios a seguir adote:

**Valores para cada caractere:**
| Posição | Hashcode |
|---------:|:---------|
| 1       | A        |
| 2       | B        |
| 3       | C        |
| 4       | D        |
| 5       | E        |
| …       | …        |

**Texto:** N = 6
| Índice | 0 | 1 | 2 | 3 | 4 | 5 |
|:-------|:-:|:-:|:-:|:-:|:-:|:-:|
| Texto  | A | A | A | A | A | B |

**Padrão:** M = 3

| Índice | 0 | 1 | 2 |
|:-------|:-:|:-:|:-:|
| Padrão | A | A | B |

**Exercício 1 – Hash do Padrão**

A ideia mais basica de um hash é transformar o padrão em um número, somando os valores de cada caractere.

Assim, o algoritmo pode comparar números em vez de letras, tornando a busca muito mais rápida.

Por exemplo, se atribuirmos `~ A = 1` e `~ B = 2`, o padrão `~ A A B` pode ser representado por um hash que é...

::: Gabarito
```~
A A B = 1 + 1 + 2 = 4
```
:::
???


??? Exercício 2 – Hash do Primeiro Trecho do Texto

Agora, calcule o hash dos **3 primeiros caracteres** do texto `~ A A A`:
Qual é o valor do hash desse trecho?

::: Gabarito
```~
A A A = 1 + 1 + 1 = 3
```
:::
???


??? Exercício 3 – Percorrendo o Texto

Calcule o hash de cada trecho de tamanho M = 3 e compare com o hash do padrão.

| Posição | Trecho | h(t ) | Igual ao h(p ) ? |
|----------|---------|------|----------------|
| 0–2 | A A A | ? | ? |
| 1–3 | A A A | ? | ? |
| 2–4 | A A A | ? | ? |
| 3–5 | A A B | ? | ? |

::: Gabarito
| Posição | Trecho | h(t) | Igual ao h(p ) ? |
|----------|---------|------|----------------|
| 0–2 | A A A | 3 | ❌ |
| 1–3 | A A A | 3 | ❌ |
| 2–4 | A A A | 3 | ❌ |
| 3–5 | A A B | 4 | ✅ |

✅ O padrão foi encontrado na posição **3** do texto.
:::
???

---

!!! Otimização: Evitando Recálculos

Você deve ter notado que no Exercício 3, recalcular o hash (`~ 1+1+1`), (`~ 1+1+1`), etc., parece repetitivo.

Não precisamos! Podemos "deslizar" o hash em tempo $O(1)$ usando uma fórmula simples, pois é apenas uma soma:

h(next) = h(curr) − value(outgoing) + value(incoming)

**Exemplo:**
* Hash atual (índice 2-4) `~ "A A A"` = **3**
* Próxima janela (índice 3-5) `~ "A A B"`
* Sai o caractere: 'A' (valor 1)
* Entra o caractere: 'B' (valor 2)
* Novo Hash = 3 - 1 + 2 = **4**

Esta técnica de atualização em $O(1)$ é o "pulo do gato" que nos permite deslizar a janela eficientemente.
!!!

---


??? Exercício 4 – Reflexão

Por que o algoritmo usa o valor de hash em vez de comparar caractere por caractere?

::: Gabarito
Porque comparar apenas os **valores hash** é **muito mais rápido**:  
em vez de comparar cada letra, o algoritmo compara **números resumidos** de cada trecho.  
Se os números são diferentes, ele já sabe que o trecho não combina — sem precisar verificar caractere por caractere.
:::
???

??? Exercício 5 – Complexidade sem falsos positivos

O algoritmo encontrou **apenas um resultado correto** e **nenhum falso positivo**.  
Ou seja, ele percorreu o texto comparando apenas os valores de hash, sem precisar verificar caractere por caractere.

**Pergunta:** Qual é a complexidade desse caso?  
Explique o motivo.

::: Gabarito
Quando não há falsos positivos, o algoritmo realiza apenas as comparações de hash,  
percorrendo o texto uma vez $O(N)$ e calculando o hash do padrão $O(M)$.

Complexidade: $O(N + M)$ O algoritmo é rápido, pois não precisa fazer verificações adicionais caractere a caractere.
:::
???
---
## Problema dos *Spurious Hits* (falsos positivos)

Otimizar o recálculo para $O(1)$ é ótimo, mas nossa função de hash (soma simples) ainda tem um problema grave.

**Falsos positivos** (ou *spurious hits*) acontecem quando **dois trechos diferentes** produzem o **mesmo valor de hash**, mesmo que os caracteres não sejam iguais.

Quando isso ocorre, o algoritmo compara o hash (que bate!) e é forçado a verificar caractere por caractere, apenas para descobrir que era um alarme falso. Esses casos aumentam o custo da busca, podendo levar à complexidade $O(N·M)$.

---

**Exemplo ilustrativo:**

**Texto:** N = 6

| Índice | 0 | 1 | 2 | 3 | 4 | 5 |
|:-------|:-:|:-:|:-:|:-:|:-:|:-:|
| Texto  | A | D | A | A | B | C |

**Padrão:** M = 3

| Índice | 0 | 1 | 2 |
|:-------|:-:|:-:|:-:|
| Padrão | B | C | A |

Suponha valores `~ A=1, B=2, C=3, D=4` e função hash = soma dos valores.

---

??? Exercício 6 — Identificando *spurious hits*

1. Calcule o **hash do padrão** ->  `~ B C A`.  
2. Depois, calcule o hash de cada trecho de 3 caracteres do texto.  
3. Compare com o hash do padrão e identifique os falsos positivos — aqueles com **hash igual**, mas **caracteres diferentes**.

| Posição | Trecho |  h(t) |
|----------|---------|------|
| 0–2 | A D A | ? |
| 1–3 | D A A | ? | 
| 2–4 | A A B | ? | 
| 3–5 | A B C | ? |

::: Gabarito
**Hash do padrão `~ B C A` = 2 + 3 + 1 = 6**

**Falsos positivos encontrados:**

| Posição | Trecho | h(t) |
|----------|---------|------|
| 0–2 | A D A | 6 |
| 1–3 | D A A | 6 |
| 2-4 | A A B | 4 |
| 3–5 | A B C | 6 |

Os trechos **"A D A"**, **"D A A"** e **"A B C"** têm hash **6**, igual ao do padrão `~ B C A`,  
mas nenhum deles corresponde realmente ao padrão - são *spurious hits*.
:::
???


??? Exercício 7 – Complexidade com falsos positivos (*spurious hits*)

Alguns trechos diferentes do texto geraram **o mesmo valor de hash** do padrão,  
mas ao comparar os caracteres, o algoritmo percebeu que **não eram correspondências reais** (*spurious hits*).

**Pergunta:** Como isso afeta a complexidade do algoritmo?  
Explique se ele se torna mais lento nesse caso.

::: Gabarito

Quando ocorrem falsos positivos, o algoritmo precisa **comparar caractere por caractere** em cada posição onde o hash é igual - aumentando o número de operações.

Complexidade: $O(N·M)$

Isso acontece porque, no pior caso, o algoritmo pode ter que fazer uma comparação completa $O(M)$ em *todas* as N janelas, assim como a força bruta.
:::
???
---

## Introduzindo o algoritmo de Rabin-Karp


Agora que entendemos o que é hashing e vimos os problemas de colisão (*spurious hits*),  
vamos conhecer uma forma **mais robusta de gerar hashes** — e **mais eficiente** de calculá-los.

A resposta para tornar a busca ainda mais eficiente está em duas ideias-chave:
1. O uso de **hashing com potências**;
2. E, mais importante, uma otimização genial chamada **rolling hash**.


---

## Construindo o hash com potências de 10

Até agora, usamos a soma simples (ex: 1 + 1 + 2).  

Mas o **Rabin-Karp** trata a string como um número em uma **base**,  
onde cada posição tem um peso diferente ($10^{m-1}, 10^{m-2},… 10^0$).

Assim, strings diferentes raramente terão o mesmo hash —  
reduzindo o risco de **falsos positivos**.

---
??? Exercício 8 — Recalcule com base = 10

**Instruções:** usando **base = 10** e os valores `~ A = 1`, `~ B = 2`, `~ C = 3`, `~ D = 4`,  
calcule o hash posicional (com potências) de cada trecho de tamanho 3 e compare com o hash do padrão `~ B C A`.

Use a fórmula:

$h(\text{trecho}) = p_0\times10^2 + p_1\times10^1 + p_2\times10^0$


Tabela original (para referência):

| Posição | Trecho | h(t) |
|----------|---------|------|
| 0–2 | A D A | 6 |
| 1–3 | D A A | 6 |
| 2-4 | A A B | 4 |
| 3–5 | A B C | 6 |

1. Calcule o **hash do padrão** `~ B C A`.  
2. Calcule o hash dos trechos que tinham dado o mesmo valor do padrão.  

::: Gabarito
**Cálculos e respostas:**

- **Hash do padrão `~ B C A`:**  
  $h_p = 2\times10^2 + 3\times10^1 + 1\times10^0 = 200 + 30 + 1 = \mathbf{231}$

- **Hashes dos trechos (base = 10):**

| Posição | Trecho | h(t) |
|---------|--------|------:|
| 0–2     | A D A  | 141   |
| 1–3     | D A A  | 411   |
| 2–4     | A A B  | 112   |
| 3–5     | A B C  | 123   |

**Conclusão:** nenhum dos trechos tem hash igual a **231**, portanto **não há spurious hits** neste esquema posicional (base = 10).
:::
???

??? Exercício 9 – Hash com Potências
 
1. Por que usar potências ajuda a evitar colisões (comparado à soma simples)?  
2. Qual é a complexidade de calcular esse hash do zero?

::: Gabarito

1. Usar potências faz com que **a posição de cada caractere influencie o resultado**.  
   Diferente da soma, a ordem agora importa: “A B C” ≠ “B C A”.
Vamos comparar os dois padrões com $d=10$:
- `~ A B C` = $(1\times10^2)+(2\times10^1)+(3\times10^0)=123$
- `~ B C A` = $(2\times10^2)+(3\times10^1)+(1\times10^0)=231$ 

(Na soma simples, ambos seriam '6').

2. O cálculo direto do hash tem complexidade **O(M)**,  
   pois percorre cada caractere uma vez para aplicar a fórmula.  
   (Com *rolling hash*, a atualização entre janelas cai para **O(1)**.)
:::
???

!!!Implementando o Rolling Hash

O problema do hash polinomial é que, ingenuamente, ele também parece ser $O(M)$ para calcular em cada janela (como no Exercício 8). Se fizermos isso, voltamos à complexidade $O(N\times M)$.

Para evitar recalcular a soma inteira a cada passo, usa-se um **rolling hash**: 
a partir do hash do trecho atual, subtrai-se o valor do caractere que saiu e adiciona-se o valor do caractere que entrou. Assim o custo por deslocamento fica $O(1)$ (em vez de $O(M)$).
!!!
---
## Aplicando a Janela Deslizante 

A ideia do rolling hash é atualizar o hash da janela atual sem recalcular do zero. Usamos a representação posicional do hash (base d) e, na implementação prática, normalmente aplicamos um módulo primo $q$ para evitar *overflow* (números gigantes).

- Remove o valor do caractere que sai da janela (que estava na posição mais significativa),
- Multiplica o restante pela base $d$ ("dá um shift" para a esquerda),
- Adiciona o novo caractere que entra (na posição menos significativa).

Fórmula geral (com módulo)

$$
h_{next} = \bigl( (h_{curr} - p_{out} \times 10^{M-1}) \times 10 + p_{in} \bigr) \
$$

onde:
- $p_{out}$: valor do caractere que saiu da janela;  
- $p_{in}$: valor do caractere que entrou;   

---

??? Exercício 10 – Acompanhe o Rolling Hash

**Texto:**  `~ A D A A B C`  
**Padrão:** `~ A A B`  
**Valores:** `~ A=1`, `~ B=2`, `~ C=3`, `~ D=4`, base $d=10$, $M=3$

1. Calcule o hash do **padrão** `~ A A B`.  
2. Calcule o hash da primeira janela (`~ A D A`).  
3. Aplique a fórmula do *rolling hash* (sem módulo, para simplificar) para encontrar o valor das janelas seguintes:  
   - `~ A D A` → `~ D A A`  
   - `~ D A A` → `~ A A B`  
4. Onde ocorre o *match*?

::: Gabarito
1. $h(padrão)=(1 \times 10^2)+(1 \times 10^1)+(2 \times 10^0)=112$ 
2. 
* **Janela 1 (A D A):**
    * $(1 \times 10^2)+(4 \times 10^1)+(1 \times 10^0)=141$
3. 
* **Janela 2 (D A A):**
    * $h_1 = (h_0 - p_{out} \times d^{M-1}) \times d + p_{in}$
    * $h_1 = (141 - 1 \times 10^2) \times 10 + 1$
    * $h_1 = (141 - 100) \times 10 + 1 = 41 \times 10 + 1 = 411$
* **Janela 3 (A A B):**
    * $h_2 = (h_1 - p_{out} \times d^{M-1}) \times d + p_{in}$
    * $h_2 = (411 - 4 \times 10^2) \times 10 + 2$
    * $h_2 = (411 - 400) \times 10 + 2 = 11 \times 10 + 2 = 112$
4. **Match** encontrado! $h_2$ (112) == $h(padrão)$ (112). A janela começa no índice **2**.
:::
???

---


Enzo Ristori​

Gabriel Vidigal​

Lucas Kamikawa