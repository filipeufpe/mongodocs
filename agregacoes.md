# mongodocs
Documentação do Projeto NoSQL da Disciplina de Banco de Dados do Mestrado do CIn-UFPE

## 1. Responsáveis por Serviços
### Items do Checklist:
- $lookup
- $unwind
- $project

```javascript
[
  {
    $lookup: {
      from: "servidores",
      localField: "responsavel",
      foreignField: "_id",
      as: "responsavel_info"
    }
  },
  {
    $unwind: "$responsavel_info"
  },
  {
    $project: {
      _id: 0,
      nomeServico: "$nome",
      nomeResponsavel: "$responsavel_info.nome"
    }
  }
]
```

## 2. Período Mínimo, Máximo e Médio
### Itens do Checklist
- $group
- $min
- $max
- $avg

```javascript
[
  {
    $group: {
      _id: null,
      minPeriodo: {
        $min: "$periodo"
      },
      maxPeriodo: {
        $max: "$periodo"
      },
      avgPeriodo: {
        $avg: "$periodo"
      }
    }
  }
]
```

## 3. Alunos dentro e fora dos 5 anos de curso
### Itens do Checklist
- $group
- $cond
- $lte
- count
- $sum
```javascript
[
  {
    $group: {
      _id: {
        $cond: [
          {
            $lte: ["$periodo", 10]
          },
          "Periodo <= 10",
          "Periodo > 10"
        ]
      },
      count: {
        $sum: 1
      }
    }
  }
]
```

## 4. Média de Período por Serviço
### Itens do Checklist
- $unwind
- $lookup
- $group
- $avg
- $sort

```javascript
[
  {
    $unwind: "$vagas"
  },
  {
    $lookup: {
      from: "estudantes",
      localField: "vagas.estudante_id",
      foreignField: "_id",
      as: "estudante_info"
    }
  },
  {
    $unwind: "$estudante_info"
  },
  {
    $group: {
      _id: "$nome",
      mediaPeriodo: {
        $avg: "$estudante_info.periodo"
      }
    }
  },
  {
    $sort: {
      mediaPeriodo: -1
    }
  }
]
```
## 5. Contagem de Vagas ativas na data corrente por Serviço
### Itens do Checkpoint
- $unwind
- $match
- $lt
- $gt
- $group
- $first
- $sum
- $sort
- 

```javascript
[
  {
    $unwind: "$vagas"
  },
  {
    $match: {
      "vagas.inicio": {
        $lt: new Date()
      },
      "vagas.fim": {
        $gt: new Date()
      }
    }
  },
  {
    $group: {
      _id: "$_id",
      nome: {
        $first: "$nome"
      },
      count: {
        $sum: 1
      }
    }
  },
  {
    $sort: {
      count: 1
    }
  }
]
```