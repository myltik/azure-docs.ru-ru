---
title: Установка и использование интерфейса командной строки для важнейших задач — Машинное обучение Azure
description: Узнайте, как установить и использовать CLI для наиболее распространенных задач машинного обучения в службе "Машинное обучение Azure".
services: machine-learning
author: haining
ms.author: haining
manager: cgronlun
ms.reviewer: mldocs
ms.service: machine-learning
ms.component: desktop-workbench
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/10/2018
ms.openlocfilehash: 4b6ed5c70201b918a5bfa252719b2673303b38e9
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/07/2018
ms.locfileid: "34830866"
---
# <a name="install-and-use-the-machine-learning-cli-for-top-tasks-in-azure-machine-learning"></a>Установка и использование интерфейса командной строки для важнейших задач службы "Машинное обучение Azure"

Служба "Машинное обучение Microsoft Azure" — это полнофункциональное интегрированное решение для расширенной аналитики, обработки и анализа данных. Она позволяет специалистам по обработке и анализу данных подготавливать данные, разрабатывать эксперименты и развертывать модели в облачном масштабе. 

Служба "Машинное обучение Azure" предоставляет интерфейс командной строки (CLI), с помощью которого вы можете:
+ управлять рабочей областью и проектами;
+ настраивать целевые среды для вычислений;
+ запускать обучающие эксперименты;
+ просматривать журнал и метрики прошлых запусков;
+ развертывать модели в рабочей среде в качестве веб-служб;
+ управлять моделями и службами в рабочей среде.

В этой статье для вашего удобства представлены некоторые из наиболее полезных команд CLI. 

![Интерфейс командной строки службы "Машинное обучение Azure"](media/cli-for-azure-machine-learning/flow.png)

## <a name="what-you-need-to-get-started"></a>Что необходимо для начала работы

Вам нужны права участника на доступ к подписке Azure или группе ресурсов, где можно развернуть модели. Кроме того, необходимо установить Azure Machine Learning Workbench для запуска интерфейса командной строки. 

>[!IMPORTANT]
>CLI, поставляемый со службой "Машинное обучение Azure", отличается от [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest), который используется для управления ресурсами Azure.

## <a name="get-and-start-cli"></a>Получение и запуск CLI

Чтобы получить этот CLI, установите приложение Azure Machine Learning Workbench, которое можно скачать здесь:
    + Для Windows — https://aka.ms/azureml-wb-msi. 
    + MacOS — https://aka.ms/azureml-wb-dmg. 

Для запуска CLI:
+ В Azure Machine Learning Workbench запустите CLI из меню **Файл->Открыть командную строку.**

## <a name="get-command-help"></a>Получение справки по командам 

Вы можете получить дополнительную информацию о командах CLI, указав параметр `--debug` или `--help` после команд, например `az ml <xyz> --debug`, где `<xyz>` - имя команды. Например: 
```azurecli
az ml computetarget --debug 

az ml experiment --help
```

## <a name="common-cli-tasks-for-azure-machine-learning"></a>Общие задачи в CLI для службы "Машинное обучение Azure" 

В этом разделе вы узнаете о наиболее распространенных задачах, которые можно выполнить в CLI, в том числе:
+ [настройка целевых сред для вычислений;](#target)
+ [отправка заданий для удаленного выполнения;](#jobs)
+ [работа с записными книжками Jupyter;](#jupyter)
+ [взаимодействие с журналами выполнений;](#history)
+ [настройка среды для работы.](#o16n)

<a name="target"></a>

### <a name="set-up-a-compute-target"></a>Настройка целевой среды для вычислений

Можно использовать модель машинного обучения в разных средах, в том числе:
+ в локальной среде;
+ на виртуальных машинах для обработки и анализа данных (DSVM);
+ в кластере HDInsight.

Чтобы настроить в качестве цели виртуальной машины для обработки и анализа данных:
```azurecli
az ml computetarget attach remotedocker -n <target name> -a <ip address or FQDN> -u <username> -w <password>
``` 

Чтобы настроить в качестве цели кластера HDInsight:
```azurecli
az ml computetarget attach cluster -n <target name> -a <cluster name, e.g. myhdicluster-ssh.azurehdinsight.net> -u <ssh username> -w <ssh password>
```

В папке **aml_config** можно изменить зависимости conda. 

Кроме того, вы можете работать с PySpark, Python или Python в DSVM с GPV. 

Чтобы определить режим работы Python:
+ Для Python добавьте `Framework:Python` в `<target name>.runconfig`. 

+ Для PySpark добавьте `Framework:PySpark` в `<target name>.runconfig`. 

+ Для Python в DSVM с GPU:
    1. Добавьте `Framework:Python` в `<target name>.runconfig`. 

    1. Кроме того, добавьте `baseDockerImage: microsoft/mmlspark:plus-gpu-0.9.9 and nvidiaDocker:true` в `<target name>.compute`.

Чтобы подготовить целевую среду для вычислений:
```azurecli
az ml experiment prepare -c <target name>
```

>[!TIP]
>Чтобы получить сведения о подписке:<br/>
>`az account show`<br/>
>
>Чтобы настроить подписку:<br/>
>`az account set –s "my subscription name" `

<a name="jobs"></a>

### <a name="submit-remote-jobs"></a>Отправка удаленных заданий

Чтобы отправить задание к удаленной целевой среде:
```azurecli
az ml experiment submit -c <target name> myscript.py
```

<a name="jupyter"></a>

### <a name="work-with-jupyter-notebooks"></a>Работа с записными книжками Jupyter

Чтобы запустить записную книжку Jupyter:
```azurecli
az ml notebook start
```

Эта команда запускает записную книжку Jupyter на локальном узле. Можно работать на локальном компьютере, выбрав ядро Python 3, или на удаленной виртуальной машине, выбрав ядро `<target name>`.

<a name="history"></a>

### <a name="interact-with-and-explore-the-run-history"></a>Взаимодействие с журналом выполнения и его изучение

Чтобы просмотреть журнал выполнения:
```azurecli
az ml history list -o table
```

Чтобы просмотреть все завершенные запуски:
```azurecli
az ml history list --query "[?status=='Completed']" -o table
```

Чтобы найти запуски с лучшей точностью:
```azurecli
az ml history list --query "@[?Accuracy != null] | max_by(@, &Accuracy).Accuracy"
```

Можно также загрузить файлы, сгенерированные каждым запуском. 

Для повышения уровня модели, которая сохранена в выходных данных папки:
```azurecli
az ml history promote -r <run id> -ap outputs/model.pkl -n <model name>
```

Чтобы загрузить модель:
```azurecli
az ml asset download -l assets/model.pkl.link -d <model folder path>
```

<a name="o16n"></a>

### <a name="configure-your-environment-to-operationalize"></a>Настройка среды для работы

Для настройки среды выполнения необходимо создать:

> [!div class="checklist"]
> * Группа ресурсов. 
> * Учетная запись хранения.
> * Реестр контейнеров Azure (ACR).
> * учетную запись Application Insights;
> * Развертывание Kubernetes в кластере Службы контейнеров Azure (ACS).


Чтобы настроить локальное развертывание для тестирования в контейнере Docker:
```azurecli
az ml env setup -l <region, e.g. eastus2> -n <env name> -g <resource group name>
```

Чтобы настроить кластер ACS с Kubernetes:
```azurecli
az ml env setup -l <region, e.g. eastus2> -n <env name> -g <resource group name> --cluster
```

Чтобы отслеживать состояние развертывания:
```azurecli
az ml env show -n <environment name> -g <resource group name>
```

Чтобы настроить среду, которая должна использоваться:
```azurecli
az ml env set -n <environment name> -g <resource group name>
```

## <a name="next-steps"></a>Дополнительная информация

Используйте одну из этих статей, чтобы приступить к работе: 
+ [Краткое руководство. Установка и начало работы со службами Машинного обучения Azure](../service/quickstart-installation.md)
+ [Руководство. Часть 1. Классификация цветков ириса: подготовка данных](tutorial-classifying-iris-part-1.md)

Более подробная информация содержится в этой статье:
+ [Справочник по интерфейсу командной строки для службы управления моделями](model-management-cli-reference.md)
