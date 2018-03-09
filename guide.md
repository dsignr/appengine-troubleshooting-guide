### Troubleshooting AppEngine

Enable manual scaling:
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
