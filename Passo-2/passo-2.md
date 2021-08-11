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

Vamos dessa vez fazer um endpoint para pegar uma pessoa por nome a partir de **path params**? Até então só usamos query params em nosso tutorial, vamos ver como funciona o path params! (Vamos usar tudo que já aprendemos e um pouco mais, então depois de tentar fazer por si, leia o código atentamente!)

```
import { Request, Response} from "express"
import {pessoas} from "../data"
import { pessoa } from "../types"

export const pegarPessoaPorNome = (
    req:Request, res:Response
): void => {
    let errorCode:number= 400
    try {
        const nome:string = req.query.nome as string
        const usuario: pessoa | undefined = pessoas.find(
            pessoa => pessoa.nome === nome
        )
        if(!usuario) {
           errorCode= 404
           throw new Error ('Usuario nao encontrado')
        }
        res.status(200).send(usuario)
    } catch (error) {
        res.status(errorCode).send({message: error.message})
    }
}
```
A variável errorCode serve para podermos usar o código de erro à depender da situação! Note que na **const nome** temos no final um *as string*, o que é isso? Se não fizermos isso o typescript vai reclamar que a const nome pode ser de diferentes tipos e não apenas string. Aqui basicamente fazemos ele calar a boca e dizemos pra ele: "VAI SER STRING SIM, DESGRAÇA", mas veja que isso não é uma garantia de que será uma string! Em breve veremos um melhor jeito de fazer uma verificação para isso. Na condição if embaixo da const usuario, fazemos uma verificação, caso procuremos um usuário não existente, receberemos um erro 404 com a mensagem de usuario nao encontrado. Caso dê tudo certo recebemos o código 200 e o usuário!

Veja aqui a organização em nosso index.ts: 

![index.ts](https://i.imgur.com/V94KJb8.png)
Olhe bem, tive que criar uma nova entidade, porque se usasse '/pessoas' não conseguiria acessar os path params. Mas e se você colocasse ela na linha 10, antes do endpoint de pegar todas as pessoas? Aí não conseguiríamos acessar o array com todas as pessoas a partir da entidade '/pessoas'!

Olha o postman com path params:
![postmanCerto](https://i.imgur.com/j2yxP5C.png)
Tá vendo que para acessar o path params precisamos do ? após o nome de nossa entidade,  a key que definimos em nosso código que nesse caso foi "nome", depois dela um sinal de **=** e o valor da nossa pesquisa. Também podemos fazer a mesma coisa digitando a key e o value no postman, como sublinhado na imagem

Quando pesquisamos um nome não existente acontece isso:
![postmanErro](https://i.imgur.com/qwx3cQi.png)

Olha lá nosso errorCode e nossa mensagem!

E vamos seguindo assim! Aos poucos melhorando nosso código e vendo pequenas mudanças que podemos fazer para torná-lo melhor! Ele está longe de ser perfeito ainda mas olhe a diferença entre ele e nosso primeiro endpoint de busca: 
```
import { Request, Response} from "express"
import {pessoas} from "../data"

export const pegarTodasPessoas = (
    req: Request, 
    res: Response
    ):void =>{
        res.send(pessoas)
}

```
Vamos a próxima seção? Clique [aqui](./Passo-3.md).