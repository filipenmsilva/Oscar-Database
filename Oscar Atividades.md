# Nomeados ao Oscar

Contém a base de indicados ao Oscar em formato SQL para treinar comandos CRUD. 

Abaixo, algumas atividades para trabalharmos.

--- 

* Atualize os registros da tabela com os dados do Oscar 2025

---

* Qual o **total** de registros na tabela indicados?

R:11013
Q:
db.filmes.countDocuments({})

---

* Qual o número de indicações por categoria agrupadas por categoria?

R: 2

Q:
```js
db.indicados_ao_oscar.aggregate([
  {
    $group: {
      _id: "$categoria",  
      total_indicacoes: { $sum: 1 }  
    }
  },
  {
    $sort: { total_indicacoes: -1 } 
  }
]);
```

---

* Quantas vezes Natalie Portman foi indicada ao Oscar?

R: 3 vezes

Q:
```js
 db.oscar.find({"Nome": "Natalie Portman"}).countDocuments()
```

---

* Quantos Oscars Natalie Portman ganhou?

R: 1 vez

Q:

db["filmes"].countDocuments({nome_do_indicado: "Natalie Portman", vencedor: "true"})


---

* Quantas vezes Viola Davis foi indicada ao Oscar?
R: 4 vezes
Q:

db["filmes"].countDocuments({nome_do_indicado: "Viola Davis"})

---

* Quantos Oscars Viola Davis ganhou?

R: 1 vez
Q:

db["filmes"].countDocuments({nome_do_indicado: "Viola Davis", vencedor: "true"})


---

* Amy Adams já ganhou algum Oscar?
R: Não
Q:

db["filmes"].countDocuments({nome_do_indicado: "Amy Adams", vencedor: "true"})

---

* Quais os atores/atrizes que foram indicados mais de uma vez?

R:1439

Q: 

db.filmes.aggregate([
  { $group: { 
      _id: "$nome_do_indicado", 
      total: { $sum: 1 } 
  }},
  { $match: { total: { $gt: 1 } }},
  { $count: "qtd_repetidos" }
])


---

* A série de filmes Toy Story ganhou Oscars em quais anos?
R: Nenhum

Q:


db["filmes"].find({nome_do_filme: "Toy Story", vencedor:"true"},
                  {ano_cerimonia: 1 }).pretty();

---

* A partir de que ano que a categoria "Actress" deixa de existir? 

R: 1976
Q:

db.filmes.find({ categoria: "ACTRESS" }, {_id:0, ano_cerimonia:1}).sort({ ano_cerimonia: -1 }).limit(1)

---

* Quem ganhou o primeiro Oscar para Melhor Atriz? Em que ano?

R:Louise Dresser

Q:

db.filmes.find({categoria: "ACTRESS"},{_id:0, nome_do_indicado:1}).sort({ano_cerimonia: 1}).limit(1)


---

* No campo "Vencedor", altere todos os valores com "true" para 1 e todos os valores "false" para 0.

Q:


db.filmes.updateMany({vencedor: "true"}, {$set:{
  vencedor: 1
}});



---

* Em qual edição do Oscar "Crash" concorreu ao Oscar?

R:Edição 78

Q:

db.filmes.find({nome_do_filme: "Crash"},{cerimonia: 1, _id: 0}).pretty();

---

* O filme Central do Brasil aparece no Oscar?

R: Não
Q:db.filmes.find({nome_do_filme: "Central do Brasil"})


---

* Inclua no banco 3 filmes que nunca foram nem nomeados ao Oscar, mas que merecem ser. 

Q:

db.filmes.insertMany([
  {
    id_registro: 11014,
    ano_filmagem: 2024,
    ano_cerimonia: 2025,
    cerimonia: 97,
    categoria: "SHOULD HAVE BEEN NOMINATED",
    nome_do_indicado: "Sonic the Hedgehog 3",
    nome_do_filme: "Sonic the Hedgehog 3",
    vencedor: 0
  },
  {
    id_registro: 11015,
    ano_filmagem: 2007,
    ano_cerimonia: 2008,
    cerimonia: 80,
    categoria: "SHOULD HAVE BEEN NOMINATED",
    nome_do_indicado: "Tropa de Elite",
    nome_do_filme: "Tropa de Elite",
    vencedor: 0
  },
  {
    id_registro: 11016,
    ano_filmagem: 1999,
    ano_cerimonia: 2000,
    cerimonia: 72,
    categoria: "SHOULD HAVE BEEN NOMINATED",
    nome_do_indicado: "Fight Club",
    nome_do_filme: "Fight Club",
    vencedor: 0
  }
])

---

* Denzel Washington já ganhou algum Oscar?

R:sim

Q: 

db.filmes.find({nome_do_indicado: "Denzel Washington"})

---

* Quais os filmes que ganharam o Oscar de Melhor Filme?

R: Foram 97 filmes, acho que listar todos aqui é difícil. (OBS: a categoria mudou de nome algumas vezes)

Q:

db.filmes.find({
  $or:[
    {categoria:/OUTSTANDING/}, {categoria: "BEST PICTURE"}, {categoria: "BEST MOTION PICTURE"}],
  vencedor:1}) //(OBS: Atualizei para todos serem considerados "BEST PICTURE" para fazer a penúltima, mas mesmo assim não estou conseguindo)


---

* Sidney Poitier foi o primeiro ator negro a ser indicado ao Oscar. Em que ano ele foi indicado? Por qual filme?

R: 1959

Q:

db.filmes.find({nome_do_indicado:"Sidney Poitier"},{ano_cerimonia:1, _id:0}).pretty();


---

* Quais os filmes que ganharam o Oscar de Melhor Filme e Melhor Diretor na mesma cerimonia?

Q:


---

* Denzel Washington e Jamie Foxx já concorreram ao Oscar no mesmo ano?
R: Não

Q:

db.filmes.aggregate([{
    $match: {
      nome_do_indicado: { $in: ["Denzel Washington", "Jamie Foxx"] }}
  },{
    $group: {
      _id: "$ano_cerimonia",
      indicados: { $addToSet: "$nome_do_indicado" }}
  },{
    $match: {
      indicados: { $all: ["Denzel Washington", "Jamie Foxx"] }}
  },{
    $project: {_id: 0,ano: "$_id",indicados: 1}
  },{
    $sort: { ano: 1 }
  }
]);
