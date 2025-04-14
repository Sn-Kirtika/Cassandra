29. cqlsh:esgi_cassandra> CREATE OR REPLACE FUNCTION avgState ( state tuple<int,bigint>, val int ) CALLED ON NULL INPUT RETURNS tuple<int,bigint> LANGUAGE java AS 'if (val!=null) { state.setInt(0, state.getInt(0)+1); state.setLong(1, state.getLong(1)+val.intValue()); } return state; ';



30. cqlsh:esgi_cassandra> CREATE OR REPLACE FUNCTION avgFinal ( state tuple<int,bigint> ) CALLED ON NULL INPUT RETURNS double LANGUAGE java AS 'double r = 0; if(state.getInt(0) == 0) return null; r = state.getLong(1); r/= state.getInt(0); return Double.valueOf(r);';


31. CREATE AGGREGATE IF NOT EXISTS average ( int ) SFUNC avgState STYPE tuple<int, bigint> FINALFUNC avgFinal INITCOND (0, 0);


32. cqlsh:esgi_cassandra> SELECT average(pos) FROM authors_publis WHERE author = 'Oscar Castillo';

 esgi_cassandra.average(pos)
-----------------------------
                     1.29167


33. cqlsh:esgi_cassandra> SELECT average(pages_end-pages_start) FROM authors_publis WHERE author = 'Oscar Castillo';

 esgi_cassandra.average(pages_end - pages_start)
-------------------------------------------------
                                        51.20833


34. cqlsh:esgi_cassandra> CREATE OR REPLACE FUNCTION countGroupState( state map<int,int>, val int) CALLED ON NULL INPUT RETURNS map<int, int> LANGUAGE java AS 'if (val==null) return state; Integer current = state.get(val); if (current==null) current =0; state.put(val, current +1); return state;';
cqlsh:esgi_cassandra> CREATE OR REPLACE FUNCTION countGroupFinal( state map<int,int>) CALLED ON NULL INPUT RETURNS map<int, int> LANGUAGE java AS 'return state;';
cqlsh:esgi_cassandra> 

cqlsh:esgi_cassandra> CREATE AGGREGATE IF NOT EXISTS countGroup( int ) SFUNC countGroupState STYPE map<int,int> FINALFUNC countGroupFinal INITCOND {};




cqlsh:esgi_cassandra> SELECT countGroup(pos) FROM authors_publis WHERE author = 'Oscar Castillo';

 esgi_cassandra.countgroup(pos)
---------------------------------
 {0: 3, 1: 15, 2: 3, 3: 2, 4: 1}

