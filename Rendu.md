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

15 -  
