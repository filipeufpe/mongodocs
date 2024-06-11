# MongoDB

## Parte 1: Programação

Essa parte não é especificamente relacionada ao _mongodb_, mas são conceitos que são pré-requisitos para usar o _mongodb_ de forma correta.

### 1.1 Variáveis

Uma variável é uma ferramenta de linguagens de programação que permite que nós armazenemos dados de forma a reutilizá-los de maneira simples e repetitiva.

Em _javascript_ (linguagem que usamos para interagir com o _mongodb_) as variáveis são definidas da seguinte forma:

```javascript
var pi = 3.14159265359

var nome = "Filipe Aguiar"

var servidor = {
    cpf: 12345698774,
    nome: "Lamartine"
    matricula: 1233211444
}
```

Essas variáveis podem ser utilizadas sempre que precisarmos manipular os valores definidos. Por exemplo:

```javascript
// Calcular área de um círculo
var pi = 3.14159265359;
var raio = 5;

//Área = pi x raio²
var area = pi * raio ** 2;
```

### 1.2 Objetos

Objetos são estruturas de dados mais complexas que `números` ou `texto`. Objetos podem conter um ou mais `atributos`, que são escritos na forma de `chave-valor`. Objetos são delimitados por `{}`. Esse é um exemplo de um objeto simples:

```javascript
{
  "nome": "Filipe Aguiar"
}
```

Nesse exemplo o atributo `nome` é definido pela chave `nome` e pelo valor `"Filipe Aguiar"`.

Um objeto pode conter outros objetos, como no exemplo:

```javascript
{
    nome: "Universidade Federal de Pernambuco",
    cnpj: 5554447778899,
    endereco: {
        rua: "Av. xxx",
        bairro: "Cidade universitária"
    }
}
```

`endereco` é um atributo de uma instituição, e é um objeto por si mesmo.

Muitas vezes, para representar um objeto, é necessário que um atributo contenha várias informações, como telefones numa instiuição, nesses casos podemos utilizar _arrays_, que nada mais são que um conjunto de outros dados. Um array é delimitado por `[]`.

```javascript
{
    nome: "Universidade Federal de Pernambuco",
    cnpj: 5554447778899,
    endereco: {
        rua: "Av. xxx",
        bairro: "Cidade universitária"
    },
    telefones: [
        "8121264466",
        "8121266644"
    ]
}
```

No exemplo acima, o atributo `telefones` é definido como um _array_ de valores simples. No entanto, podemos criar um _array_ de **objetos**, segue exemplo:

```javascript
{
    nome: "Universidade Federal de Pernambuco",
    cnpj: 5554447778899,
    sigla: "ufpe",
    endereco: {
        rua: "Av. xxx",
        cidade: "Recife",
        bairro: "Cidade universitária"
    },
    telefones: [
        {
            ddd: "81",
            numero: "21264466"
        },
        {
            ddd: "81",
            numero: "21266644"
        }
    ]
}
```
Objetos podem ser atribuídos à variáveis. Caso queiramos utilizar o seu valor posteriormente. O seguinte código atribui o objeto acima à uma variável chamada `ufpe`:

```javascript
var ufpe = {
    nome: "Universidade Federal de Pernambuco",
    cnpj: 5554447778899,
    sigla: "ufpe",
    endereco: {
        rua: "Av. xxx",
        cidade: "Recife",
        bairro: "Cidade universitária"
    },
    telefones: [
        {
            ddd: "81",
            numero: "21264466"
        },
        {
            ddd: "81",
            numero: "21266644"
        }
    ]
}
```
### 1.3 Acessando atributos de objetos
Considere o objeto atribuído à variável `ufpe` acima. Para acessar seus atributos, podemos utilizar a notação de `.`. Ou seja, para acessar o nome da instituição, podemos fazer:
```javascript
ufpe.nome //isso equivale à "Universidade Federal de Pernambuco"
```
Para acessar a `rua`, podemos usar:
```javascript
ufpe.endereco.rua
```

Acessar itens em um _array_ não é tão simples, uma vez que é preciso identificar o item dentro de um **conjunto**. O _mongodb_ tem ferramentas para fazer essa identificação.

## Parte 2: MongoDB
O _mongodb_ tem um conjunto de funções para manipular objetos que são armazenados em um banco de dados.

Primeiro, precisamos identificar o **Banco de Dados** que vamos manipular. Isso é feito com o comando:
```javascript
use('bancodedados') //as aspas duplas ou simples são obrigatórias
```
Uma vez que o Banco de Dados foi selecionado, as operações do _mongodb_ tem sempre a forma:
```javascript
db.coleção.ação()
```
Onde `coleção` é a coleção que você quer manipular (instituições, serviços, ...) e `ação` é **como** você quer manipular essa coleção.

Uma ação do _mongodb_ **geralmente** recebe um objeto de _query_, ou seja, para executar uma operação é necessário informar à ação **em qual documento** você quer executar tal operação.

Por exemplo:
```javascript
db.instituicoes.find( { sigla: "ufpe" } )
```
Essa **ação** retorna o documento da **coleção** `instituicoes` cuja sigla seja `"ufpe"`

```javascript
db.instituicoes.find( { endereco.cidade: "Recife" } )
```
Essa **ação** retorna o documento da **coleção** `instituicoes` cujo atributo `cidade` do atributo `endereco` seja `"Recife`.

### 2.1 Inserindo documentos
Para inserir a insituição definida pela variável `ufpe` acima, usamos a ação:
```javascript
db.instituicao.insertOne(ufpe)
```
A ação `insertOne` recebe um **objeto** e o insere no banco de dados. Caso não tenhamos definido uma variável, é possível construir o objeto diretamente dentro da ação, como a seguir:

```javascript
db.instituicao.insertOne({
    nome: "Universidade Federal de Pernambuco",
    cnpj: 5554447778899,
    sigla: "ufpe",
    endereco: {
        rua: "Av. xxx"
        cidade: "Recife",
        bairro: "Cidade universitária"
    },
    telefones: [
        {
            ddd: "81",
            numero: "21264466"
        },
        {
            ddd: "81",
            numero: "21266644"
        }
    ]
})
```

Muitas das ações do _mongodb_ possuem variações que permitem manipular **vários** objetos ao mesmo tempo. Essas ações possuem a palavra `Many`. Um exemplo é a operação de inserção, que possui a variante `insertMany()`. Caso tivéssemos várias variáveis, cada uma representando uma instituicao diferente, poderíamos executar:
```javascript
db.instituicoes.insertMany( ufpe, ufpb, ufal, ufrpe )
```

### 2.2 Atualizando documentos
Para atualizar documentos, existem várias **ações** disponíveis. Entretanto é necessário **filtrar** primeiro os documentos para que, em seguida, possamos atualizar os valores dos atributos.
#### 2.2.1 Filtros
Um filtro é um objeto que contém parte de um documento que queremos encontrar. É onde definimos **qual (ou quais)** documento(s) queremos atualizar.

Por exemplo ao usar o seguinte filtro:
```javascript
    { sigla: "ufpe" }
```
Conseguimos isolar o documento referente à Universidade Federal de Pernambuco. 

#### 2.2.2 Operadores mais comuns
Uma vez que isolamos o(s) documento(s) que queremos alterar, precisamos informar qual **operação** queremos fazer. As mais comuns são:
 - **$set**: Define o valor de um atributo;
 - **$unset**: Remove um campo;
 - **$inc**: Incrementa um campo (soma + 1 ao seu valor anterior);
 - **$push**: Adiciona um valor à um **_array_**;
 - **$pull**: Remove um valor de um **_array_**;
 - **$addToSet**: Adiciona um valor à um **_array_** caso esse valor ainda não esteja presente.

## Parte 3: Exemplos
Os exemplos estão separados por categoria
[Atualizações](./atualizacoes.md)