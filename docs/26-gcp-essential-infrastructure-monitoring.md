# Resource Monitoring

## Objectives

In this lab, you learn how to perform the following tasks:

Explore Cloud Monitoring

Add charts to dashboards

Create alerts with multiple conditions

Create resource groups

Create uptime checks

## Tasks

### Task 1: Create a Cloud Monitoring workspace

Multiple projects can be connected to a workspace.

Access to the monitoring workspace will provide access to metrics of all projects connected to the workspace. As a result, we need to create workspaces to manage access of Dev Ops by project. 

### Task 2: Custom dashboards, Metric explorer
### Task 3: Alerting policies, Notification channels

Its better to alert for symptoms instead of errors.

Email, slack channel, pub/sub, webhooks etc. 

### Task 4: Resource groups

Can make groups of resources based on name, type, labels, etc.

### Task 5: Uptime monitoring

Check uptime at a set interval.

# Error Reporting and Debugging

## Objectives
In this lab, you learn how to perform the following tasks:

Launch a simple Google App Engine application

Introduce an error into the application

Explore Cloud Error Reporting

Use Cloud Debugger to identify the error in the code

Fix the bug and monitor in Cloud Operations

### Task 1: Create an application

#### Get and test the application

In the Cloud Console, launch Cloud Shell by clicking Activate Cloud Shell ( 857dc9d7dd799cb2.png). If prompted, click Continue.

To create a local folder and get the App Engine Hello world application, run the following commands:

```
mkdir appengine-hello
cd appengine-hello
gsutil cp gs://cloud-training/archinfra/gae-hello/* .
```

To run the application using the local development server in Cloud Shell, run the following command:

```
dev_appserver.py $(pwd)
```

In Cloud Shell, click Web Preview > Preview on port 8080 to view the application. You may have to collapse the Navigation menu pane to access the Web Preview icon.

In Cloud Shell, press Ctrl+C to exit the development server.

#### Deploy the application to App Engine

To deploy the application to App Engine, run the following command:
```
gcloud app deploy app.yaml
```

If prompted for a region, enter the number corresponding to a region.

When prompted, type Y to continue.

When the process is done, verify that the application is working by running the following command:
```
gcloud app browse
```

#### Introduce an error to break the application

To examine the main.py file, run the following command:
```
cat main.py
```

To use the sed stream editor to change the import library to the nonexistent webapp22, run the following command:
```
sed -i -e 's/webapp2/webapp22/' main.py
```

To verify the change you made in the main.py file, run the `cat main.py` again.

#### Re-deploy the application to App Engine

To re-deploy the application to App Engine, run the following command:
```
gcloud app deploy app.yaml --quiet
```

```
The --quiet flag disables all interactive prompts when running gcloud commands. If input is required, defaults will be used. In this case, it avoids the need for you to type Y when prompted to continue the deployment.
```

When the process is done, verify that the application is broken by running the following command:
```
gcloud app browse
```
If Cloud Shell does not detect your browser, click the link in the Cloud Shell output to view your app.

If needed, press Ctrl+C to exit development mode.

Leave Cloud Shell open.

### Task 2: Explore Cloud Error Reporting

#### View Error Reporting and trigger additional errors

In the Cloud Console, on the Navigation menu ( 7a91d354499ac9f1.png), click Error Reporting.

You should see an error regarding the failed import of webapp22.

Click Auto reload.

In Cloud Shell, run the following command:
```
gcloud app browse
```

Click the link several times to generate more errors.

#### View details and identify the cause

Click the Error name: ImportError: No module named webapp22.

Now you can see a detailed graph of the errors. The Response Code field shows the explicit error: a 500 Internal Server Error.

For Stack trace sample, click Parsed. This opens the Cloud Debugger, showing the error in the code!
