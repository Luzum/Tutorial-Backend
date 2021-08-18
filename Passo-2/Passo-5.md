## Início:

Achou que a gente ia começar com o Query Builder de cara? ACHOU ERRADO. Vamos começar refrescando nossa memória e lembrar de todas as dependências e dependências de desenvolvedor que precisamos até então!

**Dependências:**
* express
* cors
* knex
* dotenv
* mysql

**Dependências de desenvolvedor:**

* @types/express
* @types/cors
* @types/node
* ts-node-dev
* typescript

Para instalar todas elas com apenas duas linhas de comandos fazemos assim:

```
npm i express cors knex dotenv mysql
npm i -D @types/express @types/node @types/cors ts-node-dev typescript
```

Dentro da nossa pasta raiz vamos fazer o seguinte também:
```
npm init --yes
tsc --init
touch .env request.rest
mkdir src build
//Agora dentro da pasta src criaremos:
touch index.ts connection.ts app.ts
mkdir endpoints
```
Pronto, temos todas as configurações básicas praticamente prontas! As configurações do package.json e do tsconfig.json e de todos os outros arquivos ficam por conta de vocês :)
## **Knex: Query Builder**

O knex tem uma funcionalidade chamada **Query Builder**. Com ela também podemos fazer a integração de nossa API com um banco de dados, mas não precisamos nos prender à sintaxe pura do MySQL, pois poderemos utilizar os métodos do Query Builder (eles são até bem parecidos com a sintaxe pura do MySQL). Vale a pena gastar um tempo olhando a [documentação](https://knexjs.org/#Builder) porque são vários!

Vamos começar de um jeito simples: da mesma maneira que fizemos nosso endpoint de pegar todas as pessoas com o RAW, vamos fazê-lo agora com o query builder: 

```
import {Request, Response} from "express"
import connection from "../connection"

 const pegarTodasAsPessoas = async (req:Request, res: Response):Promise<any> => {
        try {
         const resultado =  await connection ("pessoas")
            .select('*')
            if (!resultado){
                return res.status(404).send({error: "Tabela nao encontrada"})
            }
                res.status(200).send(resultado)
        } catch (error) {
            if (res.status(200)){
                res.status(500).send({message: "Erro interno do servidor"})
            } else {
                res.send({ error: error.sqlMessage || error.message})
            }
        }
    }

export default pegarTodasAsPessoas
```
Compare com o jeito do Raw: 

```
const pegarTodasAsPessoas = async (req: Request, res: Response):Promise<any> => {
    try {
        const resultado = await connection.raw(`
            SELECT * FROM pessoas
        `)
        if(!resultado){
           return res.status(404).send({error: "Tabela nao encontrada!"})
        }
            res.status(200).send(resultado[0])
    } catch (error) {
        if (res.status(200)){
            res.status(500).send({error: "Erro interno do servidor"})
        }
            res.status(400).send(error.sqlMessage || error.message)
    }
} 
// omiti os imports e exports aqui, mas são os mesmos
```
As principais diferenças nesse caso são:
* Não precisamos usar await connection.raw, apenas await connection("nome_da_tabela")
* Podemos usar métodos como o .select('*') ao invés de ter que digitar SELECT * FROM nome_da_tabela.
* Em nosso resultado não precisamos botar o index[0], já vem a informação que queremos.

Como é uma requisição relativamente simples, não fez tanta diferença assim usar o Query Builder e o RAW, mas a tendência é que em buscas mais complicadas o Query Builder facilite um pouco nosso trabalho, já que podemos encadear os métodos, da mesma maneira que podemos encadear os métodos do express! Mas lembre-se, em termos práticos de resultados não há muitas diferenças entre um e outro. Treine criar endpoints das duas maneiras! O RAW é ótimo para manter seus conhecimentos da sintaxe de MySQL em dia! O Query Builder por sua vez é ótimo pra deixar sua vida mais simples, mais tranquila.

**Refatoração do endpoint de postar pessoa**
A gente fez um endpoint para criar uma nova pessoa com o knex RAW não fizemos? Vamos fazer agora com o Query Builder:
```
export const postarPessoa = async(req: Request, res: Response):Promise<any> => {
    try {
        const {nome, email, apelido} = req.body;
        
        const novaPessoa = {
            id: Date.now().toString(),
            nome,
            email,
            apelido
        }
       
        if (!nome || !email || !apelido) {
            return res.status(422).send({ error: "Preencha todos os campos corretamente!"})
        }

        await connection("pessoas").insert(novaPessoa)

        res.status(201).send("Pessoa criada!").end()
    } catch (error) {
        if(res.status(200)){
            return res.status(500).send({error: "Erro interno do servidor"})
        } else {
            res.send({error: error.sqlMessage || error. message})
        }
    }
}
```
Veja como fica mais fácil de ler o código! Olhe também como ficou simples inserir uma pessoa na tabela, só precisamos usar o .insert e colocar nossa const lá!

Vamos treinar mais um pouco o Query Builder. Em nossa tabela, nem fulano nem ciclano tem apelidos, vamos fazer um endpoint que atualize essas informações e dê apelidos para eles?

```
import { Response, Request } from 'express'
import connection from '../connection'

const atualizaApelido = async (req: Request, res: Response): Promise<any> => {
    try {
        const {apelido, id} = req.body
        
        if(!apelido || !id){
            return res.status(422).send("Preencha o campo corretamente!")
        }

        await connection("pessoas")
        .update({
            apelido
        })
        .where({
            id
        })
        
        res.status(200).send("Apelido atualizado!")
    } catch (error) {
        if(res.status(200)){
          return  res.status(500).send("Erro interno do servidor")
        } else {
            res.send({ error: error.sqlMessage || error.message})
        }
    }
}

export default atualizaApelido
```
Vamos entender o código? Primeiro fazemos uma desestruturação para deixar nosso código mais limpo. Depois no primeiro if checamos se os parâmetros apelido e id foram preenchidos corretamente no corpo da nossa requisição, caso contrário o código para ali e manda o usuário consertar seus erros. Depois começa o Query Builder: primeiro nos conectamos à tabela "pessoas" e depois usamos o métodos que precisamos para atualizar uma pessoa já existente: o .update que vai atualizar o apelido e o .where que garante com que apenas o apelido da pessoa com o id especificado seja atualizado! Não se esqueça do **.where**, se você não especificar onde quer atualizar algo, **TODAS** as linhas da tabela serão atualizadas naquela coluna!

Olha como fica nosso request.rest:
![request.rest](https://i.imgur.com/U4JtAXN.png)

Olha o Fulano com apelido: 
![Apelido](https://i.imgur.com/RZzGi3H.png)

Quer ver se nosso código realmente está aprova de erros? Tente mudar no body do patch "id" para "ID" ou qualquer outro valor, faça o mesmo com o "apelido". Você consegue atualizar algum valor fazendo isso ou recebe uma mensagem de erro? Essa mensagem vem de que parte do nosso código?

**Sobre a desestruturação:**

Sem ela nosso código seria assim no .update e .where:
```
 .update({
            apelido: req.body.apelido
        })
        .where({
            id: req.body.id
        })

Com ela fica assim:
 const {id, apelido} = req.body
 
 .update({
            apelido
        })
        .where({
            id
        })

// veja que fica bem mais simples, mas faça como achar melhor!
// Vamos lembrar que dentro do objeto, se a variável que armazena o valor
// tem o mesmo nome da propriedade, podemos encurtar o código,
// assim, ao invés de colocarmos apelido:apelido ou id:id
// podemos deixar só apelido e id.
```
E se quiséssemos passar o id via path params? Pense um pouco e tente fazer por si! 

Possível código por path params:

```
import {Response, Request} from "express"
import connection from "../connection"

const atualizaApelido = async (req: Request, res: Response): Promise<any> =>{
    try {
    const {apelido} = req.body
    const {id} = req.params
    if(!apelido){
        res.status(422).send("Preencha os campos corretamente!")
    }
    await connection("pessoas")
    .update({
        apelido
    }).where ({
        id
    })
       res.status(200).send("Apelido atualizado!")
    } catch(error){
        if (res.status(200)){
            res.status(500).send("erro interno do servidor")
        } else {
            res.send({ error: error.sqlMessage || error.message})
        }
    }
}

export default atualizaApelido
```
nosso index e request.rest:
![index](https://i.imgur.com/HR9hGoc.png)

Ciclano com apelido atualizado, repare no id passado por path params:
![apelidoAtualizado](https://i.imgur.com/O3MlAgn.png)

Agora é a sua vez: crie um endpoint para **deletar** uma pessoa da lista  a partir de seu id, utilizando o knex RAW e depois o Query Builder. Use os códigos já existentes como base sem medo! Leia eles e veja o que é necessário mudar para deletar uma pessoa. NÃO SE ESQUEÇA DA CLAUSULA **WHERE** SE NÃO VOCÊ DELETARÁ TODOS OS DADOS DE SUA TABELA!


RAW:
```
const deletaPessoa = async (req: Request, res: Response): Promise<any> => {
    try {
        const { id } = req.params
        await connection.raw(`
            DELETE FROM pessoas 
            WHERE id = ${id}
        `)
        res.status(200).send(`Pessoa deletada!`)
    } catch (error) {
        if (res.status(200)) {
            res.status(500).send({ error: "Erro interno do servidor!" })
        } else {
            res.send({ error: error.sqlMessage || error.message })
        }
    }
}
```

Query Builder: 

```
const deletaPessoa = async (req: Request, res:Response): Promise<any> =>{
    try { 
     const {id} = req.params
    await connection('pessoas')
    .delete()
    .where({id})
     res.status(200).send("Pessoa deletada!")
    } catch (error) {
        if (res.status(200)){
            res.status(500).send({error: "Erro interno do servidor!"})
        } else {
            res.send({error: error.sqlMessage || error.message})
        }
    }
}
```

Agora que já sabemos um pouco do knex vamos voltar aos bancos de dados e aprender sobre [Relações em SQL](./Passo-6.md)?