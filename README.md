# **GCP final project frontend**
> ## **Load Balancer IP**
> `34.148.25.108`


## Web app deployment
### Image build
    docker build --platform linux/amd64 -t zofiapx/frontend:amd

### Docker hub push
    docker push zofiapx/frontend:amd

### Docker image run
    docker run -d -p 80:3000 --name gcpfrontend zofiapx/frontend:amd
## VMs
### Comando para crear la VM 1
    gcloud compute instances create firstvm \
        --image-family ubuntu-pro-1804-lts \
        --image-project ubuntu-os-pro-cloud \
        --tags finalproject \
        --metadata startup-script="#! /bin/bash
            sudo apt update -y; 
            sudo apt install docker.io -y; 
            sudo chmod 666 /var/run/docker.sock; 

            docker run -d -p 80:3000 --name gcpfrontend zofiapx/frontend:amd
        "

### Comando para crear la VM 2
    gcloud compute instances create secondvm \
        --image-family ubuntu-pro-1804-lts \
        --image-project ubuntu-os-pro-cloud \
        --tags finalproject \
        --metadata startup-script="#! /bin/bash
            sudo apt update -y; 
            sudo apt install docker.io -y; 
            sudo chmod 666 /var/run/docker.sock; 

            docker run -d -p 80:3000 --name gcpfrontend zofiapx/frontend:amd
        "

### Comando para crear la VM 3
    gcloud compute instances create thirdvm \
        --image-family ubuntu-pro-1804-lts \
        --image-project ubuntu-os-pro-cloud \
        --tags finalproject \
        --metadata startup-script="#! /bin/bash
            sudo apt update -y; 
            sudo apt install docker.io -y; 
            sudo chmod 666 /var/run/docker.sock; 

            docker run -d -p 80:3000 --name gcpfrontend zofiapx/frontend:amd
        "
### Visualizacion de VMs creadas
    gcloud compute instances list
### Creando regla de acceso en el firewall -> port 80
    gcloud compute firewall-rules create firewall-network-gcp \
        --target-tags finalproject \
        --allow tcp:80

## Load Balancer
### Load balancer creation
    gcloud compute addresses create finalproject-lb-1

### Health check creation
    gcloud compute http-health-checks create finalproject-hc-1

### Create a target pool for health check
    gcloud compute target-pools create finalproject-pool \
        --http-health-check finalproject-hc-1

### Assign instances to target pool
    gcloud compute target-pools add-instances finalproject-pool \
        --instances firstvm,secondvm,thirdvm

### Adding a forwarding rule to lb
    gcloud compute forwarding-rules create finalproject-rule \
        --ports 80 \
        --address finalproject-lb-1 \
        --target-pool finalproject-pool

### Load balancer review
    gcloud compute forwarding-rules describe finalproject-rule
