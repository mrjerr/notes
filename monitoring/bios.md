
## dmidecode
Команда выводит информацио об физических комплектующих сервера
```
># dmidecode |grep -A4 'Base Board Information'
Base Board Information
        Manufacturer: ASUSTeK COMPUTER INC.
        Product Name: P9X79 WS
        Version: Rev 1.xx
        Serial Number: 120801261100265
```

```
># dmidecode |grep -A6 'Processor Information'
Processor Information
        Socket Designation: LGA2011
        Type: Central Processor
        Family: Other
        Manufacturer: Intel            
        ID: D7 06 02 00 FF FB EB BF
        Version: Intel(R) Xeon(R) CPU E5-2630 0 @ 2.30GHz
```
