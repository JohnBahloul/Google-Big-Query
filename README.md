# Google Big Query - Batch Load CSV Files

The following code demonstrates how to load csv files into a Google Cloud SQL database


## Intake


```python
# Dependencies
import pandas as pd
import glob
import os

pd.set_option('display.max_rows', 50000)
pd.set_option('display.max_columns', 50000)

path = os.getcwd()

files_path = path+"/byteboard/"

# obtain all files in 'data_files' folder
csv_files = glob.glob(os.path.join(files_path, "*.csv"))

# loop over the list of csv files
df_list = []

for i in csv_files:
    # read the csv file
    df = pd.read_csv(i)
    df_list.append(df)
    print(f'{i} has been processed')
```

    /Users/johnbahloul/Desktop/Data_Projects/Pilot_Tech_Assessment/byteboard/widgets_tested.csv has been processed
    /Users/johnbahloul/Desktop/Data_Projects/Pilot_Tech_Assessment/byteboard/widgets_created.csv has been processed


## Batch Load CSV Files


```python
from google.cloud import bigquery
from google.oauth2 import service_account


key_path = "/Users/johnbahloul/Desktop/Data_Projects/Pilot_Tech_Assessment/service_account.json"

credentials = service_account.Credentials.from_service_account_file(
    key_path, scopes=["https://www.googleapis.com/auth/cloud-platform"],
)

client = bigquery.Client(credentials=credentials, project=credentials.project_id,)

Set table_id to the ID of the table to create.
table_id = "pilot-338503.test.orders"

job_config = bigquery.LoadJobConfig(
    source_format=bigquery.SourceFormat.CSV, skip_leading_rows=1, autodetect=True,
)

file_path = 'path to file'

with open(file_path, "rb") as source_file:
    job = client.load_table_from_file(source_file, table_id, job_config=job_config)

job.result()  # Waits for the job to complete.

table = client.get_table(table_id)  # Make an API request.
print(
    "Loaded {} rows and {} columns to {}".format(
        table.num_rows, len(table.schema), table_id
    )
)
```

    Loaded 1996 rows and 3 columns to pilot-338503.test.orders

