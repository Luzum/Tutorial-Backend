# EXPRESS JS

Olá! Para começar o back-end necessitamos instalar alguns pacotes do node para facilitar nossa vida. De início necessitamos o typescript, o express e o cors. Já nas devDependencies, necessitamos do @types/Express e o @types/node. As bibliotecas @types fornecem as tipagens das bibliotecas utilizadas para o typescript.

O que são as devDependencies? São as dependências de desenvolvimento utilizadas na construção do projeto, mas que não precisam estar disponíveis para o usuário final. 

## **Instalando o Express e o cors:**

```
npm install express
npm install cors

//ou então para ficar mais simples: 

npm i express cors
```

**Instalando o types do express, do node e do cors:**

```
npm install @types/node --save-dev
npm install  @types/express --save-dev
npm install @types/cors --save-dev

//De um jeito mais simples: 
npm i @types/node @types/express @types/cors -D
```

Você pode instalar mais de um pacote de uma só vez, é só separá-los por espaço. O -D é uma abreviação para --save-dev (que cria dev dependencies), assim como o i é uma abreviação para install.  

**ts-node-dev:**
Vamos facilitar nossa vida um pouco mais? Agora que vamos criar um servidor, sempre que fizermos qualquer alteração no código, teremos que transpilar e então rodar o código novamente para vermos as atualizações. Mesmo já tendo configurado o script "start" para fazer isso, pode ser um pouco trabalhoso recorrer sempre ao npm run start. Para nos ajudar podemos instalar como dependência de desenvolvimento o ts-node-dev, da seguinte forma:
```
npm i -D ts-node-dev
```
Com isso, podemos executar diretamente um arquivo typescript, não precisaremos sempre transpilar as nossas alterações. No package.json podemos criar um novo script:

```
"dev-start": "ts-node-dev src/index.ts"
// ou ainda, de maneira abreviada:
"dev": "tsnd src/index.ts"
```
Esse script sempre recarregará sozinho!
**Nossos scripts ficarão assim:**
![scripts](https://i.imgur.com/Egvqdt1.png)

## **Iniciando o servidor:**

Para iniciar o servidor necessitamos importar e invocar a função que a biblioteca express exporta por padrão (o famoso export default). Ah! Por organização faremos isso no arquivo app.ts 

```
import express, { Express } from 'express'

const app: Express = express();

// Na primeira linha são importados a função padrão do express seguida da sua tipagem entre chaves.
// Na próxima linha vemos a função ser invocada através da const app, e a tipagem sendo utilizada nela.
```

Beleza, mas é só isso? Não! Precisamos também configurar dois serviços para a inicialização correta do servidor, um que converte o body das nossas resposta em json e outro que evita o erro de CORS (usa o google pra saber mais sobre isso, rsrs)

Como fazer isso:

```
import express, { Express } from 'express'
import cors from 'cors'

const app: Express = express();

app.use(express.json());
app.use(cors());

```

Por fim, o servidor precisa se comunicar com alguma porta da nossa máquina que faremos utilizando a seguinte função:

```
app.listen(3003, () => {
    console.log('Servidor rodando em http://localhost:3003')
})
```

Repare que na função app.listen recebemos dois parâmetros: o primeiro é a porta do localhost onde queremos que ocorra a comunicação. Ali a porta não necessariamente tem que ser a 3003, você pode escolher, mas tenha cuidado para não escolher uma porta que seja utilizada por outra aplicação!
Já o segundo parâmetro é uma função de callback que serve para nos sinalizar se a aplicação está rodando. Se a mensagem do console.log aparecer em seu terminal significa que tá tudo certo!

Para facilitar a vida de todos, o código inteiro do app.ts fica assim: 
```
import express, { Express } from 'express'
import cors from 'cors'

const app: Express = express();

app.use(express.json());
app.use(cors());

app.listen(3003, () => {
    console.log ('Servidor rodando')
})

```
Mas é só isso? Sim! Assim já podemos iniciar nosso servidor, mas ainda nesse estágio inicial podemos melhorar um pouco o código, deixando ele um pouco mais robusto e a prova de erros da seguinte maneira:

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

Aqui, criei a const portNumber para garantir que o console.log mostre corretamente a porta em que o servidor está rodando (note que a mensagem do console.log está em uma template string para podermos chamar a variável. Lembrando que você pode escolher o número da porta! Só tenha cuidado para não escolher uma que já esteja sendo utilizada. Evite a 3000). 
Além disso criei também a const server para poder utilizar um if/else para termos um melhor retorno no terminal se o servidor realmente está rodando ou não. 

No vscode fica assim: 
![expresscors](https://i.imgur.com/cpogXir.png)
**Importante:**
A *const app* é importada no arquivo index.ts. Com essas configurações já deve ser possível rodar o comando npm run dev e verificar se nosso servidor está funcionando, dá seguinte maneira:
![index.ts](https://i.imgur.com/yVSH6RV.png)

## **Resumo dos comandos:**
Vamos juntar todos os comandos aprendidos até então aqui na ordem de sua execução:


```
npm init
//cria um package.json que deve ser preenchido

npm init --yes 
//cria um package.json com valores padrão

npm install -D typescript
//instala o TypeScript nas devDependencies

tsc --init
// cria um arquivo de configuração do typescript

npm install express
npm install cors
// instala o express e o cors

npm install ts-node-dev --save-dev
//instal o ts-node-dev

npm install @types/node --save-dev
npm install  @types/express --save-dev
npm install @types/cors --save-dev
//instala as tipagens do node, express e cors

//Como digitar os comandos em menos linhas:

npm i express cors
npm i -D @types/node  @types/express @types/cors typescript ts-node-dev
```

Passo-3(express.js)

Vamos ao [Passo-4](./Passo-4(Endpoints).md)?