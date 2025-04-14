Pour les résultats de requêtes longs, j'ai limité l'affichage à 5 lignes.

1. SELECT * FROM publications;

 art_id                   | booktitle | journal_editor | journal_isbn | journal_series | journal_volume | pages_end | pages_start | title                                                                                              | type    | url                                         | year
--------------------------+-----------+----------------+--------------+----------------+----------------+-----------+-------------+----------------------------------------------------------------------------------------------------+---------+---------------------------------------------+------
 conf/icassp/ArsikereLA13 |    ICASSP |           null |         null |           null |           null |      7926 |        7922 | Non-linear frequency warping for VTLN using subglottal resonances and the third formant frequency. | Article | db/conf/icassp/icassp2013.html#ArsikereLA13 | 2013
     conf/icassp/KangQM13 |    ICASSP |           null |         null |           null |           null |      8016 |        8012 |                                       Multi-distribution deep belief network for speech synthesis. | Article |     db/conf/icassp/icassp2013.html#KangQM13 | 2013
     books/daglib/0024011 |      null |           null |         null |           null |           null |      null |        null |                                                                Neural Networks and Micromechanics. |    Book |                                        null | 2010
       books/sp/ZhaoSLB14 |      null |           null |         null |           null |           null |       189 |           1 |                                                                              Cloud Data Management |    Book |                                        null | 2014
    books/sp/Aggarwal2013 |      null |           null |         null |           null |           null |      null |        null |                                                                                   Outlier Analysis |    Book | http://dx.doi.org/10.1007/978-1-4614-6396-2 | 2013

(...)

2. cqlsh:esgi_cassandra> SELECT title FROM publications;

 title
----------------------------------------------------------------------------------------------------
 Non-linear frequency warping for VTLN using subglottal resonances and the third formant frequency.
                                       Multi-distribution deep belief network for speech synthesis.
                                                                Neural Networks and Micromechanics.
                                                                              Cloud Data Management
                                                                                   Outlier Analysis

(...)

3 - cqlsh:esgi_cassandra> SELECT title FROM publications WHERE art_id = 'series/sci/2008-156';

 title
-----------------------------------------------------------
 Innovations in Bayesian Networks: Theory and Applications


4 - cqlsh:esgi_cassandra> SELECT COUNT(*) FROM publications WHERE type='Book';

 count
-------
  6178


5 - cqlsh:esgi_cassandra> SELECT COUNT(*) FROM publications WHERE booktitle='HICSS';
InvalidRequest: Error from server: code=2200 [Invalid query] message="Cannot execute this query as it might involve data filtering and thus may have unpredictable performance. If you want to execute this query despite the performance unpredictability, use ALLOW FILTERING"


6 - cqlsh:esgi_cassandra> SELECT COUNT(*) FROM publications WHERE booktitle='HICSS' ALLOW FILTERING;

 count
-------
  1271

7 - cqlsh:esgi_cassandra> CREATE INDEX btree_publi_booktitle ON publications(booktitle);
cqlsh:esgi_cassandra> SELECT COUNT(*) FROM publications WHERE booktitle='HICSS';

 count
-------
  1271

8 - qlsh:esgi_cassandra> SELECT COUNT(*) FROM publications WHERE type='Article' AND booktitle='HICSS' ALLOW FILTERING;

 count
-------
  1271


9 - cqlsh:esgi_cassandra> SELECT COUNT(*) FROM authors WHERE pos=3;

 count
-------
  4462

10 -cqlsh:esgi_cassandra> SELECT COUNT(*) FROM authors WHERE pos>3 ALLOW FILTERING;

 count
-------
  4674




Partie 2

11 - cqlsh:esgi_cassandra> SELECT author, COUNT(*) FROM authors_publis GROUP BY author LIMIT 5;

 author                | count
-----------------------+-------
 Mohammad Noor Hidayat |     1
       Michael Massimi |     5
    Wolfgang Seiringer |     1
    Gregory A. Krudysz |     1
        Romain Laroche |     1


12 - cqlsh:esgi_cassandra> SELECT author, COUNT(*) FROM authors_publis WHERE pos=3 GROUP BY author LIMIT 5 ALLOW FILTERING;

 author                       | count
------------------------------+-------
              Hafizur Rahaman |     1
 Ana Cristina Bicharra Garcia |     1
                 Thad Starner |     1
              Hossein Fotouhi |     1
                Mohamed Wahbi |     1

13 - cqlsh:esgi_cassandra> CREATE TABLE authors_pos (art_id TEXT, author TEXT, pos INT, PRIMARY KEY ((pos), art_id));

cqlsh:esgi_cassandra> CREATE INDEX btree_authors_pos_art_id on authors_pos(art_id);
cqlsh:esgi_cassandra> CREATE INDEX btree_authors_pos_author on authors_pos(author);
cqlsh:esgi_cassandra> COPY authors_pos(art_id,author,pos) FROM 'authors.csv' WITH HEADER = true AND DELIMITER=';';

ELECT pos, COUNT(*) FROM authors_pos GROUP BY pos;

 pos | count
-----+-------
  23 |     6
  33 |     2
   5 |   981
  28 |     4
  42 |     1
  10 |    95
  16 |    22
  13 |    46
  30 |     3
  11 |    74
   1 | 13633
  19 |    10
  46 |     1
  43 |     1
   8 |   183
   0 | 19011
   2 |  8810
  45 |     1
   4 |  2079
  18 |    16
  44 |     1
  15 |    29
  22 |     8
  27 |     4
  20 |     9
   7 |   302
  36 |     1
  40 |     1
  38 |     1
  39 |     1
   6 |   519
  29 |     4
  37 |     1
   9 |   122
  14 |    36
  26 |     5
  21 |     9
  17 |    19
  35 |     2
  31 |     3
  24 |     6
  32 |     2
  41 |     1
  25 |     5
  34 |     2
  12 |    56
   3 |  4462





cqlsh:esgi_cassandra> ALTER TABLE authors_pos WITH GC_GRACE_SECONDS = 0;







14 - cqlsh:esgi_cassandra> SELECT pos FROM authors_publis where author = 'Oscar Castillo';

 pos
-----
   0
   0
   1
   1
   3
   2
   1
   1
   1
   1
   1
   1
   1
   1
   1
   2
   4
   3
   2
   0
   1
   1
   1
   1






Partie 3

15 -  SELECT author FROM authors_publis where title = 'Medical imaging archiving: A comparison between several NoSQL solutions.';

 author
-----------------------
          Carlos Costa
          Louis Beroud
 Luis A. Bastiao Silva
    Jos? Lu?s Oliveira



Il n'est pas possible de faire de jointure entre les tables publications et authors sous CQL.

16 -  SELECT author FROM authors_publis where title = 'Medical imaging archiving: A comparison between several NoSQL solutions.';

 author
-----------------------
          Carlos Costa
          Louis Beroud
 Luis A. Bastiao Silva
    Jos? Lu?s Oliveira




17 - cqlsh:esgi_cassandra> SELECT pos, title FROM authors_publis WHERE author = 'Oscar Castillo' LIMIT 5;

 pos | title
-----+------------------------------------------------------------------------------------------------------------------------------------------
   0 |                                                                                   Type-2 Fuzzy Logic in Intelligent Control Applications
   0 |                                                                                              Type-2 Fuzzy Logic: Theory and Applications
   1 | Hybrid Intelligent Systems for Pattern Recognition Using Soft Computing - An Evolutionary Approach for Neural Networks and Fuzzy Systems
   1 |                    Optimization of Membership Functions for Type-1 and Type 2 Fuzzy Controllers of an Autonomous Mobile Robot Using PSO.
   3 |                                                                                              Type-2 Fuzzy Grammar in Language Evolution.

(5 rows)


18 - Il n'est pas possible d'avoir la liste des auteurs ayant publié avec Oscar Castillo cà partir des tables authors ou authors_publis car il n'est pas possible de faire des jointure sous CQL.


19 - cqlsh:esgi_cassandra> CREATE TYPE journal_type (series TEXT, editor TEXT, volume INT, isbn LIST<TEXT>);

cqlsh:esgi_cassandra> CREATE TABLE publicationsNorm (id TEXT, type TEXT, year INT, title TEXT, authors LIST<TEXT>, pages MAP<TEXT,INT>, booktitle TEXT, journal FROZEN<journal_type>, url TEXT, cites LIST<TEXT>, PRIMARY KEY (id));

20 - INSERT INTO publicationsNorm JSON '{"id":"series/cogtech/BrandhermSNL13", "type":"Article", "year":2013, "title":"A SemProM Use Case: Health Care and Compliance.", "authors":["Boris Brandherm","Michael Schmitz","Robert Ne?elrath","Frank Lehmann"], "pages":{"start":349, "end":361}, "booktitle":"SemProM", "journal":{"series":"", "editor":"", "volume":0, "isbn":[ "" ]}, "url":"db/series/cogtech/364237376.html#BrandhermSNL13", "cites":[ "" ]}';

SOURCE 'DBLP.json';


21 - cqlsh:esgi_cassandra> CREATE INDEX btree_publicationsNorm_title on publicationsNorm(title);


22 - cqlsh:esgi_cassandra> SELECT authors FROM publicationsNorm WHERE title = 'Data Quality';

 authors
-----------------------------------------------------
 ['Richard Y. Wang', 'Mostapha Ziad', 'Yang W. Lee']


23 - cqlsh:esgi_cassandra> SELECT journal.series FROM publicationsNorm WHERE title = 'Data Quality';

 journal.series
------------------------------
 Advances in Database Systems

24 - cqlsh:esgi_cassandra> SELECT pages['end'] FROM publicationsNorm WHERE title = 'Data Quality';

 pages['end']
--------------
          147

25 . Il n'est pas possible d'accéder directement à un élément précis d'une liste avec une requête sur Cassandra. Nous pouvons toutefois extraire la liste des auteurs puis identifier le premier à l'aide d'un script externe.


26 - cqlsh:esgi_cassandra> SELECT title FROM publicationsNorm WHERE authors CONTAINS 'Oscar Castillo' LIMIT 5 ALLOW FILTERING;

 title
---------------------------------------------------------------------------------------------
                             Soft Computing and Fractal Theory for Intelligent Manufacturing
 Hierarchical Genetic Optimization of the Fuzzy Integrator for Navigation of a Mobile Robot.
                      Handling of Synergy into an Algorithm for Project Portfolio Selection.
                                Design of Fuzzy Control Systems with Different PSO Variants.
            Designing Systematic Stable Fuzzy Logic Controllers by Fuzzy Lyapunov Synthesis.

27 - cqlsh:esgi_cassandra> SELECT title, pages['start'] FROM publicationsNorm WHERE pages['end'] = 99 LIMIT 5 ALLOW FILTERING;

 title                                                                                                                  | pages['start']
------------------------------------------------------------------------------------------------------------------------+----------------
                                  Building a Schistosomiasis Process Ontology for an Epidemiological Monitoring System. |             75
                                                                         Paid Search: Modeling Rank Dependent Behavior. |             93
                       Hand-Held Mobile Augmented Reality for Collaborative Problem Solving: A Case Study with Sorting. |             91
 AMCTD: Adaptive Mobility of Courier Nodes in Threshold-Optimized DBR Protocol for Underwater Wireless Sensor Networks. |             93
                                                                            Universal Enterprise Adaptive Object Model. |             89


28 . Il n'est pas possible de filtrer directement sur un champs personnalisé avec un WHERE dans une requête sur Cassandra. Nous pouvons toutefois extraire la liste des série de journal avec leur titre et filtrer sur le champs series afin d'identifier ceux correspondant à 'Advances in Database Systems' à l'aide d'un script externe.
