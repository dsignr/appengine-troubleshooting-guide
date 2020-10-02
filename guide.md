## Troubleshooting AppEngine

### 1. Enable manual scaling:
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

### 2. You get this error:
```
ERROR: (gcloud.app.deploy) Error Response: [4] Timed out waiting for the app infrastructure to become healthy.
```
If you get this error, deploy a new service by using `service: abc` in your `app.yaml`.


### 3. You get this error:
```
ERROR: (gcloud.app.deploy) Permissions error fetching application [apps/xxx-yyy-zzz]. Please make sure you are using the correct project ID and that you have permission to view applications on the project.
```
If you get this error, then head over to the IAM page and set the necessary permissions for the service account that's throwing this error (Eg. Cloud Build).

### 4. You get this error:
```
ERROR: context deadline exceeded appengine
```
This happens because the cloud build is failing after waiting for `x` number of seconds. AppEngine works by setting up a cloud build step under the hood. Once you understand this, the solution becomes pretty simple. In your `cloudbuild.yaml` file, set the `timeout` to whatever it takes. Or, if you don't use cloudbuild, you can pass this before you run `gcloud app deploy` on your machine:

`gcloud config set app/cloud_build_timeout [NUMBER OF SECONDS]`

### 5. Elixir Runtime - Your assets aren't working or throwing up a 404 from within the application.

For example, a file `index.html` inside `priv/static` isn't being picked up from inside the controller to be rendered (Eg. usecase: SPA). 

This happens because AppEngine uses some complicated internal methodology to organize assets that shows up to the nginx server, but doesn't work from within the Elixir application. This is descibed in https://github.com/GoogleCloudPlatform/elixir-runtime at the time of this edit.

The solution is two-fold.

First, you must stop using distillery. Comment this section out in your `app.yml`:
```
# runtime_config:
#   release_app: abc
```
Then, you must tell AppEngine to directly use your application.
```
entrypoint: mix phx.server
```
That's it.

### 6. You get this error:
```
(gcloud.app.deploy) Permissions error fetching application
```
Go to CloudBuild settings page and enable AppEngine from there.

### 7. Internal server error:
```
Internal server error
```
If you get an internal server error during deployment (and not after), it means something is misconfigured inside your project.
Go to the API Lenny in the console, search for appengine disable and enable again the API. It should fix the problem.
If not, try the same process but delete the appengine flex service account before service-<project number>@gae-api-prod.google.com.iam.gserviceaccount.com
If it still doesn't work, delete also the default service in appengine component.

### 8. Can't connect to CloudSQL:
Make sure in your application (Eg. Phoenix), you use socket DIR to talk to the database.
Inside your `app.yaml`:
env_variables:
  DB_USERNAME: <username>
  DB_PASSWORD: <password>
  DB_NAME: <db name>
  DB_SOCKET: /cloudsql/<instance name>:<region>:<db name>
Note - Recent AppEngine versions seem to prefix the `/.s.PSQL.5432` to your socket URL, so you don't need to add it yourself.
Next, make sure CloudSQL Admin API is enabled and your AppEngine service accounts (2 of them - except the standard environment one) have CloudSQL Admin role.
