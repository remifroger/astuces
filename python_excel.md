# Python et Excel
Exemples de manipulation de fichiers Excel ou CSV avec Python

#### Comment utiliser Python ?
Téléchargez la dernière version de Python, puis vérifiez que tout fonctionne,
```
python --version
```

### Nombre de lignes de la première feuille d'un fichier Excel (Python 3.x)
```
pip install openpyxl
```
```python
import openpyxl as xl
wb = xl.load_workbook(r"C:\data\rpls\test\loi_decret_21.xlsx", enumerate)
# sheet = wb.get_sheet_by_name('sheet') 
sheet = wb.worksheets[0]
row_count = sheet.max_row
column_count = sheet.max_column
print(row_count)
```
