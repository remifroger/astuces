# csvkit exemples
Exemples d'usage de la librairie csvkit

#### Comment utiliser csvkit ?
Téléchargez Python, puis installez csvkit,
```
pip install csvkit
```
Plus d'informations : https://csvkit.readthedocs.io/en/latest/tutorial/1_getting_started.html  
Documentation : https://csvkit.readthedocs.io/en/latest/

### Compter le nombre de lignes
```
csvstat C:\data\rpls\source\compil_decret_loi_utf8.csv --count
```

### Interroger un CSV en SQL
```
csvsql --query "select count(*) from 'compil_decret_loi_utf8'" C:\data\rpls\source\compil_decret_loi_utf8.csv
```
