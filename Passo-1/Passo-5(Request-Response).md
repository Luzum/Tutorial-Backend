## **Request e Response**

Vamos falar um pouco mais sobre o Request(req) e o Response(res) agora:
Recapitulando, são os dois parâmetros obrigatórios que uma função de endpoint deve receber, sempre sendo req o primeiro e res o segundo.

**Request:**
As principais propriedades do req, são as diferentes formas que o front pode enviar dados e são elas:

* req.headers - headers significa cabeçalho em português, são os parâmetros de cabeçalho, ou seja, acessa as chaves no cabeçalho da requisição
* req.body -  body significa corpo em português, são os parâmetros de corpo, ou seja, acessa as chaves no corpo da requisição
* req.query -  query significa consulta em português, são os parâmetros de consulta, ou seja, acessa as chaves passadas por query parameters
* req.params -  parameters significa parâmetros em português, mas nesse caso são os parâmetros de caminho, acessando valores direto na URL

**Response:**

* res.status(x) - Envia uma resposta de status **x**
* res.send({y}) - Envia uma resposta com o objeto **y**
* res.end() - encerra a requisição, sem corpo na resposta

### Vamos a prática:

**Ex 1:**

Como ainda não vimos nada sobre banco de dados, vamos criar no vscode uma lista de pessoas para praticar o que vimos até então. Dentro da pasta src crie dois arquivos: data.ts e types.ts. Na pasta types.ts crie um type para representar uma **pessoa com id, nome e sobrenome**. Na pasta data.ts crie uma lista de pessoas que será do tipo pessoa. Antes de olhar as imagens tente fazer por você mesmo!

**types.ts**

![types.ts](https://i.imgur.com/e2p72ZT.png)

**data.ts**
![data.ts](https://i.imgur.com/rYD8ZwH.png)

**Ex 2:**

Vamos agora criar um endpoint para buscar todas as pessoas:

![pegarTodasPessoas](https://i.imgur.com/K8LSGkB.png)
![index](https://i.imgur.com/XIrHg34.png)
![postman](https://i.imgur.com/Lm9Wku2.png)


**Ex 3:**

Endpoint para buscar uma pessoa por id via path params

![pessoaPorId](https://i.imgur.com/N0uLC3V.png)
**IMPORTANTE:**
Note que aqui criamos uma const result do tipo pessoa **OU** undefined. Caso o id passado não exista na lista, o resultado será undefined. Note também que a criação dessa variável permite com que possamos fazer uma verificação sobre o resultado da nossa requisição possibilitando uma melhor experiência ao usuário. O res.statusCode =200 nos mostra que a requisição deu certo e receberemos o resultado que esperamos. O res.statusCode404 nos indica que não encontramos a pessoa e receberemos a mensagem de que não encontramos, vamos ver isso no postman!

![index](https://i.imgur.com/QvHaoDJ.png)
![postmanCerto](https://i.imgur.com/9JYHRch.png)
**Olhe o statusCode sublinhado!**
![postmanErro](https://i.imgur.com/oevEgHt.png)
**Olhe o statusCode e a mensagem!**

Brinque um pouco aqui com a requisição e o código. Experimente tirar o if/else e use apenas o res.send(result). O que acontece quando você manda um id não existente? E com um id existente tudo funciona? Com o if/else, mude a mensagem e veja o que acontece quando você busca um id não existente. As possibilidades são muitas, vá com calma que dá pra aprender muito!

**Acho que já deu pra entender a organização das pastas né? A partir de agora vou postar apenas o código e a imagem do postman!**

### **A ordem dos endpoints no index.ts importa!**
O nosso servidor quando recebe uma requisição, percorre todos os endpoints **na ordem de declaração**, então fique atento a isso! Às vezes você pode achar que tem algum erro no código do seu endpoint quando na verdade, só os colocou em uma ordem onde apenas o primeiro é lido! Por exemplo, se você tiver dois endpoints na seguinte ordem:

```
app.get('/pessoas/:id', pegarPessoasPorId )

app.get('/pessoas/mensagem', (req:Request, res:Response) =>{
    res.send("Mensagem")
})
```
Apenas o primeiro será executado no postman! Ao tentar executar o segundo, receberá um erro porque o id "mensagem" não existe. Teste da primeira maneira, e agora teste assim:

```
app.get('/pessoas/mensagem', (req:Request, res:Response) =>{
    res.send("Mensagem")
})
app.get('/pessoas/:id', pegarPessoasPorId )
```
Vê que da primeira forma é impossível chegar no endpoint da mensagem? Já da **segunda maneira você consegue acessar ambos os endpoints!**

**Ex 4:**  

Vamos deletar uma pessoa da nossa lista a partir de seu id via path params? O código vai ser muito parecido com o de procurar uma pessoa pelo seu ID (Dica: nessa lista o ID e o Index coincidem, o que você pode fazer com essa informação?), então pense um pouco no que mudaria no código e tente fazê-lo antes de olhar o código abaixo:

```
export const deletarPessoaPorId = (
    req: Request,
    res: Response
): void => {
    const deletar: number = pessoas.findIndex(
        pessoa => pessoa.id === Number(req.params.id)
    )
    pessoas.splice(deletar, 1)

    res.status(204)

}
```
Note que dessa vez transformei o req.params.id em Number, o resultado é o mesmo de transformar o pessoa.id em string!

no index.ts:
```
app.delete('/pessoas/:id', deletarPessoaPorId)
```

Mas podemos melhorar ainda mais esse código, não podemos? Vamos então utilizar um try/catch junto com alguns if e if/else:

```
export const deletarPessoaPorId = (
    req: Request,
    res: Response
): void => {

    try {
        const deletar: number = pessoas.findIndex(
            pessoa => pessoa.id === Number(req.params.id)
        )
        
            if (deletar <= -1 || deletar >= pessoas.length) {
                res.statusCode = 404
                throw new Error("Pessoa nao encontrada!")
        }
        pessoas.splice(deletar, 1)

        res.status(204).end()
    
    } catch (error) {
        if(res.statusCode === 200){
            res.status(500).end()
        } else {
            res.statusMessage = error.message
            res.end()
        }
        
    }

```
Neste último exemplo, utilizamos o  [try/catch](https://developer.mozilla.org/pt-BR/docs/Web/JavaScript/Reference/Statements/try...catch) (Sempre leiam tudo que tiver no MDN, rs) para fazer validações na requisição de forma que a experiência do usuário seja melhor. A lógica do primeiro if é simples: o id fornecido não pode ser negativo e também não pode ser maior ou igual ao index!
Mais uma coisa, quando usarmos um banco de dados tipo o MySql, para deletar um objeto de uma lista vai ser bem mais fácil!, não vamos precisar de usar o splice, vamos usar funções do próprio MySql :)

**Quando usamos um index válido:**
![postman](https://i.imgur.com/7udSnD1.png)
**Olhe os sublinhados de vermelho!**

**Quando usamos um index inválido:**
![postman](https://i.imgur.com/V9iFwd5.png)
**Note nosso novo erro ali embaixo!**

**Como fica nossa lista:**
![postman](https://i.imgur.com/XxqF4oN.png)
**Note que beltrano foi deletado!**

**Aprenda mais sobre os códigos de status**

Lê [aqui](https://developer.mozilla.org/pt-BR/docs/Web/HTTP/Status)