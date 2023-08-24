---
lab:
    title: 'Explore foundation models in Azure Machine Learning's model catalog'
---

# Explore foundation models in Azure Machine Learning's model catalog

Large Language Models (LLMs) used for Natural Language Processing (NLP) can be costly to train. To save time and effort, you can use open source models that are pre-trained on a large corpus of text. Many open source LLMs are available as foundation models in the model catalog of Azure Machine Learning.

In this exercise you'll explore the available foundation models in Azure Machine Learning's model catalog. You'll review model cards and their test results to evaluate which model best fits your needs.

## Before you start

You'll need an [Azure subscription](https://azure.microsoft.com/free?azure-portal=true) in which you have administrative-level access.

## Provision an Azure Machine Learning workspace

An Azure Machine Learning *workspace* provides a central place for managing all resources and assets you need to train and manage your models. You can interact with the Azure Machine Learning workspace through the studio, Python SDK, and Azure CLI.

You'll use the Azure CLI to provision the workspace and necessary compute, and you'll use the studio to explore the model catalog and fine-tune a model.

### Create the workspace and upload the dataset

To create the Azure Machine Learning workspace, and upload the dataset to the workspace, you'll use the Azure CLI. All necessary commands are grouped in a Shell script for you to execute.

1. In a browser, open the Azure portal at `https://portal.azure.com/`, signing in with your Microsoft account.
1. Select the \[>_] (*Cloud Shell*) button at the top of the page to the right of the search box. This opens a Cloud Shell pane at the bottom of the portal.
1. Select **Bash** if asked. The first time you open the cloud shell, you will be asked to choose the type of shell you want to use (*Bash* or *PowerShell*).
1. Check that the correct subscription is specified and select **Create storage** if you are asked to create storage for your cloud shell. Wait for the storage to be created.
1. In the terminal, enter the following commands to clone this repo:

    ```azurecli
    rm -r gen-ai-labs -f
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

### Explore the data
While you wait for the setup script to complete, let's explore the data you'll use to finetune a foundation model.

You can use foundation models for different purposes, one of them is to classify text. The data in this exercise contains reviews of hotels that customers may have posted on a website. You may want to classify the sentiment of the hotel reviews. Though you could classify them as simply `positive` or `negative`, you could also create your own labels like `terrible`, `poor`, `average`, `very good`, `excellent`.

The dataset provided for this exercise contains two columns:
- `Text`: A hotel review (one or two sentences).
- `Label`: Manually added labels, either `terrible`, `poor`, `average`, `very good`, or `excellent`.

Some examples from the dataset include:

| Text | Label |
|---|---|
| I stayed at Contoso Suites and my experience was superb. The hotel had fantastic amenities and the staff was wonderful. |	Excellent |
| Alpine Ski House was pleasing. The rooms were satisfying. | Very good |
| Margie's Travel is a fair hotel. | Average|
| If you're looking for a lackluster getaway, look no further than Contoso Suites. | Poor |
| Contoso Suites is a dreadful hotel. | Terrible |

By finetuning a model with the provided dataset, you'll be able to classify hotel reviews quickly and easily, simply based on the text.

## Create a compute cluster

*Azure Machine Learning studio* is a web-based portal through which you can access the Azure Machine Learning workspace. You can use the Azure Machine Learning studio to manage all assets and resources within your workspace. To finetune a language model, you'll need a GPU cluster. You can create a GPU cluster in the studio.

1. In the Azure portal, navigate to the Azure Machine Learning workspace that starts with **rg-genai-...**.
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

1. Navigate to the **Model catalog**, using the menu on the left.
1. Search for and select the `bert-base-uncased` model.
1. Select **Finetune**.
1. Select `text classification` for **Select task type**.
1. **Select dataset** for **Training data**, to open the **Select training data** pop-up.
1. Select the **Registered datasets** tab.
1. Select the `hotel-reviews` dataset that was created for you with the setup script.
1. Select **Next** and wait for the dataset preview to be loaded. You should have two columns: `Text` and `Label`.
1. Configure the **Required data columns**:
    - **Select the 'sentence1 key' column**: `Text`
    - **Select the 'label key' column**: `Label`
1. Select **Add** to close the pop-up and go back to the finetuning configuration.
1. Leave the validation and test data at the default settings.
1. Verify that the compute cluster specifies the `gpu-cluster` that you created during setup.
1. Select **Finish** to trigger the finetuning job. The job will first prepare the compute cluster, and then finetune the model. The time needed to finetune the model will depend on the cluster you use. You may need to refresh the page to get the latest job status.

## Test the model
Finally, to test and use the model, you need to register and deploy the model.

### Register the model
First, you'll register the model from the job's output.

1. Navigate to the job overview.
1. Select the **Outputs + logs** tab.
1. Select **+ Register model**. 
1. Leave the default settings for the **Model type** (`MLflow`) and **Job output**.
1. Select **Next**.
1. In the **Model settings** tab, name the model `hotel-classification-model`.
1. Select **Next**.
1. Select **Register**.

### Deploy the model
Now that the model is registered, you can deploy the model.

1. Navigate to the **Models** page, using the menu on the left of the workspace.
1. Select the `hotel-classification-model` model.
1. Select **Deploy**, and select the **Real-time endpoint** option.
1. Leave all settings at their defaults.
1. Select **Deploy**.

An endpoint will be created first. Then, the registered model will be deployed to the endpoint. The deployment may take some time. Wait until the deployment is complete.

### Test the model
When the model is successfully deployed, you can test your model. The quickest way to test your model is directly in the Azure Machine Learning Studio.

1. Navigate to your endpoint.
1. Select the **Test** tab.

    > **Note:**
    > If the **Test** tab doesn't appear, the deployment isn't finalized yet. Wait until the model is deployed to the endpoint and refresh your page to find the **Test** tab.

1. Copy and paste the following JSON into the **Input data to test endpoint** field:

    ```json
    {"input_data": {"index": [0, 1, 2, 3, 4], "columns": ["text"], "data": [["Contoso Suites exceeded our expectations with their impeccable service and luxurious amenities."], ["The breathtaking mountain views from our room at Alpine Ski House made our stay truly magical."], ["We had a disappointing experience at Margie's Travel due to the lack of cleanliness and outdated facilities."], ["The cozy fireplace in our room at Alpine Ski House created a warm and inviting atmosphere after a day on the slopes."], ["The rooftop pool at Contoso Suites offered a stunning panoramic view of the city, making our stay unforgettable."]]}}
    ```

1. Select **Test** to send the input data to the endpoint.
1. The test result should appear on the right.
1. Review the output and you'll find that each sentence has been labeled with a classification of the text.

Feel free to experiment and change the text of the hotel reviews to explore the model's classifications.

## Delete Azure resources

When you finish exploring Azure Machine Learning, you should delete the resources you’ve created to avoid unnecessary Azure costs.

- Close the Azure Machine Learning studio tab and return to the Azure portal.
- In the Azure portal, on the **Home** page, select **Resource groups**.
- Select the resource group starting with **rg-genai...**.
- At the top of the **Overview** page for your resource group, select **Delete resource group**.
- Enter the resource group name to confirm you want to delete it, and select **Delete**.
