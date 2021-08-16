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

O knex tem uma funcionalidade chamada **Query Builder**. Com ela também podemos fazer a integração de nossa API com um banco de dados, mas não precisamos nos prender à sintaxe pura do MySQL, pois poderemos utilizar os métodos do Query Builder (eles são até bem parecidos com a sintaxe pura do MySQL). Vale a pena gastar um tempo olhando a [documentação] (https://knexjs.org/#Builder) porque são vários!

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

Como é uma requisição relativamente simples, não fez tanta diferença assim usar o Query Builder e o RAW, mas a tendência é que em buscas mais complicadas o Query Builder facilite um pouco nosso trabalho, já que podemos encadear os métodos, da mesma maneira que podemos encadear os métodos do express! Mas lembre-se, em termos práticos de resultados não há muitas diferenças entre um e outro. Treine criar endpoints das duas maneiras! O RAW é ótimo para manter seus conhecimentos da sintaxe de MySQL em dia! O Query Builder por sua vez é ótimo pra deixar sua vida mais simples, mais tranquila.

