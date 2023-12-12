# sqlalchemy

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
