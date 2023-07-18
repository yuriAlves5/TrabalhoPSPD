# TrabalhoPSPD

Construindo aplicações de larga escala com frameworks de programação paralela/distribuída

## Alunos

|Matrícula | Aluno |
| -- | -- |
| 18/0074741  | Caio Martins |
| 18/0078640 |  Yuri Alves|

## Foco

o foco é a pesquisa em tecnologias que resolvem esses dois requisitos, embutindo-os no
algoritmo “jogo da vida” (vide <https://en.wikipedia.org/wiki/Conway%27s_Game_of_Life>)

## Requisitos

O requisito da performance deve ser resolvido pelo teste de dois tipos de paralelismo: (i) usando o
Apache Hadoop/Spark, e, (ii) usando as bibliotecas e respectivos pragmas OpenMP e MPI, em
conjunto, no código original do jogo da vida. Essas duas opções de paralelismo são denominadas aqui
de engines.

O requisito da elasticidade deve ser resolvido pela adoção do orquestrador kubernetes
(<https://kubernetes.io>), que deve ser estudado e devidamente configurado para uso numa versão
em cluster, preferencialmente.
