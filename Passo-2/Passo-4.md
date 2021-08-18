## **KNEX**

Tá, até então a gente viu um pouco de typescript, express e MySQL, mas como uma dessas coisas se junta com as outras? Uma das maneiras é com **Knex**, mais uma maravilhosa biblioteca que facilita muito nossa vida de backenders. Ela basicamente permite com que façamos conexões entre o código em JS(não se esqueça que nosso código em TS é transpilado para JS) e bancos SQL. Para que o Knex funcione precisamos de um **client** de SQL e aqui usaremos o MySQL. Para instalá-los rodamos os seguintes comandos:

```
npm i knex mysql
//não precisamos instalar o @types do knex
//a própria biblioteca já vem com as tipagens
```

E para usar o knex? Igual ao nosso app.ts, criaremos dentro da pasta src um arquivo chamado connection.ts com as seguintes configurações:

```
import knex from "knex"

const connection = knex ({
    client: "mysql",
    connection: {
        host: "número do host aqui",
        port: 3306,
        user: "nome do usuário",
        password: "senha123456",
        database: "nome-do-database",
        multipleStatements: true
    }
});

export default connection
```
### **dotenv**

Vamos instalar também o dotenv assim:
```
npm i dotenv
```

O dotenv possibilita que nossas informações de host, port, user, password e database fiquem seguras em um arquivo local que não irá para a nuvem. 
Depois de instalar o dotenv, crie um arquivo de nome ".env" na pasta raiz do projeto e nele coloque as configurações de seu banco de dados da seguinte maneira:
```
DB_HOST = número do host aqui
DB_USER = nome do usuário aqui
DB_PASSWORD = senha aqui
DB_SCHEMA = banco de dados escolhido aqui
```
Depois de feito isso, podemos configurar nosso arquivo connection.ts da seguinte maneira:
```
import knex from "knex";
import dotenv from "dotenv";

dotenv.config();

const connection = knex ({
    client: "mysql",
    connection: {
        host: process.env.DB_HOST,
        port: 3306,
        user: process.env.DB_USER,
        password: process.env.DB_PASSWORD,
        database: process.env.DB_SCHEMA,
        multiStatements: true
    }
})

export default connection;
```

### **Para facilitar ainda mais nossa vida:**
Vamos instalar duas extensões em nosso VSCode:
* MySQL - para podermos usar o MySQL diretamente no VSCode
* REST Client - para fazer requisições diretamente no VSCode

Depois de instalado o **REST Client**, podemos criar um arquivo na pasta raiz do projeto chamado request.rest e fazer nela nossas requisições, mostrarei como mais a frente!
Nossa organização de pastas e arquivos está assim por enquanto: 

![pastas](https://i.imgur.com/IHTTSg2.png)

**Knex: raw**

Temos mais de uma forma de utilizar o knex, uma delas é o **raw**, um método onde utilizamos a linguagem do SQL pura, crua, ou seja, os mesmos comandos e funções que utilizamos dentro do nosso MySQL. Para utilizá-lo é recomendado que utilizemos template strings (a famosa crase **`**). As funções que faremos com o knex farão sempre uma comunicação externa com o banco, ou seja, devem ser funções **assíncronas**. Vamos começar com um endpoint para pegar todas as pessoas de nossa tabela:
```
import {Request, Response} from "express"
import connection from "../connection"

const pegarTodasAsPessoas = async (req: Request, res: Response):Promise<any> => {
    try {
        const listaPessoas = await connection.raw(`
            SELECT * FROM pessoas
        `)
        if(!listaPessoas){
            res.status(404).send({error: "Tabela nao encontrada!"})
        }
            res.status(200).send(listaPessoas[0])
    } catch (error) {
        if (res.statusCode = 200){
            res.status(500).send({error: "Erro interno do servidor"})
        }
            res.status(400).send(error.sqlMessage || error.message)
    }
} 

export default pegarTodasAsPessoas
```
Veja como usamos exatamente a **mesma** sintaxe do MySQL para se selecionar todas as pessoas da tabela. Você percebeu que ao mandarmos a const listaPessoas utilizamos o índice 0 (listaPessoas[0])? Experimente mandar sem e veja o que recebe de volta!





Vamos para um exemplo de como adicionar uma pessoa em nossa tabela:

```
import { Request, Response } from "express"
import connection from "../connection"

export const postarPessoa = async (req: Request, res: Response) => {
    try {
        const { nome, email, apelido } = req.body
        
        if (!nome || !email || !apelido) {
           return res.status(422).send({ error: "Preencha todos os campos corretamente!"})
            }
        
           
            await connection.raw(`
            INSERT INTO pessoas
                (id, nome, email, apelido)
            VALUES (
                ${Date.now().toString()},
                "${nome}",
                "${email}",
                "${apelido}"
            );
        `)

    
        res.status(201).send("Usuário criado!")
    } catch (error) {
        if (res.status(200)) {
          return  res.status(500).send({ error: "Erro interno do servidor" })
        } else {
            res.send({ error: error.sqlMessage || error.message})
        }
    }
}

```
**Importante:** Tá vendo a verificação  if(!nome || !email || !apelido) ali em cima? **ELA TEM QUE SER FEITA ANTES DA INSERÇÃO DOS DADOS**, e **TEM QUE TER UM RETURN NELA** para que o código pare de rodar e o usuário não seja criado desrespeitando o formato.
O nosso banco de dados também emite alguns erros, que podemos mostrar a partir do **error.sqlMessage**.

Note que tudo que for **string** deve ser passado entre aspas e os números sem aspas. A função Date.now().toString() nos fornece um id criado com base no momento em que a pessoa for criada na nossa tabela. Em breve veremos uma maneira mais eficiente de se criar ids, mas por enquanto vamos deixar assim! Vamos reparar uma coisa interessante? Como sempre existe mais de uma maneira de se fazer a mesma coisa. Logo após o nosso INSERT INTO, temos nosso primeiro if que faz uma validação dos valores passados no body da requisição. Note que podemos usar tanto:
```
res.statusCode = 422
throw new Error("Preencha todos os campos corretamente!")
```
como: 
```
res.status(422).send({ error: "Preencha todos os campos corretamente!" })
```
A primeira maneira é um jeito nativo do [node](https://nodejs.org/api/http.html#http_response_statuscode). Já a segunda é um jeito do [express](https://expressjs.com/de/api.html#res.send) de se fazer a mesma [coisa](https://stackoverflow.com/questions/50888305/res-status-vs-res-statuscode) com a possibilidade de encadeamento de métodos. Use a que preferir! A partir de agora utilizarei principalmente a maneira do express pelo fato dela ser um pouco mais enxuta e não atrapalhar em nada a legibilidade do código.

Vamos ver como fazer essas requisições em nosso vscode? Primeiramente nosso index fica assim:

![index](https://i.imgur.com/rJquz65.png)
![request](https://i.imgur.com/qJIXGQa.png)

Aqui fizemos o request para pegar todas as pessoas antes de adicionar o Beltrano. Adicionando o Beltrano:
![Beltrano](https://i.imgur.com/hBI62W6.png)

Temos sempre que criar no send request correspondente ao que queremos fazer!

Conferindo se Beltrano está na lista:
![lista](https://i.imgur.com/VIJisIt.png)

Tente brincar um pouco com o corpo da requisição no arquivo request.rest. Se você muda o parâmetro "nome" para "name" o código continua funcionando ou vem uma mensagem de erro? A nova pessoa é postada ou não? Tente mudar a verificação para depois do connection.raw, assim:
```
  await connection.raw(`
            INSERT INTO pessoas
                (id, nome, email, apelido)
            VALUES (
                ${Date.now().toString()},
                "${nome}",
                "${email}",
                "${apelido}"
            );
        `)
           if (!nome || !email || !apelido) {
           return res.status(422).send({ error: "Preencha todos os campos corretamente!"})
            }
```
Ela ainda funciona? Você recebe uma mensagem de erro caso tenha passado algum parâmetro errado? E com essa mensagem de erro, a tabela é ou não atualizada?

Ótimo, acho que já deu pra entender um pouco do Knex RAW, né? Vamos então ao [Knex: Query Builder](./Passo-5.md)?