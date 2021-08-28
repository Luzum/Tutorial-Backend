## **Relações em SQL**

Nós aprendemos como criar tabelas, como manipulá-las, como integrar nossa API com o banco de dados, mas ainda não vimos algo importantíssimo: como relacionar tabelas SQL, como realizar consultas que abrangem **mais de uma tabela**.

**Tipos de relações:**

* **1:1**:

O nome é bem explicativo, um pra um. Ou seja é uma relação onde **uma** linha de uma tabela se relaciona com **uma** linha de outra tabela. Mas como assim? Vamos supor que em um site tenhamos dois bancos de dados dos usuários, um para informações pessoais como nome, idade, email, etc. E outro para dados de login, como nome de usuário e senha. Uma pessoa ocupa uma linha em uma tabela, assim como as informações da conta ocupam uma linha na outra tabela. Assim temos **uma** linha que se relaciona com apenas **uma** outra linha.

* **1:N**

Essa é uma relação de 1 pra N. Ou seja, uma relação onde **uma** linha de uma tabela se relaciona com **duas ou mais** linhas de outra tabela.
Vamos supor que tenhamos duas tabelas: uma de agricultores orgânicos e outra de produtos orgânicos. O agricultor André vende alfaces e tomates orgânicos, já a agricultora Raissa vende morangos, laranjas e couves orgânicas. **UM** agricultor vende mais **dois ou mais** produtos. Note que nesse caso sempre vai haver **mais de um** produto vendido, **mas** Raissa **em nenhum** caso venderá alfaces e/ou tomates e André jamais venderá morangos, laranjas e couves.

* **N:M** 

Já essa é a relação onde **duas ou mais** linhas de uma tabela se relacionam com **duas ou mais** linhas de outra tabela. Vamos supor que agora tenhamos uma tabela de compradores de produtos orgânicos: a Amanda e o Bruno. Amanda e Bruno podem comprar **qualquer** um dos produtos e **cada produto** pode estar relacionado com **mais de um** comprador. Amanda vai comprar tomates e couve, enquanto Bruno vai comprar couve, laranja e alface. 

### **Prática**

Vamos criar algumas tabelas para ver como essas relações funcionam na prática:

**Relação 1:1:**
```
CREATE TABLE consumidores (
    id VARCHAR(255) PRIMARY KEY,
    nome VARCHAR(255),
    apelido VARCHAR(255)
);

INSERT INTO consumidores VALUES
('a', 'Amanda', 'Amandinha'),
('b', 'Bruno', 'Brunoia');

SELECT * FROM consumidores;
```
```
CREATE TABLE contas (
    id VARCHAR(255),
    saldo FLOAT DEFAULT 0,
    id_usuario VARCHAR(255),
    FOREIGN KEY (id_usuario) REFERENCES consumidores(id)
);

INSERT INTO contas VALUES
('0001', 250.50, 'a'),
('0002', 250.50, 'b');
SELECT * FROM contas;
```
Vamos destrinchar um pouco as tabelas. Na criação da tabela de contas estamos vendo pela primeira vez o uso da FOREIGN KEY e das REFERENCES. Foreign key é a chave estrangeira, e REFERENCES nesse caso significa referencia. Então a FOREIGN KEY (id_usuario) referencia a tabela consumidores. Veja que **precisamos** criar uma coluna da tabela a id_usuario nesse caso que faz uma referência à coluna id da tabela consumidores.

Veja que colocamos 'a' e 'b' como id_usuario na tabela contas. E que os ids 'a' e 'b' na tabela consumidores pertencem à Amanda e Bruno, respectivamente. Assim podemos inferir que a conta de id '0001' pertence à Amanda e a conta de id '0002' pertence ao Bruno. Temos aí uma relação de 1:1. **Uma** linha da tabela se relaciona com **uma** linha de outra tabela. Um consumidor possui uma conta!

Mas não poderíamos fazer tudo em uma tabela só? Sim, mas por questões de performance talvez seja mais interessante se ter duas tabelas, ainda mais se alguma das colunas for lida e/ou alterada com mais frequência que as outras. E também tudo fica mais organizado assim! Dá um pouco mais de trabalho inicialmente, mas tudo é compensado mais tarde.

No campo de id_usuario se tentássemos preencher com um id que não existe na tabela de consumidores, a query não funcionaria! Tente fazer isso e veja a mensagem que recebe!

**Relação 1:N**

Vamos ver agora a relação 1:N 

```
CREATE TABLE vendedores (
    id VARCHAR(255) PRIMARY KEY,
    nome VARCHAR(255),
    apelido VARCHAR(255)
);
INSERT INTO vendedores VALUES
('c', 'Raissa', 'Cissa'),
('d', 'André', 'Dé');

SELECT * FROM vendedores;
```

```
CREATE TABLE produtos( 
     id VARCHAR(255) PRIMARY KEY,
    nome VARCHAR(255) UNIQUE,
    preço FLOAT,
    id_vendedor VARCHAR(255),
    FOREIGN KEY (id_vendedor) REFERENCES vendedores(id)
);

INSERT INTO produtos VALUES
('alf', 'alface', 1.5, 'd'),
('lar', 'laranja', 3.5, 'c'),
('tom', 'tomate', 2.5, 'd'),
('cov', 'couve', 1.5, 'c'),
('mor', 'morango', 3, 'c');

SELECT * FROM produtos;
```
Veja que assim que temos uma relação **1:n**. Cissa que ocupa uma linha da tabela vendedores se relaciona com 3 linhas da tabela produtos, e André por sua vez com 2 linhas. Novamente lembrando que na última coluna de nossa tabela produtos (id_vendedor) só podemos utilizar como valor um **id existente** da tabela de vendedores! Note também que na coluna nome adicionamos a **constraint UNIQUE** que faz com que não possamos adicionar mais de uma vez o mesmo produto!

O código abaixo funcionaria?
```
INSERT INTO produtos VALUES
('cen', 'cenoura', 2, 'j');
```
**Não**, pois não há em nossa tabela de vendedores um vendedor com o id J. Tente fazer isso no seu MySQL e veja a mensagem de erro!

**Relação N:M**
Por fim vamos ver na prática como ocorre a relação onde várias linhas de uma tabela se relacionam com várias linhas de outra tabela. Nesse caso, precisamos criar uma **tabela intermediária**:

```
CREATE TABLE compras (
    id_consumidor VARCHAR(255),
    id_produto VARCHAR(255),
    FOREIGN KEY (id_consumidor) REFERENCES consumidores(id),
    FOREIGN KEY (id_produto) REFERENCES produtos(id)
);

INSERT INTO compras VALUES
('a', 'alf'), ('a', 'tom'), ('a', 'cov'),
('b', 'cov'), ('b', 'mor'), ('b', 'lar');

SELECT * FROM compras
```
Veja que temos uma tabela com **2 colunas**. Uma dedicada para o id do consumidor e outra para o id do produto e **ambas** usam FOREIGN KEYS, ou seja, dependem de valores **já existentes** em outra tabela para funcionarem. Assim, nessa tabela de compras temos a Amanda comprando alface, tomate e couve e Bruno comprando couve, morango e laranja. Faça no MySQL e veja o resultado. Tente inserir ids que não existem nas tabelas referenciadas e veja o que acontece.

E se você quisesse deletar a tabela de consumidores agora? Você precisaria deletar primeiro a tabela **compras**, porque ela referencia a tabela de consumidores. Tente deletar a tabela de consumidores **sem** deletar a de compras e veja o que acontece. E se quiséssemos deletar apenas uma linha da tabela de produtos, por exemplo? Aí precisaríamos antes de deletar todas as referências deste produto antes! Vamos supor que não tenhamos mais alfaces: teríamos que deletar de nossa tabela de compras a compra de André para então conseguirmos deletar as alfaces de nossos produtos!

**Join**
Tá, temos as tabelas que referenciam umas as outras mas e aí? Como faremos para fazer uma busca que pegue dados de mais de uma tabela? Vamos usar o operador [JOIN](https://www.w3schools.com/mysql/mysql_join.asp) do MySQL. Com ele podemos combinar linhas de **duas ou mais** tabelas que tenham alguma coluna de relação. 

Vamos ver como fazer:

```
SELECT * 
FROM contas
JOIN consumidores 
ON contas.id_usuario = consumidores.id;
```
Ao rodarmos este comando, receberemos de volta o *id*, *saldo* e *id_usuario* da tabela de contas e o *id*, *nome*, e *apelido* da tabela de consumidores. Note que a coluna *id_usuario* da tabela contas e a coluna *id* da tabela consumidores tem o **mesmo** valor. Isso acontece por conta da **referência** que fizemos anteriormente! O comando **ON** é extremamente importante! Se você não utilizá-lo, receberá quatro linhas de retorno e em duas delas terá os dados **misturados**. O **ON** garante com que a junção das tabelas ocorra apenas onde o id_usuario for **igual** ao **id**.

Podemos diminuir o número de colunas que queremos receber no retorno, basta especificar quais colunas queremos, por exemplo:

```
SELECT consumidores.id, nome, saldo  
FROM contas
JOIN consumidores 
ON contas.id_usuario = consumidores.id;
```

Assim teremos como retorno o id da tabela consumidores, o nome e o saldo. Note que tive que especificar de qual tabela queria que o id fosse retornado. Se quisesse que fosse da tabela contas, teria que utilizar o comando contas.id. Sempre que duas ou mais tabelas tem colunas com **nomes iguais** devemos especificar o nome da tabela que queremos a coluna retornada. 

E se quiséssemos juntar mais de duas tabelas? Poderíamos fazer da seguinte forma:
```
SELECT id_produto, consumidores.nome, produtos.nome AS produto_organico, preço 
FROM compras
JOIN consumidores
ON id_consumidor = consumidores.id
JOIN produtos
ON id_produto = produtos.id;
```
Assim, teremos no retorno o id do produto(da tabela compras), o nome do consumidor (da tabela consumidores), o nome do produto comprado(tabela produtos) e também o preço(da tabela produtos)! Veja que **alteramos** o nome de uma das colunas da tabela! Teríamos duas colunas de nome **nome** e para evitar isso mudamos para **produto_organico** a coluna que veio da tabela produtos. Isso **não** muda o nome da coluna original, apenas **em nossa consulta**.

Se quiséssemos ainda colocar o nome do vendedor em nossa tabela, a consulta ficaria assim:
```
SELECT id_produto, consumidores.nome, produtos.nome AS produto_organico, preço, vendedores.nome AS vendedor FROM compras
JOIN consumidores
ON id_consumidor = consumidores.id
JOIN produtos
ON id_produto = produtos.id
JOIN vendedores
ON id_vendedor = vendedores.id;
```
Vá explorando as possibilidades!

**Abreviação:**

Podemos abreviar os nomes de nossas tabelas para deixar as consultas menos extensas da seguinte forma:
```
SELECT id_produto, c.nome, p.nome AS produto_organico, preço, v.nome AS vendedor FROM compras
JOIN consumidores c
ON id_consumidor = c.id
JOIN produtos AS p
ON id_produto = p.id
JOIN vendedores v
ON id_vendedor = v.id;
```
Basta colocar logo **após** o nome da tabela a abreviação que você quer usar. Não precisamos obrigatoriamente utilizar o operador AS para fazer isso, faça da maneira que for mais confortável para você! . Lembre-se que ao abreviar em sua consulta todas as referências àquela coluna agora **deverão** ser abreviadas! Se você tentasse no primeiro JOIN utilizar:
```
JOIN consumidores c
ON id_consumidor = consumidores.id
```
receberia um erro! Ou você opta por abreviar (que pode ser um pouco confuso no começo) ou utiliza o nome completo da tabela sempre!

**Importante**: JOIN é uma abreviação para o comando INNER JOIN, existem vários tipos de JOIN, não me aprofundarei neles aqui, mas da para ver mais no [w3schools](https://www.w3schools.com/mysql/mysql_join.asp)

Agora vamos ver como fazer filtros, ordenação e paginação no backend? Clica [aqui](./Passo-7.md)!