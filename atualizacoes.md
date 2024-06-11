# Atualizações
[Voltar](./README.md)  

## Atualizações em instituicoes
### Usando $set
Exemplo: Atualizar o nome da instituição

```javascript
db.instituicoes.updateOne(
  { "sigla": "ufrpe" },
  { $set: { "nome": "Universidade Federal Rural de Pernambuco - Atualizada" } }
)
```

### Usando $unset
Exemplo: Remover o campo cnpj da instituição

```javascript
db.instituicoes.updateOne(
  { "sigla": "ufrpe" },
  { $unset: { "cnpj": "" } }
)
```

### Usando $inc
Exemplo: Incrementar o período de todos os estudantes em 1

```javascript
db.instituicoes.updateMany(
  { "sigla": "ufrpe" },
  { $inc: { "estudantes.$[].periodo": 1 } }
)
```

### Usando $push
Exemplo: Adicionar um novo telefone à instituição

```javascript
db.instituicoes.updateOne(
  { "sigla": "ufrpe" },
  { $push: { "telefones": "81988888888" } }
)
```

### Usando $pull
Exemplo: Remover um estudante pelo CPF

```javascript
db.instituicoes.updateOne(
  { "sigla": "ufrpe" },
  { $pull: { "estudantes": { "cpf": "051.058.059-51" } } }
)
```

### Usando $addToSet
Exemplo: Adicionar um curso à lista de cursos oferecidos pela instituição, se ainda não estiver presente

```javascript
db.instituicoes.updateOne(
  { "sigla": "ufrpe" },
  { $addToSet: { "cursos_oferecidos": "Biologia" } }
)
```

## Atualizações em servicos
### Usando $set
Exemplo: Atualizar o nome do responsável pelo serviço

```javascript
db.servicos.updateOne(
  { "sigla": "onco" },
  { $set: { "responsavel.nome": "Dr. Maria da Silva" } }
)
```

### Usando $unset
Exemplo: Remover o campo capacidade do serviço

```javascript
db.servicos.updateOne(
  { "sigla": "onco" },
  { $unset: { "capacidade": "" } }
)
```

### Usando $inc
Exemplo: Incrementar a capacidade do serviço em 5

```javascript
db.servicos.updateOne(
  { "sigla": "onco" },
  { $inc: { "capacidade": 5 } }
)
```

### Usando $push
Exemplo: Adicionar uma nova vaga para um estudante

```javascript
db.servicos.updateOne(
  { "sigla": "onco" },
  {
    $push: {
      "vagas": {
        "estudante_id": ObjectId("666744298b35e600228fc44a"),
        "inicio": new Date("2024-01-01T00:00:00.000Z"),
        "fim": new Date("2024-06-30T00:00:00.000Z")
      }
    }
  }
)
```

Usando $pull
Exemplo: Remover uma vaga específica pelo ID do estudante

```javascript
db.servicos.updateOne(
  { "sigla": "onco" },
  { $pull: { "vagas": { "estudante_id": ObjectId("666741026107f4095f542723") } } }
)
```

