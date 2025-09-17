## ✨ Desafio: Web Exploitation

> Link do desafio: https://play.picoctf.org/practice/challenge/270
> Link do material: [https://portswigger.net/web-security/file-path-traversal](https://portswigger.net/web-security/file-path-traversal)

---

## Introdução

<center><img src= 'fotos Forbidden Paths/Questão.png' ></center>

[Link do sesafio](http://saturn.picoctf.net:50826/)

```
# Web eReader

..
divine-comedy.txt
oliver-twist.txt
the-happy-prince.txt

  
Read
```

---
## 🛠️ Resolução

<center><img src= 'fotos Forbidden Paths/web_site.png' ></center>


```
divine-comedy.txt
oliver-twist.txt
the-happy-prince.txt
```

* Ao colocar esses **.txt** no campo **Filename**, somos levados a uma página onde aparecem esses livros.
- Inserindo os `..` somos levados para o diretório anterior.
- Sabe-se que o diretório para o qual a questão apontou foi `/usr/share/nginx/html/`.
- Então, voltando e procurando por `/../flag.txt`

A reposta completa é  ```../../../../flag.txt ``` 

<center><img src= 'fotos Forbidden Paths/flag.png' ></center>

---


## ✅ Conclusão

Neste desafio aplicamos a técnica de _path traversal_ para escapar do diretório web exposto e acessar arquivos sensíveis do sistema. Identificamos que a aplicação lista arquivos `.txt` e que a inserção de `..` permite subir na hierarquia de diretórios. Com isso, navegamos até a raiz do sistema de arquivos disponível ao serviço e localizamos o arquivo `/flag.txt`, cuja leitura revelou a flag do desafio:

```

picoCTF{7h3_p47h_70_5ucc355_e5a6fcbc}

```