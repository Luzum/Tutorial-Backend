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

**Knex: raw**

Temos mais de uma forma de utilizar o knex, uma delas é o **raw**, um método onde utilizamos a linguagem do SQL pura, crua, ou seja, os mesmos comandos e funções que utilizamos dentro do nosso MySQL. Para utilizá-lo é recomendado que utilizemos template strings (a famosa crase **`**). As funções que faremos com o knex farão sempre uma comunicação externa com o banco, ou seja, devem ser funções **assíncronas**. Vamos para um exemplo de como adicionar uma pessoa em nossa tabela:

```
app.post('/pessoas', async (req, res) => {
    try{
        await connection.raw (`
            INSERT INTO pessoas
                (id, nome, email, apelido)
            VALUES (
                ${Date.now().toString()},
                "${req.body.nome}",
                "${req.body.email}",
                "${req.body.apelido}"
            );

        `)

        res.status(201).send("Deu bom")
    } catch(error) {
        console.log(error.message);
        res.status(500).send("Ocorreu um erro!")
    }
});
```

Note que tudo que for **string** deve ser passado entre aspas e os números sem aspas. A função Date.now().toString() nos fornece um id criado com base no momento em que a pessoa for criada na nossa tabela. Em breve veremos uma maneira mais eficiente de se criar ids, mas por enquanto isso aqui basta!