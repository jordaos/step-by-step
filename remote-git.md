# Adicionar um git remoto no servidor

## Pré-requisitos
- Ter uma chave local (de preferência id_rsa)

## Passo a passo

### 1. `ssh-copy-id user@host`: copiar sua chave local para o servidor remoto. Com isso você poderá acessar o servidor sem informar a senha.
### 2. Configurar um alias, para não ficar digitando `user@host`:

`vim ~/.ssh/config`

```
Host myhost
 HostName <host>
 Port <port>
 User <user>
 IdentityFile <~/.ssh/id_rsa>
 PreferredAuthentications publickey,password
```

agora para acessar o servidor basta digitar `ssh myhost`
### 3. Configurar o usuário git no servidor remoto

```
git config --global user.email "name@example.com"
git config --global user.name "Your Name"
```
### 4. Crie um repositório dentro de `www` com o nome do seu projeto e inicie como um projeto GIT

```
mkdir repo
cd repo
git init
```

### 5. Aceitar que o repositório aceite _Pushes_

```
git config receive.denyCurrentBranch ignore
```

### 6. Usar um `git hook` para receber o push e modificar o código

```
nano .git/hooks/post-receive
```

Cole isto:
```
#!/bin/sh
GIT_WORK_TREE=../ git checkout -f
```

Torne o arquivo executável
```
chmod +x .git/hooks/post-receive
```

## Configurando o repositório local

### 7. Associando com o servidor remoto

Entre (ou crie) no repositório do projeto local. Se você já tiver um projeto inicializado com git, não precisa executar o próximo comando.
```
cd meu-projeto
git init
```

Adicionar o servidor remoto:
```
git remote add deploy ssh://user@host:PORT/home/USER/public_html/REPOSITORY
```

### 8. Subir o código para o servidor remoto (deploy)

```
git add .
git commit -m "Commit"
git push -u deploy master
```

## Redirecionar o fluxo para o diretório do seu projeto

Como o projeto está em um diretório dentro de `public_html`, para acessar o site, seria necessário adicionar o nome do diretório na URL. 

EX: http://www.meudominio.com/repository-name

Para evitar isso, podemos adicionar um arquivo que encaminha as requisições do domínio principal (http://www.meudominio.com) para o diretório de nossa escolha. Para fazer isso, basta adicionar um arquivo chamado `.htaccess` dentro do diretório `public_html`, com o seguinte conteúdo:

```
RewriteEngine On
RewriteBase /

RewriteCond %{THE_REQUEST} /REPO-NAME/([^\s?]*) [NC]
RewriteRule ^ %1 [L,NE,R=302]

RewriteRule ^((?!REPO-NAME/).*)$ REPO-NAME/$1 [L,NC]
```

Lembre-se de substituir `REPO-NAME` pelo nome do seu repositório.

## Possíveis erros

Erro: `fatal: The remote end hung up unexpectedly`

Correção: `git config http.postBuffer 524288000`
