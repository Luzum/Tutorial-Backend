## Classes e Encapsulamento

O básico do Backend já aprendemos. Vimos como instalar o TypeScript, utilizar o node, criar nossas APIs e seus endpoints, como criar e alterar tabelas no MySQL, como integrá-las, como manipular o MySQL dentro de nosso editor de código e como integrar outras APIs à nossa API. Se você ainda tem algumas dificuldades volte atrás um pouco e estude com calma suas dúvidas, isso vai facilitar muito os estudos daqui para frente. Essa próxima parte de nossos estudos é mais focada na organização do código. Vamos nos aprofundar mais no TypeScript e também aprender um pouco mais sobre Programação Orientada a objetos. 

**Paradigmas de programação:**
Vamos falar sobre alguns paradigmas de programação:

* Estruturada:

Estruturas condicionais e loops controlam o fluxo de execução do código e impede o controle absoluto do fluxo de execução do programa (impede que o código pule linhas em sua execução).

* Orientada a Objetos:

Utiliza objetos para modelar e controlar dados e suas manipulações. Também restringe o acesso a variáveis e rotinas em partes mais específicas do código

* Funcional:

É baseado em funções puras que operam em cima dos dados.

As linguagens de programação normalmente se encaixam em um ou mais paradigmas de programação, Java, por exemplo é voltada à Orientação a Objetos, LIST é funcional. Alguns exemplos de linguagems multi-paradigma são JavaScript/TypeScript e Python.

Vamos nos aprofundar na **Programação Orientada a Objetos (POO)**. Esse paradigma é um dos mais conhecidos e foi criado por volta dos anos 1960. Tem como unidade central os... objetos, que podem ser criados como uma forma de **abstração** de conceitos do mundo real o que acaba por simplificar o entendimento do código. Mas o que são esses objetos? Então, dentro da POO, cada objeto é definido por um conjunto de **atributos** e **métodos**. Os atributos são as informações, os dados do objeto. Já os métodos são as funções, as operações que operam sobre esses atributos ou que se relacionam com esse objeto de alguma forma. Esses objetos são criados a partir de uma **classe** que define os atributos e os métodos que ele terá. Uma classe é de certa forma semelhante a um *type* ou *interface* **mas possui suas diferenças!** Vale lembrar também que os objetos aqui citados são **diferentes** do objeto do JavaScript: no JS, os objetos podem ter atributos e métodos também, mas  não são criados a partir de uma classe. A diferença principal entre um *type* e *interface* do typescript para uma *classe*, consiste no fato de que enquanto o type e a interface definem somente o formato de um objeto, a classe define o formato, implementa os métodos e também pode definir valores padrões para os atributos do objeto!

E o que é essa abstração que falei mais cedo? A ideia da abstração é ser uma maneira de se criar uma representação mais simples de coisas que são relativamente complexas, de modo a esconder essa complexidade em interfaces mais simples. Ou seja, ela esconde detalhes da implementação e facilita o uso dessa interface, mas em contrapartida limita um pouco as possibilidades dela. Vamos pensar em um campo de *input*: podemos criar um input a partir do zero no React (ou no html/css/js), e estilizá-lo da maneira que quisermos. Ou podemos pegar um input já pronto de uma biblioteca como a material-ui. No primeiro caso consigueremos deixar o input do jeito que quisermos, mas teremos muito mais trabalho que às vezes não é necessário. No segundo caso teremos um input já estilizado e bonitão, mas teremos uma maior dificuldade em personalizá-lo. Esse input do material-ui é uma **abstração** do input do html.  Outra coisa importante: é possível criar **múltiplos** níveis de abstração. Uma boa abstração acaba por deixar o código mais legível, consistente, reaproveitável e expansível, mas é algo relativamente difícil de ser feito.

O próprio **React** é uma abstração do código **JavaScript**, que por sua vez é uma abstração do **C++**, que é por sua vez de **Assembly** e assim por diante.

**Voltando ao TypeScript**

Dentro do TypeScript temos alguns tipos nativos que são abstrações: booleanos, números, strings, arrays, objetos. Na POO, podemos criar novos tipos de dados, a partir de abstrações que são as classes. Podemos chamá-los de TAD: **T**ipos **A**bstratos de **D**ados. Mas como assim? Ainda não entendi direito! Vamos ver então alguns exemplos de TAD. Pense num gato: gatos existem no mundo real e tem algumas características próprias. No TypeScript não existe um tipo nativo que representa um gato nem nada assim. Mas podemos criar uma classe para abstrair esse conceito em nosso código (sei lá, imagina que tá fazendo um app pra um petshop ou alguma coisa assim, rsrs). Uma abstração **escolhe** as coisas que importam ou são necessárias daquilo que escolhe abstrair. Um gato possui várias características diferentes e para fins didáticos vamos escolher algumas: um gato domesticado possui um nome, um peso, ele mia e também come. Seria impossível descrever completamente um gato no código, mas podemos fazer uma aproximação a partir do contexto da aplicação que desenvolveremos.

**Classes**

Uma classe representa o TAD e podem ser criadas várias instâncias de uma classe. Vamos ver como ficaria a classe "gato"

```
class Gato {
    nome: string;
    peso: number;

    miar(): void {
        console.log("miau 🐱")
    }

    comer(quantidade: number): void {
        console.log("O gato comeu ", quantidade)
    }
}
```



**Construtor**

Existem casos em que queremos que algumas ações sejam executadas quando criarmos uma instância de classe. Para isso utilizaremos o construtor. **Toda** classe possui um construtor, mesmo se não o declararmos - nesse caso temos um construtor vazio - e os construtor pode receber parâmetros. Quando criamos uma instância esses parâmetros devem ser passados para ela. Utilizaremos a palavra **this** para referenciar um atributo e/ou método dentro daquela instância de classe criada. 
Nesse caso da classe Gato, queremos que todas as vezes que criarmos uma nova instância ela tenha um nome e um peso definidos, para isso utilizaremos a constructor function assim:

```
class Gato {
    nome: string;
    peso: number;
    
    constructor(nome: string, peso: number){
        this.nome= nome;
        this.peso = peso;
    }

    miar(): void {
        console.log("miau 🐱")
    }

    comer(quantidade: number): void {
        console.log("O gato comeu ", quantidade)
    }
}
```
Agora se quisermos criar uma nova instância da classe "Gato" basta fazer o seguinte:

```
const garfield = new Gato("Garfield", 15)

const felix = new Gato("Félix", 5)
```

Se não tivessemos a constructor function não conseguiríamos atribuir um nome e um peso para nossas consts garfield e felix! Mas e esses métodos miar e comer, como chamamos? Assim:

```
felix.miar()
garfield.comer(15)
```
Veja aqui na imagem como ficaria tudo(foi feita no codesandbox):
![Gato](https://i.imgur.com/Q9DApxA.png)
