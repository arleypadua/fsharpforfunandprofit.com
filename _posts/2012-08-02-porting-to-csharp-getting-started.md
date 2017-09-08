---
layout: post
title: "Iniciando com a conversão direta"
description: "Equivalências do C# e F#"
nav: fsharp-types
seriesId: "Porting from C#"
seriesOrder: 2
---

Antes de começar com os exemplos detalhados, vamos voltar ao básico e fazer algumas conversões triviais.

Nesse posst, vamos chegar à equivalência de statements e keywords mais próxima do F# e do C# para te guiar ao fazer as conversões diretas.

## Sintaxe básica de conversão ##

Antes de iniciar a conversão, você precisa entender a diferença entra a sintaxe do F# e do F#. Essa seção apresenta algumas instruções gerais para converter de um para outro. (Como um rápido lembrete da sintaxe do F# como um todo, veja o post: ["Sintaxe do F# em 60 segundos"](/posts/fsharp-in-60-seconds/))

### Chaves e identação ###

O C# usa as chaves para indicar o início e o fim de um bloco de código. O F# utiliza a identação para definir blocos de código.

Chaves também são usadas no F#, mas ao invés do C#, são usadas:

* 	Para definições e uso dos "record" types.
* 	Em conjunto com expressões de computação, como `seq` e `async`. Em geral, você não usará essas expressões nas conversões básicas.

Para detalhes nas regras de identação, [veja esse post](/posts/fsharp-syntax).

### Ponto e vírgula (;)

Diferente do C#, o F# não exige nenhum tipo de sinalizador de fim da linha.

### Vírgulas

O F# não usa vírgulas para separar lista de elementos como é feito no C#. Lembre-se de não usar vírgulas na conversão!

*Para separar uma lista de elementos, use ponto e vírgula ao invés da vírgula*

```csharp
// Exemplo em C#
var list = new int[] { 1,2,3 }
```

```fsharp
// Exemplo em F#
let list = [1;2;3] // ponto e vírgula
```

*Para separar parâmetros de funções nativas do F#, use espaços em branco*

```csharp
// Exemplo em C#
int minhaFuncao(int x, int y, int z) { ... corpo da função ...}
```

```fsharp
// Exemplo em F#
let minhaFuncao (x:int) (y:int) (z:int) :int = ... corpo da função ...
let minhaFuncao x y z = ... corpo da função ...
```

Vírgulas são geralmente usadas para tuplas ou para separar parâmetros ao chamar funções de bibliotecas do .NET. (Veja [esse post](/posts/defining-functions/#tuples) para mais informações sobre Tuplas vs Vários Parâmetros)

### Declarando variáveis, funções e tipos

Para declarar variáveis e funções no F#, faça da seguinte forma:

```fsharp
let qualquerCoisa = // definição
```

Para declarar todos os tipos (classes, estruturas, interfaces, etc.) faça da seguinte forma:

```fsharp
type qualquerTipo = // definição
```

O uso do símbolo `=` representa uma diferença importante entre o F# e o C#. Quando no C# se usa chaves, no F# usa-se o símbolo `=` e então o bloco de código seguinte deve ser identado.

### Valores mutáveis

No F#, os valores são imutáveis por padrão. Se você estiver fazendo uma conversão direta, você provavelmente terá que tornar alguns valores em valores mutáveis, para isso basta usar a keyword `mutable`.
Então para atribuir valores, use o operador `<-` e não o símbolo de igualdade.

```csharp
// Exemplo em C#
var nomeDaVariavel = 42
nomeDaVariavel = nomeDaVariavel + 1
```

```fsharp
// Exemplo em F#
let mutable nomeDaVariavel = 42
nomeDaVariavel <- nomeDaVariavel + 1
```

### Atribuição vs. comparação de igualdade

No C# o símbolo `=` é usado para atribuição de valores, e o símbolo `==` é usado para a comparação de igualdade de valores.

No entanto, no F# o símbolo de igualdade é usado para comparar valores e também é usado para atribuir valores inicias a outros valores ao declarar.

```fsharp
let mutable nomeDaVariavel = 42       // Atribuir 42 na declaração
nomeDaVariavel <- nomeDaVariavel + 1  // Atribuindo novo valor
nomeDaVariavel = nomeDaVariavel + 1   // Comparação e não atribuição
```

Para comparar a diferença, use a comparação no estilo do SQL: `<>` ao invés de `!=`

```fsharp
let nomeDaVariavel = 42             // Atribuir 42 na declaração
nomeDaVariavel <> 43                // A comparação irá retornar true.
nomeDaVariavel != 43                // Erro FS0020.
```

Se você acidentalmente usar `!=`, provavelmente um [erro FS0020](/troubleshooting-fsharp/#FS0020) será exibido.

## Exemplo de conversão #1

Dadas as regras básicas, vamos ver um exemplo real e fazer uma conversão direta.

O primeiro exemplo tem um código bem simples que será convertido linha à linha. Abaixo o código em C#:

```csharp
using System;
using System.Collections.Generic;

namespace ConvertendoParaFsharp
{
    public class Squarer
    {
        public int ElevarAoQuadrado(int entrada)
        {
            var resultado = entrada * entrada;
            return resultado;
        }

        public void PrintElevarAoQuadrado(int entrada)
        {
            var resultado = this.ElevarAoQuadrado(entrada);
            Console.WriteLine("Entrada={0}. Resultado={1}", 
              entrada, resultado);
        }
    }
}
```
    
### Convertendo o "using" e o "namespace"

Essas keywords são diretas:

* 	`using` é convertida em `open`
* 	`namespace` com chaves é convertida em apenas `namespace`. 

Diferente do C#, os arquivos do F# não declaram namespaces, a menos que você precise fazer chamadas a bibliotecas do .NET. O próprio nome do arquivo atua como o namespace padrão.

Observe que o namespace, se usado, deve vir antes de qualquer coisa, como "open". Essa é a ordem oposta da maior parte de um código C#.

### Convertendo a classe

Para declarar uma classe simples, use:

```fsharp
type nomeDaClasse() = 
   ... código ...  
```

Observe que existem parenteses depois do nome da classe. Elas são obrigatórias para definir classes.

Definições mais avançadas de classes serão mostradas no próximo exemplo. Leia também o post: [Discussão completa de classes](/posts/classes/).

### Convertendo assinaturas de métodos/funções

Para assinaturas de funções/métodos

* Parenteses não são necessários ao redor da lista de parâmetros
* Espaço em branco é usado para separar parâmetros, não vírgulas
* Ao invés de chaves, um símbolo de igual sinaliza o início do corpo da função
* Normalmente, tipos nos parâmetros não são necessários, mas se precisar:
  *	O nome do tipo é declarado depois do valor ou parâmetro
  * O nome do parâmetro e o tipo são separados por dois pontos (:)
  *	Quando especificar os tipos de parâmetros, você provavelmente deverá envolver o parâmetro entre parâmetros para evitar comportamentos inesperados
  *	O tipo de retorno da função como um todo, deverá ser prefixado por dois pontos e é declarado depois de todos os parâmetros

Veja abaixo a assinatura de uma função em C#:

```csharp
int ElevarAoQuadrado(int entrada) { ... código ...}
```

E agora a forma correspondente da função em F# com os tipos explícitos:

```fsharp
let ElevarAoQuadrado (entrada:int) :int =  ... código ...
```

Você raramente irá precisar de especificar os tipos de retorno e parâmetros da função, pois normalmente o F# infere o tipo de retorno.

Agora uma assinatura típica de uma função em F#, com tipos inferidos:

```fsharp
let ElevarAoQuadrado entrada =  ... código ...
```

### Keyword void

Geralmente não é necessário especificar a keyword `void` no C#, mas se necessário na conversão, use `unit`

O código C#:

```csharp
void PrintElevarAoQuadrado(int entrada) { ... código ...}
```

Poderia ser convertido para:

```fsharp
let PrintElevarAoQuadrado (entrada:int) :unit =  ... código ...
```

Mas novamente, é muito raro especificar os típos. A versão sem tipos no F# seria:

```fsharp
let PrintElevarAoQuadrado entrada =  ... código ...
```

### Convertendo corpor de funções/métodos

Em um corpo de função, você geralmente tem a combinação de:

* 	Declaração e atribuição de variáveis
* 	Chamadas de funções
* 	Statements de controle de fluxo
* 	Valores de retorno

Daremos uma olhada rápida na conversão de cada um desses, exceto pelo fluxo de controle, que será discutido depois.

### Convertendo declaração de variáveis

Quase sempre, você poderá usar `let`, da mesma forma que `var` em C#:

```csharp
// Declarando variáveis no C#
var resultado = entrada * entrada;
```

```fsharp
// Declarando valores no F#
let resultado = entrada * entrada
```

Diferente do C#, você sempre deverá atribuir algo para um valor do F# como parte da sua declaração.

```csharp
// Exemplo C#
int variavelSemAtribuicao; // válido
```

```fsharp
// Exemplo F#
let variavelSemAtribuicao // inválido
```

Como observado acima, se você precisa mudar o valor depois da declaração, você deve torná-lo mutável com a keyword "mutable".

Se você precisa especificar um tipo para um valor, o nome do tipo é inserido depois do valor ou parâmetro, separado por dois pontos.

```csharp
// Exemplo C#
int nomeDaVariavel = 42;
```

```fsharp
// Exemplo F#
let nomeDaVariavel:int = 42
```

### Convertendo chamada de funçoes

Ao chamar funções nativas do F#, não há necessidade de parênteses ou vírgulas. Em outras palavras, chamamos funções no F# da mesma forma em que são declaradas.

Veja abaixo um código C# para definir uma função e depois chamá-la:

```csharp
// definindo um método/função
int ElevarAoQuadrado(int entrada) { ... código  ...}

// chamada
var resultado = ElevarAoQuadrado(entrada);
```

Pelo fato de que o F# normalmente infere os tipos de retorno e parâmetros, raramente você terá que especificá-los
Então, segue abaixo um código típico de como definir e chamar uma função no F#:

```fsharp
// definindo uma função
let ElevarAoQuadrado entrada = ... code ...

// chamada
let resultado = ElevarAoQuadrado entrada
```

### Valores de retorno

No C#, você usa a keyword `return`. Mas no F#, o último valor no bloco de código se torna o valor de retorno.

Veja abaixo um exemplo:

```csharp
public int ElevarAoQuadrado(int entrada)
{
    var resultado = entrada * entrada;
    return resultado;   //Keyword explícita
}
```

O equivalente no F#:

```fsharp
let ElevarAoQuadrado entrada = 
    let resultado = entrada * entrada
    resultado        // Valor de retorno implícito
```

Isso se deve por que o F# é baseado em expressões. Tudo se torna uma expressão e o valor de uma expressão de bloco como um todo é só o valor da última expressão no bloco.

Para mais detalhes no assunto, veja o post ["expressões vs statements"](/posts/expressions-vs-statements/).

### Exibindo valores no console

Para exibir valores de saída no C#, é comum vermos o método `Console.WriteLine` ou similar. No F#, geralmente usamos a função `printf`, que é *typesafe*. ([Mais detalhes no uso da função "printf"](/posts/printf)).

### A conversão completa do exemplo #1

Resumindo tudo, abaixo segue a conversão completa do exemplo #1 em F#:

O código C# novamente:
```csharp
using System;
using System.Collections.Generic;

namespace ConvertendoParaFsharp
{
    public class Squarer
    {
        public int ElevarAoQuadrado(int entrada)
        {
            var resultado = entrada * entrada;
            return resultado;
        }

        public void PrintElevarAoQuadrado(int entrada)
        {
            var resultado = this.ElevarAoQuadrado(entrada);
            Console.WriteLine("Entrada={0}. Resultado={1}", 
              entrada, resultado);
        }
    }
}
```

O equivalente em F#:

```fsharp
namespace ConvertendoParaFsharp

open System
open System.Collections.Generic

type Squarer() =  

    let ElevarAoQuadrado entrada = 
        let resultado = entrada * entrada
        resultado

    let PrintElevarAoQuadrado entrada = 
        let resultado = ElevarAoQuadrado entrada
        printf "Entrada=%i. Resultado=%i" entrada resultado
```        
    

