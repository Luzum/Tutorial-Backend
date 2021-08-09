## **TypeScript?**
O TypeScript é uma linguagem de programação baseada no JavaScript desenvolvida pela Microsoft, mas ao invés de ser uma linguagem de paradigma orientado a eventos, é uma linguagem de paradigma orientado a objetos (POO). As linguagens **POO** tem como objetivo criar um sistema de modelos que se aproximam do mundo real, através de, pasmem, **objetos** e também de **classes**.

### **Instalando o TypeScript**

Para instalar o TypeScript globalmente, basta utilizar o seguinte código:

```
npm install -g typescript

// o -g garante que ele seja instalado globalmente em sua máquina, o comando pode ser assim tb:

npm i -g typescript 
```
### **Transpilação**

**O que é a transpilação?** No universo da programação, temos linguagens de alto nível (**HLL** - High Level Language) e linguagens de baixo nível (**LL** - Low Level Language). Quando convertemos uma linguagem de alto nível em uma de baixo, o fazemos pelo processo de **compilação**. Já quando convertemos uma linguaguem de alto nível em outra de alto nível o nome do processo é **transpilação**. Ou seja, o processo de transpilação de maneira simplificada é a conversão de uma HLL em outra HLL.

**Mas por que isso é importante?** 

Em alguns casos é necesário que uma linguagem passe pelo processo de transpilação antes de poder ser compilada para sua máquina entender o código, e esse é o caso do TypeScript. Ele necessita ser transpilado para JS antes de ser compilado. 

No primeiro passo, já falamos um pouco sobre o node e como com ele conseguimos visualizar a execução do código JS em nosso terminal, sem a necessidade do navegador. Porém o node não consegue ler TS (só em alguns casos muito específicos onde você usa apenas um console.log básico), então precisamos antes, transpilar o código. **Como fazemos isso?** Simples, presicmaos apenas digitar o seguinte comando:

```
tsc nomeDoArquivo.ts
```

Ou seja, vamos supor que tenhamos criado uma pasta index.ts e queremos ver a execução do código no terminal, como faremos? Assim:

```
tsc index.ts
// o comando tsc, transpila o index.ts, criando um novo arquivo chamado index.js
node index.js

// já o node index.js, executa o código já transpilado para JavaScript

```


### **Simplificando a transpilação**

Podemos criar um arquivo de configuração do TypeScript para facilitar nossa vida na transpilação através do comando:

```
tsc --init
```

Isso irá gerar um arquivo tsconfig.json que terá a seguinte configuração:

![tsconfig.json](https://i.imgur.com/ZSTFtZz.png)
![tsconfig.json](https://i.imgur.com/q1Qt4AY.png)

**Configurando o tsconfig.json**
Neste arquivo, precisamos fazer algumas configurações básicas:

1. Na linha 7 devemos mudar o "target" para "es6"
2. devemos tirar o comentário das linhas 15, 17,18, 21 e 29
3. Na linha 17, devemos definir o outDir como "./build"
4. Na linha 18, devemos definir o rootDir como "./src"
**O arquivo fica assim:**
![tsconfig.json](https://i.imgur.com/5xSGPCM.png)
**Criando as pastas src e build**

Como demos o endereço das pastas src e build para o rootDir e o outDir respectivamente, devemos criá-las para guardar os arquivos TS e o JS para isso utilizaremos dentro do terminal:

```
mkdir src build
```
Nosso arquivo index.ts deverá ficar dentro da pasta **src** e o index.js dentro da basta build.

## **Incrementando o script start**

Se lembram quando mexemos no package.json e o deixamos assim:

```
"start": "node index.js"
```

Então, agora que conhecemos o tsc, podemos incrementá-lo no script de start para que a transpilação ocorra sempre que dermos o comando npm run start, da seguinte forma:

```
"start": "tsc && node index.js"

```
**script start no arquivo:**
![script](https://i.imgur.com/Rdzotlz.png)

**Organização das pastas e arquivos**
Devemos ter até então algo parecido com isso: 
![pastas](https://i.imgur.com/hQ5qYhF.png)

## **Resumo dos comandos:**

```
npm i -g typescript 
//instala o typescript globalmente

tsc index.ts
// transpila o index.ts, criando um novo arquivo chamado index.js

tsc --init
// cria um arquivo de configuração do typescript

```
