---
layout: post
title: "Migrando do C# para o F#: Introdução"
description: "Três abordagens de converter um código existente em C# para o F#"
nav: fsharp-types
seriesId: "Porting from C#"
seriesOrder: 1
---

*Obs.: Antes de ler os posts dessa série, recomendo que você leia as seguintes séries como pré-requisito: ["Pensamento Funcional"](/series/thinking-functionally.html), ["Expressões e Sintaxe"](/series/expressions-and-syntax.html), e ["Entendendo os Tipos no F#"](/series/understanding-fsharp-types.html).*

Para a maioria dos desenvolvedores ao aprender uma nova linguagem de programação, o mais comum é tentar converter um código existente na nova linguagem, com o objetivo de ter um bom comparativo entre as duas linguagens.

Como mencionado anteriormente, linguagem de programação funcional é muito diferente da linguagem de programação convencional (imperativa), e tentar fazer uma conversão de uma para a outra, geralmente não é possível, mesmo se for possível realizar a conversão, o novo código provavelmente não estará usando as vantagens da linguagem funcional.

É claro que o F# é uma linguagem *multi-paradigma*, incluindo suporte para orientação ao objeto e técnicas de linguagens imperativas, mas mesmo assim, uma conversão direta em geral não será a melhor forma de escrever um código em linguagem funcional.

Então, nessa série, vamos abordar diversas maneiras de migrar um código existente em C# para um código em F#.

## Níveis de sofisticação para conversão ##

Como descrito [nesse post](/posts/key-concepts), existem quatro conceitos-chave que diferenciam o F# do C#.

* Orientado à função e não à objeto.
* Expressões e não *statements*
* Tipos Algébricos para criar modelos de domínio
* Pattern matching (*Correspondência de padrões*) para controle de fluxo

![quatro conceitos-chave](/assets/img/four-concepts2.png)

E como explicado nesse post e suas sequências, esses aspectos não são somente acadêmicos, mas fornecem ao desenvolvedor benefícios concretos.

Então eu dividi o processo de conversão em três níveis de sofisticação (na falta de um termo melhor), que representam o nível que o código convertido explora os benefícios da linguagem funcional.

### Nível Básico: Conversão Direta ###

No primeiro nível, o código F# é uma conversão direta (onde possível) do código C#. Classes e métodos são usados ao invés de módulos e funções, e valores são frequentemente mutáveis.

### Nível Intermediário: Código Funcional ###

Nesse nível, o código F# é refatorado para se tornar completamente funcional.

* Classes e métodos são substituídos por módulos e funções. Os valores são geralmente imutáveis.
* Funções de ordem superior são usadas para substituir as interfaces e herança.
* Pattern matching é usado para o controle de fluxo
* Loops são substituídos por funções de lista, por exemplo "map" ou recursão

Existem dois caminhos diferentes para chegar nesse nível

* O primeiro caminho é uma conversão direta para o F# e depois refatorar o código.
* O segundo caminho é converter o código convencional existente para o código funcional ainda no C#, e só depois converter o código funcional em C# para o F#.

A segunda opção pode parecer estranha, mas para a conversão de um código real, provavelmente será a forma mais rápida e confortável. Mais rápida pois você poderá usar ferramentas de refatoração, como o Resharper, e mais confortável porque você está desenvolvendo em C# até o momento da conversão final. Esse caminho de migração também torna evidente que a parte mais difícil não é a conversão de C# para F#, mas a conversão do código imperativo para o código fiuncional.

### Nível Avançado: Tipos representando o domínio ###

Nesse nível final, não só o código é funcional, mas o design é alterado para obter vantagem dos tipos de dados algébricos, especialmente os union types (*tipos de união*).

O domínio é [codificado em tipos](/posts/designing-with-types-single-case-dus/) para que os [estados ilegais não sejam representáveis](/posts/designing-with-types-making-illegal-states-unrepresentable/) e o [código correto é forçado em tempo de compilação](/posts/correctness-type-checking/).
Para uma demonstração concreta do poder dessa abordagem, veja o [exemplo do carrinho de compras](/posts/designing-for-correctness) na série de posts ["porque usar o F#?"](/series/why-use-fsharp.html) e toda a série de posts ["Modelando com tipos"](/series/designing-with-types.html).

Esse nível só pode ser feito em F#.

### Diagrama de conversão ###

Veja abaixo um diagrama para te ajudar a visualizar todos os caminhos de conversão descritos acima.

![níveis de conversão](/assets/img/porting-paths.png)

## A abordagem para essa série ##

Para ver como esses três níveis funcionam na prática, vamos aplicá-los em alguns exemplos:

* O primeiro exemplo é um sistema simples para criar e pontuar o jogo de boliche de dez pinos, baseado no código do conhecido kata "bowling game" descrito pelo "Uncle" Bob Martin. O código original em C# só tem uma classe e aproximadamente 70 linhas de código, ainda assim é possível demonstrar um série de princípios importantes.
* Após o primeiro exemplo, vamos olhar um código de carrinho de compras, baseado [nesse exemplo](/posts/designing-for-correctness/).
* O exemplo final é um código que representa os estados de um sistema de catraca de metrô, também baseado em um exemplo do Bob Martin. Esse exemplo demonstra como os union types no F# podem representar um modelo de estado de transição com mais facilidade do que uma abordagem orientada a objeto.

Mas antes de começar nos exemplos detalhados, vamos voltar ao básico e fazer algumas conversões simples. Isso será o assunto para o próximo post.

