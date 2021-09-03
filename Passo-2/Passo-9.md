## **Cheat code verificações**

Temos algumas verificações que são essenciais de serem feitas

Método get:
```
// Se você tem um get para pegar todos os itens da tabela é bom verificar o resultado retornado
//ex:
const result = await selecionarTodos
//verificação:
if(!result){
    res.status(404).send({error: "não encontramos ninguém"})
}

```