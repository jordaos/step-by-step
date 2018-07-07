# Adicionar um git remoto no servidor

## Pré-requisitos
- Ter uma chave local (de preferência id_rsa)

## Passo a passo

1. `ssh-copy-id user@host`: copiar sua chave local para o servidor remoto. Com isso você poderá acessar o servidor sem informar a senha.
2. Configurar um alias, para não ficar digitando `user@host`:

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

...
[continua quando eu fizer o resto]
