## **Resumo dos primeiros passos**
Vamos começar a ver sobre as APIs REST e também um pouco sobre os bancos de dados, mas  antes vamos fazer uma pequena revisão sobre o que já aprendemos anteriormente.

**Node e package.json**
O node é um interpretador de JavaScript e permite que rodemos nosso código no terminal sem a necessidade do navegador. O package.json é um arquivo que fica na raiz de um projeto em JS e nele estão os scripts, os pacotes, informações do projeto, etc. 
```
npm init --yes 
//cria um package.json com valores padrão

node index.js
//roda o código contido no arquivo index.js
```

**TypeScript**
O TypeScript é uma linguagem de programação orientada a objetos (POO) desenvolvida pela Microsoft baseada no JavaScript. Para sua execução, precisa primeiro ser transpilado para JS

```
npm i -g typescript 
//instala o typescript globalmente

npm install --save-dev typescript
//instala o typescript nas devDependencies

tsc index.ts
// transpila o arquivo index.ts

tsc --init
// cria um arquivo de configuração do tsconfig.json
```
**Express e cors**
O express é uma biblioteca do NPM que nos permite fazer o roteamento de uma solicitação do cliente por um endpoint específico através do:

```
app.METHOD(PATH, HANDLER)
```
O CORS é isso [aqui](https://developer.mozilla.org/pt-BR/docs/Web/HTTP/CORS), e a biblioteca CORS nos ajuda a evitar erros CORS no cliente.

```
npm i express cors
//instala o express e o cors

npm i @types/node @types/express @types/cors -D
//instala as tipagens necessárias ao typescript
```
**ts-node-dev**
Biblioteca que nos permite o hot-reload de nossa aplicação, com ela não precisamos transpilar e rodar nosso código a cada nova alteração.

```
npm i -D ts-node-dev
```
criando o script dev:
```
"dev": "tsnd src/index.ts"
```

**Comandos para instalar tudo:**
```
npm i express cors
npm i -D @types/node  @types/express @types/cors typescript ts-node-dev
```

Clique [aqui](./Passo-2.md) para a próxima seção!

