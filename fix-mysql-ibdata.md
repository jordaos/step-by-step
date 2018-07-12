# Corrigindo "erro" `ibdata` no MySQL

Isso não é exatamente um erro. Alguns servidores tem armazenamento em disco muito restrito, e precisam armazenar muitos dados. Quando se utiliza o MySQL com as configurações padrões, ele irá criar um arquivo chamado `ibdata1`, que guardará todas as ações que ocorrem no banco de dados. Sendo assim, esse arquivo irá crescer bastante (no meu caso, tenho um `ibdata` de 66 GB), e assim, tomará muito espaço do servidor.

A solução é criar um arquivo desses por tabela. Essa opção já vem habilitada por padrão a partir do MySQL 5.6.6.

## Passo-a-passo

### 1. Prevenção a erros

Para previnir o pior caso, que é onde nada dá certo, faça um backup dos arquivos do mysql para um diretório de sua escolha.

```
service mysql stop && cp -ra /var/lib/mysql mysqldata && service mysql start
```

### 2. Faça um backup dos bancos de dados existentes

```
mysqldump --routines --events --flush-privileges --all-databases > all-db.sql
```

### 3. Criar e executar um script para apagar os bancos de dados. Não se pode apagar o banco `mysql` e nem `information_schema`

```
mysql -u root -p -e "SELECT DISTINCT CONCAT ('DROP DATABASE ',TABLE_SCHEMA,' ;') FROM INFORMATION_SCHEMA.TABLES WHERE TABLE_SCHEMA <> 'mysql' AN
D TABLE_SCHEMA <> 'information_schema';" | tail -n+2 > drop-script.sql
```

Verifique se o script deleta os bancos de dados necessário e execute-o:

```
mysql -u root -p < drop-script.sql
```
