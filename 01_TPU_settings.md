# TPU environment settings
## What is TPU?
I highly recommend you to read **[this](https://cloud.google.com/tpu/docs/system-architecture-tpu-vm)** if you are not familiar with TPU. Otherwise, you can skip this section.

## TPU VM vs TPU
In this guide, you will use the bleeding edge feature of GCP: __TPU VM__.

Before the TPU VM, you can use TPU as follows
1. Create VM (Virtual Machine, usually a linux server w/ CPU & RAM)
2. Create TPU
3. Connect VM and TPU via network
But now TPU is physically attached to each TPU VM (imagine the GPU cards which is plugged in the server.), so you can use TPU VM with a single step,
1. Create TPU & access with SSH

One more advantage of TPU VM is the low cost because the cost for CPU, RAM and 100GB storage in TPU VM is included in the TPU price. So, if you are using TRC program, you only need to pay for the additional storage.

## TPU environment setting
### (1) Create TPU VM in GCP console
1. Access to [GCP console](console.cloud.google.com)
2. Turn on the GCP shell
![image](https://user-images.githubusercontent.com/35256263/133045746-48afb741-3a47-44b4-81da-793d88d8fddf.png)
3. Add some configurations to the shell.
```
export PROJECT_ID=[Your GCP project ID]
export MTYPE=[TPU type, v2-8 or v3-8]
export ZONE=[appropriate zone for the TPU type]
gcloud config set project ${PROJECT_ID}
```
You can find the information about the zone in the mail from TRC team. For example,
![image](https://user-images.githubusercontent.com/35256263/133046546-6db121c1-baca-44b9-8b07-75f3b08a2013.png)

In this case, you should use
```
export MTYPE=v2-8
export ZONE=us-central1-f
```
for free TPU.

4. _(optional)_ Create HDD for an additional storage.
```
export DISK_NAME=[Name of HDD]
gcloud compute disks create ${DISK_NAME} \
--size=[Size of HDD, e.g. 200GB]  \
--zone=${ZONE} \
--type=pd-standard
```
You can find out the created disk in __Compute Engine-Disk__ section.
![image](https://user-images.githubusercontent.com/35256263/133048126-6b9795db-9fce-44c1-8652-46b69041c9c8.png)

5. Create TPU VM
```
export TPU_NAME=[Name of TPU]
gcloud alpha compute tpus tpu-vm create ${TPU_NAME} \
--project=${PROJECT_ID} \
--zone=${ZONE} \
--accelerator-type=${MTYPE} \
--version=v2-alpha \
--data-disk source=projects/${PROJECT_ID}/zones/${ZONE}/disks/${DISK_NAME},mode=read-write <--- Please remove this line if you skip the step 4.
```
You can check the created TPU in __Compute Engine-TPU__ section.

### (2) Access to TPU VM
It is possible to access to TPU VM w/ GCP shell like below,
```
gcloud alpha compute tpus tpu-vm ssh ${TPU_NAME} --zone ${ZONE} --project ${PROJECT_ID}
``` 
but you might want to access to TPU VM w/ SSH. If so, please follow the instructions below.
__(Important) If you already register the SSH key to GCP, then you don't need the following steps__

1. Generate SSH key
To access the VM in GCP, you should register the SSH key to GCP.

_For Windows user,_

1) Download [PuttyGen](https://the.earth.li/~sgtatham/putty/latest/w64/puttygen.exe)

2) Click "generate" and shake your mouse on screen.

3) Type your Google ID to the "key comment". For example,
img

4) Click "save private key".

_For Linux & Mac user,_
```
ssh-keygen -t rsa -f ~/.ssh/[KEY_FILE_NAME] -C "[GoogleID]" --> private key
```

2. Check & Copy the public key.
_For Windows user,_
[img]

_For Linux & Mac user,_
```
cat ~/.ssh/[KEY_FILE_NAME].pub
```

3. Go to __Compute Engine-metadata-SSH key__ section in GCP.
[img]
4. Press "Edit"
[img]
5. Paste the public key to the empty box.
[img]
6. Press "Save".

7. Access to VM w/ SSH clients.
```
sudo ssh -t [GoogleID]@[TPU external IP] -p 22 -i [Private Key Path]
```
[img]

## (3) VM settings
1. Add XRT configuration
```
sudo vi ~/.bashrc
export XRT_TPU_CONFIG="localservice;0;localhost:51011" --> add this to bashrc
```
2. _(optional)_ Mount HDD to VM
Please access to TPU VM and follow the [link](https://cloud.google.com/compute/docs/disks/add-persistent-disk)

### Reference
1. https://cloud.google.com/blog/products/compute/introducing-cloud-tpu-vms
2. https://cloud.google.com/tpu/docs/users-guide-tpu-vm?hl=ko#prepare_a_gcp_project