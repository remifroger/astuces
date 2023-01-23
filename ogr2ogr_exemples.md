# ogr2ogr exemples
Exemples d'usage de la librairie ogr2ogr

#### Comment utiliser ogr2ogr ?
Téléchargez QGIS (ici version 3.10), puis rendez-vous dans votre terminal (cmd),
```
cd C:\Program Files\QGIS 3.10\bin
```
Puis vérifiez qu'ogr2ogr tourne bien,
```
ogr2ogr --version
```

Documentation : https://gdal.org/programs/ogr2ogr.html

### Import d'un SHP vers PostgreSQL/PostGIS
Ici, import de C:\data\foncier\geo\parcelle_cadastrale.shp dans geo.parcelle_cadastrale
```
ogr2ogr -f "PostgreSQL" --config PG_USE_COPY YES PG:"host=localhost port=5432 dbname=foncier user=postgres password=postgres" -nlt "MULTIPOLYGON" "C:\data\foncier\geo\parcelle_cadastrale.shp" -nln geo.parcelle_cadastrale
```

### Import d'un CSV vers PostgreSQL/PostGIS
```
ogr2ogr -f "PostgreSQL" --config PG_USE_COPY YES PG:"host=localhost port=5432 dbname=foncier user=postgres password=postgres" "C:\data\file.csv" -nln geo.table_import
```

### Export de PostgreSQL/PostGIS vers SHP
Ici, export de la table spde.spde_copy_septembre (personnalisation possible avec -sql) vers D:\data\spde\2022\spde_copy_septembre.shp
```
ogr2ogr -f "ESRI Shapefile" D:\data\spde\2022\spde_copy_septembre.shp PG:"host=localhost port=5432 dbname=work user=postgres password=postgres" -sql "SELECT * FROM spde.spde_copy_septembre"
```

### Copie d'une table d'une BDD PostgreSQL vers une autre BDD PostgreSQL
Voir : https://www.postgresonline.com/journal/index.php?/archives/31-GDALOGR2OGR-for-Data-Loading.html  
Ici, copie de ff_d75_2021_test.dependance_75_2021 de la BDD foncier vers la BDD test dans le schéma froger
```
ogr2ogr -f "PostgreSQL" PG:"host=localhost port=5432 dbname=test user=postgres password=postgres" PG:"host=localhost port=5432 dbname=foncier user=postgres password=postgres" -lco OVERWRITE=yes -lco schema=froger ff_d75_2021_test.dependance_75_2021
```

### Copie d'une table avec géométrie Esri (sde) d'une BDD PostgreSQL vers une autre BDD PostgreSQL avec conversion en géométrie PostGIS
Ici, copie de diffusion.parcelle_cadastrale de la BDD Apur (production) vers la BDD work dans la table test.parcelle_cadastrale_copy
```
ogr2ogr -f "PostgreSQL" --config PG_USE_COPY YES PG:"host=localhost port=5432 dbname=work user=postgres password=postgres" PG:"host=zpostgresig port=5432 dbname=apur user=froger password=xxx" -lco OVERWRITE=yes -lco schema=test -sql "select st_astext(shape) as geom, * from diffusion.parcelle_cadastrale" -geomfield geom -nln parcelle_cadastrale_copy
```

Puis, sur PostGIS, dans la BDD work :
```sql
update test.parcelle_cadastrale_copy
set geom = st_multi(st_geomfromtext(geom))::geometry(multipolygon, 900914)

alter table test.parcelle_cadastrale_copy
alter column geom type geometry(multipolygon, 900914) using geom::geometry(multipolygon, 900914)
```

### Import d'une GDB (Esri) vers PostgreSQL/PostGIS
Ici, import de chaque couche de la GDB C:\data\foncier\pu\scripts\travail\pu_traitement.gdb vers des tables PostgreSQL (une ligne par couche)
```
ogr2ogr -f "PostgreSQL" PG:"host=localhost port=5432 dbname=foncier user=postgres password=postgres" "C:\data\foncier\pu\scripts\travail\pu_traitement.gdb" "emprise_batie_pu" -nlt PROMOTE_TO_MULTI -nln ff_2020.emprise_batie_pu
ogr2ogr -f "PostgreSQL" PG:"host=localhost port=5432 dbname=foncier user=postgres password=postgres" "C:\data\foncier\pu\scripts\travail\pu_traitement.gdb" "parcelle_cadastrale" -nlt PROMOTE_TO_MULTI -nln ff_2020.parcelle_cadastrale
ogr2ogr -f "PostgreSQL" PG:"host=localhost port=5432 dbname=foncier user=postgres password=postgres" "C:\data\foncier\pu\scripts\travail\pu_traitement.gdb" "parcelle_urbaine" -nlt PROMOTE_TO_MULTI -nln ff_2020.parcelle_urbaine
ogr2ogr -f "PostgreSQL" PG:"host=localhost port=5432 dbname=foncier user=postgres password=postgres" "C:\data\foncier\pu\scripts\travail\pu_traitement.gdb" "pc_point_pu" -nlt PROMOTE_TO_MULTI -nln ff_2020.pc_point_pu
ogr2ogr -f "PostgreSQL" PG:"host=localhost port=5432 dbname=foncier user=postgres password=postgres" "C:\data\foncier\pu\scripts\travail\pu_traitement.gdb" "pu_point_pc" -nlt PROMOTE_TO_MULTI -nln ff_2020.pu_point_pc
```
