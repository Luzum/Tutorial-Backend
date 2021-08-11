## **O que é uma API?**

**API**

API é um acrônimo para **A**pplication **P**rogramming **I**nterface (Interface de programação de aplicações) que são interfaces que servem para acessar alguma fonte de dados. Ela geralmente é composta por um conjunto de métodos padronizados, eventos, propriedades e URLs que podem ser usados para interagir com components do navegador Web ou outros softwares e hardwares no computador do usuário ou sites e serviços de terceiros.

**APIs REST**

REST significa **R**epresentational **S**tate **T**ransfer e as APIs REST são aquelas que utilizam na maior parte das vezes os verbos [HTTP](https://developer.mozilla.org/pt-BR/docs/Web/HTTP/Methods). Quando criamos um endpoint em um API REST, utilizamos um desses métodos HTTP para indicar o que faremos com ele. Cada endpoint criado vai tratar uma entidade, ou seja, vai se comunicar com o banco de dados e fazer àquilo pré-estabelecido pelo método (consumir algum dado, manipular, inserir, etc). A entidade então é um caminho para se acessar uma tabela no banco.

Vamos olhar aqui como isso funciona quando utilizamos o express:

```
app.METHOD(PATH, HANDLER)
//
app.get('/usuarios', (req, res) => {
//insira o restante da função aqui
});
```
Temos o **get** como método, o path ou entidade é **usuarios** e o handler poderia ser uma função para se pegar todos os usuários, como já vimos anteriormente no [Passo-5](../Passo-1/Passo-5(Request-Response).md).

**Boas práticas**

É considerada uma boa prática manter o mesmo nome da entidade e se trocar apenas o método na hora que criarmos um endpoint, por exemplo:

```
app.get('/usuarios', (req, res) => {
//insira o restante da função aqui
});

app.post('/usuarios', (req, res) => {
//insira o restante da função aqui
});
```
aqui mantemos a mesma entidade, mas trocamos seu método. Enquanto o get nos indica que queremos pegar alguma informação da entidade usuarios, o post nos indica que queremos adicionar algo a tal entidade (se fosse um put, indicaria que estamos atualizando algo, delete deletando e assim por diante). 

Mas **atenção**, podemos ter uma entidade dentro da outra e neste caso também podemos adicionar valores ao caminho, como no exemplo:

```
app.get('/usuarios/:id/email', (req, res) => {
//insira o restante da função aqui
});
```
aqui poderíamos buscar o email de um usuário específico através de seu id.

Como o express dá suporte à manipulação de diversas entidades ao mesmo tempo, é considerada uma boa prática organizá-los por **entidade** e depois **método** da seguinte maneira:

```
app.get('/usuarios', (req:Request, res:Response) => {
//insira o restante da função aqui
});

app.get('/usuarios/:id', (req:Request, res:Response) => {
//insira o restante da função aqui
});

app.get('/usuarios/:id/email', (req:Request, res:Response) => {
//insira o restante da função aqui
});

app.post('/usuarios', (req:Request, res:Response) => {
//insira o restante da função aqui
});

app.get('/turmas', (req:Request, res:Response) => {
//insira o restante da função aqui
});


```
Note primeiro agrupamos todos os endpoints que se relacionam com a entidade "usuarios" a partir de seus métodos na ordem GET, POST, PUT, DELETE e depois começamos a fazer o mesmo com as outras entidades. No exemplo acima se continuássemos com a entidade "/turmas", a boa prática seria continuar com os métodos get e em seguida os próximos.

**Vamos fazer alguns exercícios?**

Vamos começar fazendo novamente um endpoint para pegar todos os usuários como no [Passo-5](../Passo-1/Passo-5(Request-Response).md), mas dessa vez usando tudo que aprendemos até então?