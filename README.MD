Python sample application for Cloud Run.

### Setup
PROJECT_NAME=$(gcloud config list --format="value(core.project)")
gcloud iam service-accounts create urlsigner --display-name="GCS URL Signer" --project=${PROJECT_NAME}
gcloud iam service-accounts keys  create service_account.json --iam-account=urlsigner@${PROJECT_NAME}.iam.gserviceaccount.com
gsutil mb gs://$PROJECT_NAME-urlsigner
gsutil iam ch  serviceAccount:urlsigner@${PROJECT_NAME}.iam.gserviceaccount.com:roles/storage.admin gs://$PROJECT_NAME-urlsigner
gsutil cors set cors.txt gs://$PROJECT_NAME-urlsigner
BUCKET_NAME=$PROJECT_NAME-urlsigner

### Build
docker build -t gcr.io/$PROJECT_NAME/uploader . && docker push gcr.io/$PROJECT_NAME/uploader:latest

### Deploy
gcloud run deploy uploader --image gcr.io/$PROJECT_NAME/uploader:latest --platform managed --region asia-northeast1