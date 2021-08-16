## **Iniciando o backend com Node e package.json**

### **O que é o node?**

O node é um interpretador de JavaScript que utiliza a engine V8 (criada pelo Google para executar JS no Chrome). É por conta dele que é possível o uso do JS para arquitetar servidores.

### **Por que usar o JS e Node?**
O JS possui um grande número de bibliotecas que facilitam o trabalho do desenvolvedor. Mas como utilizamos essas bibliotecas? Através do NPM! Que significa **N**ode **P**ackage **M**anager, traduzindo para o português é o gerenciador de pacotes do Node. 
E **pacotes** o que são? São quaisquer arquivos ou pastas que possuam um programa descrito por um package.json como, por exemplo, o já conhecido React.
Como dito anteriormente, o JS é tão utilizado assim pelo grande número de pacotes disponíveis para ele. O NPM tem mais de 10 milhões de usuários que baixam mais de **30 bilhões de pacotes por mês**.

### **package.json**

O package.json é um arquivo de configuração que fica na raiz dos projetos JS que utiliza o NPM como administrador de dependências. Nele estão contidos os scripts, os pacotes, as informações sobre o projeto, o autor do projeto, etc. 

#### **Como criar o package.json:**
```
npm init
//cria um package.json que deve ser preenchido

npm init --yes 
//cria um package.json com valores padrão

```

O package.json é a referência para os comandos NPM, quando se roda o comando npm install nomeDoPacote, por exemplo,  tal pacote é incluído nas "dependencies" do package.json e também no node_modules. O mesmo acontece com  npm install nomeDoPacote --save-dev, com a diferença de que assim o pacote vai para as "devDependencies" ao invés das "dependencies" (mas ainda assim vai para o node_modules!). Os pacotes nas devDependencies são aqueles que são essenciais para o seu desenvolvimento mas não necessariamente para sua execução (nas dependencies temos aqueles necessários à execução). Um exemplo é o TypeScript, necessário para o desenvolvimento, mas não é necessário para a execução, assim, para sua instalação usamos o comando:
```
npm install --save-dev typescript
//assim é instalado nas devDependencies
```

Outro comando que pode ser utilizado é o npm run nomeDoComando, que procura o comando dentro dos "scripts" e o executa caso exista.

**O arquivo package.json fica assim:**
![package.json](https://i.imgur.com/PG2Ovnd.png)


### **Voltando ao node:** 

O node pode ser utilizado para executar um código em JS no terminal, sem a necessidade da utilização do navegador. O comando utilizado é: 

```
node index.js
//aqui ele roda o código contido no arquivo index.js
//caso queira rodar outro arquivo, basta digitar o caminho dele depois da palavra node assim:

node nomeDaPasta/nomeDoArquivo.js
```

#### **process.argv**

É uma propriedade nativa do Node que consiste basicamente em um array de strings que são argumentos para que o processo seja executado. Os dois primeiros argumentos são fixos:

process.argv[0]: É o próprio node;
process.argv[1]: é o arquivo que vamos executar

E a partir do índice [2], podemos atribuir nossos próprios valores.

**Exemplo:**

```
//criando um número com process.argv e fazendo uma soma no console.log:
const num = Number(process.argv[2])

console.log(`A soma do seu número com 2 é ${2 + num}`)
```

Em seguida, é necessário executar o código com o node e atribuir um valor à const num, no terminal da seguinte forma:

```
//Esse comando é escrito e executado no terminal
node index.js 10

// Relembrando: o comando node executa o JS, o index.js é o endereço da execução do código
// e a string 10 é o valor atribuído à const num através do process.argv[2]
```


### **Criando Scripts**

Dentro do package.json existe um objeto e neste objeto existem os scripts que podem ser executados pelo node. Podemos criar nossos próprios scripts para nos auxiliar no ambiente de desenvolvimento. 

Na parte de "scripts" cire o seguinte script: 

```
"start": "node index.js"
```
Com esse script criado, ao invés de utilizar o comando  "node index.js 10 " do exemplo acima, podemos usar: 

```
npm run start 10
```

obtendo o mesmo resultado!

**package.json com novo start:**
![package.js](https://i.imgur.com/hj5uu05.png)

## **Resumo dos comandos aprendidos:**

```
npm init
//cria um package.json que deve ser preenchido

npm init --yes 
//cria um package.json com valores padrão

npm install --save-dev typescript
//assim é instalado nas devDependencies, podemos substituir o --save-dev por -D

node index.js
//roda o código contido no arquivo index.js

npm run nomeDoScript
//roda o script nomeado contido no package.json, como o start por exemplo que funcionaria assim:
npm run start

```

Vamos ao [Passo-2](./Passo-2(typescript).md)?