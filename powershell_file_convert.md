# PowerShell : conversion de fichiers
Exemples d'usage de PowerShell pour manipuler, transformer, convertir des fichiers.

#### Comment utiliser PowerShell ?
Tapez PowerShell dans votre barre de recherche Windows, puis copiez et collez les scripts à votre guise.

### Convertir un XLSX en CSV
Dans le cas de gros fichiers ne pouvant être manipulés avec Notepad++ ou Excel, PowerShell est une bonne alternative.
```powershell
Function ExcelToCsv ($File) {
    $myDir = "D:\Excel"
    $excelFile = "$myDir\" + $File + ".xlsx"
    $Excel = New-Object -ComObject Excel.Application
    $wb = $Excel.Workbooks.Open($excelFile)
	
    foreach ($ws in $wb.Worksheets) {
        $ws.SaveAs("$myDir\" + $File + ".csv", 6)
    }
    $Excel.Quit()
}

$FileName = "list_of_names"
ExcelToCsv -File $FileName
```

Source : https://excel.officetuts.net/examples/convert-excel-file-xlsx-to-csv-in-powershell/

### Convertir un CSV en CSV encodé UTF-8
```powershell
Get-Content C:\data\rpls\source\compil_decret_loi.csv | Set-Content -Encoding utf8 compil_decret_loi_utf8.csv
```

### Import/export d'un CSV en UTF-8
```powershell
powershell -Command "Import-Csv -Delimiter ';' -Path 'C:\data\rpls\source\2022\rpls2022_decret_reg11.csv' | Export-Csv -NoTypeInformation -Delimiter ';' -Encoding UTF8 -Path 'C:\data\rpls\source\2022\rpls2022_decret_reg11_utf8.csv'"
```
