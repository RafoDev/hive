## Para conectarse al primary node
```shell
sudo ssh -i hive-practice.pem hadoop@52.90.19.17
```

## Para copiar archivos al bucket
```shell
aws s3 cp ./file.txt s3://hive-practice/files/
```
## Para sincronizar carpetas 
```shell
aws s3 sync s3://hive-practice/files/ /files-local
``````

# Consultas básicas

Para mostrar las tablas:
```sql
show tables;
```

Para eliminar una tabla:
```sql
drop table word_counts;
```

# Comandos hive

Para ejecutar un script:
```shell
hive -f script.hql;
```

# WordCount

Consulta completa en wordcounts.hql
```sql
CREATE TABLE docs(line STRING);
LOAD DATA LOCAL INPATH './file.txt' OVERWRITE INTO TABLE docs;
CREATE TABLE word_counts AS
SELECT w.word, count(1) AS count FROM (SELECT explode(split(line, ' ')) AS word FROM docs) w
GROUP BY w.word
ORDER BY w.word;
```
wordcountsmod.hql
```sql
CREATE TABLE docs(line STRING);
LOAD DATA LOCAL INPATH './file.txt' OVERWRITE 
INTO TABLE docs;

CREATE TABLE word_counts_mod AS
SELECT w.word, count(1) AS count FROM (SELECT explode(split(line, '\s')) AS word FROM docs) w
GROUP BY w.word
ORDER BY w.word;
```

La diferencia entre ambas versiones es que la segunda considera todos los espacios (varios espacios, tabulaciones, etc) para dividir la línea. Mientras que la primera solo considera los espacios simples.


# Calcular el ńumero de entradas en el log por cada usuario

queries.log

```
1 9709916105432 c++
2 9709916105432 hive
2 9709916105432 aws
3 9709916105432 aws
1 9709916105432 hive
2 9709916105432 c++
```

logs.hql
```sql
CREATE TABLE logs(user_a STRING, `time` STRING, query STRING) 
ROW FORMAT DELIMITED
FIELDS TERMINATED BY ' ';

LOAD DATA LOCAL INPATH './queries.log' OVERWRITE INTO TABLE logs;

CREATE TABLE result AS
SELECT user_a, count(1) AS log_entries 
FROM logs
GROUP BY user_a
ORDER BY user_a;
```