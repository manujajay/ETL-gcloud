# Complete ETL Workflow with Python and Google Cloud

This README includes detailed instructions and full Python scripts for setting up and executing an ETL workflow using Python and Google Cloud.

## Setup Instructions

1. **Install Required Python Libraries**

First, ensure that you have the necessary Python packages by creating a `requirements.txt` file and installing the packages listed:

```plaintext
# requirements.txt
google-cloud-bigquery==2.34.0
google-cloud-storage==2.1.0
pandas==1.3.4
requests==2.26.0
```

Install the packages using pip:

```bash
pip install -r requirements.txt
```

2. **Python Scripts**

### Extract Script (`extract.py`)

Fetch data from a source, such as an API:

```python
# extract.py
import requests

def fetch_data(url):
    response = requests.get(url)
    data = response.json()
    return data
```

### Transform Script (`transform.py`)

Transform data using pandas:

```python
# transform.py
import pandas as pd

def transform_data(data):
    df = pd.DataFrame(data)
    df.columns = [col.lower() for col in df.columns]  # Example transformation
    return df
```

### Load Script (`load.py`)

Upload data to Google Cloud BigQuery or Google Cloud Storage:

```python
# load.py
from google.cloud import bigquery
from google.cloud import storage
import pandas as pd

def load_to_bigquery(data_frame, project_id, dataset_id, table_id):
    client = bigquery.Client(project=project_id)
    table_ref = client.dataset(dataset_id).table(table_id)
    job = client.load_table_from_dataframe(data_frame, table_ref)
    job.result()

def load_to_gcs(data_frame, bucket_name, blob_name):
    client = storage.Client()
    bucket = client.bucket(bucket_name)
    blob = bucket.blob(blob_name)
    blob.upload_from_string(data_frame.to_csv(), 'text/csv')
```

### Main Script (`main.py`)

Orchestrate the entire ETL process:

```python
# main.py
from extract import fetch_data
from transform import transform_data
from load import load_to_bigquery, load_to_gcs

def main():
    url = 'https://api.example.com/data'
    project_id = 'your-project-id'
    dataset_id = 'your-dataset-id'
    table_id = 'your-table-id'
    bucket_name = 'your-bucket-name'
    blob_name = 'your-blob-name'
    
    data = fetch_data(url)
    transformed_data = transform_data(data)
    # Choose where to load the data
    load_to_bigquery(transformed_data, project_id, dataset_id, table_id)
    load_to_gcs(transformed_data, bucket_name, blob_name)

if __name__ == '__main__':
    main()
```

3. **Running the ETL Process**

Execute the process by running the main script:

```bash
python main.py
```

4. **Google Cloud Setup**

Ensure you have enabled APIs for BigQuery and Cloud Storage, created a dataset, a storage bucket, and set up authentication using a service account.

## Conclusion

This comprehensive guide and scripts provide a complete setup for an ETL workflow on Google Cloud. Customize the scripts as necessary to fit your data sources and transformation requirements.
