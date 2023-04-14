# PostgreSQL exemples
Astuces pour PostgreSQL

### Pivoter une colonne en lignes sur un délimiteur "saut de ligne"
Cas d'usage : un fichier Excel contient des cellules num_voie (voie) et adresse avec plusieurs adresses séparées par un saut de ligne. L'objectif est d'éclater les cellules contenant plusieurs adresses en lignes. Voici comment opérer avec l'aide de PostgreSQL.  
La première étape est d'enregistrer ce fichier Excel en CSV puis de l'importer sur PostgreSQL avec ogr2ogr : voir la rubrique [exemples ogr2ogr](https://github.com/remifroger/astuces/blob/main/ogr2ogr_exemples.md). Normalement, l'import conserve bien le saut de ligne dans les cellules.  
Ensuite, sur PostgreSQL, nous allons créer une nouvelle table *table_pivot* en utilisant la fonction regexp_split_to_table() qui nous permet d'éclater les colonnes souhaitées sur un caractère spécifique, ici le saut de ligne ('\n').
```sql
CREATE TABLE table_pivot 
AS SELECT
id, regexp_split_to_table(num_voie, E'\\n+') AS num_voie, regexp_split_to_table(adresse, E'\\n+') AS adresse, code_postal, commune
FROM table
```
Le tour est joué.  
 #### Formatage de l'adresse
Ici nous avons des adresses formatées de cette façon "Argenteuil (rue d')". Nous souhaitons obtenir "rue d'Argenteuil". Voici la requête :
```sql
SELECT num_voie || ' ' || substring(adresse, '\((.+)\)') || ' ' || split_part(adresse, '(',1)  as adresse_ok, * 
FROM table_pivot
```
### Fonction PL/pgSQL : retourner les années disponibles selon un pas de temps

```sql
CREATE FUNCTION check_year_exist(liste integer[], substract integer) 
RETURNS int[] AS $$
DECLARE
  r int[];
  liste_ordered int[];
  max_val int;
  x int;
BEGIN
  execute format('SELECT max(a) from unnest(''%1$s''::int[]) a', liste) into max_val;
  execute format('WITH temp_ AS (SELECT unnest(''%1$s''::int[]) AS x ORDER BY x DESC) SELECT array_agg(x)::int[] FROM temp_', liste) into liste_ordered;
  raise notice '%', liste;
  raise notice '%', liste_ordered;
  r := array_append(r, max_val);
  FOREACH x IN ARRAY liste_ordered
    loop
      raise notice 'x : %', x;
      raise notice 'max : %', max_val;
      if x <= max_val - substract then
        r := array_append(r, x);
        max_val := x;
        raise notice '---- Ok : %', max_val;
      end if;
    END LOOP;
  RETURN r;
END;
$$ LANGUAGE plpgsql;
```

#### Usage

```sql
select * from check_year_exist('{1968, 1975, 2006, 2007, 2008, 2009, 2010, 2011, 2012, 2013, 2014, 2015, 2016, 2017, 2018, 2019}', 5)
```
Résultat
```
{2019,2014,2009,1975,1968}
```
