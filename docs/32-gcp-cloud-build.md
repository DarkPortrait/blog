## Enable APIs

* Cloud Build API
* Container Registry API

## Building Containers with DockerFile and Cloud Build

You can write build configuration files to provide instructions to Cloud Build as to which tasks to perform when building a container. These build files can fetch dependencies, run unit tests, analyses and more. In this task, you'll create a DockerFile and use it as a build configuration script with Cloud Build. You will also create a simple shell script (quickstart.sh) which will represent an application inside the container.

On the Google Cloud Console title bar, click Activate Cloud Shell.

When prompted, click Continue.

Cloud Shell opens at the bottom of the Google Cloud Console window.

Create an empty `quickstart.sh`

Add:

```
#!/bin/sh
echo "Hello, world! The time is $(date)."
```

Create an Dockerfile

```
FROM alpine
COPY quickstart.sh /
CMD ["/quickstart.sh"]
```

In Cloud Shell, run the following command to make the quickstart.sh script executable.

```
chmod +x quickstart.sh
```

In Cloud Shell, run the following command to build the Docker container image in Cloud Build.

```
gcloud builds submit --tag gcr.io/${GOOGLE_CLOUD_PROJECT}/quickstart-image .
```


Docker image is built and pushed to Container Registry.

## Building Containers with a build configuration file and Cloud Build

Cloud Build also supports custom build configuration files. In this task you will incorporate an existing Docker container using a custom YAML-formatted build file with Cloud Build.

In Cloud Shell enter the following command to clone the repository to the lab Cloud Shell.

```
git clone https://github.com/GoogleCloudPlatform/training-data-analyst
```

Create a soft link as a shortcut to the working directory.

```
ln -s ~/training-data-analyst/courses/ak8s/v1.1 ~/ak8s
```

Change to the directory that contains the sample files for this lab.

```
cd ~/ak8s/Cloud_Build/a
```

A sample custom cloud build configuration file called cloudbuild.yaml has been provided for you in this directory as well as copies of the Dockerfile and the quickstart.sh script you created in the first task.

View the could build configuration file.

```
cat cloudbuild.yaml
```

```
steps:
- name: 'gcr.io/cloud-builders/docker'
  args: [ 'build', '-t', 'gcr.io/$PROJECT_ID/quickstart-image', '.' ]
images:
- 'gcr.io/$PROJECT_ID/quickstart-image'
```

This file instructs Cloud Build to use Docker to build an image using the Dockerfile specification in the current local directory, tag it with `gcr.io/$PROJECT_ID/quickstart-image` (`$PROJECT_ID` is a substitution variable automatically populated by Cloud Build with the project ID of the associated project) and then push that image to Container Registry.

In Cloud Shell, execute the following command to start a Cloud Build using cloudbuild.yaml as the build configuration file:

```
gcloud builds submit --config cloudbuild.yaml .
```

The build output to Cloud Shell should be the same as before. When the build completes, a new version of the same image is pushed to Container Registry.

## Building and Testing Containers with a build configuration file and Cloud Build

The true power of custom build configuration files is their ability to perform other actions, in parallel or in sequence, in addition to simply building containers: running tests on your newly built containers, pushing them to various destinations, and even deploying them to Kubernetes Engine. In this lab, we will see a simple example: a build configuration file that tests the container it built and reports the result to its calling environment.

In Cloud Shell, change to the directory that contains the sample files for this lab.

```
cd ~/ak8s/Cloud_Build/b
```

Three files are necessary for this step.
`quickstart.sh`

```
#!/bin/sh
if [ -z "$1" ]
then
        echo "Hello, world! The time is $(date)."
        exit 0
else 
        exit 1
fi
```

`cloudbuild.yaml`

```
steps:
- name: 'gcr.io/cloud-builders/docker'
  args: [ 'build', '-t', 'gcr.io/$PROJECT_ID/quickstart-image', '.' ]
- name: 'gcr.io/$PROJECT_ID/quickstart-image'
  args: ['fail']
images:
- 'gcr.io/$PROJECT_ID/quickstart-image
```

`Dockerfile`

```
FROM alpine
COPY quickstart.sh /
CMD ["/quickstart.sh"]
```


In Cloud Shell, execute the following command to start a Cloud Build using cloudbuild.yaml as the build configuration file:

```
gcloud builds submit --config cloudbuild.yaml .
```

Output from the command that ends with `FAILURE`

Confirm that your command shell knows that the build failed

```
echo $?
```
