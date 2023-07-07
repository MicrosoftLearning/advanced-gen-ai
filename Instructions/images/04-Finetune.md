---
lab:
    title: 'Finetune a foundation model in the Azure Machine Learning studio'
---

# Finetune a foundation model in the Azure Machine Learning studio

Azure Machine Learning provides a data science platform to train and manage machine learning models. In this lab, you'll create an Azure Machine Learning workspace and explore the various ways to work with the workspace. The lab is designed as an introduction of the various core capabilities of Azure Machine Learning and the developer tools. If you want to learn about the capabilities in more depth, there are other labs to explore.

## Before you start

You'll need an [Azure subscription](https://azure.microsoft.com/free?azure-portal=true) in which you have administrative-level access.

## Provision an Azure Machine Learning workspace

An Azure Machine Learning *workspace* provides a central place for managing all resources and assets you need to train and manage your models. You can interact with the Azure Machine Learning workspace through the studio, Python SDK, and Azure CLI. 

You'll use the Azure CLI to provision the workspace and necessary compute, and you'll use the Python SDK to run a command job.

### Create the workspace and upload the dataset

To create the Azure Machine Learning workspace, and upload the dataset to the workspace, you'll use the Azure CLI. All necessary commands are grouped in a Shell script for you to execute.

1. In a browser, open the Azure portal at `https://portal.azure.com/`, signing in with your Microsoft account.
1. Select the \[>_] (*Cloud Shell*) button at the top of the page to the right of the search box. This opens a Cloud Shell pane at the bottom of the portal.
1. Select **Bash** if asked. The first time you open the cloud shell, you will be asked to choose the type of shell you want to use (*Bash* or *PowerShell*).
1. Check that the correct subscription is specified and select **Create storage** if you are asked to create storage for your cloud shell. Wait for the storage to be created.
1. In the terminal, enter the following commands to clone this repo:

    ```azurecli
    rm -r azure-ml-labs -f
    git clone https://github.com/MicrosoftLearning/advanced-gen-ai.git gen-ai-labs
    ```

    > Use `SHIFT + INSERT` to paste your copied code into the Cloud Shell.

1. After the repo has been cloned, enter the following commands to change to the folder for this lab and run the **setup.sh** script it contains:
    
    ```azurecli
    cd gen-ai-labs/Labs/setup-script
    ./setup.sh
    ```

    > Ignore any (error) messages that say that the extensions were not installed.

1. Wait for the script to complete - this typically takes around 5-10 minutes.

## Create a compute cluster

*Azure Machine Learning studio* is a web-based portal through which you can access the Azure Machine Learning workspace. You can use the Azure Machine Learning studio to manage all assets and resources within your workspace. To finetune a language model, you'll need a GPU cluster. You can create a GPU cluster in the studio.

1. In the Azure portal, navigate to the Azure Machine Learning workspace named **mlw-dp100-labs**.
1. Select the Azure Machine Learning workspace, and in its **Overview** page, select **Launch studio**. Another tab will open in your browser to open the Azure Machine Learning studio.
1. Close any pop-ups that appear in the studio.
1. Navigate to the **Compute** page, using the menu on the left.
1. Select the **Compute clusters** tab.
1. Create a **+ New** cluster.
1. Configure the compute cluster with the following settings:
    - **Virtual machine tier**: `Low priority`
    - **Virtual machine type**: `GPU`
    - **Virtual machine size**: *The cheapest available, for example the `Standard_NC6s_v3*
    - **Name**: `gpu-cluster`
    - Keep all other settings at the default value.
1. Create the compute cluster and verify that it shows up in the list of compute clusters.

## Finetune the model

To finetune the model, you need training data, a compute cluster, and a pretrained model. The script registered a training dataset for you with hotel reviews. You created the necessary compute in the previous section. Now, it's time to choose the pretrained model you want to use and finetune the model.

1. Navigate to the **Model catelog**, using the menu on the left.
1. Search for and select the `bert-base-uncased` model.
1. Select **Finetune**.
1. Configure the finetuning with the following settings:
    - **Select task type**: `Text classification`
    - **Select training data**: **Select dataset**
        - Select the **Registered datasets** tab.
        - Select `hotel-reviews`

```json
{"input_data": {"index": [0, 1, 2, 3, 4], "columns": ["text"], "data": [["Contoso Suites exceeded our expectations with their impeccable service and luxurious amenities."], ["The breathtaking mountain views from our room at Alpine Ski House made our stay truly magical."], ["We had a disappointing experience at Margie's Travel due to the lack of cleanliness and outdated facilities."], ["The cozy fireplace in our room at Alpine Ski House created a warm and inviting atmosphere after a day on the slopes."], ["The rooftop pool at Contoso Suites offered a stunning panoramic view of the city, making our stay unforgettable."]]}}
```
