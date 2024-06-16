# Agregações
[Voltar](README.md)  
Documentação do Projeto NoSQL da Disciplina de Banco de Dados do Mestrado do CIn-UFPE

## 1. Total de Estudantes por Instituição
### Itens do Checklist Utilizados:
- $project
```javascript
db.instituicoes.aggregate([
  {
    $project: {
      nome: 1,
      totalEstudantes: { $size: "$estudantes" }
    }
  }
]);

```

Explicação Passo a Passo

1. Pipeline de Agregação

A função `aggregate` recebe um _array_ de estágios, onde cada estágio representa uma operação a ser realizada na coleção. Aqui temos um único estágio `$project`.

2. Estágio $project

O estágio `$project` é usado para incluir, excluir ou adicionar campos no resultado final. Neste caso, estamos configurando dois campos:
- nome: 1: Inclui o campo nome no resultado, mantendo o nome da instituição.
- totalEstudantes: Usamos a expressão `$size` para contar o número de elementos no _array_ estudantes de cada documento.

3. Expressão $size

A expressão `$size` é usada para contar o número de elementos em um _array_. No contexto da nossa consulta, `$size`: "$estudantes" conta o número de estudantes no _array_ estudantes para cada documento da coleção instituicoes.

## 2. Mínimo, Máximo e Média de Período por Instituição

```javascript
db.instituicoes.aggregate([
  { $unwind: "$estudantes" },
  {
    $group: {
      _id: "$nome",
      periodoMinimo: { $min: "$estudantes.periodo" },
      periodoMaximo: { $max: "$estudantes.periodo" },
      periodoMedio: { $avg: "$estudantes.periodo" }
    }
  }
])
```

Explicação Passo a Passo
1. Pipeline de Agregação

A função aggregate recebe um _array_ de estágios, onde cada estágio representa uma operação a ser realizada na coleção. Neste caso, temos dois estágios: `$unwind` e `$group`.

2. Estágio $unwind

O estágio `$unwind` deconstrói um _array_ embutido em documentos e cria um documento para cada elemento do _array_. No contexto da nossa consulta, `$unwind`: "$estudantes" transforma cada documento da coleção instituicoes em múltiplos documentos, um para cada estudante no _array_ estudantes.

3. Estágio $group

O estágio $group agrupa documentos pelo valor de um campo especificado `_id` e pode calcular valores agregados para cada grupo.

- _id: "$nome": Agrupa os documentos pelo nome da instituição.
- periodoMinimo: { $min: "$estudantes.periodo" }: Calcula o período mínimo para cada grupo (instituição).
- periodoMaximo: { $max: "$estudantes.periodo" }: Calcula o período máximo para cada grupo (instituição).
- periodoMedio: { $avg: "$estudantes.periodo" }: Calcula a média dos períodos para cada grupo (instituição).

## 3. Alunos Dentro e Fora do Período Normal de Conclusão
```javascript
db.instituicoes.aggregate([
  { $unwind: "$estudantes" },
  {
    $group: {
      _id: "$nome",
      estudantesMenorIgual10: {
        $push: {
          $cond: [
            { $lte: ["$estudantes.periodo", 10] },
            "$estudantes",
            null
          ]
        }
      },
      estudantesMaior10: {
        $push: {
          $cond: [
            { $gt: ["$estudantes.periodo", 10] },
            "$estudantes",
            null
          ]
        }
      }
    }
  },
  {
    $project: {
      estudantesMenorIgual10: {
        $filter: {
          input: "$estudantesMenorIgual10",
          as: "estudante",
          cond: { $ne: ["$$estudante", null] }
        }
      },
      estudantesMaior10: {
        $filter: {
          input: "$estudantesMaior10",
          as: "estudante",
          cond: { $ne: ["$$estudante", null] }
        }
      }
    }
  }
])
```

1. Pipeline de Agregação

A função `aggregate` recebe um _array_ de estágios, onde cada estágio representa uma operação a ser realizada na coleção. Neste caso, temos três estágios: `$unwind`, `$group` e `$project`.

2. Estágio `$unwind`

O estágio `$unwind` deconstrói um _array_ embutido em documentos e cria um documento 
para cada elemento do _array_. No contexto da nossa consulta, `$unwind`: "$estudantes" 
transforma cada documento da coleção instituicoes em múltiplos documentos, um para cada estudante no _array_ estudantes.

3. Estágio `$group`

O estágio `$group` agrupa documentos pelo valor de um campo especificado `_id` e pode calcular valores agregados para cada grupo.

 - _id: "$nome": Agrupa os documentos pelo nome da instituição.
 - estudantesMenorIgual10: Usamos a expressão $push junto com $cond para criar um _array_ de estudantes cujo período é menor ou igual a 10.
    - $push: Adiciona elementos a um _array_.
    - $cond: Avalia uma condição e retorna um valor com base no resultado. Se a condição for verdadeira, o estudante é adicionado ao _array_; caso contrário, null é adicionado.
  - estudantesMaior10: Similar ao estudantesMenorIgual10, mas para estudantes com período maior que 10.

4. Estágio $project

O estágio `$project` é usado para incluir, excluir ou adicionar campos no resultado final. Aqui, ele é usado para filtrar os _arrays_ resultantes e remover os null.

- estudantesMenorIgual10: Usamos $filter para remover os valores null do array.
  - $filter: Filtra elementos de um array com base em uma condição.
  - input: O array a ser filtrado.
  - as: Um identificador para cada elemento do array.
  - cond: A condição para incluir o elemento no array filtrado.
 - estudantesMaior10: Similar ao estudantesMenorIgual10, mas para o array de estudantes com período maior que 10.  

 ## 4. Vagas Ativas no Momento

 ```javascript
 db.servicos.aggregate([
  { $unwind: "$vagas" },
  {
    $match: {
      "vagas.inicio": { $lte: new Date() },
      "vagas.fim": { $gte: new Date() }
    }
  },
  {
    $group: {
      _id: "$nome",
      vagasAtivas: { $sum: 1 }
    }
  }
])
 ```

1. Pipeline de Agregação

A função aggregate recebe um _array_ de estágios, onde cada estágio representa uma operação a ser realizada na coleção. Neste caso, temos três estágios: `$unwind`, `$match` e `$group`.

2. Estágio `$unwind`

O estágio `$unwind` deconstrói um _array_ embutido em documentos e cria um documento para cada elemento do _array_. No contexto da nossa consulta, $unwind: "$vagas" transforma cada documento da coleção servicos em múltiplos documentos, um para cada vaga no _array_ vagas.

3. Estágio `$match`

O estágio `$match` filtra os documentos da coleção com base em uma condição especificada. Aqui, estamos filtrando as vagas ativas, onde a data atual está entre vaga.inicio e vaga.fim.

4. Estágio `$group`

O estágio `$group` agrupa documentos pelo valor de um campo especificado _id e pode calcular valores agregados para cada grupo.

 - _id: "$nome": Agrupa os documentos pelo nome do serviço.
 - vagasAtivas: { $sum: 1 }: Calcula o total de vagas ativas para cada grupo (serviço).

## 5. Serviços e Vagas com nomes de alunos
```javascript
[
  {
    $unwind: "$vagas"
  },
  {
    $lookup: {
      from: "instituicoes",
      let: {
        estudanteId: "$vagas.estudante_id"
      },
      pipeline: [
        {
          $unwind: "$estudantes"
        },
        {
          $match: {
            $expr: {
              $eq: [
                "$estudantes._id",
                "$$estudanteId"
              ]
            }
          }
        },
        {
          $project: {
            "estudantes.nome": 1,
            _id: 0
          }
        }
      ],
      as: "estudante_info"
    }
  },
  {
    $unwind: "$estudante_info"
  },
  {
    $group: {
      _id: "$_id",
      nome: {
        $first: "$nome"
      },
      vagas: {
        $push: {
          nome_do_estudante:
            "$estudante_info.estudantes.nome",
          inicio: "$vagas.inicio",
          fim: "$vagas.fim"
        }
      }
    }
  },
  {
    $project: {
      _id: 0,
      nome: 1,
      vagas: 1
    }
  }
]

```
Vamos detalhar a funcionalidade dessa consulta MongoDB usando a agregação em várias etapas. A consulta envolve operações como `$unwind`, `$lookup`, `$group` e `$project` para transformar e combinar documentos de duas coleções: uma coleção principal e uma coleção relacionada chamada "instituicoes".

### 1. Desmontagem de Arrays com `$unwind`

```javascript
{
  $unwind: "$vagas"
}
```

Esta etapa desmonta o array `vagas` para que cada documento contendo um array `vagas` se torne vários documentos, cada um contendo um único elemento desse array. Se um documento tiver três vagas, ele será transformado em três documentos, cada um com um único elemento `vagas`.

### 2. Juntando com Outra Coleção usando `$lookup`

```javascript
{
  $lookup: {
    from: "instituicoes",
    let: {
      estudanteId: "$vagas.estudante_id"
    },
    pipeline: [
      {
        $unwind: "$estudantes"
      },
      {
        $match: {
          $expr: {
            $eq: [
              "$estudantes._id",
              "$$estudanteId"
            ]
          }
        }
      },
      {
        $project: {
          "estudantes.nome": 1,
          _id: 0
        }
      }
    ],
    as: "estudante_info"
  }
}
```

Nesta etapa, a operação `$lookup` realiza uma junção com a coleção "instituicoes". Aqui está como funciona:

- **`from: "instituicoes"`**: Indica a coleção para realizar a junção.
- **`let: { estudanteId: "$vagas.estudante_id" }`**: Define uma variável `estudanteId` que será usada no pipeline da junção.
- **`pipeline`**: Este é um pipeline de agregação aplicado à coleção "instituicoes".
  - **`$unwind: "$estudantes"`**: Desmonta o array `estudantes` na coleção "instituicoes".
  - **`$match: { $expr: { $eq: [ "$estudantes._id", "$$estudanteId" ] } }`**: Filtra documentos onde `estudantes._id` corresponde ao `estudanteId` definido anteriormente.
  - **`$project: { "estudantes.nome": 1, _id: 0 }`**: Projeta apenas o campo `nome` do estudante.

- **`as: "estudante_info"`**: Define o nome do array de resultados da junção.

### 3. Desmontagem do Resultado da Junção com `$unwind`

```javascript
{
  $unwind: "$estudante_info"
}
```

Esta etapa desmonta o array `estudante_info` para que cada documento que tenha um array `estudante_info` se torne vários documentos, cada um contendo um único elemento desse array.

### 4. Agrupamento dos Documentos com `$group`

```javascript
{
  $group: {
    _id: "$_id",
    nome: {
      $first: "$nome"
    },
    vagas: {
      $push: {
        nome_do_estudante: "$estudante_info.estudantes.nome",
        inicio: "$vagas.inicio",
        fim: "$vagas.fim"
      }
    }
  }
}
```

Aqui, os documentos são agrupados de volta pelo campo `_id` original. As seguintes operações são realizadas no agrupamento:

- **`_id: "$_id"`**: Define o campo `_id` para agrupar os documentos.
- **`nome: { $first: "$nome" }`**: Mantém o primeiro valor do campo `nome` em cada grupo.
- **`vagas: { $push: { ... } }`**: Cria um array `vagas` contendo objetos com `nome_do_estudante`, `inicio` e `fim`.

### 5. Projeção dos Campos com `$project`

```javascript
{
  $project: {
    _id: 0,
    nome: 1,
    vagas: 1
  }
}
```

Finalmente, esta etapa projeta os campos desejados:

- **`_id: 0`**: Exclui o campo `_id` dos resultados finais.
- **`nome: 1`**: Inclui o campo `nome`.
- **`vagas: 1`**: Inclui o campo `vagas`.

### Resumo da Consulta

A consulta completa realiza as seguintes operações:

1. Desmonta o array `vagas`.
2. Realiza uma junção com a coleção "instituicoes" para obter informações sobre os estudantes relacionados a cada vaga.
3. Desmonta o array resultante da junção para lidar com cada estudante individualmente.
4. Agrupa de volta os documentos originais, coletando as informações necessárias.
5. Projeta apenas os campos desejados no resultado final.

O resultado final é uma lista de documentos contendo o `nome` e um array `vagas` com informações detalhadas sobre cada vaga, incluindo o nome do estudante, a data de início e a data de fim.