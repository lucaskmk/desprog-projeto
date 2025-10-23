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

**Hashing** é uma ideia matemática que transforma o texto em números.  A melhor forma de pensar nisso é como se fosse uma “impressão” digital” de um texto. O algoritmo pega uma string, que pode ser longa, e converte em um único numero.
A regra de ouro do hashing é: se duas strings são idênticas, seus hashes têm que ser o mesmo. Isso nos leva a uma conclusão muito importante, que é o segredo da velocidade do algoritmo, de que se dois hashes são diferentes, temos 100% de certeza que as strings também são diferentes.

Em vez de comparar cada caractere do texto com o padrão, o algoritmo compara apenas os **valores hash**,  
o que é muito mais rápido.

![](hash1.png)

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

Por exemplo, se atribuirmos ` A = 1` e ` B = 2`, o padrão ` A A B` pode ser representado por um hash que é...

::: Gabarito
` A A B` = 1 + 1 + 2 = **4**  
:::
???


??? Exercício 2 – Hash do Primeiro Trecho do Texto

Agora, calcule o hash dos **3 primeiros caracteres** do texto ` A A A`:
Qual é o valor do hash desse trecho?

::: Gabarito
` A A A` = 1 + 1 + 1 = **3**
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
| Texto  | A | D | A | A | B | C |

**Padrão:** M = 3

| Índice | 0 | 1 | 2 |
|:-------|:-:|:-:|:-:|
| Padrão | B | C | A |

Suponha valores `A=1, B=2, C=3, D=4` e função hash = soma dos valores.


**Exemplo ilustrativo:**

---

??? Exercício 6 — Identificando *spurious hits*

1. Calcule o **hash do padrão** ->  ` B C A`.  
2. Depois, calcule o hash de cada trecho de 3 caracteres do texto.  
3. Compare com o hash do padrão e identifique os falsos positivos — aqueles com **hash igual**, mas **caracteres diferentes**.

| Posição | Trecho |  h(t) |
|----------|---------|------|
| 0–2 | A D A | ? |
| 1–3 | D A A | ? | 
| 2–4 | A A B | ? | 
| 3–5 | A B C | ? |

::: Gabarito
**Falsos positivos encontrados:**

| Posição | Trecho | h(t) |
|----------|---------|------|
| 0–2 | A D A | 6 |
| 3–5 | A B C | 6 |

Ambos têm hash **6**, igual ao do padrão ` B C A`,  
mas nenhum dos dois trechos corresponde realmente ao padrão — são *spurious hits*.
:::
???


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

## Introduzindo o algoritmo de Rabin-Karp


Agora que entendemos o que é hashing e vimos os problemas de colisão (*spurious hits*),  
vamos conhecer uma forma **mais robusta de gerar hashes** — e **mais eficiente** de calculá-los.

A resposta para tornar a busca ainda mais eficiente está em duas ideias-chave:
1. O uso de **hashing**;
2. E, mais importante, uma otimização genial chamada **rolling hash**.


---

## Construindo o hash com potências


$$h(p) = p_0 \times d^{M-1} + p_1 \times d^{M-2} + p_2 \times d^{M-3} + \dots + p_{M-2} \times d^{1} + p_{M-1} \times d^{0}$$

$$h(p) = \sum_{i=0}^{M-1} p_i \times d^{M-1-i}$$


* pi ​→ valor numérico do caractere na posição i do padrão

* d  → base (ex: 10, 26, 256...)

* M  → tamanho do padrão

Até agora, usamos a soma simples (ex: 1 + 1 + 2).  
Mas o **Rabin-Karp** trata a string como um número em uma **base**,  
onde cada posição tem um peso diferente (10⁰, 10¹, 10²…).

Assim, strings diferentes raramente terão o mesmo hash —  
reduzindo o risco de **falsos positivos**.

---
??? Exercício — Calculando o hash geral

Use a fórmula:


$$h(p) = p_0 \times d^{M-1} + p_1 \times d^{M-2} + \dots + p_{M-1} \times d^0
$$

Sabendo que:
- p0 = 1, p1 = 1, p2 = 2
- base d = 10
- M = 3

Dado o padrão ` A A B` e **base = 10**,  
com valores `A=1`, `B=2`, calcule o **hash** usando potências:

::: Gabarito
$$h(p) = (1\times10^2) + (1\times10^1) + (2\times10^0) = $$
$$h(p) = 1×100 + 1×10 + 2×1 = 112 $$
:::
???

??? Exercício 2 – Hash com Potências
 
1. Por que usar potências ajuda a evitar colisões?  
2. Qual é a complexidade desse método?

::: Gabarito

1. Usar potências faz com que **a posição de cada caractere influencie o resultado**,  
   o que reduz a chance de dois trechos diferentes produzirem o mesmo valor de hash.  
   Assim, “A B” ≠ “B A”.
Vamos comparar os dois padrões:
- ` A B` = 1×10 + 2×1 = 12 
- ` B A` = 2×10 + 1×1 = 21 
2. O cálculo direto do hash tem complexidade **O(M)**,  
   pois percorre cada caractere uma vez para aplicar a fórmula.  
   (Com *rolling hash*, a atualização entre janelas cai para **O(1)**.)
:::
???

---

## Rolling Hash — a ideia central do Rabin-Karp

Para evitar recalcular a soma inteira a cada passo, usa-se um **rolling hash**: 
a partir do hash do trecho atual, subtrai-se o valor do caractere que saiu e adiciona-se o valor do caractere que entrou. Assim o custo por deslocamento fica O(1) (em vez de O(M)). É a ideia básica por trás do **algoritmo de Rabin-Karp**.

!!! Fórmula simples (soma):
h(next) = h(curr) − value(outgoing) + value(incoming)
!!!

---
### Aplicando a Janela Deslizante (Rolling Hash)

A ideia do rolling hash é atualizar o hash da janela atual sem recalcular do zero. Usamos a representação posicional do hash (base d) e, na implementação prática, normalmente aplicamos um módulo primo q para evitar overflow.

- Remove o valor do caractere que sai da janela (que estava na posição mais significativa),
- Multiplica o restante pela base $d$,
- Adiciona o novo caractere que entra. (novo caractere na posição menos significativa),

Fórmula geral (com módulo)

$$
h_{next} = \bigl( (h_{curr} - p_{out} \times d^{M-1}) \times d + p_{in} \bigr) \bmod q
$$

onde:
- $p_{out}$: valor do caractere que saiu da janela;  
- $p_{in}$: valor do caractere que entrou;  
- $d$: base (ex: 10, 26, 256);  
- $q$: número primo usado para módulo (evita *overflow*).
- $d^{M−1}$ normalmente é pré-computado (chamado de hpow).

---

??? Exercício – Acompanhe o Rolling Hash

**Texto:** `a d a a b c`  
**Padrão:** `a a b`  
Valores: `a=1`, `b=2`, `c=3`, `d=4`, base \(d=10\), \(M=3\)

1. Calcule o hash do padrão `a a b`.  
2. Calcule o hash da primeira janela (`a d a`).  
3. Aplique a fórmula do *rolling hash* para encontrar o valor das janelas seguintes:  
   - `"a d a"` → `"d a a"`  
   - `"d a a"` → `"a a b"`  
4. Onde ocorre o *match*?

::: Gabarito
1. $h(p) = 1×10^2 + 1×10^1 + 2×10^0 = 112 $ 
2. $h_0 = 1×10^2 + 4×10^1 + 1×10^0 = 141  $
3. 
* $h_1 = (141 − 1×10^2)×10 + 1 = 411$ 
 *  $h_2 = (411 − 4×10^2)×10 + 2 = 112$
4. *Match* encontrado na janela que começa no índice **3**. ✅
:::
???

---

## Código em C — Rolling Hash e Saída dos Hashes

O programa abaixo mostra como o *Rabin–Karp* calcula e atualiza o hash em cada janela, imprimindo todos os valores no console.

``` c
#include <stdio.h>
#include <string.h>

int main(void) {
    const char *text = "adaabc";   // texto sem espaços
    const char *pattern = "aab";
    int n = strlen(text);
    int m = strlen(pattern);

    const int d = 10;   // base (exemplo simples)
    const int q = 101;  // número primo para módulo

    int h = 1; // d^(m-1) % q
    for (int i = 0; i < m - 1; i++)
        h = (h * d) % q;

    int p_hash = 0; // hash do padrão
    int t_hash = 0; // hash da janela do texto

    // Hash inicial
    for (int i = 0; i < m; i++) {
        p_hash = (d * p_hash + (pattern[i] - 'a' + 1)) % q;
        t_hash = (d * t_hash + (text[i] - 'a' + 1)) % q;
    }

    printf("Hash do padrão: %d\n", p_hash);
    printf("Hash inicial da janela: %d\n", t_hash);

    // Percorre o texto
    for (int i = 0; i <= n - m; i++) {
        printf("Janela %d: %.*s → Hash = %d\n", i, m, text + i, t_hash);

        if (p_hash == t_hash) {
            int match = 1;
            for (int j = 0; j < m; j++) {
                if (pattern[j] != text[i + j]) { match = 0; break; }
            }
            if (match)
                printf(">> Match encontrado no índice %d ✅\n", i);
        }

        // Calcula o hash da próxima janela
        if (i < n - m) {
            int outgoing = text[i] - 'a' + 1;
            int incoming = text[i + m] - 'a' + 1;
            t_hash = (d * (t_hash - outgoing * h) + incoming) % q;
            if (t_hash < 0)
                t_hash += q;
        }
    }

    return 0;
}
```



## Conclusão

Essa é a essência do **Rabin-Karp**:  
usar **hashes numéricos** para acelerar a comparação, e **rolling hash** para evitar cálculos repetidos.  
O resultado é uma busca eficiente com complexidade média **O(N + M)**, mas que continua precisa mesmo em casos de colisão de hash.

---

### Escolhendo a Base \(d\) no Rabin–Karp

A base \(d\) define **quantos símbolos diferentes** o algoritmo pode representar.  
Ela funciona como o “tamanho do alfabeto” — ou seja, o número de caracteres possíveis no texto.

| Tipo de texto | Base \(d\) mais comum | Explicação |
|----------------|----------------------|-------------|
| Letras minúsculas (a–z) | 26 | Um valor para cada letra do alfabeto. |
| Letras maiúsculas e minúsculas (a–z, A–Z) | 52 | Diferencia maiúsculas e minúsculas. |
| Texto ASCII (ex: frases, código) | 256 | Cobre todos os caracteres ASCII. |
| Texto binário (0 e 1) | 2 | Cada caractere é um bit. |

No código real, é comum usar **d = 256**, pois isso cobre todos os caracteres possíveis da tabela ASCII.

O cálculo completo do hash fica:
\[
h(p) = \left( \sum_{i=0}^{M-1} p_i \times d^{M-1-i} \right) \bmod q
\]

O módulo \(q\) é um **número primo grande** (ex: 101, 997, 1009...) usado para evitar *overflow* e reduzir colisões.

---

??? Exercício — Escolhendo a base

1. Se o texto usa apenas letras minúsculas (a–z), qual base \(d\) é suficiente?  
2. E se o texto usa letras, números e pontuação (ex: "Olá, Mundo!")?  
3. Qual é a vantagem de usar o módulo \(q\)?

::: Gabarito
1. \(d = 26\)  
2. \(d = 256\) — cobre todos os caracteres ASCII.  
3. O módulo \(q\) impede que o valor do hash cresça demais e **reduz colisões**,  
   mantendo os resultados em um intervalo controlado.
:::
???



| Tipo de texto                                             | Base (d) mais comum | Explicação                                             |
| --------------------------------------------------------- | ------------------- | ------------------------------------------------------ |
| **Texto com letras minúsculas (a–z)**                     | 26                  | Uma posição para cada letra do alfabeto.               |
| **Texto com letras maiúsculas e minúsculas (a–z, A–Z)**   | 52                  | Considera as letras maiúsculas e minúsculas distintas. |
| **Texto com caracteres ASCII (ex: código-fonte, frases)** | 256                 | Considera todos os caracteres ASCII possíveis.         |
| **Texto binário (0 e 1)**                                 | 2                   | Cada caractere é um bit.                               |


Você também pode criar

1. listas;

2. ordenadas,

assim como

* listas;

* não-ordenadas

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