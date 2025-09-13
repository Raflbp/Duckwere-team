>	Autor: @Ralfbp{Z4π}

---
## ✨ Desafio: Web Exploitation

> Link do desafio:  https://play.picoctf.org/practice/challenge/180
> Link do material: https://portswigger.net/web-security/file-path-traversal
---
## Introdução

<center><img src='Imagens Super Serial/Quetão.png'></center> 

```
http://mercury.picoctf.net:25395/
```
---
## 🛠️ Resolução

<center><img src='Imagens Super Serial/Pagina_login.png'></center>  

* utilizando o robots.txt

```
http://mercury.picoctf.net:25395/robots.txt
```

<center><img src='Imagens Super Serial/Robots.txt.png'></center>  

```
User-agent: *
Disallow: /admin.phps
```

* Com isso sabemos que existe um /admin
* Mas al tentar acessar, não conseguimos usar o /admin.phps, /admin e nem o /admin.php

<center><img src='Imagens Super Serial/admin.phps.png'></center>  

* Sabendo que ele existe, é necessário outra abordagem, para se ter a flag.
*  Fazendo o teste, com letras e números aleatório no login e senha.
*  login e senha utilizado **teste** .
  

<center><img src='Imagens Super Serial/Test.png'></center>  


*  somos levados para outro diretório .

```
http://mercury.picoctf.net:25395/index.php
```

* Adicionando a letra **S** no final desse **index.php** para se analisar o código
* Receberemos o código do diretório **index.php**

```
http://mercury.picoctf.net:25395/index.phps
```

 * Com isso recebemos o código desse diretório 
 
```
 
	 <?php
	require_once("cookie.php");
	
	if(isset($_POST["user"]) && isset($_POST["pass"])){
		$con = new SQLite3("../users.db");
		$username = $_POST["user"];
		$password = $_POST["pass"];
		$perm_res = new permissions($username, $password);
		if ($perm_res->is_guest() || $perm_res->is_admin()) {
			setcookie("login", urlencode(base64_encode(serialize($perm_res))), time() + (86400 * 30), "/");
			header("Location: authentication.php");
			die();
		} else {
			$msg = '<h6 class="text-center" style="color:red">Invalid Login.</h6>';
		}
	}
	?>
	
	<!DOCTYPE html>
	<html>
	<head>
	<link href="https://stackpath.bootstrapcdn.com/bootstrap/4.3.1/css/bootstrap.min.css" rel="stylesheet" integrity="sha384-ggOyR0iXCbMQv3Xipma34MD+dH/1fQ784/j6cY/iJTQUOhcWr7x9JvoRxT2MZw1T" crossorigin="anonymous">
	<link href="style.css" rel="stylesheet">
	<script src="https://stackpath.bootstrapcdn.com/bootstrap/4.3.1/js/bootstrap.min.js" integrity="sha384-JjSmVgyd0p3pXB1rRibZUAYoIIy6OrQ6VrjIEaFf/nJGzIxFDsf4x0xIM+B07jRM" crossorigin="anonymous"></script>
	</head>
		<body>
			<div class="container">
				<div class="row">
					<div class="col-sm-9 col-md-7 col-lg-5 mx-auto">
						<div class="card card-signin my-5">
							<div class="card-body">
								<h5 class="card-title text-center">Sign In</h5>
								<?php if (isset($msg)) echo $msg; ?>
								<form class="form-signin" action="index.php" method="post">
									<div class="form-label-group">
										<input type="text" id="user" name="user" class="form-control" placeholder="Username" required autofocus>
										<label for="user">Username</label>
									</div>
	
									<div class="form-label-group">
										<input type="password" id="pass" name="pass" class="form-control" placeholder="Password" required>
										<label for="pass">Password</label>
									</div>
	
									<button class="btn btn-lg btn-primary btn-block text-uppercase" type="submit">Sign in</button>
								</form>
							</div>
						</div>
					</div>
				</div>
			</div>
		</body>
		</html>
 ```

* Ao analisar esse códigos se vê que existe um **cookie.php** ao se testar, para ver se é um diretório que te retorna algo

<center><img src='Imagens Super Serial/cookie.php.png'></center>

```
http://mercury.picoctf.net:25395/cookie.php
```

* Utilizando novamente o **S** para se ler o código do  **cookie.php**

```
http://mercury.picoctf.net:25395/cookie.phps
```

* Recebemos o código  cookie.phps

```
<?php
session_start();

class permissions
{
	public $username;
	public $password;

	function __construct($u, $p) {
		$this->username = $u;
		$this->password = $p;
	}

	function __toString() {
		return $u.$p;
	}

	function is_guest() {
		$guest = false;

		$con = new SQLite3("../users.db");
		$username = $this->username;
		$password = $this->password;
		$stm = $con->prepare("SELECT admin, username FROM users WHERE username=? AND password=?");
		$stm->bindValue(1, $username, SQLITE3_TEXT);
		$stm->bindValue(2, $password, SQLITE3_TEXT);
		$res = $stm->execute();
		$rest = $res->fetchArray();
		if($rest["username"]) {
			if ($rest["admin"] != 1) {
				$guest = true;
			}
		}
		return $guest;
	}

        function is_admin() {
                $admin = false;

                $con = new SQLite3("../users.db");
                $username = $this->username;
                $password = $this->password;
                $stm = $con->prepare("SELECT admin, username FROM users WHERE username=? AND password=?");
                $stm->bindValue(1, $username, SQLITE3_TEXT);
                $stm->bindValue(2, $password, SQLITE3_TEXT);
                $res = $stm->execute();
                $rest = $res->fetchArray();
                if($rest["username"]) {
                        if ($rest["admin"] == 1) {
                                $admin = true;
                        }
                }
                return $admin;
        }
}

if(isset($_COOKIE["login"])){
	try{
		$perm = unserialize(base64_decode(urldecode($_COOKIE["login"])));
		$g = $perm->is_guest();
		$a = $perm->is_admin();
	}
	catch(Error $e){
		die("Deserialization error. ".$perm);
	}
}

?>
```


* Analisando-o se entende como é feito o cookie de admin e usuário
* Se vê também que existe outro arquivo **php** que seria o **authentication.php**   


<center><img src='Imagens Super Serial/welcome.png'></center>

* Adicionando o **S** para analisarmos esse código temos 

```
http://mercury.picoctf.net:25395/authentication.phps
```

* **authentication.phps**

```
	<?php
	session_start();
	
	class permissions
	{
		public $username;
		public $password;
	
		function __construct($u, $p) {
			$this->username = $u;
			$this->password = $p;
		}
	
		function __toString() {
			return $u.$p;
		}
	
		function is_guest() {
			$guest = false;
	
			$con = new SQLite3("../users.db");
			$username = $this->username;
			$password = $this->password;
			$stm = $con->prepare("SELECT admin, username FROM users WHERE username=? AND password=?");
			$stm->bindValue(1, $username, SQLITE3_TEXT);
			$stm->bindValue(2, $password, SQLITE3_TEXT);
			$res = $stm->execute();
			$rest = $res->fetchArray();
			if($rest["username"]) {
				if ($rest["admin"] != 1) {
					$guest = true;
				}
			}
			return $guest;
		}
	
	        function is_admin() {
	                $admin = false;
	
	                $con = new SQLite3("../users.db");
	                $username = $this->username;
	                $password = $this->password;
	                $stm = $con->prepare("SELECT admin, username FROM users WHERE username=? AND password=?");
	                $stm->bindValue(1, $username, SQLITE3_TEXT);
	                $stm->bindValue(2, $password, SQLITE3_TEXT);
	                $res = $stm->execute();
	                $rest = $res->fetchArray();
	                if($rest["username"]) {
	                        if ($rest["admin"] == 1) {
	                                $admin = true;
	                        }
	                }
	                return $admin;
	        }
	}
	
	if(isset($_COOKIE["login"])){
		try{
			$perm = unserialize(base64_decode(urldecode($_COOKIE["login"])));
			$g = $perm->is_guest();
			$a = $perm->is_admin();
		}
		catch(Error $e){
			die("Deserialization error. ".$perm);
		}
	}
	
	?>
 
```
