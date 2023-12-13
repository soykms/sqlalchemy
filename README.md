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

sqlalchemy-test
```
from sqlalchemy import create_engine

db_uri = "postgresql+pyodbc://<Your Username>:<Your Password>@<Your PostgreSQL Server>/<Your Database Name>"
engine = create_engine(db_uri)

# 데이터베이스와의 세션 생성
from sqlalchemy.orm import sessionmaker
Session = sessionmaker(bind=engine)
session = Session()

# 쿼리 실행
results = session.execute("SELECT * FROM your_table")
for row in results:
    print(row)

# 세션 종료
session.close()

```
