Step 1: Create the core GCP resources. 
    0. Create a project... remember the name. You will need it in step e
    a. PubSub Topic named: Inbound
    b. Big Query Dataset named: Insights
    c. A Table in the Insights dataset named: Messages
        Note: The schema for the Messages table is in the repository here: bq_table_schema.json
    d. Create a storage bucket for your test harness config file 
        i. Copy the test_stream_config.json file to that storage bucket
    e. Create the streaming test harness with the command. 
        gcloud beta dataflow flex-template run stream-fakes-4 --template-file-gcs-location gs://dataflow-templates-us-east1/latest/flex/Streaming_Data_Generator --region us-east1 --parameters schemaLocation=gs://<storage_bucket_from_step_d>/test_stream_config.json,topic=projects/<project_name>/topics/Inbound,qps=300
    f. Create the dataflow job to flow data from pub/sub to BQ
        gcloud dataflow sql query 'SELECT * FROM pubsub.topic.bandwidthstream.Inbound' --job-name streamFromPubSubToBQ --region us-east1 --bigquery-write-disposition write-empty --bigquery-project <project_name> --bigquery-dataset Insights --bigquery-table Messages
