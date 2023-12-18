# sqlalchemy
pyodbc-sql server

```
conn = pyodbc.connect(
    Trusted_Connection="Yes",
     DRIVER='{ODBC Driver 17 for SQL Server}',
     SERVER="DESKTOP-NA1010\SQLEXPRESS",
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

server="NA1010\SQLEXPRESS"
database="AdventureWorks2019"
driver="ODBC Driver 17 for SQL Server"

conn_str=f'mssql+pyodbc://{server}/{database}?driver={driver}'

engine = create_engine(conn_str)
query = "SELECT * FROM [HumanResources].[Department]" 
df = pd.read_sql(query, engine)
df
```


SQLAlchemy를 사용하여 여러 개의 파일을 읽어와 데이터베이스에 업데이트하고, 데이터프레임에 파일명을 포함한 열을 생성하는 과정을 구현하려면 다음과 같은 단계를 따를 수 있습니다.

파일을 읽어와 데이터베이스에 업데이트:
판다스를 사용하여 파일을 읽어와 데이터프레임으로 저장한 다음, SQLAlchemy를 사용하여 데이터베이스에 업데이트합니다. 파일을 읽을 때 반복문을 사용하여 여러 파일을 처리할 수 있습니다. 예를 들어 CSV 파일을 읽는 경우 다음과 같은 코드를 사용할 수 있습니다:

python
Copy code
import pandas as pd
from sqlalchemy import create_engine

# SQLAlchemy 연결 생성
engine = create_engine('sqlite:///your_database.db')

# 파일 목록 정의
file_list = ['file1.csv', 'file2.csv', 'file3.csv']

for file in file_list:
    # 파일을 데이터프레임으로 읽어오기
    df = pd.read_csv(file)
    
    # 데이터베이스에 데이터 업데이트
    df.to_sql('your_table_name', engine, if_exists='append', index=False)
파일명을 포함한 열 생성:
각 데이터프레임에 파일명을 추가하는 것은 assign 메서드를 사용하여 할 수 있습니다. 파일명을 저장하는 열을 새로운 데이터프레임에 추가하고, 그 열을 원래 데이터프레임에 병합합니다. 예를 들어:

python
Copy code
import pandas as pd

# 파일 목록 정의
file_list = ['file1.csv', 'file2.csv', 'file3.csv']

# 빈 데이터프레임 생성
result_df = pd.DataFrame()

for file in file_list:
    # 파일을 데이터프레임으로 읽어오기
    df = pd.read_csv(file)
    
    # 파일명을 저장하는 열 추가
    df = df.assign(file_name=file)
    
    # 결과 데이터프레임에 병합
    result_df = pd.concat([result_df, df], ignore_index=True)

# 결과 데이터프레임 출력
print(result_df)
위 코드에서 file_name 열은 각 행에 해당 파일의 파일명을 가지게 됩니다. 이 열을 데이터베이스에 업데이트하는 방법은 데이터베이스에 추가할 때 해당 열을 함께 추가하면 됩니다.

User
아 SQL server를 쓰고 있고 이제 데이터베이스에 합쳐진 데이터 프레임을 업로드하는걸 작성해줘
ChatGPT
SQL Server에 데이터프레임을 업로드하려면 pandas의 to_sql 메서드를 사용하면 됩니다. 다음은 데이터프레임을 SQL Server 데이터베이스에 업로드하는 예제 코드입니다. 코드를 실행하기 전에 sqlalchemy와 pandas 패키지를 설치해야 합니다.

python
Copy code
import pandas as pd
from sqlalchemy import create_engine

# SQL Server 연결 문자열 설정
# 아래 연결 문자열 예시에서 <server>, <database>, <username>, <password>를 실제 정보로 대체하세요.
# 'mssql+pyodbc://<username>:<password>@<server>/<database>?driver=ODBC+Driver+17+for+SQL+Server'
connection_string = 'mssql+pyodbc://<username>:<password>@<server>/<database>?driver=ODBC+Driver+17+for+SQL+Server'

# SQLAlchemy 엔진 생성
engine = create_engine(connection_string)

# 데이터프레임 생성 (예시)
data = {'Column1': [1, 2, 3],
        'Column2': ['A', 'B', 'C']}
df = pd.DataFrame(data)

# 데이터프레임을 SQL Server에 업로드
# 'your_table_name'은 데이터를 업로드할 테이블 이름으로 대체하세요.
# if_exists='replace'를 사용하면 테이블이 이미 존재할 경우 덮어쓰기, 'append'를 사용하면 데이터 추가 가능
df.to_sql('your_table_name', engine, if_exists='replace', index=False)
위 코드에서 <server>, <database>, <username>, <password>, 그리고 'your_table_name'을 실제 SQL Server 및 테이블 정보로 대체해야 합니다. 연결 문자열에는 SQL Server의 서버 주소, 데이터베이스 이름, 사용자 이름 및 암호가 포함되어야 합니다.

데이터프레임의 데이터는 'your_table_name' 테이블로 업로드됩니다. if_exists 매개변수를 'replace'로 설정하면 테이블이 이미 존재할 경우 이를 덮어쓸 수 있습니다. 'append'로 설정하면 데이터를 테이블에 추가할 수 있습니다.

User
그럼 이제 데이터셋은 준비가 되었다치고 어떤파일명이 "2023_12"라고 했을때 컬럼명에 2023_12를 가지고 있는 행은 싹 지우고 

다시 새롭게 2023_12파일을 데이터프레임으로  불러와서 db에 불어넣어주는 작업을해줘
ChatGPT
"2023_12"라는 파일명을 가진 컬럼을 가진 행을 데이터프레임에서 삭제한 다음, 해당 파일을 다시 데이터프레임으로 읽어서 SQL Server 데이터베이스에 업로드하는 작업을 수행하려면 다음과 같이 할 수 있습니다:

python
Copy code
import pandas as pd
from sqlalchemy import create_engine

# SQL Server 연결 문자열 설정
# 아래 연결 문자열 예시에서 <server>, <database>, <username>, <password>를 실제 정보로 대체하세요.
# 'mssql+pyodbc://<username>:<password>@<server>/<database>?driver=ODBC+Driver+17+for+SQL+Server'
connection_string = 'mssql+pyodbc://<username>:<password>@<server>/<database>?driver=ODBC+Driver+17+for+SQL+Server'

# SQLAlchemy 엔진 생성
engine = create_engine(connection_string)

# 데이터프레임 생성 (예시)
data = {'Column1': [1, 2, 3],
        'Column2': ['A', 'B', 'C']}
df = pd.DataFrame(data)

# 2023_12 파일명을 가진 컬럼을 가진 행 삭제
if '2023_12' in df.columns:
    df = df.drop('2023_12', axis=1)

# '2023_12.csv' 파일을 데이터프레임으로 읽어오기
new_data = {'Column1': [4, 5, 6],
            'Column2': ['D', 'E', 'F']}
df_2023_12 = pd.DataFrame(new_data)

# 데이터프레임을 SQL Server에 업로드
# 'your_table_name'은 데이터를 업로드할 테이블 이름으로 대체하세요.
# if_exists='replace'를 사용하면 테이블이 이미 존재할 경우 덮어쓰기, 'append'를 사용하면 데이터 추가 가능
df_2023_12.to_sql('your_table_name', engine, if_exists='replace', index=False)
위 코드에서는 먼저 '2023_12'라는 파일명을 가진 컬럼을 가진 행을 데이터프레임에서 삭제하고, 그 다음에 "2023_12.csv" 파일을 새로운 데이터프레임인 df_2023_12로 읽어옵니다. 마지막으로 df_2023_12를 SQL Server 데이터베이스에 업로드합니다. 필요한 경우 테이블 이름과 연결 문자열을 실제 정보로 대체하세요.

User
아니야 데이터프레임에서 삭제하는게 아니라 DB에서 삭제를 해야하니까 명령어가 다르게 나가야할 것같은데
ChatGPT
데이터베이스에서 "2023_12" 파일을 가진 행을 삭제하려면 SQL 쿼리를 사용하여 데이터베이스에서 해당 행을 삭제해야 합니다. 아래의 예제는 데이터베이스에서 해당 행을 삭제한 다음 "2023_12.csv" 파일을 다시 데이터베이스에 업로드하는 방법을 보여줍니다.

python
Copy code
import pandas as pd
from sqlalchemy import create_engine

# SQL Server 연결 문자열 설정
# 아래 연결 문자열 예시에서 <server>, <database>, <username>, <password>를 실제 정보로 대체하세요.
# 'mssql+pyodbc://<username>:<password>@<server>/<database>?driver=ODBC+Driver+17+for+SQL+Server'
connection_string = 'mssql+pyodbc://<username>:<password>@<server>/<database>?driver=ODBC+Driver+17+for+SQL+Server'

# SQLAlchemy 엔진 생성
engine = create_engine(connection_string)

# 데이터베이스에서 "2023_12" 파일을 가진 행 삭제 (예시)
# 'your_table_name'은 데이터를 삭제할 테이블 이름으로 대체하세요.
sql_query = "DELETE FROM your_table_name WHERE file_name = '2023_12'"
with engine.connect() as connection:
    connection.execute(sql_query)

# "2023_12.csv" 파일을 데이터프레임으로 읽어오기
new_data = {'Column1': [4, 5, 6],
            'Column2': ['D', 'E', 'F']}
df_2023_12 = pd.DataFrame(new_data)

# 데이터프레임을 SQL Server에 업로드
# 'your_table_name'은 데이터를 업로드할 테이블 이름으로 대체하세요.
# if_exists='replace'를 사용하면 테이블이 이미 존재할 경우 덮어쓰기, 'append'를 사용하면 데이터 추가 가능
df_2023_12.to_sql('your_table_name', engine, if_exists='replace', index=False)
위 코드에서 SQL 쿼리 DELETE FROM your_table_name WHERE file_name = '2023_12'은 데이터베이스에서 "2023_12.csv" 파일을 가진 행을 삭제합니다. 그런 다음 "2023_12.csv" 파일을 데이터프레임으로 읽어와 SQL Server 데이터베이스에 업로드합니다. 필요한 경우 테이블 이름과 연결 문자열을 실제 정보로 대체하세요.

User
근데 최초로 데이터프레임을 테이블에 집어넣을때 테이블은 SQL로 짜줘야하는거아니야?
ChatGPT
네, 데이터프레임을 최초로 데이터베이스 테이블에 넣을 때, 테이블 스키마(구조)를 먼저 SQL로 정의해야 합니다. 이를 위해서는 SQL 문을 사용하여 데이터베이스 내에 테이블을 생성하거나 이미 존재하는 테이블과 일치하도록 테이블 스키마를 정의해야 합니다.

다음은 SQLAlchemy를 사용하여 테이블을 생성하고 데이터프레임을 해당 테이블에 업로드하는 예제 코드입니다. 코드를 실행하기 전에 <server>, <database>, <username>, <password>, 그리고 테이블 이름을 실제 정보로 대체하세요.

python
Copy code
import pandas as pd
from sqlalchemy import create_engine

# SQL Server 연결 문자열 설정
connection_string = 'mssql+pyodbc://<username>:<password>@<server>/<database>?driver=ODBC+Driver+17+for+SQL+Server'

# SQLAlchemy 엔진 생성
engine = create_engine(connection_string)

# 데이터프레임 생성 (예시)
data = {'Column1': [1, 2, 3],
        'Column2': ['A', 'B', 'C']}
df = pd.DataFrame(data)

# 테이블 스키마 정의 (테이블이 이미 존재하는 경우 생략 가능)
# 'your_table_name'은 테이블 이름으로 대체하세요.
table_schema = """
CREATE TABLE your_table_name (
    Column1 INT,
    Column2 VARCHAR(255)
);
"""

# 테이블 스키마를 데이터베이스에 적용
with engine.connect() as connection:
    connection.execute(table_schema)

# 데이터프레임을 SQL Server에 업로드
# 'your_table_name'은 데이터를 업로드할 테이블 이름으로 대체하세요.
# if_exists='replace'를 사용하면 테이블이 이미 존재할 경우 덮어쓰기, 'append'를 사용하면 데이터 추가 가능
df.to_sql('your_table_name', engine, if_exists='replace', index=False)
위 코드에서는 먼저 테이블 스키마를 SQL로 정의하고, 해당 스키마를 데이터베이스에 적용합니다. 그런 다음 데이터프레임을 해당 테이블에 업로드합니다. 테이블이 이미 존재하는 경우 스키마 정의 부분을 생략하고 데이터프레임을 if_exists='append'로 업로드하여 데이터를 추가할 수 있습니다.




# 파일 목록 정의
file_list = [r'C:\Users\mkim27\Documents\Works\EPM_BI\PMA_WAVE_HELPER\rawdata\LHM\LHM_heatmap\lhm_2023_5678.xlsx', 
             r'C:\Users\mkim27\Documents\Works\EPM_BI\PMA_WAVE_HELPER\rawdata\LHM\LHM_heatmap\lhm_2023_1234.xlsx',
             r'C:\Users\mkim27\Documents\Works\EPM_BI\PMA_WAVE_HELPER\rawdata\LHM\LHM_heatmap\lhm_2022_9101112.xlsx', 
             r'C:\Users\mkim27\Documents\Works\EPM_BI\PMA_WAVE_HELPER\rawdata\LHM\LHM_heatmap\lhm_2022_5678.xlsx', 
             r'C:\Users\mkim27\Documents\Works\EPM_BI\PMA_WAVE_HELPER\rawdata\LHM\LHM_heatmap\lhm_2022_1234.xlsx',
             r'C:\Users\mkim27\Documents\Works\EPM_BI\PMA_WAVE_HELPER\rawdata\LHM\LHM_heatmap\lhm_2021_101112.xlsx', 
             r'C:\Users\mkim27\Documents\Works\EPM_BI\PMA_WAVE_HELPER\rawdata\LHM\LHM_heatmap\lhm_2021_789.xlsx', 
             r'C:\Users\mkim27\Documents\Works\EPM_BI\PMA_WAVE_HELPER\rawdata\LHM\LHM_heatmap\lhm_2021_456.xlsx',
             r'C:\Users\mkim27\Documents\Works\EPM_BI\PMA_WAVE_HELPER\rawdata\LHM\LHM_heatmap\lhm_2021_123.xlsx']


# 빈 데이터프레임 생성
result_df = pd.DataFrame()

for file in file_list:
    df = pd.read_csv(file)
    df = df.assign(file_name=file)
    result_df = pd.concat([result_df, df], ignore_index=True)

result_df



---------------------------------------------------------------------------
UnicodeDecodeError                        Traceback (most recent call last)
~\AppData\Local\Temp\ipykernel_26920\2250919716.py in <module>
     15 
     16 for file in file_list:
---> 17     df = pd.read_csv(file)
     18     df = df.assign(file_name=file)
     19     result_df = pd.concat([result_df, df], ignore_index=True)

c:\Users\mkim27\Anaconda3\lib\site-packages\pandas\util\_decorators.py in wrapper(*args, **kwargs)
    309                     stacklevel=stacklevel,
    310                 )
--> 311             return func(*args, **kwargs)
    312 
    313         return wrapper

c:\Users\mkim27\Anaconda3\lib\site-packages\pandas\io\parsers\readers.py in read_csv(filepath_or_buffer, sep, delimiter, header, names, index_col, usecols, squeeze, prefix, mangle_dupe_cols, dtype, engine, converters, true_values, false_values, skipinitialspace, skiprows, skipfooter, nrows, na_values, keep_default_na, na_filter, verbose, skip_blank_lines, parse_dates, infer_datetime_format, keep_date_col, date_parser, dayfirst, cache_dates, iterator, chunksize, compression, thousands, decimal, lineterminator, quotechar, quoting, doublequote, escapechar, comment, encoding, encoding_errors, dialect, error_bad_lines, warn_bad_lines, on_bad_lines, delim_whitespace, low_memory, memory_map, float_precision, storage_options)
    676     kwds.update(kwds_defaults)
    677 
--> 678     return _read(filepath_or_buffer, kwds)
    679 
    680 

c:\Users\mkim27\Anaconda3\lib\site-packages\pandas\io\parsers\readers.py in _read(filepath_or_buffer, kwds)
    573 
...
c:\Users\mkim27\Anaconda3\lib\site-packages\pandas\_libs\parsers.pyx in pandas._libs.parsers.TextReader._tokenize_rows()

c:\Users\mkim27\Anaconda3\lib\site-packages\pandas\_libs\parsers.pyx in pandas._libs.parsers.raise_parser_error()

UnicodeDecodeError: 'utf-8' codec can't decode byte 0xa2 in position 14: invalid start byte
Output is truncated. View as a scrollable element or open in a text editor. Adjust cell output settings...
