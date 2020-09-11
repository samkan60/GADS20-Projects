# Google Cloud Fundamentals: Getting Started with App Engine

## Objectives

- Initialize App Engine.

- Preview an App Engine application running locally in Cloud Shell.

- Deploy an App Engine application, so that others can reach it.

- Disable an App Engine application, when you no longer want it to be visible.

## Set up your lab environment

Activate Google Cloud Shell. In GCP console, on the top right toolbar, click the Open Cloud Shell button.

## Task 1: Initialize App Engine

1. Initialize App Engine and choose region

```
gcloud app create --project=$DEVSHELL_PROJECT_ID
```

2. Clone source code from Github

```
git clone https://github.com/GoogleCloudPlatform/python-docs-samples
```

3. Navigate to the source directory

```
cd python-docs-samples/appengine/standard_python3/hello_world
```

## Task 2: Run Hello World application locally

1. Update packages and set up a virtual environment in which to run your application.

```
sudo apt-get update && sudo apt-get install virtualenv -y
```

2. Activate the virtual environment

```
source venv/bin/activate
```

3. Install dependencies 
```
pip install  -r requirements.txt
```

5. Run the application and ignore any warnings
```
python main.py
```

6. Press Ctrl+C to stop app from running.

## Task 3: Deploy and run Hello World on App Engine

1. Navigate to the source directory
cd ~/python-docs-```
samples/appengine/standard_python3/hello_world
```

2. Deploy your Hello World application and press **Y** when prompted
```
gcloud app deploy
```

3. Launch your browser to view the app at http://YOUR_PROJECT_ID.appspot.com
```
gcloud app browse
```

Task 4: Disable the application

Since when setting up a project has to be created to stop the application this project must be deleted.

```
 gcloud projects delete
```

The End