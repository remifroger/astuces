# PostGIS exemples
Astuces pour PostgreSQL

### Transformation de coordonnées Google Maps (EPSG 4326) en géométrie PostGIS Lambert 93 (EPSG 2154)
Ici `table` possède une colonne geom de type `geometry(MULTIPOINT, 2154)`, et nous cherchons à convertir les coordonnées en géométries (bien veiller à faire correspondre correctement X, Y avec latitude, longitude).
```sql
UPDATE table SET geom = st_transform(st_setsrid(st_oint(y::numeric, x::numeric), 4326), 2154))
```
