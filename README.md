# Data migration script
echo "
import boto3
import psycopg2

def migrate_data(dynamo_table, postgres_table):
    dynamo = boto3.resource('dynamodb')
    table = dynamo.Table(dynamo_table)
    
    conn = psycopg2.connect(
        dbname='yourdbname',
        user='youruser',
        password='yourpassword',
        host='yourhost'
    )
    cursor = conn.cursor()
    
    response = table.scan()
    data = response['Items']
    
    for item in data:
        cursor.execute('INSERT INTO {} (column1, column2) VALUES (%s, %s)'.format(postgres_table),
                       (item['field1'], item['field2']))
    
    conn.commit()
    cursor.close()
    conn.close()

if __name__ == '__main__':
    migrate_data('DynamoTableName', 'PostgresTableName')
" > scripts/data_migration.py

# Data transformation script
echo "
import pandas as pd

def transform_data(data):
    df = pd.DataFrame(data)
    df['new_column'] = df['existing_column'].apply(lambda x: x * 2)
    return df

if __name__ == '__main__':
    data = [{'existing_column': 1}, {'existing_column': 2}]
    transformed_data = transform_data(data)
    print(transformed_data)
" > scripts/data_transformation.py
