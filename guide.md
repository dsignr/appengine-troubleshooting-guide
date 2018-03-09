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
