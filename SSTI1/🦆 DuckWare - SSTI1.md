# 🦆 DuckWare - SSTI2

--- 

>	Autor: @Ralfbp{Z4π}
---
## ✨ Desafio: Web Exploitation

> Link do desafio: https://play.picoctf.org/practice/challenge/492  
> Link do material: https://portswigger.net/web-security/server-side-template-injection
---
## Introdução

<center><img src= 'SSTI1 fotos/quest.png' ></center>

- Um desafio do site [picoCTF](https://play.picoctf.org/practice), que tem como objetivo a **Web Exploitation**
- **Web Exploitation**: explorar vulnerabilidades em aplicações web para obter acesso não autorizado, extrair informações ou executar ações indevidas.
- Neste desafio será usada a vulnerabilidade **Server-side template injection (SSTI)**.  
-   **Server-side template injection (SSTI)**: ocorre quando a entrada controlada pelo usuário é injetada diretamente em um _template_ do servidor sem o devido tratamento.
-----
## 🧩 Desafio

<center><img src= 'SSTI1 fotos/pagina.png' ></center>  

- Esta é a página exibida ao gerar e clicar no link do [picoCTF](https://play.picoctf.org/practice).

```
	# Home
	
	I built a cool website that lets you announce whatever you want!*
	
	What do you want to announce:
	
	
	
	*Announcements may only reach yourself
```
----
## 🛠️ Resolução

- Inicialmente, é ideal testar como funciona o campo de anúncio do site, por exemplo usando `{{7 * 7}}`.


<center><img src= 'SSTI1 fotos/test.png' ></center>


<center><img src= 'SSTI1 fotos/49.png' ></center>  

- Isso indica que o site está **interpretando a entrada como código dentro de um template engine**.**
- Observando que o nome da questão refere-se a **Server-side Template Injection**, podemos tentar executar comandos do sistema através do template.
- Inserindo o payload abaixo no mesmo campo onde foi colocado `{{7 * 7}}`:
```

	{{request.application.__globals__.__builtins__.__import__('os').popen('cat flag').read()}}

```


<center><img src= 'SSTI1 fotos/flag.png' ></center>  

---- 
### ✅ Conclusão

Neste desafio identificamos uma vulnerabilidade tipo **SSTI** ao observar que entradas no formato `{{ ... }}` eram avaliadas pelo servidor (por exemplo, `{{7 * 7}}` resultou em `49`). A exploração dessa falha permitiu executar um comando no sistema remoto para ler o conteúdo do arquivo `flag`. Em ambientes reais, essa classe de vulnerabilidade pode possibilitar execução remota de código (RCE) e comprometer completamente o servidor — por isso deve ser corrigida com prioridade. Medidas mitigadoras incluem: não inserir diretamente dados do usuário em templates, escapar/validar entradas, e usar motores de template que não permitam avaliação de expressões arbitrárias.

```
picoCTF{s4rv3r_s1d3_t3mp14t3_1nj3ct10n5_4r3_c001_09365533}
```
