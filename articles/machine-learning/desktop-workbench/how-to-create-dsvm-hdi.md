---
title: Создание виртуальной машины для обработки и анализа данных и кластера HDInsight как целевых объектов вычислений для Машинного обучения Azure
description: Создание виртуальной машины для обработки и анализа данных и кластера HDInsight Spark как целевых объектов вычислений для экспериментирования в Машинном обучении.
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: desktop-workbench
ms.workload: data-services
ms.topic: article
ms.date: 09/26/2017
ms.openlocfilehash: 40711c424d3d552253deba85110b0c4447f4ec62
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/07/2018
ms.locfileid: "34831031"
---
# <a name="create-dsvm-and-hdi-spark-cluster-as-compute-targets"></a>Создание виртуальной машины для обработки и анализа данных и кластера HDInsight Spark как целевых объектов вычислений

Вы можете без труда выполнять увеличение масштаба или развертывание эксперимента машинного обучения, добавляя дополнительные целевые объекты вычислений, такие как виртуальная машина для обработки и анализа данных под управлением Ubuntu и кластер Apache Spark для Azure HDInsight. В этой статье показано, как создать эти целевые объекты вычислений в Azure. Дополнительные сведения о целевых объектах вычислений Azure ML см. в [обзоре службы "Экспериментирование в Машинном обучении Azure"](experimentation-service-configuration.md).

>[!NOTE]
>Прежде чем продолжить, необходимо убедиться в наличии соответствующих разрешений для создания ресурсов, таких как виртуальная машина и кластеры HDI в Azure. Также оба этих ресурса могут использовать много вычислительных ядер в зависимости от конфигурации. Убедитесь, что ваша подписка обладает достаточной емкостью для ядер виртуального ЦП. Вы всегда можете обратиться в службу поддержки Azure, чтобы увеличить максимальное число ядер, допустимое в вашей подписке.

## <a name="create-an-ubuntu-dsvm-in-azure-portal"></a>Создание виртуальной машины для обработки и анализа данных под управлением Ubuntu на портале Azure

Виртуальную машину для обработки и анализа данных можно создать на портале Azure. 

1. Войдите на портал Azure по адресу https://portal.azure.com.
2. Щелкните ссылку **+Создать** и найдите "Виртуальная машина Linux для обработки и анализа данных".
    ![Ubuntu](media/how-to-create-dsvm-hdi/ubuntu_dsvm.png)
4. Выберите из списка **Виртуальные машины для обработки и анализа данных для Linux (Ubuntu)** и следуйте инструкциям на экране, чтобы создать виртуальную машину для обработки и анализа данных.

>[!IMPORTANT]
>Обязательно выберите для параметра _Тип проверки подлинности_ значение **Пароль**.

![использовать пароль](media/how-to-create-dsvm-hdi/use_pwd.png)

## <a name="create-an-ubuntu-dsvm-using-azure-cli"></a>Создание виртуальной машины для обработки и анализа данных под управлением Ubuntu с помощью azure-cli

Для развертывания виртуальной машины для обработки и анализа данных также можно использовать шаблон Azure Resource Manager.

>[!NOTE]
>Предполагается, что все последующие команды запускаются из корневой папки проекта Машинного обучения Azure.

Во-первых, с помощью предпочитаемого текстового редактора создайте файл `mydsvm.json` в папке `docs`. (Если у вас нет папки `docs` в корневой папке проекта, то создайте ее.) Мы используем этот файл, чтобы настроить несколько основных параметров для шаблона Azure Resource Manager. 

Скопируйте следующий фрагмент JSON и вставьте его в файл `mydsvm.json`, а затем заполните соответствующие значения:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
     "adminUsername": { "value" : "<admin username>"},
     "adminPassword": { "value" : "<admin password>"},
     "vmName": { "value" : "<vm name>"},
     "vmSize": { "value" : "<vm size>"}
  }
}
```

Для поля _vmSize_ можно использовать любой из поддерживаемых размеров виртуальных машин, перечисленных в [шаблоне Azure Resource Manager виртуальной машины для обработки и анализа данных под управлением Ubuntu](https://github.com/Azure/DataScienceVM/blob/master/Scripts/CreateDSVM/Ubuntu/multiazuredeploywithext.json). В качестве целевых объектов вычислений для Машинного обучения Azure рекомендуется использовать один из приведенных ниже размеров. 


>[!TIP]
> [Рабочие нагрузки глубокого обучения](how-to-use-gpu.md) можно развернуть на виртуальных машинах на базе GPU.

- [Виртуальные машины общего назначения](/virtual-machines/linux/sizes-general.md)
  - Standard_DS2_v2 
  - Standard_DS3_v2 
  - Standard_DS4_v2 
  - Standard_DS12_v2 
  - Standard_DS13_v2 
  - Standard_DS14_v2 
- [Виртуальные машины на базе GPU](/virtual-machines/linux/sizes-gpu.md)
  - Standard_NC6 
  - Standard_NC12 
  - Standard_NC24 
 

Узнайте больше об этих [размерах виртуальных машин Linux в Azure](../../virtual-machines/linux/sizes.md) и [цены](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) на них.

Откройте окно интерфейса командной строки из приложения Azure ML Workbench, щелкнув **Файл** --> **Открыть командную строку**, или выберите пункт меню **Open PowerShell** (Открыть PowerShell). 

>[!NOTE]
>Это также можно сделать в любой среде командной строки, где установлен пакет az-cli.

В окне командной строки введите следующие команды:

```azurecli
# first make sure you have a valid Azure authentication token
$ az account get-access-token

# if you don't have a valid token, please log in to Azure first. 
# if you already do, you can skip this step.
$ az login

# list all subscriptions you have access to
$ az account list -o table

# make sure you set the subscription you want to use to create DSVM as the current subscription
$ az account set -s <subscription name or Id>

# it is always a good idea to create a resource group for the VM and associated resources to live in.
# you can use any Azure region, but it is best to create them in the region where your Azure ML Experimentation account is, e.g. eastus2, westcentralus or australiaeast.
# also, only certain Azure regions has GPU-equipped VMs available.
$ az group create -n <resource group name> -l <azure region>

# now let's create the DSVM based on the JSON configuration file you created earlier.
# note we assume the mydsvm.json config file is placed in the "docs" sub-folder.
$ az group deployment create -g <resource group name> --template-uri https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/azuredeploy.json --parameters @docs/mydsvm.json

# find the FQDN (fully qualified domain name) of the VM just created. 
# you can also use IP address from the next command if FQDN is not set.
$ az vm show -g <resource group name> -n <vm name> --query "fqdns"

# find the IP address of the VM just created
$ az vm show -g <resource group name> -n <vm name> --query "publicIps"
```
## <a name="attach-a-dsvm-compute-target"></a>Подключение целевого объекта вычислений виртуальной машины для обработки и анализа данных
После создания виртуальной машины для обработки и анализа данных ее можно подключить к проекту Машинного обучения Azure.

```azurecli
# attach the DSVM compute target
# it is a good idea to use FQDN in case the IP address changes after you deallocate the VM and restart it
$ az ml computetarget attach remotedocker --name <compute target name> --address <ip address or FQDN> --username <admin username> --password <admin password> 

# prepare the Docker image on the DSVM 
$ az ml experiment prepare -c <compute target name>
```
Теперь у вас все готово для выполнения экспериментов на этой виртуальной машины для обработки и анализа данных.

## <a name="deallocate-a-dsvm-and-restart-it-later"></a>Отмена выделения виртуальной машины для обработки и анализа данных и последующая ее перезагрузка
После завершения вычислительных задач в Машинном обучении Azure можно отменить выделение виртуальной машины для обработки и анализа данных. Это действие завершает работу виртуальной машины, освобождает вычислительные ресурсы, но сохраняет виртуальные диски. Если выделение виртуальной машины отменено, то плата за использование вычислительных ресурсов не взимается.

Для отмены выделения виртуальной машины выполните следующую команду:

```azurecli
$ az vm deallocate -g <resource group name> -n <vm name>
```

Чтобы снова запустить виртуальную машину, используйте команду `az ml start`:

```azurecli
$ az vm start -g <resource group name> -n <vm name>
```

## <a name="expand-the-dsvm-os-disk"></a>Расширение диска ОС виртуальной машины для обработки и анализа данных
Ubuntu DSVM поставляется с диском ОС размером 50 ГБ и диском данных размером 100 ГБ. В Docker образы хранятся на диске данных, так как на нем больше места. При использовании в качестве целевого объекта вычислений для службы "Машинное обучение Azure" пространство на диске может быстро расходоваться модулем Docker, который извлекает образы Docker и создает на его основе слои Conda. Пока не завершено выполнение, возможно, потребуется увеличить размер диска (например, до 200 ГБ) во избежание ошибки "Диск заполнен". В статье [Расширение виртуальных жестких дисков на виртуальной машине Linux с помощью Azure CLI](../../virtual-machines/linux/expand-disks.md), показано, как это легко сделать с помощью azure-cli. 

## <a name="create-an-apache-spark-for-azure-hdinsight-cluster-in-azure-portal"></a>Создание кластера Apache Spark для Azure HDInsight на портале Azure

Для выполнения заданий масштабирования Spark необходимо создать кластер Apache Spark для Azure HDInsight на портале Azure.

1. Войдите на портал Azure по адресу https://portal.azure.com.
2. Щелкните ссылку **+Создать** и найдите "HDInsight".

    ![найти hdi](media/how-to-create-dsvm-hdi/hdi.png)
    
3. В списке выберите **HDInsight**, а затем нажмите кнопу **Создать**.
4. Убедитесь, что на экране настройки **Основы** в параметрах **Тип кластера** в качества _типа кластера_ выбрано **Spark**, в качестве _операционной системы_ — **Linux**, а версия указана как **Spark 2.1.0 (HDI 3.6)**.

    ![настроить hdi](media/how-to-create-dsvm-hdi/configure_hdi.png)

    >[!IMPORTANT]
    >Обратите внимание, что на приведенном выше экране в параметрах кластера есть поля _Имя пользователя для входа в кластер_ и _Имя пользователя для Secure Shell (SSH)_. Это два разных удостоверения пользователя, хотя для удобства для обоих имен можно указать одинаковый пароль. _Имя пользователя для входа в кластер_ используется для входа в пользовательский веб-интерфейс управления кластера HDI. _Имя пользователя для Secure Shell (SSH)_ используется для входа в головной узел кластера, и это необходимо Машинному обучению Azure для диспетчеризации заданий Spark.

5. Выберите необходимые значения размера кластера и размера узла и завершите работу мастера создания. Завершение подготовки кластера может занять до 30 минут. 

## <a name="attach-an-hdi-spark-cluster-compute-target"></a>Подключение кластера HDInsight Spark как целевого объекта вычислений

После создания кластера HDInsight Spark его можно подключить к проекту Машинного обучения Azure.

```azurecli
# attach the HDI compute target
$ az ml computetarget attach cluster --name <compute target name> --address <cluster name, such as myhdicluster123.azurehdinsight.net> --username <ssh username> --password <ssh password> 

# prepare the conda environment on HDI
$ az ml experiment prepare -c <compute target name>
```
Теперь у вас все готово для выполнения экспериментов с этим кластером Spark.

## <a name="next-steps"></a>Дополнительная информация

См. также:
- [Обзор службы "Экспериментирование в Машинном обучении Azure"](experimentation-service-configuration.md).
- [Файлы конфигурации службы экспериментирования Azure Machine Learning Workbench](experimentation-service-configuration-reference.md).
- [Apache Spark для Azure HDInsight](https://azure.microsoft.com/services/hdinsight/apache-spark/)
- [Виртуальные машины для обработки и анализа данных](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/)
