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

