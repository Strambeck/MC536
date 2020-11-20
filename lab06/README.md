# Modelo para Apresentação do Lab06 - Cypher e FAERS

Estrutura de pastas:

~~~
├── README.md  <- arquivo apresentando a tarefa
~~~

## Tarefa de Cypher e o FDA Adverse Event Reporting System (FAERS)

## Exercício 1

Escreva uma sentença em Cypher que crie o medicamento de nome `Metamizole`, código no DrugBank `DB04817`.

### Resolução
~~~cypher
CREATE (:Drug {drugbank:"DB04817", name:"Metamizole"})
~~~

## Exercício 2

Considerando que a `Dipyrone` e `Metamizole` são o mesmo medicamento com nomes diferentes, crie uma aresta com o rótulo `:SameAs` que ligue os dois.

### Resolução
~~~cypher
MATCH (Di :Drug {name:"Dipyrone"})
MATCH (Me :Drug {name:"Metamizole"})
CREATE (Di)-[:SameAs]->(Me)
CREATE (Me)-[:SameAs]->(Di)
~~~

## Exercício 3

Use o `DELETE` para excluir o relacionamento que você criou (apenas ele).

### Resolução
~~~cypher
MATCH (:Drug {name:"Dipyrone"})-[a1 :SameAs]->(:Drug {name:"Metamizole"})
MATCH (:Drug {name:"Metamizole"})-[a2 :SameAs]->(:Drug {name:"Dipyrone"})
DELETE a1
DELETE a2
~~~

## Exercício 4

Faça a projeção em relação a Patologia, ou seja, conecte patologias que são tratadas pela mesma droga.

### Resolução
~~~cypher
MATCH (p1:Pathology)<-[a]-(d:Drug)-[b]->(p2:Pathology)
MERGE (p1)<-[t:Treats]->(p2)
ON CREATE SET t.weight=1
ON MATCH SET t.weight=t.weight+1
~~~

## Exercício 5

Construa um grafo ligando os medicamentos aos efeitos colaterais (com pesos associados) a partir dos registros das pessoas, ou seja, se uma pessoa usa um medicamento e ela teve um efeito colateral, o medicamento deve ser ligado ao efeito colateral.

### Resolução
~~~cypher

LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/santanche/lab2learn/master/data/faers-2017/drug-use.csv' AS line
CREATE (:drugUse {drugCode:line.codedrug, codePathology:line.codepathology,idPerson:line.idperson})
LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/santanche/lab2learn/master/data/faers-2017/sideeffect.csv' AS line
CREATE (:sideEffect {idPerson:line.idPerson, codePathology:line.` codePathology`})

MATCH (d:Drug)
MATCH (p:Pathology)
MATCH (u:drugUse)
MATCH (s:sideEffect)
WHERE d.code=u.drugCode AND u.idPerson=s.idPerson AND s.codePathology=p.code
MERGE (d)-[h:hasSideEffect]->(p)
ON CREATE SET h.weight=1
ON MATCH SET h.weight=h.weight+1

MATCH (d:Drug)-[h:hasSideEffect]->(p:Pathology)
RETURN d,p
~~~

## Exercício 6

Que tipo de análise interessante pode ser feita com esse grafo?

Proponha um tipo de análise e escreva uma sentença em Cypher que realize a análise.

Drogas que tiveram menos pessoas com efeitos colaterais

### Resolução
~~~cypher
MATCH (d:Drug)-[h:hasSideEffect]->(p:Pathology)
RETURN d, count(h) as Qnt
ORDER BY Qnt asc
LIMIT 10
~~~