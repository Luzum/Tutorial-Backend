## **Bancos de dados**

Existem dois tipos principais de bancos de dados: os **relacionais (SQL)** e os **não relacionais (NoSQL)**

Os relacionais têm seus dados armazenados em tabelas com uma estrutura (schema) definida. Ter uma estrutura definida, significa que uma coluna da tabela vai aceitar apenas o valor pré definido para ela, por exemplo, uma coluna de nome com schema definido para receber strings só aceitará strings, uma de idade configurada para receber apenas números não aceitará receber uma string ou qualquer outro valor que não seja um número e assim por diante. As tabelas de um banco de dados podem se relacionar entre si e todos os bancos de dados relacionais utilizam a linguagem SQL (**S**tructured **Q**uery **L**anguage). Por aqui vamos focar em bancos relacionais exatamente pelo fato de que utilizam a mesma linguagem.

**Estrutura**

Os dados são dividos em tabelas, onde cada coluna possui um tipo de valor (obrigatório) e também pode possuir restrições (opcionais), como obrigatoriedade, um valor padrão, etc. Cada item da tabela de possuir **obrigatoriamente** uma Primary Key-**PK**- que é um identificador único para diferenciar os items entre si, casos existam itens com o mesmo nome (é como se fosse um id).

**Workbench**

O banco de dados roda em uma máquina remota e para acessá-lo necessitamos de uma aplicação que consegue interagir com o banco. Em nosso caso utilizaremos o MySQL Workbench.
Para ais informações sobre as configurações, clique [aqui](https://ajuda.hostnet.com.br/conexao-ao-banco-de-dados-via-mysql-workbench/)).

**Structured Query Language**

É a linguagem que utilizamos para fazer consultas em bancos de dados. Ela possui uma sintaxe própria e bem documentada, veja [aqui](https://www.w3schools.com/sql/default.asp) e também [aqui](https://www.w3schools.com/mysql/default.asp).

A indicação do tipo de dado que queremos em cada coluna é diferente do typescript:

* VARCHAR(x) - strings com um número máximo de (x) de caracteres
* INT - números inteiros
* FLOAT - números não inteiros
* DATE - data no formato (AAAA-MM-DD)
* TIMESTAMP - data com tempo (AAAA-MM-DD hh:mm:ss)

**Criar Tabela**
```
CREATE TABLE nome_tabela(
    primeira_coluna VARCHAR(255) PRIMARY KEY,
    segunda_coluna INT NOT NULL
    terceira_coluna DATE NOT NULL,
    quarta_coluna FLOAT NOT NULL,
    quinta_coluna
);
```
Primeiro temos o comando CREATE TABLE, seguido do nome que quermos dar à tabela e cada linha dentro dos parenteses representam um coluna da tabela. Primeiro vemo o nome da coluna, seguido de seu tipo (VARCHAR, INT, DATE, etc.) e por fim a restrição que é opcional, no caso utilizamos o NOT NULL, que significa que toda vez que se for adicionar uma nova linha na tabela aquela coluna tem que obrigatoriamente ser preenchida e o UNIQUE, que indica que aquele valor deve ser único por item (como um email registrado, por exemplo). Lembrando que a PK é **obrigatória** em todas as tabelas. Ah, o **;** é **obrigatório** no fim de cada comando no MySQL. 

Para deletar uma tabela utilizamos o comando:

```
DROP TABLE nome_tabela
```

E para adicionar algo em uma tabela? Utilizamos o seguinte comando: 

```
INSERT INTO nome_tabela (primeira_coluna, segunda_coluna, terceira_coluna, quarta_coluna, quinta_coluna)
VALUES (valor1, valor2, valor3, valor4, valor5)
```
Cada conjunto de parenteses é equivalente à um objeto dentro de uma lista de objetos. Nos primeiros parenteses você escreve quais colunas você quer preencher, lembrando que as obrigatórias sempre tem que ser preenchidas, e no segundo conjunto de parenteses você passa o valor de cada coluna.

É possível preencher mais de uma linha de uma vez e nos primeiros parenteses as colunas não precisam necessariamente estar em ordem, basta respeitar a ordem que você estabelecer nos VALUES. Vamos para um exemplo mais concreto: 

```
CREATE TABLE pessoas (
    id VARCHAR(255) PRIMARY KEY,
    nome VARCHAR(255) NOT NULL,
    email VARCHAR(255) NOT NULL,
    apelido VARCHAR(255)
);

INSERT INTO pessoas (nome, id, email)
VALUES ("FULANO", "0001", "fulano@email.com"),
("Ciclano", "0002", "ciclano@email.com");
```
Assim criamos uma tabela de nome pessoas com as colunas id, nome, email e apelido. Depois inserimos nela duas pessoas. Note que não passamos os parâmetros na mesma ordem da tabela e não tem problema, desde que a dos VALUES seja a mesma daquela do INSERT INTO. O MySQL faz sua magia por debaixo dos panos e deixa na ordem certa pra gente (eu particularmente prefiro deixar na mesma ordem que fiz a tabela, só para não criar confusões). Por fim veja que não passamos o apelido de nenhuma das duas pessoas, porque ele é opcional! Se quisessemos inserir uma pessoa com seu apelido seria assim:

```
INSERT INTO pessoas (id, nome, email, apelido)
VALUES ("Beltrano", "0003", "beltrano@email.com", "Beltraninho");
```

Mais uma forma de inserir uma pessoa na tabela seria assim:

```
INSERT INTO pessoas
VALUES ("josé", "0004", "jose@email.com", "zezim");
```
Aqui precisamos respeitar a ordem das colunas e preencher todas obrigatoriamente.

**Apagar linhas da tabela**

Usamos o comando:

```
DELETE FROM pessoas WHERE id = "0001";
```
Where significa onde em português, então esse comando deletará a linha onde o id é igual a "0001".
**CUIDADO**, se você utilizar apenas o comando:

```
DELETE FROM pessoas;
```
você deletará **TODOS** os dados de sua tabela. Outro comando para deletar **TODOS** os dados de sua tabela é o TRUNCATE, utilizado da seguinte forma:

```
TRUNCATE TABLE pessoas;
```
**CUIDADO**, o TRUNCATE é **IRREVERSÍVEL** e só deve ser usado caso você realmente queria apagar **TODOS** os dados de sua tabela. E **SEMPRE** certifique-se de ter especificado o **NOME** da tabela onde você vai usar o TRUNCATE, se não pode acabar deletando os dados de **TODAS AS SUA TABELAS**.


**Consultando tabelas**

* SHOW TABLES: mostra todas as tabelas que temos em nosso SCHEMA: 

```
SHOW TABLES;
```

* DESCRIBE: comando utilizado para se ver a estrutura de uma tabela, tente utilizá-lo com a tabela pessoas da seguinte forma:

```
DESCRIBE pessoas;
```

* SELECT: comando utilizado para pegar dados no banco. Deve receber as colunas que queremos buscar em qual tabela. Podemos adicionar algumas condições se utilizarmos em conjunto com WHERE (o mesmo que vimos no delete).

```
SELECT * FROM pessoas;
-- retorna todas as informações da tabela pessoas

SELECT * FROM pessoas WHERE id = "0001";
-- retorna todas as colunas da tabela pessoas onde o id é "0001", no caso
-- serão as informações de "Ciclano"

SELECT nome, apelido FROM pessoas WHERE ID = "0003";
-- retorna o nome e o apelido da tabela pessoas onde o id é "0003"
```

**Operadores condicionais**
Existem vários operadores condicionais no MySQL veja eles [aqui](https://www.w3schools.com/mysql/mysql_operators.asp), [aqui](https://www.w3schools.com/mysql/mysql_and_or.asp) e [aqui](https://www.w3schools.com/mysql/mysql_in.asp)



