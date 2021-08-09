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

**Ex 1**
Como ainda não vimos nada sobre banco de dados, vamos criar no vscode uma lista de pessoas para praticar o que vimos até então. Dentro da pasta src crie dois arquivos: data.ts e types.ts. Na pasta types.ts crie um type para representar uma **pessoa com id, nome e sobrenome**. Na pasta data.ts crie uma lista de pessoas que será do tipo pessoa. Antes de olhar as imagens tente fazer por você mesmo!

**Types.ts e data.ts:**

![types.ts](https://i.imgur.com/e2p72ZT.png)
![data.ts](https://i.imgur.com/rYD8ZwH.png)

**Ex 2**
Vamos agora criar um endpoint para buscar todas as pessoas:
![pegarTodasPessoas](https://i.imgur.com/K8LSGkB.png)
![index](https://i.imgur.com/XIrHg34.png)
![postman](https://i.imgur.com/Lm9Wku2.png)


**Ex 3**
Endpoint para buscar uma pessoa por id via path params
![pessoaPorId](https://i.imgur.com/N0uLC3V.png)
**IMPORTANTE:**
Note que aqui criamos uma const result do tipo pessoa **OU** undefined. Caso o id passado não exista na lista, o resultado será undefined. Note também que a criação dessa variável permite com que possamos fazer uma verificação sobre o resultado da nossa requisição possibilitando uma melhor experiência ao usuário. O res.statusCode =200 nos mostra que a requisição deu certo e receberemos o resultado que esperamos. O res.statusCode404 nos indica que não encontramos a pessoa e receberemos a mensagem de que não encontramos, vamos ver isso no postman!

![index](https://i.imgur.com/QvHaoDJ.png)
![postmanCerto](https://i.imgur.com/9JYHRch.png)
Olhe o statusCode sublinhado!
![postmanErro](https://i.imgur.com/oevEgHt.png)
Olhe o statusCode e a mensagem!

Brinque um pouco aqui com a requisição e o código. Experimente tirar o if/else e use apenas o res.send(result). O que acontece quando você manda um id não existente? E com um id existente tudo funciona? Com o if/else, mude a mensagem e veja o que acontece quando você busca um id não existente. As possibilidades são muitas, vá com calma que dá pra aprender muito!

**Acho que já deu pra entender a organização das pastas né? A partir de agora vou postar apenas o código e a imagem do postman!**