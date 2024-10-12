# Running nanoGPT on AWS

This guide will walk you through running Karpathy's [nanoGPT](https://github.com/karpathy/nanoGPT) on AWS infrastructure. The steps outlined include selecting suitable AWS instances for data preprocessing, storage, and training while minimizing costs by leveraging different instances at different stages.

## Step 1: Download and Preprocess Data
For the data download and preprocessing stage, we will use an **Amazon EC2 c5.2xlarge** instance, which offers a good balance of CPU and memory resources at a reasonable cost.

**Instance: c5.2xlarge**
- vCPUs: 8
- Memory: 16 GB RAM
- GPU: None
- **Hourly Cost**: ~$0.34 per hour

### Why c5.2xlarge?
This instance provides ample CPU power for data processing without incurring the higher costs of a GPU instance. The lack of a GPU for this step makes sense since preprocessing is CPU-bound, and we can save on the overall budget.

### Estimated Cost for Data Preprocessing:
- **Total Hours**: Varies depending on dataset size, but for an estimated 12 hours:
  - 12 hours * $0.34 = **$4.08**

## Step 2: Store Data on EBS and Attach to Training Instance
Once the preprocessing is complete, store the processed data on an **Amazon Elastic Block Store (EBS)** volume. This EBS volume will be attached to the training instance. EBS provides persistent storage that can be easily transferred between instances.

- **EBS Storage Cost**: ~$0.10 per GB per month
  - Size depends on your dataset, but for an example of a 100 GB dataset:
    - **Estimated Monthly Cost**: 100 GB * $0.10 = **$10/month**

## Step 3: Train Model with GPU Instance
After preprocessing, attach the EBS volume containing the processed data to a **p4d.24xlarge** instance. This instance provides the GPU power required to train medium-sized GPT models efficiently.

**Instance: p4d.24xlarge**
- GPUs: 8x NVIDIA A100 40GB
- vCPUs: 96
- Memory: 1.1 TB
- **Hourly Cost**: ~$33.05 per hour

### Why p4d.24xlarge?
This instance is optimal for training large-scale models like GPT-2 due to its multi-GPU configuration and high-performance networking. The training process is highly GPU-bound, so using the A100 GPUs will significantly reduce training time compared to other instances.

### Training Time and Costs:
- Based on the nanoGPT repo, training GPT-2 (124M) on OpenWebText can take approximately 4 days (96 hours).
- **Estimated Training Cost**:
  - 96 hours * $33.05 = **$3,172.80**

## Total Estimated Costs:
1. **Preprocessing (c5.2xlarge)**: ~$4.08
2. **EBS Storage (100 GB for 1 month)**: ~$10
3. **Training (p4d.24xlarge for 4 days)**: ~$3,172.80

