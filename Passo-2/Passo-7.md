## **Filtros, ordenação e paginação**

Você já deve ter se deparado com situações no front end onde tinha que se preocupar com a filtragem de alguns elementos do site, como no caso de um carrinho de compras, e na ordenação e paginação de elementos do site, como no caso da seção de produtos de um site. Era chato fazer tudo isso né? Dava trabalho. No Back-End, as coisas são diferentes. Aqui é tudo mais amigável e mais tranquilo de se fazer, vamos então ver como cuidamos de tudo isso no back. 

### **Filtros**

Antes de desenvolver nossos filtros, devemos pensar em algumas coisas: 

* O que nosso endpoint deve fazer? O que planejamos que aconteça ao utilizá-lo?
* Esboce o fluxo de dados e as etapas do processo 
(Requisição feita pela o usuário que se comunica com o front. Front se comunica com o back. Back se comunica com o banco e faz uma query SQL. Banco retorna um array a partir da requisição. O Back pode tratar novamente esse array que é então passado para o front. Por fim chega o resultado da requisição do usuário)
* Faça o tratamento de erros! (Depois de receber a consulta via query params, **verifique e valide** o valor recebido. Depois da consulta do banco de dados **faça uma verificação do retorno**, ou seja, se algo é retornado ou não do banco. Por fim responda a requisição com o resultado da consulta)
*  Por fim implemente seu código baseado naquilo que pensou!

No back não utilizaremos o método *filter* para fazer nossas filtragens, iremos fazê-las a partir de clausulas do MySQL. As mais utilizadas serão o [WHERE](https://www.w3schools.com/mysql/mysql_where.asp), o [LIKE](https://www.w3schools.com/mysql/mysql_like.asp), o [ORDER BY](https://www.w3schools.com/mysql/mysql_orderby.asp) o [LIMIT](https://www.w3schools.com/mysql/mysql_limit.asp) e OFFSET (achei uma referência pra treinar aqui não, hehe). Sempre deêm uma olhada na documentação e explorem esses materiais disponíveis gratuitos na internet, dá pra aprender e praticar muita coisa! 

**Criando tabelas a partir do VSC**

Vamos aprender como criar tabelas diretamente em nosso VSC? Primeiro vamos criar uma nova pasta para treinarmos tudo novamente, podemos chamá-la de "filtros". Dentro dela então rodaremos no terminal os comandos:
```
npm i express cors knex dotenv mysql
npm i -D @types/express @types/node @types/cors ts-node-dev typescript
```
```
npm init --yes
tsc --init
touch .env request.rest .gitignore
mkdir src build
//Agora dentro da pasta src criaremos:
touch index.ts  app.ts
mkdir endpoints data
//dentro da pasta data criaremos:
touch connection.ts migrations.ts users.json
```
Veja que dessa vez colocamos o arquivo connection.ts dentro da pasta **data**. Essa organização foi escolhida apenas para facilitar o tutorial, você pode organizar suas pastas de outras maneiras. Outra coisa que vai mudar vai ser nosso tsconfig.json: vamos adicionar à ele a seguinte linha: 

```
  "resolveJsonModule": true
```
Basta colar isso lá! Essa linha serve para que consigamos importar nossa lista do arquivo .json que criamos.

Ah! Acho que não usamos o .gitignore até então né? Ele é simplesmente um arquivo para quando subirmos nosso código para algum lugar (como o github) evitemos que algumas pastas e/ou arquivos sejam enviados conjuntamente. O .env é um exemplo de arquivo que queremos deixar apenas em nível local para nossa segurança e o node_modules uma pasta que evitamos de subir pois ela ocupa muito espaço (a pasta build é outra que normalmente não precisamos subir na rede). Para fazermos isso podemos apenas escrever o nome da pasta/arquivo dentro do arquivo .gitignore assim:
```
node_modules
build

.env
```
Pronto! Esses arquivos agora ficarão só em seu computador.

**users.json e migrations.ts**
Depois de configurar nossos package.json, tsconfig.json, .env, connection.ts, .gitignore e app.ts vamos criar nossa lista de usuarios no arquivo users.json da seguinte forma:
```
[
    {
        "id": "0",
        "nome": "Alexandre",
        "apelido": "Alex",
        "email": "alex@email.com",
        "tipo": "Admin"
    },
    {
        "id": "1",
        "nome": "Predo",
        "apelido": "Peredim",
        "email": "predo@email.com",
        "tipo": "Usuario"
    },
    {
        "id": "2",
        "nome": "Jones",
        "apelido": "Jon",
        "email": "jones@email.com",
        "tipo": "Usuario"
    },
    {
        "id": "3",
        "nome": "Bruno",
        "apelido": "Brunim",
        "email": "bruno@email.com",
        "tipo": "Usuario"
   },
    {
        "id": "4",
        "nome": "Alice",
        "apelido": "lili",
        "email": "alice@email.com",
        "tipo": "Admin"
    },
    {
        "id": "5",
        "nome": "Beatriz",
        "apelido": "Bia",
        "email": "bia@email.com",
        "tipo": "Admin"
    }
]
```
Agora vamos ver como configuramos o migrations.ts:
```
const printarErro = (error: any) => {console.log(error.sqlMessage || error.message)}
const criarTabela = async () => {
    try {
        const criaTabela = await connection.raw(`
    CREATE TABLE IF NOT EXISTS tutorial_filtros(
        id VARCHAR(255) PRIMARY KEY,
        nome VARCHAR(255) NOT NULL,
        apelido VARCHAR(255) NOT NULL,
        email VARCHAR(255) UNIQUE NOT NULL,
        tipo VARCHAR(255) NOT NULL
    )
`)
        if (criaTabela) {
            return console.log("tabela criada!")
        }
    } catch (error) {
        printarErro
    }
}
const inserirUsuarios = async() => {
    try {
         const insereNaTabela =  await connection('tutorial_filtros')
        .insert(users)
        if(insereNaTabela){
            console.log( 'Usuários criados!')
        }
    } catch (error) {
        printarErro
    }

}

const fecharConexao =() => {connection.destroy()}

criarTabela()
.then(inserirUsuarios)
.finally(fecharConexao)
```
Vamos por partes enteder o código. Primeiro criamos uma função para printar quaisquer erros que acontecerem no processo de criação da tabela. A segunda função serve para bom, criar a tabela. Nela utilizamos o knex RAW, ou seja, usamos exatamente a mesma sintaxe de criar tabela dentro do MySQL, fazemos uma verificação se ela foi criada, caso positivo retornamos uma mensagem confirmando, caso negativo chamaos a função printarErro. Depois temos a função inserirUsuarios. Ela também é uma função assíncrona, assim como a de criar tabela, mas dessa vez utilizamos o knex Query Builder. No método .insert estamos inserindo todos os usuários de nossa lista contida dentro do arquivo users.json. Então fazemos uma verificação que nos dá um console.log de confirmação caso os usuários sejam inseridos. 

Depois criamos a função para encerrar nossa conexão. Por fim chamamaos a função criarTabela, utilizamos um .then para chamar a função inserirUsuarios e criar nossos usuarios e um .finally para encerrar a conexão assim que a tabela for criada e preenchida.

Por fim, para rodarmos esse código contido em migrations.ts vamos criar mais um script no package.json:
```
 "migrations": "tsnd ./src/data/migrations.ts"
```
Para utilizá-lo é só digitar
```
npm run migrations
```
e provavelmente vai dar tudo certo! Lembrando que criar a tabela pelo VSCode **não é necessário**, crie a tabela onde achar mais confortável!

**Filtros e verificações**

Vamos finalmente ao assunto que interessa! Já temos nossa tabela preenchida e populada com 6 pessoas (se você quiser pode praticar suas habilidades em SQL e inserir mais pessoas, mas com essas 6 já dá para treinar o básico).

Vamos começar com um filtro bem simples onde iremos filtrar nossa lista via query string para retornar as pessoas a partir de seu tipo (usuario ou admin). Primeiro vamos separar a função que pega os dados do nosso banco de nosso endpoint criando um arquivo escolherUsuarioPorTipo.ts dentro da pasta **data**. O código dele ficará assim:

```
import { connection } from "./connection";

export const escolheUsuarioPorTipo = async(tipo: string): Promise<any> => {
    const usuario = await connection('tutorial_filtros')
    .select('*')
    .where('tipo', tipo)
    return usuario
}
```

Agora vamos criar dentro da pasta endpoints um arquivo com o nome getUsuarioPorTipo.ts que ficará da seguinte forma:
```
import { Request, Response } from "express"
import { escolheUsuarioPorTipo } from "../data/escolherusuarioPorTipo"

const getUsuarioPorTipo = async (req: Request, res: Response): Promise<any> => {

    try {
        const tipo = req.params.tipo
        
        const usuariosFiltrados = await escolheUsuarioPorTipo(tipo)
        
        if(!usuariosFiltrados.length){
            return res.status(404).send({error: "usuarios não encontrados"})
        }
        
        res.status(200).send(usuariosFiltrados)
    } catch (error) {
        if(res.status(200)){
            return res.status(500).send({error: "Erro interno do servidor!"})
        }
       res.send({error: error.message || error.sqlMessage})
    }


}

export default getUsuarioPorTipo
```
Vamos ver o que acontece aqui:
criamos um endpoint que recebe filtragem via query params. Nossa const usuariosFiltrados passa a const tipo que recebemos via path params como parâmetro para a função escolheUsuarioPortipo e espera ela fazer a requisição. Depois em nosso primeiro if, temos a verificação se recebemos a resposta ou não. Sabemos que receberemos um array de usuários a partir de nosso filtro, então se a **length** dele for 0, significa que não recebemos nada como resposta, que por sua vez significa que na hora de passar o tipo algo foi escrito errado. Depois se tudo tiver certo, recebemos  então nossa lista de usuários filtrados.

Em nosso index e request.rest chamamos o endpoint assim:
```
//index:
app.get('/usuarios/:tipo', getUsuarioPorTipo)
//request.rest:
GET http://localhost:3003/usuarios/admin
```

**Importante:** às vezes pode ocorrer um erro em nosso res.send({error: error.message || error.sqlMessage}) por não reconhecer o tipo deles. É raro, mas pode acontecer. Como estamos apenas treinando nossas habilidades basta tipar o catch(error) com any assim:
```
catch (error:any)
```
Isso ocorreu comigo quando eu estava escrevendo e testando esse código, mas foi a primeira vez, nunca tinha ocorrido antes. Se acontecer com você faça o sugerido acima-**MAS** apenas aqui enquanto tiver treinando, depois a gente vê uma maneira melhor de resolver isso.

Por que separar o código assim? Apesar de dar um pouco mais de trabalho na hora do desenvolvimento, fica mais fácil de fazer  a manutenção depois. É claro que por enquanto estamos fazendo APIs muito pequenas que mexem com poucos dados e usam poucos endpoints, então para a gente não faz muita diferença. Mas ao mesmo tempo é bom ir se acostumando a ir dividindo o código em pequenas partes para depois já estarmos um pouco melhor preparados quando formos trabalhar em aplicações maiores com arquiteturas definidas.  

Ok, vamos fazer mais um endpoint, desta vez para filtrar por nome via **query** params, mas com algumas condições: se não passarmos um nome, devemos receber a lista completa. Se passarmos apenas uma ou mais letras, mas não um nome completo, devemos receber de volta as pessoas que contém aquelas letras no nome. Também queremos que a lista venha em ordem **decrescente** por padrão a partir do nome, ou se não especificarmos a partir do id. Vamos lá?

Primeiro em nosso arquivo escolherUsuarioPorNome.ts (na pasta data):
```
import { connection } from "./connection";

const escolherUsuarioPorNome= async(nome: string ,sort:string, order:string):Promise<any> =>{
   const usuarios = await connection('tutorial_filtros')
    .where('nome', 'like', `%${nome}%`)
    .orderBy(sort, order)
    return usuarios
}

export default escolherUsuarioPorNome
```
Veja que dessa vez utilizamos o 'like' e também o caractere '%'. O caractere '%' é um **coringa**, ou seja, ele pode assumir **qualquer valor**, quando utilizamos igual no caso acima junto com o operador LIKE podemos garantir que um nome que comece com **qualquer** coisa e  termine com **qualquer** coisa, desde que tenha aquilo que for passado no nosso parâmetro **nome** seja exibido em nosso resultado final. Para exemplificar melhor: caso tivéssemos feito 'a%', receberíamos qualquer nome que começasse com a letra a, caso fosse '%a' seria qualquer nome que terminasse com a letra a e por fim se fosse '%a%' receberíamos qualquer nome que tivesse a letra a dentro dele em qualquer lugar.

Já o operador ORDERBY ordena a nossa requisição. O primeiro parâmetro 'sort', é por qual coluna queremos ordenar nossa requisição, já o order é para escolher se a ordenação será crescente ou decrescente.

Ah! Viram também que não usamos o .select('*')? Quando usamos o Query Builder, se não especificarmos o que queremos selecionar, ele selecionará todas as colunas para nós!

Agora vamos olhar nosso arquivo getUsuarioPorNome.ts(na pasta endpoints)?
```
import { Request, Response } from "express"
import escolherUsuarioPorNome from "../data/escolherUsuarioPorNome"

const getUsuarioPorNome = async(req:Request,res: Response):Promise<any>=>{
    try {
        let {nome, sort = "id"} = req.query 
        
        if(typeof nome !=='string'){
           nome = '%'
        }
      
        if(sort !=="id" && sort !=="nome"){
        return res.status(422).send('Insira um Parametro válido para ordenação')
    }
        const order = req.query.order ==="ASC" ? "ASC": "DESC"

        const listaUsuarioPorNome = await escolherUsuarioPorNome(nome , sort, order)
        if(!listaUsuarioPorNome){
            return res.status(404).send("Alguma coisa tá errada!")
        }
       
        res.status(200).send(listaUsuarioPorNome)

    } catch (error) {
        
        if(res.status(200)){
            return res.status(500).send({error: "erro interno do servidor"})
        }
        
        res.send({error: error.message || error.sqlMessage})
    }
}

export default getUsuarioPorNome
```
Vamos ver o que temos aqui: primeiro temos uma desestruturação e atribuímos ao parâmetro sort o valor padrão de id. Assim se o usuário não definir uma coluna de ordenação padrão ela será feita por id. Depois matamos dois coelhos com uma cajadada só na próxima verificação de tipo. Se a variável nome não for string, ela automaticamente será transformada na string '%', o que garantirá que o valor dela seja sempre uma string e também que se o usuário não passar nenhum nome tenhamos como retorno a lista completa. Depois fazemos o mesmo com a variável sort: garantimos que ela só aceite **dois** valores como padrão de ordenação, assim como o exercício determina: id e nome, se o usuário tentar ordenar por uma coluna diferente receberá um erro. Por fim temos um ternário na const order que garante que se o usuário não determinar se quer uma ordenação crescente ou decrescente terá como padrão a ordenação decrescente. As outras verificações você já conhece!

Eu estou longe de ser um desenvolvedor experiente, mas refatorei um pouco do código acima para fazer alguns tratamentos que deixam ele um pouco melhor para o usuário (tá cheio de ifs), talvez fique um pouco confuso mas vou deixar ele aqui para vocês verem (vou explicar abaixo dele também):
```
const getUsuarioPorNome = async (req: Request, res: Response): Promise<any> => {
    try {
        let { nome, sort = "id", order = "DESC" } = req.query

        if (typeof nome !== 'string') {
            nome = '%'
        }

        if (typeof sort === "string") {
            sort = sort.toLowerCase()
        }

        if (sort !== "id" && sort !== "nome") {
            return res.status(422).send('Insira um Parametro válido para ordenação')
        }

        if (typeof order === "string") {
            order = order.toUpperCase()
        }
        if (order !== "ASC" && order !== "DESC") {
            return res.status(422).send('Insira um ordenamento válido')
        }

        const listaUsuarioPorNome = await escolherUsuarioPorNome(nome, sort, order)
        if (!listaUsuarioPorNome.length) {
            return res.status(404).send("Alguma coisa tá errada!")
        }

        res.status(200).send(listaUsuarioPorNome)

    } catch (error: any) {

        if (res.status(200)) {
            return res.status(500).send({ error: "erro interno do servidor" })
        }

        res.send({ error: error.message || error.sqlMessage })
    }
}

export default getUsuarioPorNome

```

O que mudamos: transformamos a const order em let e estabelecemos o valor padrão dela como "DESC", assim como o exercício pedia. No segundo if, fazemos com que a variável sort venha sempre em letras minusculas, porque em nossa tabela tanto o id quanto o nome estão com letras minusculas, mas por que? Porque assim se o usuário escrever alguma das colunas utilizando alguma letra maiúscula (Id, iD, NOME, Nome, etc.)  garantimos que ele ainda tenha algum retorno. Depois o mesmo é feito com a variável order: os comandos "ASC" e "DESC" são sempre em letras maiúsculas, então deixamos a prova de erro para o usuário (se ele digitar "asc" ou "desc", por exemplo, vai dar tudo certo). Por fim garantimos para o typescript que a variável order vai ser **sempre** uma string ao aceitar somente os valores "ASC" e "DESC", que por sua vez são os únicos valores que o operador ORDER BY aceita no MySQL!

Nosso index e request.rest:
```
//index:
app.get('/usuarios', getUsuarioPorNome)
//request.rest:
GET http://localhost:3003/usuarios?nome=a&sort=id&order=ASC
```
Veja que depois de cada query utilizamos o caractere & para indicar que colocaremos mais parâmetros. Mude eles! coloque o order como DESC, mude o sort para nome, mude a filtragem do nome, tire todos eles e vá vendo o que acontece! Esse é um ótimo jeito de pensar maneiras de otimizar seu código! Se você receber uma resposta inesperada quando mudar alguma query, pense no que pode mudar no código!

**Paginação**

Já aconteceu muita coisa até aqui! Vê se já deu pra absorver tudo, se não tiver releia, dê uma estudada tente fazer algumas requisições usando o que já foi aprendido acima. O que foi feito com query params faça por path params, faça uma filtragem por nome sem ordenação e vá indo devagar! É melhor aprender o máximo possível de maneira lenta do que um mínimo de maneira rápida. Dê um tempo também se achar necessário! Umas horas, ou um dia ou mais até (acredita, vai sem pressa).

Pra quem já tá confortável vamos agora aprender a fazer a paginação de nossas requisições. Para ela vamos usar dois métodos do MySQL: o LIMIT e o OFFSET. O LIMIT **limita** o número de itens de nossa lista por **n**(a gente escolhe o valor de n, hehehe). Já offset permite escolher por onde da lista vamos começar. Por exemplo se dermos um limite de 2 itens por requisição e passarmos o valor **2** para o nosso offset, nossa lista terá as pessoas 3 e 4.  Utilizar o LIMIT e o OFFSET conjuntamente permite  que possamos fazer uma paginação dinâmica. Mas como assim? Vai ser mais fácil demonstrar no código! Como complicamos um pouco anteriormente, vamos deixar esse exemplo simples.

Primeiro vamos criar um arquivo de nome selecionarUsuarioPorPagina.ts na pasta data. Ele ficará assim:
```
import { connection } from "./connection";

const selecionarUsuarioPorPagina = async(page:number): Promise<any> => {
    const resultadoPaginado = await connection('tutorial_filtros')
    .limit(2)
    .offset(page)
    return resultadoPaginado
}
export default selecionarUsuarioPorPagina
```
O .limit(2) garante que tenhamos apenas 2 pessoas de nossa lista por página. Já o offset recebe (page) como parâmetro que é um número. Esse número servirá para determinar a página de resultado que queremos, vamos ver como no arquivo getListaPaginada.ts na pasta endpoints:
```
import { Request, Response } from "express"
import selecionarUsuarioPorPagina from "../data/selecionarUsuarioPorPagina"

const getListaPaginada = async(req: Request, res: Response): Promise<any> => {
    try {
        const pagina = Number(req.query.pagina) || 1

        const offset: number = (pagina-1) * 2

        const listaPaginada = await selecionarUsuarioPorPagina(offset)
        
        if(!listaPaginada.length){
            return res.status(404).send({error: 'Não encontramos nada!'})
        }
        res.status(200).send(listaPaginada)
    } catch (error: any) {
        if(res.status(200)){
            return res.status(500).send({error: "Erro interno do servidor"})
        }
        res.send({error: error.message || error.sqlMessage})
    }
}

export default getListaPaginada
```
Primeiro vamos falar sobre a const página. Temos o operador || que garante que se o usuário não passar a página desejada ele seja direcionado à pagina 1 da requisição. Tá, isso deu pra entender mais ou menos, mas que calculo maluco é esse na const offset? Vamos lá: o offset determina a partir de que item da lista nossa requisição vai começar. Então se temos um limite de duas pessoas por página e queremos começar das duas primeiras pessoas o OFFSET tem que ser 0 correto? Então: (1-1) * 2 = 0. Se queremos ver a segunda página de nossa requisição teremos a seguinte conta: (2-1) * 2 = 2. Mas pera, não entendi! Então: Se o offset for igual a 2, isso significa que ele não exibirá os 2 primeiros itens de nossa lista, e começará a partir do terceiro. Como temos um limite de dois itens por página, receberemos então na página dois a terceira e quarta pessoa de nossa lista. Nossa lista tem apenas **6** pessoas, então a terceira página é a última, vamos ver se a conta bate? (3-1) * 2 = 4. Assim o offset não exibe as 4 primeiras pessoas de nossa lista e começa a partir da quinta pessoa. Entendi! Mas e se eu quisesse que minha lista tivesse 3 pessoas, como ficaria a conta? Ela é feita da seguinte maneira (N -1) * limit  sendo N o número da página desejada e limit o valor que você passou para o método .limit(). Ou seja, se o limite fosse de 3 pessoas por página nossa const offset acima seria feita assim:

```
const offset = (pagina-1) * 3
```
(2-1)*3 = 3. O offset cortaria as 3 primeiras pessoas e nos mostraria a lista a partir da quarta.

Agora é sua vez de treinar, se quiser adicione mais pessoas à lista para brincar mais com a paginação e entender melhor o limit e o offset. Por fim tente fazer um endpoint que use **todas** as funcionalidades aprendidas! Filtre por nome, tipo, ordene crescentemente a partir do nome e faça uma paginação que tenha a página 1 como padrão!