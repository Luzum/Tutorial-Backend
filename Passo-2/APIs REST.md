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
Temos o **get** como método, o path ou entidade é **usuarios** e o handler poderia ser uma função para se pegar todos os usuários, como já aprendemos anteriormente no [Passo-5](Passo-1/Passo-5(Request-Reponse).md)

**Boas práticas**
Uma boa prática