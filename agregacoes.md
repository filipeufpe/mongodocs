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
db.servicos.aggregate([
  {
    $unwind: "$vagas"
  },
  {
    $lookup: {
      from: "instituicoes",
      localField: "vagas.estudante_id",
      foreignField: "estudantes._id",
      as: "info_estudante"
    },
  },
  {
    $unwind: "$info_estudante"
  },
  {
    $unwind: "$info_estudante.estudantes"
  },
  {
    $match: {
      $expr: {
        $eq: [
          "$info_estudante.estudantes._id",
          "$vagas.estudante_id"
        ]
      }
    }
  },
  {
    $project: {
      _id: 0,
      "servico": "$nome",
      "instituicao": "$info_estudante.nome",
      "nomeEstudante": "$info_estudante.estudantes.nome",
      "curso": "$info_estudante.estudantes.curso",
      "data_inicio": "$vagas.inicio",
      "data_fim": "$vagas.fim",
    }
  }
])
```
Essa consulta MongoDB usa o método `aggregate` para processar e transformar dados da coleção `servicos`, combinando informações da coleção `instituicoes`. Vamos explicar cada estágio da agregação:

1. **$unwind: "$vagas"**
   - Esta etapa desestrutura o array `vagas` em múltiplos documentos, um para cada elemento do array. Se um documento tem um array `vagas` com 3 elementos, ele será desmembrado em 3 documentos separados, cada um com uma única `vaga`.

2. **$lookup**
   - O operador `$lookup` realiza uma junção com a coleção `instituicoes`.
   - `from: "instituicoes"` especifica a coleção para junção.
   - `localField: "vagas.estudante_id"` usa o campo `estudante_id` em cada elemento `vaga` como a chave local para a junção.
   - `foreignField: "estudantes._id"` especifica o campo na coleção `instituicoes` que será comparado com `localField`.
   - `as: "info_estudante"` armazena o resultado da junção no campo `info_estudante`.

3. **$unwind: "$info_estudante"**
   - Desestrutura o array `info_estudante` criado na etapa anterior, criando múltiplos documentos para cada elemento do array.

4. **$unwind: "$info_estudante.estudantes"**
   - Desestrutura o array `estudantes` dentro de `info_estudante`, criando múltiplos documentos para cada estudante dentro da instituição.

5. **$match**
   - Filtra os documentos para garantir que apenas aqueles em que o `estudante_id` na vaga corresponde ao `_id` do estudante no array `estudantes` da instituição sejam mantidos.
   - `$expr` permite a comparação de campos no mesmo documento.
   - `$eq` verifica se os valores dos dois campos são iguais.

6. **$project**
   - Seleciona e formata os campos desejados para a saída final.
   - `_id: 0` exclui o campo `_id` do resultado.
   - `"servico": "$nome"` mapeia o nome do serviço para o campo `servico`.
   - `"instituicao": "$info_estudante.nome"` mapeia o nome da instituição para o campo `instituicao`.
   - `"nomeEstudante": "$info_estudante.estudantes.nome"` mapeia o nome do estudante para o campo `nomeEstudante`.
   - `"curso": "$info_estudante.estudantes.curso"` mapeia o curso do estudante para o campo `curso`.
   - `"data_inicio": "$vagas.inicio"` mapeia a data de início da vaga para o campo `data_inicio`.
   - `"data_fim": "$vagas.fim"` mapeia a data de fim da vaga para o campo `data_fim`.

Resumindo, esta consulta agrega dados dos serviços e suas vagas, junta esses dados com informações de instituições e estudantes, filtra para garantir que os dados estejam corretamente associados e projeta uma estrutura de saída específica com informações detalhadas sobre os serviços, instituições e estudantes.