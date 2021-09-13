# TPU_tutorial
## Intro
In this tutorial, we will learn...
1. Apply for TRC program
2. Create TPU environment
3. Run the example on TPU
4. Some caveats when you're using TPU

## TPU Research Cloud (TRC) program
### What is TRC program?
TRC enables researchers to apply for access to a cluster of more than 1,000 Cloud TPUs. **Researchers accepted into the TRC program will have access to v2 and v3 devices at no charge** and can leverage a variety of frameworks including TensorFlow, PyTorch, Julia and JAX to accelerate the next wave of open research breakthroughs.

Participants in the TRC program will be expected to share their TRC-supported research with the world through peer-reviewed publications, open source code, blog posts, or other means. They should also be willing to share detailed feedback with Google to help us improve the TRC program and the underlying Cloud TPU platform over time. In addition, participants accept Google's Terms and Conditions, acknowledge that their information will be used in accordance with our Privacy Policy, and agree to conduct their research in accordance with the Google AI principles.

### How many machines they provide?
+ 5 TPU V2-8 (64GB, 180 peak TFLOPS)
+ 5 TPU V3-8 (128GB, 420 peak TFLOPS)
+ 100 Colab TPUs (can use <24hrs)

### TRC application link
You can grant an access to TPUs within a week! [Link](https://docs.google.com/forms/d/e/1FAIpQLSeBXCs4vatyQUcePgRKh_ZiKhEODXkkoeqAKzFa_d-oSVp3iw/viewfor)

## Create TPU environment
In this section, we will create TPU using Google Cloud Platform (GCP) and set the environment.

### _(optional)_  What is TPU?
I highly recommend you to read **[this](https://cloud.google.com/tpu/docs/system-architecture-tpu-vm)** if you are not familiar with TPU. Otherwise, you can skip this section.

### Create TPU VM with GCP console
1. Access to [GCP console](console.cloud.google.com)
2. Turn on the GCP shell
![image](https://user-images.githubusercontent.com/35256263/133045746-48afb741-3a47-44b4-81da-793d88d8fddf.png)
3. Add some configurations to the shell.
```
export PROJECT_ID=<**Your GCP project ID**>
export MTYPE=<**TPU type, v2-8 or v3-8**>
export ZONE=<**appropriate zone for the TPU type**>
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
gcloud compute disks create <**Name of HDD**> \
--size=<**Size of HDD, e.g. 200GB**>  \
--zone=${ZONE} \
--type=pd-standard
```
You can find out the created disk in __Compute Engine-Disk__ section.
![image](https://user-images.githubusercontent.com/35256263/133048126-6b9795db-9fce-44c1-8652-46b69041c9c8.png)

5. Create TPU VM

```
gcloud alpha compute tpus tpu-vm create <**Name of HDD**> \
--project=${PROJECT_ID} \
--zone=${ZONE} \
--accelerator-type=${MTYPE} \
--version=v2-alpha
```
