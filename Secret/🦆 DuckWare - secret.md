## ✨ Desafio: Web Exploitation

> Link do desafio: [https://play.picoctf.org/practice/challenge/296](https://play.picoctf.org/practice/challenge/296) 
> Link do material: [https://portswigger.net/web-security/file-path-traversal](https://portswigger.net/web-security/file-path-traversal)

---

## Introdução


<center><img src= 'Imagens secret/question_not_run.png' ></center>

- Ao clicar em "**Launch Instance**", a questão se inicia e disponibiliza um link.
   

<center><img src= 'Imagens secret/question_start.png' ></center>

- Clique **aqui** para começar.
   

```
http://saturn.picoctf.net:56446/
```

---

## 🛠️ Resolução

- A questão leva para um site com três diretórios.
- 

<center><img src= 'Imagens secret/home.png' ></center> <center><img src= 'Imagens secret/strong.png' ></center><center><img src= 'Imagens secret/latin.png' ></center>

```
HomeAboutContact
Lorem ipsum dolor sit amet, consectetur adipiscing elit
Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nunc diam urna, viverra id sagittis vel, ultricies vel ante. Suspendisse tempus suscipit sem ac blandit. Etiam et ex velit. Vestibulum nibh neque, placerat eget sodales bibendum, pulvinar a libero. Proin turpis nisi, imperdiet ac felis sed, posuere tincidunt enim. Maecenas in pretium velit, eu consectetur erat. Aliquam viverra laoreet laoreet. Phasellus sapien ipsum, euismod pellentesque tincidunt eu, euismod vitae lectus. Nulla in sem velit. Aliquam ut nisi molestie, auctor nulla at, tempus leo. Nulla id nisl convallis, bibendum urna eu, fringilla turpis. Nam sodales erat vel sapien fermentum scelerisque. Vivamus iaculis nisl at eros aliquam, a pulvinar magna placerat. Sed eu commodo sapien. Aliquam tristique dapibus urna, in blandit lacus volutpat vel.

Lorem ipsum dolor sit amet, consectetur adipiscing elit
Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nunc diam urna, viverra id sagittis vel, ultricies vel ante. Suspendisse tempus suscipit sem ac blandit. Etiam et ex velit. Vestibulum nibh neque, placerat eget sodales bibendum, pulvinar a libero. Proin turpis nisi, imperdiet ac felis sed, posuere tincidunt enim. Maecenas in pretium velit, eu consectetur erat. Aliquam viverra laoreet laoreet. Phasellus sapien ipsum, euismod pellentesque tincidunt eu, euismod vitae lectus. Nulla in sem velit. Aliquam ut nisi molestie, auctor nulla at, tempus leo. Nulla id nisl convallis, bibendum urna eu, fringilla turpis. Nam sodales erat vel sapien fermentum scelerisque. Vivamus iaculis nisl at eros aliquam, a pulvinar magna placerat. Sed eu commodo sapien. Aliquam tristique dapibus urna, in blandit lacus volutpat vel.

Lorem ipsum dolor sit amet, consectetur adipiscing elit
Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nunc diam urna, viverra id sagittis vel, ultricies vel ante. Suspendisse tempus suscipit sem ac blandit. Etiam et ex velit. Vestibulum nibh neque, placerat eget sodales bibendum, pulvinar a libero. Proin turpis nisi, imperdiet ac felis sed, posuere tincidunt enim. Maecenas in pretium velit, eu consectetur erat. Aliquam viverra laoreet laoreet. Phasellus sapien ipsum, euismod pellentesque tincidunt eu, euismod vitae lectus. Nulla in sem velit. Aliquam ut nisi molestie, auctor nulla at, tempus leo. Nulla id nisl convallis, bibendum urna eu, fringilla turpis. Nam sodales erat vel sapien fermentum scelerisque. Vivamus iaculis nisl at eros aliquam, a pulvinar magna placerat. Sed eu commodo sapien. Aliquam tristique dapibus urna, in blandit lacus volutpat vel.

Lorem ipsum dolor sit amet, consectetur adipiscing elit
Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nunc diam urna, viverra id sagittis vel, ultricies vel ante. Suspendisse tempus suscipit sem ac blandit. Etiam et ex velit. Vestibulum nibh neque, placerat eget sodales bibendum, pulvinar a libero. Proin turpis nisi, imperdiet ac felis sed, posuere tincidunt enim. Maecenas in pretium velit, eu consectetur erat. Aliquam viverra laoreet laoreet. Phasellus sapien ipsum, euismod pellentesque tincidunt eu, euismod vitae lectus. Nulla in sem velit. Aliquam ut nisi molestie, auctor nulla at, tempus leo. Nulla id nisl convallis, bibendum urna eu, fringilla turpis. Nam sodales erat vel sapien fermentum scelerisque. Vivamus iaculis nisl at eros aliquam, a pulvinar magna placerat. Sed eu commodo sapien. Aliquam tristique dapibus urna, in blandit lacus volutpat vel.

Lorem ipsum dolor sit amet, consectetur adipiscing elit
Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nunc diam urna, viverra id sagittis vel, ultricies vel ante. Suspendisse tempus suscipit sem ac blandit. Etiam et ex velit. Vestibulum nibh neque, placerat eget sodales bibendum, pulvinar a libero. Proin turpis nisi, imperdiet ac felis sed, posuere tincidunt enim. Maecenas in pretium velit, eu consectetur erat. Aliquam viverra laoreet laoreet. Phasellus sapien ipsum, euismod pellentesque tincidunt eu, euismod vitae lectus. Nulla in sem velit. Aliquam ut nisi molestie, auctor nulla at, tempus leo. Nulla id nisl convallis, bibendum urna eu, fringilla turpis. Nam sodales erat vel sapien fermentum scelerisque. Vivamus iaculis nisl at eros aliquam, a pulvinar magna placerat. Sed eu commodo sapien. Aliquam tristique dapibus urna, in blandit lacus volutpat vel.

Lorem ipsum dolor sit amet, consectetur adipiscing elit
Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nunc diam urna, viverra id sagittis vel, ultricies vel ante. Suspendisse tempus suscipit sem ac blandit. Etiam et ex velit. Vestibulum nibh neque, placerat eget sodales bibendum, pulvinar a libero. Proin turpis nisi, imperdiet ac felis sed, posuere tincidunt enim. Maecenas in pretium velit, eu consectetur erat. Aliquam viverra laoreet laoreet. Phasellus sapien ipsum, euismod pellentesque tincidunt eu, euismod vitae lectus. Nulla in sem velit. Aliquam ut nisi molestie, auctor nulla at, tempus leo. Nulla id nisl convallis, bibendum urna eu, fringilla turpis. Nam sodales erat vel sapien fermentum scelerisque. Vivamus iaculis nisl at eros aliquam, a pulvinar magna placerat. Sed eu commodo sapien. Aliquam tristique dapibus urna, in blandit lacus volutpat vel.
```

- Ao analisar os três diretórios, nada é encontrado.
   
- Indo para o "**page source**" com o atalho **CTRL + U** ou clicando com o botão direito do mouse e selecionando "**View page source**".
  

<center><img src= 'Imagens secret/secrets.png'></center>


- Temos um link para `[secret/assets/index.css](http://saturn.picoctf.net:56446/secret/assets/index.css)`, mas ele apenas direciona para a imagem da página 1.

- No entanto, ao adicionar `/secret/` ao link inicial, um novo diretório é descoberto.    

```
http://saturn.picoctf.net:56446/secret/
```

<center><img src= 'Imagens secret/Pag_2.png' ></center>

- A página exibe apenas um meme.
    
- Ao analisar o código desse diretório novamente, usando **CTRL + U** ou clicando com o botão direito do mouse e selecionando "**View page source**".
    

<center><img src= 'Imagens secret/hidden.png' ></center>

- Há um link no código: `[hidden/file.css](http://saturn.picoctf.net:53023/secret/hidden/file.css)`, que leva a uma página em branco.

- Mas ao usar a primeira parte do comando na barra de endereço, `/hidden/`.  


```
http://saturn.picoctf.net:53023/secret/hidden/
```

<center><img src= 'Imagens secret/Pag_3.png' ></center>  


- Um segundo diretório secreto é encontrado, que novamente não exibe nada.
- Tentativas de login ou registro não funcionam.
- Ao clicar em "login", uma mensagem de aviso aparece.
- 

<center><img src= 'Imagens secret/tentativa.png' ></center>  


- Após o `Alert()`, uma nova abordagem é iniciada.
- Acessando o "**page source**" novamente com **CTRL + U** ou clicando com o botão direito do mouse e selecionando "**View page source**".


<center><img src= 'Imagens secret/superhidden.png' ></center>  

- O código tem a mesma palavra para entrar no diretório atual, `hidden`, mas com uma pequena diferença, `superhidden`.
- Adicionando à barra de endereço para entrar no diretório.

```
http://saturn.picoctf.net:56446/secret/hidden/superhidden/
```

<center><img src= 'Imagens secret/pg_sem_flag.png' ></center>  


- Apenas uma imagem em branco é exibida, mas ao usar **CRTL + A**, a flag aparece.


<center><img src= 'Imagens secret/flag.png' ></center>  


---


## ✅ Conclusão

Este desafio demonstra a importância de **inspecionar cuidadosamente o código-fonte** de páginas web e de **explorar possíveis caminhos ocultos** em estruturas de diretórios. A técnica de **path traversal**, combinada com a persistência em investigar cada camada, permitiu encontrar a flag escondida em um diretório aninhado.

A flag `picoCTF{succ3ss_@h3n1c@10n_51b260fe}` foi obtida com sucesso, mostrando habilidades básicas de **web exploitation** e atenção a detalhes que, frequentemente, passam despercebidos.
