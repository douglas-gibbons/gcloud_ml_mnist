# Example of using gcloud ML to train MNIST data.


For a more complete picture, go to 
[the proper documentation](https://cloud.google.com/ml/docs/quickstarts/training). Code originaly from 
[here](https://github.com/GoogleCloudPlatform/cloudml-samples/tree/master/mnist/trainable)


## To run the code locally:


```
gcloud beta ml local train \
  --package-path=trainer \
  --module-name=trainer.task
```

## To run the code on GCP

Assuming you have a [Google cloud platform account](https://cloud.google.com/free-trial/),
and you've [set up the SDK](https://cloud.google.com/sdk/downloads):

```
gcloud auth login
gcloud config set projectd <your project ID>
gcloud beta ml init-project
```

Create a bucket and set the name:

```
TRAIN_BUCKET=gs://<your bucket ID>
```


Settings:

```
JOB_NAME=mnist_${USER}_$(date +%Y%m%d_%H%M%S)
PROJECT_ID=`gcloud config list project --format "value(core.project)"`
TRAIN_PATH=${TRAIN_BUCKET}/${JOB_NAME}
```


Run the job:
```
gcloud beta ml jobs submit training ${JOB_NAME} \
  --package-path=trainer \
  --module-name=trainer.task \
  --staging-bucket="${TRAIN_BUCKET}" \
  --region=us-central1 \
  -- \
  --train_dir="${TRAIN_PATH}/train"
```

Check the job:

```
watch gcloud beta ml jobs describe --project ${PROJECT_ID} ${JOB_NAME}
```

Check the output:

```
gsutil ls ${TRAIN_PATH}/train
```

..and check the logs:

```
gcloud beta logging read --project ${PROJECT_ID} --format=json \
  "labels.\"ml.googleapis.com/task_name\"=\"master-replica-0\" AND \
   labels.\"ml.googleapis.com/job_id\"=\"${JOB_NAME}\""
```


