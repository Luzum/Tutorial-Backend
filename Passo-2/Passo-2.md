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

**Aprofundando nos métodos HTTP**
Já postei este [link](https://developer.mozilla.org/pt-BR/docs/Web/HTTP/Methods) ali em cima, mas você leu? Vou fazer um pequeno resumo aqui, mas lê lá! O protocolo HTTP define nos fornece um conjunto de verbos/métodos que indicam a ação que iremos fazer em nossa aplicação, e como falado acima seguem uma convenção de boas práticas. Eles **não** influenciam no comportamento do código, então se trocássemos todos os gets acima por deletes, isso não interferiria em nada no código. Mas isso é considerado uma **péssima** prática! 

Apesar de existirem diversos verbos utilizamos principalmente 5:

* **GET** 
 Utilizamos para buscar recursos, suas requisições devem retornar apenas dados e não utiliza um body em sua requisição! Assim, a requisição deve ser feita usando **PathParams** ou **QueryParams** ou **Headers**( nesse caso, uma melhor prática seria utilizar o método **HEAD**).

* **POST** - utilizamos para criar novos recursos

* **PUT** - utilizamos para modificar **completamente** recursos existentes

* **PATCH** - utilizamos para atualizar **parcialmente** recursos existentes

* **DELETE** -  utilizamos para deletar recursos

**CRUD**

O acrônimo CRUD significa: **C**reate, **R**ead, **U**pdate, **D**elete e essas são as principais operações que podem ser feitas em bancos de dados e fazem parte da sintaxe de SQL. Já dá até pra entender a relação entre cada uma dessas operações e um ou mais dos 5 verbos HTTP acima né? Create se relaciona com POST, Read com GET, Update com PUT  e PATCH e delete com DELETE.

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

Vamos refatorar nosso código de buscar uma pessoa por id via query params usando um try/catch e usando a sintaxe de throw new Error no lugar de nosso if/else? (Se lembra também que nele usamos um res.statusCode? Essa é uma maneira diferente de fazer a mesma coisa que fizemos agora com a variável errorCode. Tente usar também uma variável de erro agora, só para treinar de um jeito diferente! )

**Ex 2:**

```
import {Request, Response} from "express"
import { pessoas } from "../data"
import { pessoa } from "../types"

export const pegarPessoaPorID = (
    req: Request, res: Response
): void => {
    let errorCode = 400
    try {
        const id: number = Number(req.params.id)
        if (isNaN(id)) {
            errorCode = 422
            throw new Error("Valor inválido para id. Tem que ser um número")
        }

        const usuario: pessoa | undefined = pessoas.find(
            pessoa => pessoa.id === id
        )

        if (!usuario) {
            errorCode = 404
            throw new Error("Usuario nao encontrado")
        }
        res.status(200).send(usuario)

    } catch (error) {
        res.status(errorCode).send({ message: error.message })
    }
}

```
Olha como ficou diferente, mais robusto nosso código. Lê tudo com calma! Primeiro atribuímos à const id o valor do req.params.id, um trabalhinho a mais pra deixar nosso código mais bonitinho. Depois, no primeiro if, fazemos uma verificação se o id é realmente um número e se não for já atiramos um novo erro [422](https://developer.mozilla.org/pt-BR/docs/Web/HTTP/Status/422) falando que este **precisa** ser um número. O próximo if verifica se o id que passamos corresponde ao de algum usuário ou não, se não o código já para por ali e atira um erro [404](https://developer.mozilla.org/pt-BR/docs/Web/HTTP/Status/404). Por fim, temos mais um tratamento de erro em nosso catch. 

**EX 3:** 

Vamos criar um novo usuário em nossa lista? Para isso utilizaremos o POST! Ah, receberemos nossas informações pelo body! 
```
import { Request, Response} from "express"
import { pessoas } from "../data"
import { pessoa } from "../types"

export const postarPessoa = (req: Request, res: Response): void => {
    let errorCode = 400
    try {
        const {id, nome, sobrenome} = req.body
        
        if(!id || !nome ||!sobrenome){
            errorCode= 422 
            throw new Error ('Preencha os campos corretamente')
        }
            const novoUsuario: pessoa ={
            // id: id,
            // nome: nome,
            // sobrenome : sobrenome
            // dentro do objeto se a variável que armazena o valor
            // tem o mesmo nome da propriedade, podemos encurtar o código para:
            id,
            nome,
            sobrenome
        }
        pessoas.push(novoUsuario)
        res.status(201).send({message: "Usuario criado com sucesso!"})

    } catch (error) {
        res.status(errorCode).send({message: error.message})
    }
}
```
Veja que fizemos uma desestruturação para que o código ficasse mais enxuto na const {id, nome, sobrenome} = req.body (poderia ser const id = req.body.id e assim por diante). Depois fazemos uma breve verificação para ver se tudo no body foi preenchido para podermos criar nosso novo usuário. Esse código está longe de ser perfeito, pois permite, por exemplo, que possamos criar 2 usuários com o mesmo id em nossa lista, mas vamos aos poucos, o importante agora é aprender como utilizar o **POST**.


Olhe nosso postman:
![postman](https://i.imgur.com/ZM3sxyi.png)
![postmanLista](https://i.imgur.com/2E4Ieab.png)
![postmanErro](https://i.imgur.com/KLs5Iv5.png)

Nosso index fica assim: 
![index](https://i.imgur.com/B2PyC2X.png)

Vamos a próxima seção? Clique [aqui](./Passo-3.md). a
