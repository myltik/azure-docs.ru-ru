---
title: "Классификация изображений аэрофотосъемки | Документация Майкрософт"
description: "Содержит инструкции для реального сценария классификации изображений аэрофотосъемки."
author: mawah
ms.author: mawah
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.topic: article
ms.service: machine-learning
services: machine-learning
ms.date: 09/15/2017
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 43b124fc3eb72adc5d299b218c9e16ec83d1a240
ms.contentlocale: ru-ru
ms.lasthandoff: 09/25/2017

---

# <a name="aerial-image-classification"></a>Классификация изображений аэрофотосъемки

В этом примере показано использование Azure Machine Learning Workbench для координации распределенного обучения и ввода в эксплуатацию модели классификации изображений. Пакет [Машинного обучения Microsoft Azure для Apache Spark (MMLSpark)](https://github.com/Azure/mmlspark) используется для создания признаков изображений с помощью предварительно обученных моделей CNTK и для обучения по ним классификаторов. Затем обученные модели параллельно применяются для наборов крупных изображений в облаке. Эти действия выполняются в кластере [Azure HDInsight Spark](https://azure.microsoft.com/en-us/services/hdinsight/apache-spark/), что позволяет изменять скорость обучения и ввода в эксплуатацию за счет добавления или удаления рабочих узлов.

Продемонстрированная форма переноса обучения имеет значительные преимущества по сравнению с переобучением или точной настройкой глубокой нейронной сети: она не требует вычислений GPU, быстрая по своей природе, произвольно масштабируемая и соответствует меньшему числу параметров. Это лучший метод при отсутствии большого количества примеров, как это часто бывает в пользовательских вариантах использования. Множество пользователей сообщает, что при использовании такого метода создаются высокоэффективные модели, позволяя им избежать обучения нейронных сетей с нуля и сопутствующих затрат.

Кластер Spark, используемый в этом примере, имеет 40 рабочих узлов, и его эксплуатация обходится в 40 долл. США/час. Завершение этого пошагового руководства займет примерно два часа. После завершения выполните инструкции по очистке, чтобы удалить созданные ресурсы и не нести расходов.

## <a name="link-to-the-gallery-github-repository"></a>Ссылка на репозиторий коллекции на GitHub

Общедоступный репозиторий GitHub содержит все материалы для этого реального сценария, в том числе примеры кода, которые нам потребуются.

[https://github.com/Azure/MachineLearningSamples-AerialImageClassification](https://github.com/Azure/MachineLearningSamples-AerialImageClassification)

## <a name="use-case-description"></a>Описание варианта использования

В этом сценарии мы обучаем модель машинного обучения для классификации типа земли, показанного на изображениях аэрофотосъемки размером 224 x 224 м. Модели классификации землепользования позволяют отслеживать урбанизацию, исчезновение лесов, исчезновение болот и другие значительные тенденции в области окружающей среды с помощью периодически собираемых аэрофотоснимков. Мы подготовили наборы изображений для обучения и проверки, снятые в рамках программы изображений национального сельского хозяйства США, и метки землепользования, опубликованные в базе данных национального растительного покрова США. Ниже показаны примеры изображений каждого класса землепользования:

![Примеры регионов для каждой метки землепользования](media/scenario-aerial-image-classification/example-labels.PNG)

После обучения и проверки модели классификации она будет применена для изображений аэрофотосъемки, охватывающих округ Мидлсекс в штате Массачусетс, где находится центр исследований и разработок Новой Англии (NERD) корпорации Майкрософт, чтобы показать, как эти модели можно использовать для изучения тенденций городского развития.

Для создания классификатора изображений с переносом обучения специалисты по обработке и анализу данных часто создают несколько моделей, использующих различные методы, и выбирают наиболее эффективную. Azure Machine Learning Workbench может помочь специалистам по обработке и анализу данных координировать обучение в различных вычислительных средах, отслеживать и сравнивать эффективность нескольких моделей, а также применять выбранную модель к большим наборам данных в облаке.

## <a name="scenario-structure"></a>Структура сценария

В этом примере данные изображений и предварительно обученные модели включены в учетную запись хранения Azure. Кластер Azure HDInsight Spark считывает эти файлы и создает модель классификации изображений с помощью MMLSpark. Обученная модель и ее прогнозы затем записываются в учетной записи хранения, где их можно проанализировать и визуализировать, используя записную книжку Jupyter, выполняемую локально. Приложение Azure Machine Learning Workbench координирует удаленное выполнение скриптов в кластере Spark. Оно также отслеживает метрики точности для нескольких моделей, обученных с помощью разных методов, позволяя выбрать наиболее эффективную модель.

![Схема для реального сценария классификации изображений аэрофотосъемки](media/scenario-aerial-image-classification/scenario-schematic.PNG)

[Пошаговые инструкции](https://github.com/MicrosoftDocs/azure-docs-pr/tree/release-ignite-aml-v2/articles/machine-learning/) начинаются с руководства по созданию и подготовке учетной записи хранения Azure и кластера Spark, включая передачу данных и установку зависимостей. Затем описывается запуск заданий обучения и сравнение эффективности итоговых моделей. Наконец, рассматривается, как применить выбранную модель к крупному набору изображений в кластере Spark и анализировать результаты прогноза локально.


## <a name="set-up-the-execution-environment"></a>Настройка среды выполнения

Следующие инструкции описывают процесс настройки среды выполнения для этого примера.

### <a name="prerequisites"></a>Предварительные требования
- [Учетная запись Azure](https://azure.microsoft.com/en-us/free/) (доступны бесплатные пробные версии).
    - В этом примере создается кластер HDInsight Spark с 40 рабочими узлами (всего 168 ядра). Убедитесь, что ваша учетная запись имеет достаточно доступных ядер, проверив вкладку "Использование и квоты" вашей подписки на портале Azure.
    - При отсутствии достаточного количества ядер можно изменить шаблон кластера HDInsight и уменьшить количество подготовленных рабочих узлов. Инструкции см. в разделе "Создание кластера HDInsight Spark".
- [Azure Machine Learning Workbench](./overview-what-is-azure-ml.md).
    - Чтобы установить эту программу и создать учетные записи Экспериментирования и Управления моделями, выполните инструкции из [краткого руководства по установке и созданию](quickstart-installation.md).
- [AzCopy](https://docs.microsoft.com/en-us/azure/storage/common/storage-use-azcopy) — это бесплатная служебная программа, координирующая передачу файлов между учетными записями хранения Azure.
    - Убедитесь, что папка, содержащая исполняемый файл AzCopy, находится в переменной среды PATH системы. Инструкции по изменению переменных среды доступны [здесь](https://support.microsoft.com/en-us/help/310519/how-to-manage-environment-variables-in-windows-xp). Этот пример был протестирован на компьютере с Windows 10, но его можно выполнить на любом компьютере Windows, включая виртуальные машины для обработки и анализа данных Azure. Могут потребоваться небольшие изменения (например, изменения в путях к файлам) при выполнении этого примера на macOS.

### <a name="set-up-azure-resources"></a>Настройка ресурсов Azure

Для этого примера требуется кластер HDInsight Spark и учетная запись хранения Azure для размещения соответствующих файлов. Выполните приведенные ниже действия для создания этих ресурсов в новой группе ресурсов Azure.

#### <a name="create-a-new-workbench-project"></a>Создание проекта в Workbench

Создайте проект, используя в качестве шаблона следующий пример:
1.  Откройте Azure Machine Learning Workbench.
2.  На странице **Projects** (Проекты) щелкните знак **+** и выберите **New Project** (Создать проект).
3.  В области **Create New Project** (Создание проекта) введите информацию о новом проекте.
4.  В поле поиска **Search Project Templates** (Поиск шаблонов проектов) введите Aerial Image Classification (Классификация изображений аэрофотосъемки) и выберите шаблон.
5.  Нажмите кнопку **Создать**
 
#### <a name="create-the-resource-group"></a>Создание группы ресурсов

1. В проекте Azure Machine Learning Workbench откройте интерфейс командной строки (CLI), щелкнув "Файл"-> Open Command Prompt (Открыть командную строку).
1. Из интерфейса командной строки войдите в свою учетную запись Azure с помощью следующей команды:

    ````
    az login
    ```

    You are asked to visit a URL and type in a provided temporary code; the website requests your Azure account credentials.
    
1. When login is complete, return to the CLI and run the following command to determine which Azure subscriptions are available to your Azure account:

    ```
    az account list
    ```

    This command lists all subscriptions associated with your Azure account. Find the ID of the subscription you would like to use. Write the subscription ID where indicated in the following command, then set the active subscription by executing the command:

    ```
    az account set --subscription [subscription ID]
    ```

1. The Azure resources created in this example are stored together in an Azure resource group. Choose a unique resource group name and write it where indicated, then execute both commands to create the Azure resource group:

    ```
    set AZURE_RESOURCE_GROUP=[resource group name]
    az group create --location eastus --name %AZURE_RESOURCE_GROUP%
    ```

#### Create the storage account

We now create the storage account that hosts project files that must be accessed by the HDInsight Spark.

1. Choose a unique storage account name and write it where indicated in the following `set` command, then create an Azure storage account by executing both commands:

    ```
    set STORAGE_ACCOUNT_NAME=[storage account name]
    az storage account create --name %STORAGE_ACCOUNT_NAME% --resource-group %AZURE_RESOURCE_GROUP% --sku Standard_LRS
    ```

1. Issue the following command to list the storage account keys:

    ```
    az storage account keys list --resource-group %AZURE_RESOURCE_GROUP% --account-name %STORAGE_ACCOUNT_NAME%
    ```

    Record the value of `key1` as the storage key in the following command, then run the command to store the value.
    ```
    set STORAGE_ACCOUNT_KEY=[storage account key]
    ```
1. In your favorite text editor, load the `settings.cfg` file from the Azure Machine Learning Workbench project's "Code" subdirectory, and insert the storage account name and key as indicated. Save and close the `settings.cfg` file.
1. If you have not already done so, download and install the [AzCopy](http://aka.ms/downloadazcopy) utility. Ensure that the AzCopy executable is on your system path by typing "AzCopy" and pressing Enter to show its documentation.
1. Issue the following commands to copy all of the sample data, pretrained models, and model training scripts to the appropriate locations in your storage account:

    ```
    AzCopy /Source:https://mawahsparktutorial.blob.core.windows.net/test /SourceSAS:"?sv=2017-04-17&ss=bf&srt=sco&sp=rwl&se=2037-08-25T22:02:55Z&st=2017-08-25T14:02:55Z&spr=https,http&sig=yyO6fyanu9ilAeW7TpkgbAqeTnrPR%2BpP1eh9TcpIXWw%3D" /Dest:https://%STORAGE_ACCOUNT_NAME%.blob.core.windows.net/test /DestKey:%STORAGE_ACCOUNT_KEY% /S
    AzCopy /Source:https://mawahsparktutorial.blob.core.windows.net/train /SourceSAS:"?sv=2017-04-17&ss=bf&srt=sco&sp=rwl&se=2037-08-25T22:02:55Z&st=2017-08-25T14:02:55Z&spr=https,http&sig=yyO6fyanu9ilAeW7TpkgbAqeTnrPR%2BpP1eh9TcpIXWw%3D" /Dest:https://%STORAGE_ACCOUNT_NAME%.blob.core.windows.net/train /DestKey:%STORAGE_ACCOUNT_KEY% /S
    AzCopy /Source:https://mawahsparktutorial.blob.core.windows.net/middlesexma2016 /SourceSAS:"?sv=2017-04-17&ss=bf&srt=sco&sp=rwl&se=2037-08-25T22:02:55Z&st=2017-08-25T14:02:55Z&spr=https,http&sig=yyO6fyanu9ilAeW7TpkgbAqeTnrPR%2BpP1eh9TcpIXWw%3D" /Dest:https://%STORAGE_ACCOUNT_NAME%.blob.core.windows.net/middlesexma2016 /DestKey:%STORAGE_ACCOUNT_KEY% /S
    AzCopy /Source:https://mawahsparktutorial.blob.core.windows.net/pretrainedmodels /SourceSAS:"?sv=2017-04-17&ss=bf&srt=sco&sp=rwl&se=2037-08-25T22:02:55Z&st=2017-08-25T14:02:55Z&spr=https,http&sig=yyO6fyanu9ilAeW7TpkgbAqeTnrPR%2BpP1eh9TcpIXWw%3D" /Dest:https://%STORAGE_ACCOUNT_NAME%.blob.core.windows.net/pretrainedmodels /DestKey:%STORAGE_ACCOUNT_KEY% /S
    ```

    Expect file transfer to take up to 20 minutes. While you wait, you can proceed to the following section. You may need to open another Command Line Interface through Workbench and redefine the temporary variables there.

#### Create the HDInsight Spark cluster

Our recommended method to create an HDInsight cluster uses the HDInsight Spark cluster Resource Manager template included in the "Code\01_Data_Acquisition_and_Understanding\01_HDInsight_Spark_Provisioning" subfolder of this project.

1. The HDInsight Spark cluster template is the "template.json" file under the "Code\01_Data_Acquisition_and_Understanding\01_HDInsight_Spark_Provisioning" subfolder of this project. By default, the template creates a Spark cluster with 40 worker nodes. If you must adjust that number, open the template in your favorite text editor and replace all instances of "40" with the worker node number of your choice.
    - You may encounter out-of-memory errors if the number of worker nodes you choose is small. To combat memory errors, you may run the training and operationalization scripts on a subset of the available data as described later in this document.
2. Choose a unique name and password for the HDInsight cluster and write them where indicated in the following command. Then create the cluster by issuing the following command:

    ```
    set HDINSIGHT_CLUSTER_NAME=[HDInsight cluster name]
    set HDINSIGHT_CLUSTER_PASSWORD=[HDInsight cluster password]
    az group deployment create --resource-group %AZURE_RESOURCE_GROUP% --name hdispark --template-file "Code\01_Data_Acquisition_and_Understanding\01_HDInsight_Spark_Provisioning\template.json" --parameters storageAccountName=%STORAGE_ACCOUNT_NAME%.blob.core.windows.net storageAccountKey=%STORAGE_ACCOUNT_KEY% clusterName=%HDINSIGHT_CLUSTER_NAME% clusterLoginPassword=%HDINSIGHT_CLUSTER_PASSWORD%
    ```

Your cluster's deployment may take up to 30 minutes (including provisioning and script action execution).

### Prepare the Azure Machine Learning Workbench execution environment

#### Register the HDInsight cluster as an Azure Machine Learning Workbench compute target

Once HDInsight cluster creation is complete, register the cluster as a compute target for your project as follows:

1.  Issue the following command from the Azure Machine Learning Command Line Interface:

    ```
    az ml computetarget attach --name myhdi --address %HDINSIGHT_CLUSTER_NAME%-ssh.azurehdinsight.net --username sshuser --password %HDINSIGHT_CLUSTER_PASSWORD% -t cluster
    ```

    This command adds two files, `myhdi.runconfig` and `myhdi.compute`, to your project's `aml_config` folder.

1. Open the `myhdi.compute` file in your favorite text editor. Modify the `yarnDeployMode: cluster` line to read `yarnDeployMode: client`, then save and close the file.
1. Run the following command to prepare your environment for use:
   ```
   az ml experiment prepare -c myhdi
   ```

#### Install local dependencies

Open a CLI from Azure Machine Learning Workbench and install dependencies needed for local execution by issuing the following command:

```
pip install matplotlib azure-storage==0.36.0 pillow scikit-learn
```

## Data acquisition and understanding

This scenario uses publicly available aerial imagery data from the [National Agriculture Imagery Program](https://www.fsa.usda.gov/programs-and-services/aerial-photography/imagery-programs/naip-imagery/) at 1-meter resolution. We have generated sets of 224 pixel x 224 pixel PNG files cropped from the original NAIP data and sorted according to land use labels from the [National Land Cover Database](https://www.mrlc.gov/nlcd2011.php). A sample image with label "Developed" is shown at full size:

![A sample tile of developed land](media/scenario-aerial-image-classification/sample-tile-developed.png)

Class-balanced sets of ~44k and 11k images are used for model training and validation, respectively. We demonstrate model deployment on a ~67k image set tiling Middlesex County, MA -- home of Microsoft's New England Research and Development (NERD) center. For more information on how these image sets were constructed, see the [Embarrassingly Parallel Image Classification git repository](https://github.com/Azure/Embarrassingly-Parallel-Image-Classification).

![Location of Middlesex County, Massachusetts](media/scenario-aerial-image-classification/middlesex-ma.png)

During setup, the aerial image sets used in this example were transferred to the storage account that you created. The training, validation, and operationalization images are all 224 pixel x 224 pixel PNG files at a resolution of one pixel per square meter. The training and validation images have been organized into subfolders based on their land use label. (The land use labels of the operationalization images are unknown and in many cases ambiguous; some of these images contain multiple land types.) For more information on how these image sets were constructed, see the [Embarrassingly Parallel Image Classification git repository](https://github.com/Azure/Embarrassingly-Parallel-Image-Classification).

To view example images in your Azure storage account (optional):
1. Log in to the [Azure portal](https://portal.azure.com).
1. Search for the name of your storage account in the search bar along the top of your screen. Click on your storage account in the search results.
2. Click on the "Blobs" link in the storage account's main pane.
3. Click on the container named "train." You should see a list of directories named according to land use.
4. Click on any of these directories to load the list of images it contains.
5. Click on any image and download it to view the image.
6. If desired, click on the containers named "test" and "middlesexma2016" to view their contents as well.

## Modeling

### Training models with MMLSpark
The `run_mmlspark.py` script in the "Code\02_Modeling" subfolder of the Workbench project is used to train an [MMLSpark](https://github.com/Azure/mmlspark) model for image classification. The script first featurizes the training set images using an image classifier DNN pretrained on the ImageNet dataset (either AlexNet or an 18-layer ResNet). The script then uses the featurized images to train an MMLSpark model (either a random forest or a logistic regression model) to classify the images. The test image set is then featurized and scored with the trained model. The accuracy of the model's predictions on the test set is calculated and logged to Azure Machine Learning Workbench's run history feature. Finally, the trained MMLSpark model and its predictions on the test set are saved to blob storage.

Select a unique output model name for your trained model, a pretrained model type, and an MMLSpark model type. Write your selections where indicated in the following command template, then begin retraining by executing the command from an Azure ML Command Line Interface:

```
az ml experiment submit -c myhdi Code\02_Modeling\run_mmlspark.py --config_filename Code/settings.cfg --output_model_name [unique model name, alphanumeric characters only] --pretrained_model_type {alexnet,resnet18} --mmlspark_model_type {randomforest,logisticregression}
```

An additional `--sample_frac` parameter can be used to train and test the model with a subset of available data. Using a small sample fraction decreases runtime and memory requirements at the expense of trained model accuracy. For more information on this and other parameters, run `python Code\02_Modeling\run_mmlspark.py -h`.

Users are encouraged to run this script several times with different input parameters. The performance of the resulting models can then be compared in Azure Machine Learning Workbench's Run History feature.

### Comparing model performance using the Workbench Run History feature

After you have executed two or more training runs of either type, navigate to the Run History feature in Workbench by clicking the clock icon along the left-hand menu bar. Select `run_mmlspark.py` from the list of scripts at left. A pane loads comparing the test set accuracy for all runs. To see more detail, scroll down and click on the name of an individual run.

## Deployment

To apply one of your trained models to aerial images tiling Middlesex County, MA using remote execution on HDInsight, insert your desired model's name into the following command and execute it:

```
az ml experiment submit -c myhdi Code\03_Deployment\batch_score_spark.py --config_filename Code/settings.cfg --output_model_name [trained model name chosen earlier]
```

An additional `--sample_frac` parameter can be used to operationalize the model with a subset of available data. Using a small sample fraction decreases runtime and memory requirements at the expense of prediction completeness. For more information on this and other parameters, run `python Code\03_Deployment\batch_score_spark -h`.

This script writes the model's predictions to your storage account. The predictions can be examined as described in the next section.

## Visualization

The "Model prediction analysis" Jupyter notebook in the "Code\04_Result_Analysis" subfolder of the Workbench project visualizes a model's predictions. Load and run the notebook as follows:
1. Open the project in Workbench and click on the folder ("Files") icon along the left-hand menu to load the directory listing.
2. Navigate to the "Code\04_Result_Analysis" subfolder and click on the notebook named "Model prediction analysis." A preview rendering of the notebook should be displayed.
3. Click "Start Notebook Server" to load the notebook.
4. In the first cell, enter the name of the model whose results you would like to analyze where indicated.
5. Click on "Cell -> Run All" to execute all cells in the notebook.
6. Read along with the notebook to learn more about the analyses and visualizations it presents.

## Cleanup
When you have completed the example, we recommend that you delete all of the resources you have created by executing the following command from the Azure Command Line Interface:

  ```
  az group delete --name %AZURE_RESOURCE_GROUP%
  ```

## References

- [The Embarrassingly Parallel Image Classification repository](https://github.com/Azure/Embarrassingly-Parallel-Image-Classification)
   - Describes dataset construction from freely available imagery and labels
- [MMLSpark](https://github.com/Azure/mmlspark) GitHub repository
   - Contains additional examples of model training and evaluation with MMLSpark

## Conclusions

Azure Machine Learning Workbench helps data scientists easily deploy their code on remote compute targets. In this example, local code was deployed for remote execution on an HDInsight cluster. Azure Machine Learning Workbench's run history feature tracked the performance of multiple models and helped us identify the most accurate model. Workbench's Jupyter notebooks feature helped visualize our models' predictions in an interactive, graphical environment.

## Next steps
To dive deeper into this example:
- In Azure Machine Learning Workbench's Run History feature, click the gear symbols to select which graphs and metrics are displayed.
- Examine the sample scripts for statements calling the `run_logger`. Check that you understand how each metric is being recorded.
- Examine the sample scripts for statements calling the `blob_service`. Check that you understand how trained models and predictions are stored and retrieved from the cloud.
- Explore the contents of the containers created in your blob storage account. Ensure that you understand which script or command is responsible for creating each group of files.
- Modify the training script to train a different MMLSpark model type or to change the model hyperparameters. Use the run history feature to determine whether your changes increased or decreased the model's accuracy.

