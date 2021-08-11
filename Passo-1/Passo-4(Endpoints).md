### Construindo Endpoints
Anteriormente vimos uma maneira bem básica de se iniciar um servidor. Agora Que já temos ele com algumas configurações mínimas, podemos criar nosso primeiro endpoint! Para isso temos um padrão:
```
app.method(path, handler)

//O method é o método que você quer utilizar em seu endpoint(GET, PUT, POST, PATCH, DELETE e mais alguns outros),
//o path o caminho que você vai utilizar e o handler a função que será acionada ao bater no endpoint.
//Vamos ver mais sobre os methods jájá.
```

### Vamos aos exemplos!

Para começar, se já não tiver criado, crie o arquivo index.ts. Lá você importará o código da pasta app.ts
Primeiro volte no app.ts e certifique-se que está exportando a const app, desta maneira:

```
import express, { Express } from 'express'
import cors from 'cors'

const app: Express = express();

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

export default app

```
**Guarde bem esse código da pasta app!Ele será basicamente o mesmo em todas aplicações do backend que você fizer!**
No index.ts importe o app e comece a criação do endpoint:

```
import {app} from './app'

app.get("/caminhoEscolhido", () => {})
```

Mas o que é essa arrow function vazia? Ela que será acionada quando chegar uma requisição para o primeiro parâmetro do app.get (no caso o "/caminhoEscolhido"). Ela precisa de parâmetros para receber a requisição e enviar uma resposta.


### Criando a pasta endpoints 

Para organizar um pouco mais o código, não vamos escrever a função diretamente no método do app. Vamos criar uma pasta para os handlers dos endpoints e exportá-los para o app.ts. Vamos criar nosso primeiro endpoint?


```
import {Request, Response} from "express"

export const primeiroEndpoint = (req: Request, res: Response) => {
    res.send ({
        message: "Venho do Express!"
    })
}

```

As funções do endpoint sempre devem receber obrigatoriamente dois parâmetros, o request (abreviado como req) e o response (abreviado como res). Entre as chaves do import estão sendo importados as tipagens destes parâmetros que são fornecidas pelo @types/express (lembra dele?). Mas o que é esse res.send? O res é um dos parâmetros obrigatórios do endpoint, nesse caso o .send serve para enviar a resposta para o endpoint. Existem   
vários outros métodos, quando você digitar res. provavelmente o seu editor de código te mostrará todos eles, mas vamos com calma! Aqui Estamos apenas enviando a mensagem "Venho do Express!" para sua API. 

Assim, se seu servidor já estiver rodando se você abrir o caminho http://localhost:3003/caminhoEscolhido na API de sua preferência e der um send você receberá o objeto do res.send nesse caso virá assim:

```
"message": "Venho do Express!"
```

**Como fica nosso vscode:**
![vscode](https://i.imgur.com/8xIgmYo.png)
![vscode2](https://i.imgur.com/envxloq.png)

**Como fica o postman:**
![postman](https://i.imgur.com/hdjymBp.png)
O que significa o endereço http://localhost:3003/primeiroEndpoint ?
o localhost:3003 é a comunicação com o seu servidor interno que você definiu no arquivo app.ts (se lembra da porta 3003?) e após ele o          /primeiroEndpoint foi o endereço definido no  método app.get. Aqui você poderia escolher qualquer outro nome que seu endpoint continuaria funcionando normalmente (desde que você também altere o endereço em sua API). Então vai lá e da uma brincada mudando o endereço pra ver o que acontece!
**Note que no postman a mensagem só aparece depois de se clicar no botão Send**

Parabéns! Criamos nosso primeiro endpoint! 