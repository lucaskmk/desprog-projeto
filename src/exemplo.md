Algoritmo de Rabin-Karp 
======

Algoritmo Busca em Texto
---------

## O Problema da Busca

Vamos começar definindo o problema: temos um Texto (uma longa sequência de caracteres, como o código-fonte de um programa)
e um Padrão (uma sequência menor, como o nome de uma função) . Queremos encontrar a Posição (o índice)
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

// montar a sequencia de imagens para a força bruta!! 
algo seguindo essa sequencia:
Tentativa 1 (Índice 0): Texto: [o r a t o] roeu a roupa... Padrão: [r o u p a] Compara 1: 'o' == 'r'? Falha. (1 comparação)

Tentativa 2 (Índice 1): Texto: o[ r a t o] roeu a roupa... Padrão: [r o u p a] Compara 1: ' ' == 'r'? Falha. (1 comparação)

Tentativa 3 (Índice 2): Texto: o [r a t o ] roeu a roupa... Padrão: [r o u p a] Compara 1: 'r' == 'r'? Ok. Compara 2: 'a' == 'o'? Falha. (2 comparações)

PULANDO VARIAS TENTATIVAS:

Tentativa 15 (Índice 14): Texto: o rato roeu a[r o u p a] do reide... Padrão: [r o u p a]

Compara 1: 'r' == 'r'? Ok. 
Compara 2: 'o' == 'o'? Ok. 
Compara 3: 'u' == 'u'? Ok. 
Compara 4: 'p' == 'p'? Ok. 
Compara 5: 'a' == 'a'? Ok. 

Match! Retorna o índice 14. (5 comparações)


??? Checkpoint

Vamos pensar no custo disso. Considere um Texto com n caracteres e um Padrão com m caracteres.

No pior caso possível (ex: Texto = "AAAAAAAAAAAAAAAAAB" e Padrão = "AAB"), quantas comparações de caracteres o algoritmo de força bruta teria que fazer? 

::: Gabarito

O algoritmo teria que "deslizar" a janela n−m+1 vezes. Em cada uma dessas posições, ele poderia ter que comparar até m caracteres.

Isso nos dá uma complexidade de O((n−m+1)×m), que simplificamos para O(n×m).

:::
???


??? Checkpoint

Imagine um cenário real, como um sistema de verificação de plágio.

    Texto (base de dados inteira) com n=1.000.000.000 caracteres.

    Padrão (frase do aluno) com m=100 caracteres.

Uma complexidade de O(n×m) parece eficiente? 

::: Gabarito

Definitivamente não. É um custo computacional altíssimo. Precisamos de uma forma mais rápida de descartar as posições que não dão match.

:::
???

---------

Um algoritmo de busca em texto serve para encontrar palavras ou frases dentro de um texto.
Ele compara o que você procura (chamado de **padrão**) com o conteúdo do **texto**, verificando onde há uma coincidência.

O método mais simples é ir letra por letra, testando o padrão em cada posição e pegar o **índice**.

---------
## A Ideia Principal: Hashing

**Hashing** é uma ideia matemática que transforma o texto em números.

Em vez de comparar cada caractere do texto com o padrão, o algoritmo compara apenas os **valores hash**,  
que são números que resumem cada trecho do texto.

![](hash1.png)

---
??? Para os exercicios a seguir adote:

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

**Exercício 1 – Hash Padrão**

A ideia de um hash padrão é transformar o padrão em um número, somando os valores de cada caractere.
Assim, o algoritmo pode comparar números em vez de letras, tornando a busca muito mais rápida.
Por exemplo, se atribuirmos a = 1 e b = 2, o padrão ` A A B` pode ser representado por um hash que é...

::: Gabarito
A A B = 1 + 1 + 2 = **4**  
:::
???


??? Exercício 2 – Hash do Primeiro Trecho do Texto

Agora, calcule o hash dos **3 primeiros caracteres** do texto ` A A A`:
Qual é o valor do hash desse trecho?

::: Gabarito
A A A = 1 + 1 + 1 = **3**
:::
???


??? Exercício 3 – Percorrendo o Texto

Calcule o hash de cada trecho de tamanho M = 3 e compare com o hash do padrão.

| Posição | Trecho | h(t) | Igual ao h(p)? |
|----------|---------|------|----------------|
| 0–2 | A A A | ? | ? |
| 1–3 | A A A | ? | ? |
| 2–4 | A A A | ? | ? |
| 3–5 | A A B | ? | ? |

::: Gabarito
| Posição | Trecho | h(t) | Igual ao h(p)? |
|----------|---------|------|----------------|
| 0–2 | A A A | 3 | ❌ |
| 1–3 | A A A | 3 | ❌ |
| 2–4 | A A A | 3 | ❌ |
| 3–5 | A A B | 4 | ✅ |

✅ O padrão foi encontrado **na posição 4** do texto.
:::
???


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

**Pergunta:**  
Qual é a complexidade desse caso?  
Explique o motivo.

::: Gabarito
Quando não há falsos positivos, o algoritmo realiza apenas as comparações de hash,  
percorrendo o texto uma vez e calculando o hash do padrão.

➡️ Complexidade: **O(N + M)**  
*(onde N é o tamanho do texto e M o tamanho do padrão)*

O algoritmo é rápido, pois não precisa fazer verificações adicionais.
:::
???

---
## Problema dos *Spurious Hits* (falsos positivos)

Mesmo com o uso de hashing, ainda podem ocorrer **falsos positivos**, chamados de *spurious hits*.  
Isso acontece quando **dois trechos diferentes** do texto produzem o **mesmo valor de hash**, mesmo que os caracteres não sejam iguais.

Quando isso ocorre, o algoritmo precisa verificar caractere por caractere para confirmar se o padrão realmente aparece ali.  
Esses casos aumentam o custo da busca, podendo levar à complexidade **O(N·M)**.

---

**Exemplo ilustrativo:**

**Texto:** N = 6

| Índice | 0 | 1 | 2 | 3 | 4 | 5 |
|:-------|:-:|:-:|:-:|:-:|:-:|:-:|
| Texto  | a | d | a | a | b | c |

**Padrão:** M = 3

| Índice | 0 | 1 | 2 |
|:-------|:-:|:-:|:-:|
| Padrão | b | c | a |

Suponha valores `a=1, b=2, c=3, d=4` e função hash = soma dos valores.


**Exemplo ilustrativo:**

---

??? Exercício 6 — Identificando *spurious hits*

1. Calcule o **hash do padrão** ->  ` b c a`.  
2. Depois, calcule o hash de cada trecho de 3 caracteres do texto.  
3. Compare com o hash do padrão e identifique os falsos positivos — aqueles com **hash igual**, mas **caracteres diferentes**.

| Posição | Trecho |  h(t) |
|----------|---------|------|
| 0–2 | a d a | ? |
| 1–3 | d a a | ? | 
| 2–4 | a a b | ? | 
| 3–5 | a b c | ? |

::: Gabarito
**Falsos positivos encontrados:**

| Posição | Trecho | h(t) |
|----------|---------|------|
| 0–2 | a d a | 6 |
| 3–5 | a b c | 6 |

Ambos têm hash **6**, igual ao do padrão ` b c a`,  
mas nenhum dos dois trechos corresponde realmente ao padrão — são *spurious hits*.
:::
???
## Bônus (curta nota sobre *rolling hash* / Rabin-Karp)

Para evitar recalcular a soma inteira a cada passo, usa-se um **rolling hash**: a partir do hash do trecho atual, subtrai-se o valor do caractere que saiu e adiciona-se o valor do caractere que entrou. Assim o custo por deslocamento fica O(1) (em vez de O(M)). É a ideia básica por trás do **algoritmo de Rabin-Karp**.

**Fórmula simples (soma):**  
h(next) = h(curr) − value(outgoing) + value(incoming)

??? Exercício 7 – Complexidade com falsos positivos (*spurious hits*)

Alguns trechos diferentes do texto geraram **o mesmo valor de hash** do padrão,  
mas ao comparar os caracteres, o algoritmo percebeu que **não eram correspondências reais** (*spurious hits*).

**Pergunta:**  
Como isso afeta a complexidade do algoritmo?  
Explique se ele se torna mais lento nesse caso.

::: Gabarito

Quando ocorrem falsos positivos, o algoritmo precisa **comparar caractere por caractere**  
em cada posição onde o hash é igual — aumentando o número de operações.

➡️ Complexidade: **O(N·M)**

💡 Isso acontece porque, além de percorrer o texto, o algoritmo faz novas comparações completas  
para cada *spurious hit*, o que aumenta o tempo total de execução.
:::
???
---

FIM DO BLOCO DE HASHING

Você também pode criar

1. listas;

2. ordenadas,

assim como

* listas;

* não-ordenadas





e imagens. Lembre que todas as imagens devem estar em uma subpasta *img*.

![](hash1.png)

Para tabelas, usa-se a [notação do
MultiMarkdown](https://fletcher.github.io/MultiMarkdown-6/syntax/tables.html),
que é muito flexível. Vale a pena abrir esse link para saber todas as
possibilidades.

| coluna a | coluna b | 
|----------|----------|
| 1        | 2        |

Ao longo de um texto, você pode usar *itálico*, **negrito**, {red}(vermelho) e
[[tecla]]. Também pode usar uma equação LaTeX: $f(n) \leq g(n)$. Se for muito
grande, você pode isolá-la em um parágrafo.

$$\lim_{n \rightarrow \infty} \frac{f(n)}{g(n)} \leq 1$$

Para inserir uma animação, use `md :` seguido do nome de uma pasta onde as
imagens estão. Essa pasta também deve estar em *img*.

:bubble

Você também pode inserir código, inclusive especificando a linguagem.

``` py
def f():
    print('hello world')
```

``` c
void f() {
    printf("hello world\n");
}
```

Se não especificar nenhuma, o código fica com colorização de terminal.

```
hello world
```


!!! Aviso
Este é um exemplo de aviso, entre `md !!!`.
!!!






Enzo Ristori​

Gabriel Vidigal​

Lucas Kamikawa