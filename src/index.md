Algoritmo de Rabin-Karp
======

## O Problema da Busca em Texto

Vamos começar definindo o problema:
*   Temos um Texto (uma longa sequência de **N** caracteres).
*   E um Padrão (uma sequência menor de **M** caracteres).

Queremos encontrar a Posição (o índice) onde o padrão aparece pela primeira vez no texto.

??? Problema da Busca

Só para garantir que estamos na mesma página, vamos usar um exemplo.

Texto = "o rato roeu a roupa do reide roma" **padrão** = "roupa"

Se o primeiro caractere 'o' está no índice 0, qual é a saída (o índice da primeira letra do **padrão**) esperada?

::: Gabarito

Resposta = 14

:::

???

## A Abordagem "Força Bruta"

A primeira solução que vem à mente é a "força bruta". A ideia é simples: "deslizar" o padrão pelo texto, uma posição de cada vez, e comparar caractere por caractere.

Vamos ver isso em ação com nosso exemplo. O **padrão** é "roupa" (tamanho 5).

:rato

??? Checkpoint

Vamos pensar no custo disso. Considere um Texto com **N** caracteres e um Padrão com **M** caracteres.

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

Em vez de comparar cada caractere do texto com o padrão, o algoritmo compara apenas os **valores hash**, o que é muito mais rápido.

---
??? Para os exercícios a seguir adote:

**Valores para cada caractere:**
| Posição | Hashcode |
|:---:|:---|
| 1 | A |
| 2 | B |
| 3 | C |
| 4 | D |
| … | … |

**Texto:** N = 6
| Índice | 0 | 1 | 2 | 3 | 4 | 5 |
|:---|:---:|:---:|:---:|:---:|:---:|:---:|
| Texto | A | B | D | C | A | B |

**Padrão:** M = 3

| Índice | 0 | 1 | 2 |
|:---|:---:|:---:|:---:|
| Padrão | C | A | B |

**Exercício 1 – Hash do Padrão**

A ideia mais basica de um hash é transformar o padrão em um número, somando os valores de cada caractere.

Assim, o algoritmo pode comparar um número, o **hash**, em vez de ter que varrer vários caractéres, tornando a busca muito mais rápida.

Por exemplo, se atribuirmos `~ A = 1`, `~ B = 2` e `~ C = 3`, o padrão `~ C A B` pode ser representado por um hash que é...

::: Gabarito
```
C A B = 3 + 1 + 2 = 6
```
:::
???

??? Exercício 2 – Hash do Primeiro Trecho do Texto

Agora, calcule o hash dos **3 primeiros caracteres** do texto `~ A B D`:
Qual é o valor do hash desse trecho?

::: Gabarito
```
A B D = 1 + 2 + 4 = 7
```
:::
???

??? Exercício 3 – Percorrendo o Texto

Calcule o hash de cada trecho de tamanho M = 3 e compare com o hash do padrão (6).

| Posição | Trecho | h(t ) | Igual ao h(p ) ? |
|:---|:---|:---|:---|
| 0–2 | A B D | ? | ? |
| 1–3 | B D C | ? | ? |
| 2–4 | D C A | ? | ? |
| 3–5 | C A B | ? | ? |

::: Gabarito
| Posição | Trecho | h(t) | Igual ao h(p ) ? |
|:---|:---|:---|:---|
| 0–2 | A B D | 7 | ❌ |
| 1–3 | B D C | 9 | ❌ |
| 2–4 | D C A | 8 | ❌ |
| 3–5 | C A B | 6 | ✅ |

✅ O padrão foi encontrado na posição **3** do texto.
:::
???
---
## O Problema do Hash Simples: *Spurious Hits* (Falsos Positivos)

Nosso primeiro exemplo funcionou bem, mas a função de soma simples tem um problema grave.

**Falsos positivos** (ou *spurious hits*) acontecem quando **dois trechos diferentes** produzem o **mesmo valor de hash**. Vamos ver um caso onde isso acontece.

---

**Exemplo ilustrativo:**

**Texto:** N = 6

| Índice | 0 | 1 | 2 | 3 | 4 | 5 |
|:---|:---:|:---:|:---:|:---:|:---:|:---:|
| Texto | A | D | A | A | B | C |

**Padrão:** M = 3

| Índice | 0 | 1 | 2 |
|:---|:---:|:---:|:---:|
| Padrão | B | C | A |

Suponha valores `~ A=1, B=2, C=3, D=4` e função hash = soma dos valores.

---

??? Exercício 4 — Identificando *spurious hits*

1.  Calcule o **hash do padrão** -> `~ B C A`.
2.  Depois, calcule o hash de cada trecho de 3 caracteres do texto.
3.  Identifique os falsos positivos — aqueles com **hash igual**, mas **caracteres diferentes**.

| Posição | Trecho | h(t) |
|:---|:---|:---|
| 0–2 | A D A | ? |
| 1–3 | D A A | ? |
| 2–4 | A A B | ? |
| 3–5 | A B C | ? |

::: Gabarito
**Hash do padrão** `~ B C A` = 2 + 3 + 1 = **6**

**Falsos positivos encontrados:**

| Posição | Trecho | h(t) |
|:---|:---|:---|
| 0–2 | A D A | 6 |
| 1–3 | D A A | 6 |
| 2-4 | A A B | 4 |
| 3–5 | A B C | 6 |

Os trechos **"A D A"**, **"D A A"** e **"A B C"** têm hash **6**, igual ao do padrão `~ B C A`, mas nenhum deles corresponde realmente ao padrão. São todos *spurious hits*.
:::
???
---
## A Solução: Hashing Polinomial

Para resolver o problema das colisões, precisamos de uma função de hash que considere a **posição** dos caracteres.

O **Rabin-Karp** faz isso tratando a string como um número em uma **base**, onde cada posição tem um peso diferente ($10^{m-1}, 10^{m-2},… 10^0$). Assim, `BCA` terá um hash diferente de `ABC`. Isso reduz drasticamente a chance de colisões, tornando-as muito raras.

---
??? Exercício 5 — Resolvendo os Falsos Positivos

**Instruções:** usando **base = 10** e os valores `~ A = 1`, `~ B = 2`, `~ C = 3`, `~ D = 4`, recalcule o hash dos trechos do exercício anterior, onde a colisão ocorreu.

Use a fórmula:

$h(\text{trecho}) = p_0\times10^2 + p_1\times10^1 + p_2\times10^0$

1.  Calcule o **hash do padrão** `~ B C A`.
2.  Calcule o hash dos trechos que antes deram o mesmo valor (6).

::: Gabarito
**Cálculos e respostas:**

-   **Hash do padrão `~ B C A`:**
    $h_p = 2\times10^2 + 3\times10^1 + 1\times10^0 = 200 + 30 + 1 = \mathbf{231}$

-   **Hashes dos trechos (base = 10):**

| Posição | Trecho | h(t) |
|:---|:---|---:|
| 0–2 | A D A | 141 |
| 1–3 | D A A | 411 |
| 3–5 | A B C | 123 |

**Conclusão:** nenhum dos trechos tem hash igual a **231**. As colisões que tínhamos antes desapareceram!
:::
???

??? Checkpoint: O Passo Final e Obrigatório

O hash polinomial torna as colisões muito raras, mas **não impossíveis**. Por exemplo, com um alfabeto maior, as strings `BAA` e `AKA` poderiam gerar o mesmo hash.

**Pergunta:** Digamos que, ao percorrer o texto, você encontra um trecho cujo hash polinomial é **idêntico** ao hash do padrão. O que o algoritmo deve fazer para ter 100% de certeza de que encontrou o padrão?

::: Gabarito
Ele deve fazer uma **verificação final, caractere por caractere**, apenas para aquele trecho.

*   Se os caracteres também baterem, é um **match verdadeiro**.
*   Caso contrário, era um *spurious hit* raro e a busca continua.

**Lembre-se: toda vez que os hashes são iguais, a verificação manual é obrigatória para confirmar.**
:::
???

---
## O Novo Problema: A Eficiência do Cálculo

O hash polinomial é ótimo, mas temos um novo problema.

??? Exercício 6 – Análise de Custo

1.  Por que usar potências ajuda a evitar colisões?
2.  Qual é a complexidade de calcular o hash polinomial do zero para **uma** janela de tamanho **M**?
3.  Se fizermos isso para **todas** as `N-M+1` janelas, qual será a complexidade total do algoritmo?

::: Gabarito

1.  Usar potências faz com que **a posição de cada caractere influencie o resultado**. Diferente da soma, a ordem agora importa: `ABC` (123) ≠ `BCA` (231).

2.  O cálculo direto do hash tem complexidade **O(M)**, pois percorre cada caractere uma vez para aplicar a fórmula.

3.  Se calcularmos um hash de custo O(M) para cada uma das ~N janelas, a complexidade total volta a ser **O(N×M)**, a mesma da força bruta! Resolvemos um problema, mas perdemos toda a eficiência.
:::
???

---
## A Solução Final: *Rolling Hash*

Para evitar recalcular o hash inteiro a cada passo, usamos a técnica do **rolling hash**: a partir do hash do trecho atual, atualizamos o valor em tempo **O(1)** para obter o hash da próxima janela.

!!!Implementando o Rolling Hash

A lógica é:
1.  Pegue o hash da janela atual.
2.  **Remova** a contribuição do caractere que está saindo (o mais à esquerda).
3.  **"Desloque"** o resto do valor para a esquerda (multiplicando pela base).
4.  **Adicione** a contribuição do novo caractere que está entrando (o mais à direita).

Fórmula: $h_{next} = (h_{curr} - p_{out} \times 10^{M-1}) \times 10 + p_{in}$
!!!
---

??? Exercício 7 – Acompanhe o Rolling Hash

**Texto:** `~ A D A A B C`
**Padrão:** `~ A A B`
**Valores:** `~ A=1`, `~ B=2`, `~ C=3`, `~ D=4`, base $d=10$, $M=3$

1.  Calcule o hash do **padrão** `~ A A B`.
2.  Calcule o hash da primeira janela (`~A D A`).
3.  Aplique a fórmula do *rolling hash* para encontrar o valor das janelas seguintes até encontrar o padrão.
    -   `~ A D A` → `~ D A A`
    -   `~ D A A` → `~ A A B`

::: Gabarito
1.  $h(padrão)=(1 \times 10^2)+(1 \times 10^1)+(2 \times 10^0)=112$
2.  
    *   **Janela 1 (A D A):**
        *   $(1 \times 10^2)+(4 \times 10^1)+(1 \times 10^0)=141$
3.  
    *   **Janela 2 (D A A):**
        *   $h_1 = (h_0 - p_{out} \times d^{M-1}) \times d + p_{in}$
        *   $h_1 = (141 - 1 \times 10^2) \times 10 + 1$
        *   $h_1 = (141 - 100) \times 10 + 1 = 41 \times 10 + 1 = 411$
    *   **Janela 3 (A A B):**
        *   $h_2 = (h_1 - p_{out} \times d^{M-1}) \times d + p_{in}$
        *   $h_2 = (411 - 4 \times 10^2) \times 10 + 2$
        *   $h_2 = (411 - 400) \times 10 + 2 = 11 \times 10 + 2 = 112$
4.  **Match** encontrado! $h_2$ (112) == $h(padrão)$ (112). A janela começa no índice **2**.
:::
???

---
## Análise de Complexidade Final

Agora que temos o algoritmo completo e eficiente, vamos analisar seu custo.

??? Checkpoint Final

**Pergunta 1:** Qual é a complexidade do Rabin-Karp no **melhor caso** (poucos ou nenhum falso positivo)?

::: Gabarito
O algoritmo calcula o hash inicial em $O(M)$ e depois faz $N-M$ atualizações de $O(1)$.

Complexidade: **$O(N + M)$**. O algoritmo é extremamente rápido.
:::
???

??? **Pergunta 2:** Qual é a complexidade no **pior caso** (uma função de hash ruim que gera falsos positivos em todas as janelas)?

::: Gabarito
Quando ocorrem falsos positivos, o algoritmo precisa **comparar caractere por caractere** ($O(M)$) em cada posição onde o hash é igual. Se isso acontecer em todas as $N$ janelas, o custo volta a ser o da força bruta.

Complexidade: **$O(N·M)$**.
:::
???

---

Enzo Ristori​

Gabriel Vidigal​

Lucas Kamikawa