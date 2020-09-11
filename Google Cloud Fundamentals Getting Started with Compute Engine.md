# Google Cloud Fundamentals: Getting Started with Compute Engine


## Objectives
1. Create a Compute Engine virtual machine using the Google Cloud Platform (GCP) Console.

1. Create a Compute Engine virtual machine using the gcloud command-line interface.

1. Connect between the two instances.

## Steps
1. Sign in to cloud.google.com or via Qwiklabs.
2. In the GCP console open Cloud Shell from the top right.
3. Choose a zone in which o place your VM. In this case, choose *us-central1-a*

```
gcloud compute instances create my-vm-1 --zone=us-central1-a --machine-type=e2-medium --image=debian-9-stretch-v20200910 --subnet=default
```

In order to allow HTTP traffic
```
gcloud compute firewall-rules create default-allow-http --direction=INGRESS --priority=1000 --network=default --action=ALLOW --rules=tcp:80 --source-ranges=0.0.0.0/0 --target-tags=http-server
```
Check VM creation
```
gcloud compute instances list
```

## Create another VM using gcloud command line
1. Set the default zone in this case us-central1-b
```
gcloud config set compute/zone us-central1-b
```
2. Create an instance  **my-vm-2**
```
gcloud compute instances create my-vm-2 --machine-type n1-standard-1 --image-project debian-cloud --image debian-9-stretch-v20190213 --subnet default
```
To check if VM has been created
 ```
gcloud compute instances list
```
## Connect between the VM instances
 1. SSH into my-vm-2
```
ssh my-vm-2
```
2. From my-vm-2 command line ping my-vm-1
```
ping -c 4 my-vm-1
```
3.SSH into my-vm-1. If you are prompted about whether you want to continue connecting to a host with unknown authenticity, enter yes to confirm that you do.
4. Install Nginx web server on **my-vm-1**
```
sudo apt-get install nginx-light -y
```
5. Add a custom message to the home page using nano.
```
sudo nano /var/www/html/index.nginx-debian.html
```
6. Use arrow keys to navigate to just under the `h1` header and place your custom message.
```
Hi from Kudzai
```
7. Press **Ctrl+O** followed by **Enter** to save your file. Then press **Ctrl+X** to exit nano text editor.
8. Confirm the web server is serving the edited page, execute at command line
```
curl http://localhost/
```
i.e This is because we are still in **my-vm-1**
9. Type `exit` on **my-vm-1** command line back to **my-vm-2**

10. To confirm that my-vm-2 can reach the web server on my-vm-1, at the command prompt on my-vm-2, execute this command
```
curl http://my-vm-1/
```
The response will again be the HTML source of the web server's home page, including your line of custom text.