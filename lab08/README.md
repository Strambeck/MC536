# Lab08

Estrutura de pastas:

```
└── README.md  <- arquivo apresentando a tarefa
```

## Tarefas com Publicações

## Questão 1

Construa uma comando SELECT que retorne dados equivalentes a este XPath

~~~xpath
//individuo[idade>20]/@nome
~~~

### Resolução

~~~sql
SELECT nome FROM Fichario 
    WHERE idade > 20
~~~

## Questão 2

Qual a outra maneira de escrever esta query sem o where?

~~~xquery
let $fichariodoc := doc('https://raw.githubusercontent.com/santanche/lab2learn/master/xml/fichario.xml')
 
for $i in ($fichariodoc//individuo)
where $i[idade>17]
return {data($i/@nome)}
~~~

### Resolução

~~~xquery
let $fichariodoc := doc('https://raw.githubusercontent.com/santanche/lab2learn/master/xml/fichario.xml')
for $i in ($fichariodoc//individuo[idade>17])
return {data($i/@nome)}
~~~

## Questão 3

Escreva uma consulta SQL equivalente ao XQuery:

~~~xquery
let $fichariodoc := doc('https://raw.githubusercontent.com/santanche/lab2learn/master/xml/fichario.xml')

for $i in ($fichariodoc//individuo)
where $i[idade>17]
return {data($i/@nome)}
~~~

### Resolução

~~~sql
SELECT nome FROM Fichario 
    WHERE idade > 17
~~~

## Questão 4

Retorne quantas publicações são posteriores ao ano de 2011.

### Resolução

~~~xquery

let $info := doc("https://raw.githubusercontent.com/santanche/lab2learn/master/data/publications/publications.xml")
return count($info/publications/publication[year>2011])
~~~

## Questão 5

Retorne a categoria cujo <label> em inglês seja 'e-Science Domain'.

### Resolução

~~~xquery
let $info := doc("https://raw.githubusercontent.com/santanche/lab2learn/master/data/publications/publications.xml")
return $info/publications/categories/category[label/@lang="en-US" and label="e-Science Domain"]
~~~

## Questão 6

Retorne as publicações associadas à categoria cujo <label> em inglês seja 'e-Science Domain'. A associação entre o label e a key da categoria deve ser feita na consulta.

### Resolução

~~~xquery
let $info := doc("https://raw.githubusercontent.com/santanche/lab2learn/master/data/publications/publications.xml")

for $pub in $info/publications/publication,
    $cat in $info/publications/categories/category[label/@lang="en-US" and label="e-Science Domain"]
where $pub/key = $cat/@key

return $pub
~~~

## Tarefas com DRON e PubChem

## Questão 1

Liste o nome de todas as classificações que estão apenas dois níveis imediatamente abaixo da raiz.

~~~xquery
let $info := doc('https://raw.githubusercontent.com/santanche/lab2learn/master/data/faers-2017-dron/dron.xml')

for $second in ($info/drug/drug/drug)
return {data($second/@name), '&#xa;'}
~~~

## Questão 2

Apresente todas as classificações de um componente a sua escolha (diferente de Acetylsalicylic Acid) que estejam hierarquicamente dois níveis acima. Note que no exemplo dado em sala foi considerado um nível hierárquico acima.

~~~xquery
let $info := doc("https://raw.githubusercontent.com/santanche/lab2learn/master/data/faers-2017-dron/dron.xml")

for $i in $info//drug[drug/drug/@name="VINBLASTINE"]
    let $cmp := $i/@name
group by $cmp
order by $cmp
return {data($cmp), '&#xa;'}
~~~

## Questão 3

### Questão 3.1

Liste todos os códigos ChEBI dos componentes disponíveis.

~~~xquery
let $info := doc("https://raw.githubusercontent.com/santanche/lab2learn/master/data/faers-2017-dron/dron.xml")

for $i in $info//drug[substring(@id,1,36) = "http://purl.obolibrary.org/obo/CHEBI"]
    let $cmp := substring($i/@id,38)
group by $cmp
order by $cmp
return {$cmp, '&#xa;'}
~~~

### Questão 3.2

Liste todos os códigos ChEBI e primeiro nome (sinônimo) de cada um dos componentes disponíveis.

~~~xquery
let $info := doc("https://raw.githubusercontent.com/santanche/lab2learn/master/data/faers-2017-dron/dron.xml")

for $i in $info//drug[substring(@id,1,36)="http://purl.obolibrary.org/obo/CHEBI"]
    let $cmp_id := substring($i/@id,38)
    let $cmp_name := $i/@name
group by $cmp_id, $cmp_name
order by $cmp_id, $cmp_name
return {$cmp_id, $cmp_name ,'&#xa;'}

~~~

### Questão 3.3

Para cada código ChEBI, liste os sinônimos e o nome que aparece para o mesmo componente no DRON (se existir). Para isso faça um JOIN com o DRON.

~~~xquery
let $info := doc("https://raw.githubusercontent.com/santanche/lab2learn/master/data/faers-2017-dron/dron.xml")

for $i in $info//drug[substring(@id,1,36) = "http://purl.obolibrary.org/obo/CHEBI"],
    $j in $info//drug[substring(@id,1,35) = "http://purl.obolibrary.org/obo/DRON"]
where $i/@name = $j/@name
    let $cmp_id := substring($i/@id,38)
    let $cmp_name := $i/@name
group by $cmp_id, $cmp_name
order by $cmp_id, $cmp_name
return {$cmp_id, $cmp_name ,'&#xa;'}
~~~
