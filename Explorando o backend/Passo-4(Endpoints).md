### Construindo Endpoints
Anteriormente vimos uma maneira bem básica de se iniciar um servidor. Agora Que já temos ele com algumas configurações mínimas, podemos criar nosso primeiro endpoint! Para isso temos um padrão:
```
app.method(path, handler)

//O method é o método que você quer utilizar em seu endpoint, o path o caminho que você vai utilizar e o handler a função que será acionada
// ao beter no endpoint
```

### Vamos aos exemplos!

Para começar, se já não tiver criado, crie o arquivo index.ts. Lá você importará o código da pasta app.ts
Primeiro volte no app.ts e exporte a const app, ficando assim: 

```
import express, { Express } from 'express'
import cors from 'cors'

 export const app: Express = express();

app.use(express.json());
app.use(cors());

const portNumber = 3003

const server = app.listen(portNumber, () => {
   if(server) {
       console.log(`Servidor rodando em http://localhost:${portNumber}`)
   } else {
       console.error('Servidor não iniciado')
   }
});

```
No index.ts import o app e comece a criação do endpoint:

```
import {app} from './app'

app.get("/caminhoEscolhido", () => {})
```

Mas o que é essa arrow function vazia? Ela que será acionada quando chegar uma requisição para o primeiro parâmetro do app.get (no caso o "/caminhoEscolhido"). Ela precisa de parâmetros para receber a requisição e enviar uma resposta.


### Criando a pasta endpoints 

Para organizar um pouco mais o código, ao invés de escrever a função diretamente no método do app, podemos criar uma pasta para as funções handlres dos endpoints e exportá-las para o app.ts. Vamos criar nosso primeiro endpoint?


```
import {Request, Response} from "express"

export const primeiroEndpoint = (req: Request, res: Response) => {
    res.send ({
        message: "Venho do Express!"
    })
}
```

As funções do endpoint sempre devem receber obrigatoriamente dois parâmetros, o request (abreviado como req) e o response (abreviado como res). Entre as chaves do import estão sendo importados as tipagens destes parâmetros que são fornecidas pelo @types/express (lembra dele?). Mas o que é esse res.send? O res é um dos parâmetros obrigatórios do endpoint, nesse caso o .send serve para enviar a resposta para o endpoint. Existem   
vários outros métodos, quando você digitar res. provavelmente o seu editor de código te mostrará todos eles, mas vamos com calma! Aqui Estamos apenas enviando a mensagem "Venho do Express!" para o postman. 

Assim, se seu servidor já estiver rodando se você abrir o caminho http://localhost:3003/caminhoEscolhido no postman e der um send você receberá o objeto do res.send nesse caso virá assim:

```
"message": "Venho do Express!"
```