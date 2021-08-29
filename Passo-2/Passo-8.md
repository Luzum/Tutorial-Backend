## **Serviços no Backend**

Podemos integrar à nossa aplicação serviços externos. Ou seja, podemos integrar outras APIs à nossa própria API. Faremos isso por meio da biblioteca [axios](https://axios-http.com/docs/intro). Hoje criaremos uma API para registrar pessoas à uma aplicação e a integraremos com a API ViaCEP para que o endereço da pessoa seja completado automaticamente a partir do fornecimento de seu cep.

Primeiro vamos à instalação do axios:

```
//axios:
npm i axios
//o axios não precisa do @types/axios,
// ele já vem com ela por padrão
```
Não vou me aprofundar muito sobre o uso do axios aqui, para saber mais dê uma olhada no link acima!

**ViaCep**

Para entender um pouco mais da ViaCEP, clica [aqui](https://viacep.com.br/). O ViaCEP é um webservice que permite que possamos consultar CEPs de todo o país e receber como retorno um objeto com várias informações sobre esse CEP, assim como esse exemplo do próprio site em JSON a partir da URL https://viacep.com.br/ws/01001000/json/ 

```
   {
      "cep": "01001-000",
      "logradouro": "Praça da Sé",
      "complemento": "lado ímpar",
      "bairro": "Sé",
      "localidade": "São Paulo",
      "uf": "SP",
      "ibge": "3550308",
      "gia": "1004",
      "ddd": "11",
      "siafi": "7107"
    }
```

Esse webservice pode retornar um objeto em várioS formatos diferentes, mas aqui vamos utilizar apenas o formato JSON. Para começar vamos começar instalando tudo que já conhecemos junto com o axios:
express, cors, dotenv, mysql, knex e axios:
```
npm i express cors dotenv mysql knex axios
```
tipagens, ts-node-dev e typescript:
```
npm i -D @types/express @types/cors @types/node @types/axios ts-node-dev typescript
```

Agora criar os arquivos e pastas:
```
touch app.ts index.ts .env request.rest .gitignore
mkdir src build
npm init --yes
tsc --init
//criando dentro da pasta src:
cd src
touch app.ts index.ts types.ts
mkdir data endpoints services
//na pasta data:
cd data
touch connection.ts migrations.ts
```

Veja que pela primeira vez estamos criando a pasta **services**. Nela guardaremos os serviços que utilizaremos em nossa aplicação! A parte do migrations.ts é **opcional**, você pode criar a lista de pessoas no próprio cliente do MySQL. **Não se esqueça de configurar o package.json e o tsconfig.json**

Se você quiser pode copiar essa configuração para o tsconfig.json:
```
{
   "compilerOptions": {
      "target": "es6",
      "module": "commonjs",
      "outDir": "./build",
      "rootDir": "./src",
      "removeComments": true,
      "strict": true,
      "noImplicitAny": true,
      "esModuleInterop": true,
      "skipLibCheck": true,
      "forceConsistentCasingInFileNames": true,
      "resolveJsonModule": true
   }
}
```

E esses scripts pro package.json:
```
  "scripts": {
    "start": "tsc && node ./build/index.js",
    "dev": "tsnd ./src/index.ts",
    "migrations": "tsnd .src/data/migrations.ts"
  }
```
Por aqui você já deve ter percebido que para começar uma aplicação no backend não se muda muita coisa. Salve esses comandos e configurações em algum lugar do seu pc para salvar tempo (é bom saber eles de cabeça também, mas se tiver com pressa só copia e cola tudo que vai dar bom)!

A configuração das pastas app.ts, connection.ts e .env vou deixar por conta de vocês.

Vamos então criar nossa tabela(dessa vez não temos um arquivo users.json pois vamos começar com ela despopulada): 

```
import connection from "./connection";

const printarError = (error: any) => { console.log(error.sqlMessage || error.message) }

const criarTabelas = async() => connection
    .raw(`
            CREATE TABLE IF NOT EXISTS services (
                id VARCHAR(255) PRIMARY KEY,
                nome VARCHAR(255) NOT NULL,
                cep VARCHAR(255) NOT NULL,
                logradouro VARCHAR(255) NOT NULL,
                numero INT NOT NULL,
                complemento VARCHAR(255),
                bairro VARCHAR(255) NOT NULL,
                localidade VARCHAR(255) NOT NULL,
                uf VARCHAR(255) NOT NULL
            )
    `)
    .then(()=> { console.log("Tabela criada!") })
    .catch(printarError)

    const encerrarConexao= () => {connection.destroy()}
    criarTabelas()
    .finally(encerrarConexao)

```

Veja que dessa vez usamos um then/catch para fazer a tabela ao invés de um async await para demonstrar que se pode fazer dos dois jeitos! Veja bem todas as colunas da tabela, elas são voltadas para registrar o endereço do usuário. Utilizando a ViaCEP, faremos com que seja necessário apenas fornecer o nome, cep, número e complemento caso haja!

O objeto que recebemos do ViaCEP possui muito mais informações do que o que queremos, então vamos criar uma função que receba um CEP mas retorne apenas aquilo que queremos: logradouro, bairro, localidade e UF.

Vamos começar criando um type no arquivo types.ts com o nome de **retornoEndereco** para fazer a tipagem de nossa função de pegar um endereço:
```
export type retornoEndereco = {
    logradouro: string
    bairro: string
    localidade: string
    uf: string
}
```

Agora vamos para  a criação da função:
```
import axios from 'axios'
import { retornoEndereco } from '../types'

export const getRetornoEndereco = async(
        numeroCep: string
): Promise<retornoEndereco | undefined> => {
try {
       const objetoEndereco = await axios.get(`https://viacep.com.br/ws/${numeroCep}/json/`)

    const { cep, logradouro, bairro,localidade, uf} = objetoEndereco.data
    const endereco = {
        cep,
        logradouro,
        bairro,
        localidade,
        uf
    }
    return endereco
} catch (error: any) {
    console.log(error.response ? error.response.data : error.message)
}
 }

 const teste = async() => {
     const cep = '01001-000'
     const result = await getRetornoEndereco(cep)
     return console.log(result)
 }
 teste()
```

Vamos entender: Nossa função recebe um parâmetro (numeroCEP) que é o número do CEP. A Promise retorna um objeto do tipo retornoEndereco caso tudo ocorrra corretamente ou algo do tipo unknown se a conexão não der certo. Depois fazemos a requisição do axios na const objetoEndereco. Observe que entre os parenteses temos uma template string para podermos incluir o parâmetro numeroCep no link na posição em que ele deve ficar para termos um retorno de um objeto json. No axios temos a propriedade .data para acessá-la e garantir que tenhamos como retorno apenas os parâmetros que queremos (cep, logradouro, bairro, localidade e UF), fazemos essa desconstrução. Depois criamos a const endereco que é um objeto que recebe os parâmetros selecionados do axios e o retornamos. Para ilustrar, sem a desconstrução seria asim:
```
const cep = objetoEndereco.data.cep
const logradouro = objetoEndereco.data.logradour
const bairro = objetoEndereco.data.bairro
const  localidade = objetoEndereco.data.localidade
const uf = objetoEndereco.data.uf
```

No catch, temos um console.log() para nos ajudar caso tenhamso algum bug no código. Dentro dele temos um ternário que verifica se nosso erro é do tipo error.response, caso negativo receberemos a mensagem de erro normal. O error.response é uma propriedade do axios e se tivermos um erro do axios na requisição receberemos um erro no error.response.data. Por fim temos um teste para ver sea função está funcionando. Criamos uma arrow function com o nome de teste, nela passamos um cep e chamamos a funçao getRetornoEndereco e retornamos a resposta dentro de um console.log. 

Para testar a função basta criar um script no package.json assim:
```
"endereco": "tsnd ./src/services/retornoEndereco.ts"}
// para executar o script:
npm run endereco
```

Agora que já testamos e vimos que deu tudo certo, vamos primeiro criar uma tipagem para o usuário criado no arquivo types.ts:
```
export type usuario = {
    id: string
    nome: string
    cep: string
    logradouro: string
    numero: number
    complemento?: string
    bairro: string
    localidade: string
    uf: String
}
```
Veja que o complemento **não** é obrigatório, por isso o **?** nele. Agora vamos na nossa pasta Data, criar um arquivi inserirUsuario.ts onde utilizaremos o knex Query Builder para fazer a conexão com a tabela:
```
import { usuario } from "../types";
import connection from "./connection";

const inserirUsuario = async(novoUsuario:usuario):Promise<any> => {
    await connection ('services')
    .insert(novoUsuario)
}

export default inserirUsuario
```
Nada de novo por aqui! Vamos então criar o endpoint criarUsuario.ts na pasta de endpoints:
```
import {Request, Response } from 'express'
import inserirUsuario from '../data/inserirUsuario'
import { getRetornoEndereco } from '../services/retornoEndereco'
import { usuario } from '../types'

export const criarUsuario = async (req: Request, res:Response ):Promise<any> =>{
    try {
        const {nome, cep, numero, complemento} = req.body
        
        if(!nome || !cep || !numero){
          return  res.status(422).send({error: "nome, cep e número são obrigatórios"})
        }

        const id = Date.now().toString()

        const {logradouro, bairro, localidade, uf}: any = await getRetornoEndereco(cep)
        
        if(!logradouro || !bairro || !localidade || !uf){
           return res.status(404).send({error: "CEP inválido!"})
        }
        const novoUsuario: usuario = {id, nome, cep, logradouro, numero, complemento, bairro, localidade, uf}

        const criarUsuario = await inserirUsuario(novoUsuario)

        res.status(201).send("Usuario Criado!")
    } catch (error: any) {
        if(res.status(200)){
            return res.status(500).send({error: "Erro interno do servidor"})
        } else {
            res.send({error: error.message || error.sqlMessage})
        }
    }
}

export default criarUsuario
```

Vamos desvendar o código aos poucos: primeiro temos uma desestruturação que pega os parâmetros nome, cep, numero e complemento a partir do corpo da requisição. Depois temos uma verificação para ver se esses parâmetros são mandados da maneira correta, caso contrário a função do endpoint termina aí (não se esqueça, o complemento é opcional!). Depois temos o id que vai ser definido a partir do timestamp e transformado em string. Então temos mais uma desestruturação que dessa vez pega o logradouro, bairro, localidade e uf da função getRetornoEndereco (A gente criou ela para retornar isso no objeto) seguida de uma verificação: se algum desses parâmetros não vier isso provavelmente significa que o CEP passado não é válido. Temos então a const novoUsuario que possui o tipo: usuario e recebe **todas** as propriedades definidas em seu tipo e necessárias à tabela. O próximo passo foi utilizar novamente o await para criar o usuário a partir da função inserirUsuario(que está na pasta data), usando como parâmetro nela a const novoUsuario (se lembra que no .insert do query builder utilizamos um parâmetro do tipo usuario? Tá aí ele!). Finalmente no catch fazemos a verificação de erro que já conhecemos anteriormente. E aí, consegue pensar em maneiras de melhorar essa função e suas verificações?

### NodeMailer
Vou colocar aqui como utilizar o nodemailer!