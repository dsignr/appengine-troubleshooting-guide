### Troubleshooting AppEngine

1. Enable manual scaling:
```
manual_scaling:
  instances: 1
```
If you enabled autoscaling by mistake:
1. Disable application under settings.
2. Then, add this to your `app.yaml` and deploy an instance
```
manual_scaling:
  instances: 1
```
```
gcloud app deploy
```

2. You get this error:
```
ERROR: (gcloud.app.deploy) Error Response: [4] Timed out waiting for the app infrastructure to become healthy.
```
If you get this error, deploy a new service by using `service: abc` in your `app.yaml`.


3. You get this error:
```
ERROR: (gcloud.app.deploy) Permissions error fetching application [apps/xxx-yyy-zzz]. Please make sure you are using the correct project ID and that you have permission to view applications on the project.
```
If you get this error, then head over to the IAM page and set the necessary permissions for the service account that's throwing this error (Eg. Cloud Build).
