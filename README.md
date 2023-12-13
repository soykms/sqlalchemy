# sqlalchemy
pyodbc-sql server

```
conn = pyodbc.connect(
    Trusted_Connection="Yes",
     DRIVER='{ODBC Driver 17 for SQL Server}',
     SERVER="DESKTOP-CK9O8A7\SQLEXPRESS",
     DATABASE="hr_hipo"
     )

cursor = conn.cursor()

import pandas as pd
query = "SELECT * FROM hr_hipo" 
df = pd.read_sql(query, conn)
df
```

sqlalchemy-sql server
```


import sqlalchemy

server="LT477007\SQLEXPRESS"
database="AdventureWorks2019"
driver="ODBC Driver 17 for SQL Server"

conn_str=f'mssql+pyodbc://{server}/{database}?driver={driver}'

engine = create_engine(conn_str)
query = "SELECT * FROM [HumanResources].[Department]" 
df = pd.read_sql(query, engine)
df
```
