# Inserções
[Voltar](README.md)  
## Inserindo Instituições
### Exemplo de Inserção

Vamos inserir a seguinte instituição:

  - Nome: Universidade Federal Rural de Alagoas
  - Endereço: Av. Lourival Melo Mota, S/n, Tabuleiro dos Martins, Maceió, Alagoas, CEP: 50670-270
  - Telefones: ["82999999999"]
  - CNPJ: "24.162.204/0001-02"
  - Sigla: "ufal"
### Comando de inserção
```javascript
    db.instituicoes.insertOne({
      "nome": "Universidade Federal Rural de Pernambuco",
      "endereco": {
        "rua": "Rua Dom Manoel de Medeiros",
        "numero": "521",
        "bairro": "Dois Irmãos",
        "cidade": "Recife",
        "estado": "Pernambuco",
        "cep": "52171-900"
        },
      "telefones": ["81999999999"],
      "cnpj": "24.162.204/0001-06",
      "sigla": "ufrpe",
      "estudantes": []
})
```

## Inserindo Estudantes
### Exemplo de Inserção

Vamos inserir dois estudantes na instituição "Universidade Federal de Alagoas":

- **Maicon do Brega**
- CPF: "051.058.059-51"
- Período: 5
- Curso: Fisioterapia  

- **Augusto Cesar**
- CPF: "077.078.079-71"
- Período: 6
- Curso: Educação Física

### Comando de Inserção

```javascript
db.instituicoes.updateOne(
  { "sigla": "ufrpe" },
  {
    $push: {
      "estudantes": {
        "_id": new ObjectId(),
        "nome": "Maicon do Brega",
        "cpf": "051.058.059-51",
        "periodo": 5,
        "curso": "Fisioterapia"
      }
    }
  }
)

db.instituicoes.updateOne(
  { "sigla": "ufrpe" },
  {
    $push: {
      "estudantes": {
        "_id": new ObjectId(),
        "nome": "Augusto Cesar",
        "cpf": "077.078.079-71",
        "periodo": 6,
        "curso": "Educação Física"
      }
    }
  }
)
```
## Inserindo Serviços
### Exemplo de Inserção

Vamos inserir o seguinte serviço:

- Nome: Oncologia
- Sigla: onco
- Responsável: Dr. José da Silva, CRM: 123456
- Capacidade: 10

### Comando de Inserção

```javascript
db.servicos.insertOne({
  "nome": "Oncologia",
  "sigla": "onco",
  "responsavel": {
    "nome": "Dr. José da Silva",
    "crm": "123456"
  },
  "capacidade": 10,
  "vagas": []
});
```

## Inserindo Vagas em Serviços
### Exemplo de Inserção

Vamos inserir duas vagas de estudantes no serviço "Oncologia":

- Estudante com ID 666741026107f4095f542723
- Início: 1º de junho de 2023
- Fim: 31 de dezembro de 2023

- Estudante com ID 666741eb994a98c9c7a9b95f
- Início: 1º de junho de 2024
- Fim: 31 de dezembro de 2024

### Comando de Inserção

```javascript
db.servicos.updateOne(
  { "sigla": "onco" },
  {
    $push: {
      "vagas": {
        "estudante_id": ObjectId("666741026107f4095f542723"),
        "inicio": new Date("2023-06-01T00:00:00.000Z"),
        "fim": new Date("2023-12-31T00:00:00.000Z")
      }
    }
  }
)

db.servicos.updateOne(
  { "sigla": "onco" },
  {
    $push: {
      "vagas": {
        "estudante_id": ObjectId("666741eb994a98c9c7a9b95f"),
        "inicio": new Date("2024-06-01T00:00:00.000Z"),
        "fim": new Date("2024-12-31T00:00:00.000Z")
      }
    }
  }
)
```
