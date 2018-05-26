# Classifying Iris with Decision Forest Algorithm

## Before Getting Started

We need to create accounts for Machine Learning Experimentation and Model Management and install ML Workbench.

### Create Machine Learning Experimentation
-   Azure account recommended ([sign up for free trial here](https://azure.microsoft.com/free) if you don’t already have an account).

#### 	Option 1:

- In the Azure portal, click the "New" button, then “Data + Analytics” and then “Machine Learning Experimentation”.

- Fill in the account name.

- Choose the subscription that you want billed.

#### Option 2:

```sh az ml account experimentation create -n (account) -g (resourceGroup) ```

### Install Azure ML Workbench

[Install Azure Machine Learning Workbench on Windows](https://docs.microsoft.com/en-us/azure/machine-learning/preview/quickstart-installation#install-azure-machine-learning-workbench-on-windows)

[Install Azure Machine Learning Workbench on macOS](https://docs.microsoft.com/en-us/azure/machine-learning/preview/quickstart-installation#install-azure-machine-learning-workbench-on-macos)


### QuickStart

- Launch Workbench.

- Create a new project. Click the plus sign to do that.
- Select `local` as the execution environment, and `iris_sklearn_forest.py` as the script, and click **Run** button. 
  

## Exploring results

After running, you can check out the results in **Run History**. Exploring the **Run History** will allow you to see the correlation between the parameters you entered and the accuracy of the models. You can get individual run details by clicking a run in the **Run History** report or clicking the name of the run on the Jobs Panel to the right. In this sample you will have richer results if you have `matplotlib` installed.

  
### Deploying a Model
[Docker Installation](https://docs.docker.com/engine/installation/#desktop)  

#### Environment Setup
To start the setup process, you need to register a few environment providers by entering the following commands:

```azurecli
az provider register -n Microsoft.MachineLearningCompute
az provider register -n Microsoft.ContainerRegistry
az provider register -n Microsoft.ContainerService
```
#### Local deployment
To deploy and test your web service on the local machine, set up a local environment using the following command. The resource group name is optional.

```azurecli
az ml env setup -l [Azure Region, e.g. eastus2] -n [your environment name] [-g [existing resource group]]
```

After setup completes successfully, set the environment to be used using the following command:

```azurecli
az ml env set -n [environment name] -g [resource group]
```

#### Create a Model Management Account
A model management account is required for deploying models. You need to do this once per subscription, and can reuse the same account in multiple deployments.

To create a new account, use the following command:

```azurecli
az ml account modelmanagement create -l [Azure region, e.g. eastus2] -n [your account name] -g [resource group name] --sku-instances [number of instances, e.g. 1] --sku-name [Pricing tier for example S1]
```

#### Storage Account Connection String

To find out which storage account is in use, type:
```azurecli
az ml env show
```
G o to the Azure Console and click on “Storage accounts”. Find the account that you got back from the command and get the connection string.

#### ADD Storage Account Connection String to Environment
```sh
export AML_MODEL_DC_STORAGE="<connection string>"
```

#### Create and deploy the web service

Register a model, create a manifest, create an image, as well as, create and deploy the webservice, as one step as follows.

```azurecli
az ml service create realtime -f score_iris.py --model-file model.pkl -s service_schema.json -n irisapp -r -python --collect-model-data true
```


#### Test the service
Use the following command to get information on how to call the service:

```azure-cli
az ml service usage realtime -i irisapp
az ml service keys realtime -i irisapp
```


``azurecli
curl -X POST -H "Content-Type:application/json" --data "{\"input_df\": [{\"petal width\": 0.25, \"sepal length\": 3.0, \"sepal width\": 3.6, \"petal length\": 1.3}]}" http://127.0.0.1:32770/score
```