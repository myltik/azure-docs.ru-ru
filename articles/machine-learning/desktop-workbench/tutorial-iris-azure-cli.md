---
title: Руководство по функциям предварительной версии служб машинного обучения Azure. Интерфейс командной строки | Документация Майкрософт
description: В этом руководстве рассматриваются все шаги, необходимые для выполнения полной классификации цветков ириса с помощью интерфейса командной строки.
services: machine-learning
author: ahgyger
ms.author: ahgyger
manager: haining
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: tutorial
ms.date: 10/15/2017
ms.openlocfilehash: 05238c27a5654ae24c619b52d769abbf90b940e7
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/19/2018
---
# <a name="tutorial-classifying-iris-using-the-command-line-interface"></a>Руководство по классификации цветков ириса с помощью интерфейса командной строки
Служба "Машинное обучение Azure" (предварительная версия) — это полнофункциональное интегрированное аналитическое решение для специалистов по обработке и анализу данных. Оно помогает подготавливать данные, разрабатывать эксперименты и развертывать модели в масштабе облака.

Из этого руководства вы узнаете, как использовать функции предварительной версии служб машинного обучения Azure с помощью интерфейса командной строки для: 
> [!div class="checklist"]
> * настройки учетной записи Экспериментирования и создания рабочей области;
> * Создание проекта
> * отправки эксперимента нескольким целевым объектам вычислений;
> * повышения уровня и регистрации обученной модели;
> * развертывания веб-службы для оценки новых данных.

## <a name="prerequisites"></a>предварительным требованиям
Для работы с этим учебником необходимы указанные ниже компоненты.
- Доступ к подписке Azure и разрешение для создания в ней ресурсов. 
  
  Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

- Установленное приложение Azure Machine Learning Workbench. Инструкции по установке см. в статье [Создание учетной записи для предварительной версии службы "Машинное обучение Azure" и установка Azure Machine Learning Workbench](../service/quickstart-installation.md). 

  >[!IMPORTANT]
  >Не создавайте учетные записи службы "Машинное обучение Azure", так как вы будете делать это с помощью CLI в этой статье.
 
## <a name="getting-started"></a>Приступая к работе
Интерфейс командной строки (CLI) Машинного обучения Azure позволяет выполнять все задачи, необходимые для полного рабочего процесса обработки и анализа данных. Вы можете использовать средства интерфейса командной строки следующим образом.

### <a name="option-1-launch-azure-ml-cli-from-azure-ml-workbench-log-in-dialog-box"></a>Вариант 1. Запуск командной строки Машинного обучения Azure из диалогового окна входа в Azure ML Workbench
Если вы впервые выполняете вход в Azure ML Workbench и у вас нет доступа к учетной записи Экспериментирования, появится следующий экран:

![учетная запись не найдена](media/tutorial-iris-azure-cli/no_account_found.png)

Щелкните ссылку **окна командной строки** в диалоговом окне, чтобы открыть окно командной строки.

### <a name="option-2-launch-azure-ml-cli-from-azure-ml-workbench-app"></a>Вариант 2. Запуск командной строки Машинного обучения Azure из приложения Azure ML Workbench
Если вы уже получили доступ к учетной записи Экспериментирования, вы сможете успешно выполнить вход. Затем можно открыть окно командной строки, щелкнув меню **Файл** --> **Открыть командную строку**.

### <a name="option-3-enable-azure-ml-cli-in-an-arbitrary-command-line-window"></a>Вариант 3. Включение командной строки Машинного обучения Azure в произвольном окне командной строки
Командную строку Машинного обучения Azure можно также включить в любом окне командной строки. Для этого откройте окно командной строки и введите следующие команды:

```sh
# Windows Command Prompt
set PATH=%LOCALAPPDATA%\amlworkbench\Python;%LOCALAPPDATA%\amlworkbench\Python\Scripts;%PATH%

# Windows PowerShell
$env:Path = $env:LOCALAPPDATA+"\amlworkbench\Python;"+$env:LOCALAPPDATA+"\amlworkbench\Python\Scripts;"+$env:Path

# macOS Bash Shell
PATH=$HOME/Library/Caches/AmlWorkbench/Python/bin:$PATH
```
Для внесения постоянных изменений можно использовать `SETX` в Windows. Для macOS можно использовать `setenv`.

>[!TIP]
>Azure CLI можно включить в предпочитаемом окне терминала, задав вышеуказанные переменные среды.

## <a name="step-1-log-in-to-azure"></a>Шаг 1. Вход в Azure
Сначала откройте интерфейс командной строки в приложении AML Workbench (Файл > Open Command Prompt (Открыть командную строку)). Это гарантирует, что вы используете соответствующую среду Рython и что команды интерфейса командной строки машинного обучения доступны. 

Теперь необходимо установить правильный контекст в вашем интерфейсе командной строки для доступа к ресурсам Azure и управления ими.
 
```azure-cli
# log in
$ az login

# list all subscriptions
$ az account list -o table

# set the current subscription
$ az account set -s <subscription id or name>
```

## <a name="step-2-create-a-new-azure-machine-learning-experimentation-account-and-workspace"></a>Шаг 2. Создание учетной записи Экспериментирования службы машинного обучения Azure и рабочей области

На этом шаге вы создаете учетную запись службы "Экспериментирование" и рабочую область. Дополнительные сведения об учетных записях Экспериментирования и рабочих областях см. в статье [Azure Machine Learning - Concepts](overview-general-concepts.md) (Основные понятия Машинного обучения Azure).

> [!NOTE]
> Для учетных записей Экспериментирования требуется учетная запись хранения, которая используется для хранения выходных данных в результате выполнения эксперимента. Имя учетной записи хранения должно быть уникальным в Azure, так как отсутствует URL-адрес, связанный с ним. Если вы не укажете существующую учетную запись хранения, будет создана новая учетная запись хранения с именем, как у учетной записи эксперимента. Обязательно используйте уникальное имя, иначе появится сообщение об ошибке, например _"Учетная запись хранения с именем \<имя_учетной_записи> уже занята"._ Кроме того, можно предоставить существующую учетную запись с помощью аргумента `--storage`.

```azure-cli
# create a resource group 
$ az group create --name <resource group name> --location <supported Azure region>

# create a new experimentation account with a new storage account
$ az ml account experimentation create --name <experimentation account name> --resource-group <resource group name>

# create a new experimentation account with an existing storage account
$ az ml account experimentation create --name <experimentation account name>  --resource-group <resource group name> --storage <storage account Azure Resource ID>

# create a workspace in the experimentation account
az ml workspace create --name <workspace name> --account <experimentation account name> --resource-group <resource group name>
```

## <a name="step-2a-optional-share-a-workspace-with-co-worker"></a>Шаг 2.a. Предоставление общего доступа к рабочей области (необязательно)
Здесь вы узнаете, как предоставить коллеге доступ к рабочей области. Шаги для предоставления общего доступа к учетной записи Экспериментирования или проекту аналогичны. Отличается только способ получения идентификатора ресурса Azure.

```azure-cli
# find the workspace Azure Resource ID
$az ml workspace show --name <workspace name> --account <experimentation account name> --resource-group <resource group name>

# add Bob to this workspace as a new owner
$az role assignment create --assignee bob@contoso.com --role owner --scope <workspace Azure Resource ID>
```

> [!TIP]
> В приведенной выше команде параметр `bob@contoso.com` должен являться допустимым удостоверением Azure AD в каталоге, которому принадлежит используемая подписка.

## <a name="step-3-create-a-new-project"></a>Шаг 3. Создание нового проекта
Теперь необходимо создать проект. Это можно сделать несколькими способами.

### <a name="create-a-new-blank-project"></a>Создание пустого проекта

```azure-cli
# create a new project
$ az ml project create --name <project name> --workspace <workspace name> --account <experimentation account name> --resource-group <resource group name> --path <local folder path>
```

### <a name="create-a-new-project-with-a-default-project-template"></a>Создание проекта с помощью стандартного шаблона
Вы можете создать проект с помощью стандартного шаблона.

```azure-cli
$ az ml project create --name <project name> --workspace <workspace name> --account <experimentation account name> --resource-group <resource group name> --path <local folder path> --template
```

### <a name="create-a-new-project-associated-with-a-cloud-git-repository"></a>Создание проекта, связанного с облачным репозиторием Git
Вы можете создать проект, связанный с репозиторием Git VSTS (Visual Studio Team Service). При каждой отправке эксперимента в удаленный репозиторий Git передается моментальный снимок всей папки проекта. Дополнительные сведения см. в статье [Использование репозитория Git в проекте Azure Machine Learning Workbench](using-git-ml-project.md).

> [!NOTE]
> Сейчас машинное обучение Azure поддерживает только пустые репозитории Git, созданные в VSTS.

```azure-cli
$ az ml project create --name <project name> --workspace <workspace name> --account <experimentation account name> --resource-group <resource group name> --path <local folder path> --repo <VSTS repo URL>
```
> [!TIP]
> Если вы увидите сообщение об ошибке, информирующее о том, что URL-адрес репозитория недействителен или у пользователя нет доступа, попробуйте создать в VSTS маркер безопасности с помощью меню _Security_ (Безопасность) > _Add personal access tokens_ (Добавить маркеры доступа) и используйте аргумент `--vststoken` при создании проекта. 

### <a name="sample_create"></a>Создание проекта с помощью примера
В этом разделе вы создадите проект, используя образец проекта в качестве шаблона.

```azure-cli
# List the project samples, find the Classifying Iris sample
$ az ml project sample list

# Create a new project from the sample
az ml project create --name <project name> --workspace <workspace name> --account <experimentation account name> --resource-group <resource group name> --path <local folder path> --template-url https://github.com/MicrosoftDocs/MachineLearningSamples-Iris
```
Когда проект будет создан, выполните команду `cd` для перехода в каталог проекта.

## <a name="step-4-run-the-training-experiment"></a>Шаг 4. Выполнение обучающего эксперимента 
В следующих шагах предполагается, что у вас уже есть проект с примером классификации цветков ириса (см. раздел [Создание проекта с помощью примера](#sample_create)).

### <a name="prepare-your-environment"></a>Подготовка среды 
Для примера классификации цветков ириса необходимо установить matplotlib.

```azure-cli
$ pip install matplotlib
```

###  <a name="submit-the-experiment"></a>Отправка эксперимента

```azure-cli
# Execute the file
$ az ml experiment submit --run-configuration local iris_sklearn.py
```

### <a name="iterate-on-your-experiment-with-descending-regularization-rates"></a>Выполнение итерации эксперимента с понижением показателей регуляризации
Вы можете легко создать скрипт Python, который отправляет эксперименты с разными показателями регуляризации. (Может потребоваться изменить файл, чтобы указать путь к необходимому проекту.)

```azure-cli
$ python run.py
```

## <a name="step-5-view-run-history"></a>Шаг 5. Просмотр журнала выполнения
Следующая команда перечисляет все предыдущие выполнения. 

```azure-cli
$ az ml history list -o table
```
В результате отобразится список всех выполнений, относящихся к этому проекту. Вы увидите, что метрики точности и показатели регуляризации также указаны. Это позволяет легко идентифицировать лучшее выполнение в списке.

## <a name="step-5a-view-attachment-created-by-a-given-run"></a>Шаг 5.a. Просмотр вложения, созданного выбранным выполнением 
Чтобы просмотреть вложения, связанные с выбранным выполнением, можно использовать команду info журнала выполнения. Найдите идентификатор определенной операции выполнения в приведенном выше списке.

```azure-cli
$ az ml history info --run <run id> --artifact driver_log
```

Чтобы загрузить артефакты из выполнения, можно использовать следующую команду:

```azure-cli
# Stream a given attachment 
$ az ml history info --run <run id> --artifact <artifact location>
```

## <a name="step-6-promote-artifacts-of-a-run"></a>Шаг 6. Повышение уровня артефактов выполнения 
Одно из выполнений имеет лучшее значение AUC, поэтому его необходимо использовать для создания оценочной веб-службы для развертывания в рабочей среде. Чтобы сделать это, необходимо сначала повысить уровень артефактов в ресурсе.

```azure-cli
$ az ml history promote --run <run id> --artifact-path outputs/model.pkl --name model.pkl
```

При этом в каталоге проекта создается папка `assets` с файлом `model.pkl.link`. Этот файл ссылок используется для выбора ресурса повышенного уровня.

## <a name="step-7-download-the-files-to-be-operationalized"></a>Шаг 7. Скачивание файлов для использования
Скачайте модель с повышенным уровнем, чтобы использовать ее для создания веб-службы прогнозирования. 

```azure-cli
$ az ml asset download --link-file assets\pickle.link -d asset_download
```

## <a name="step-8-set-up-your-model-management-environment"></a>Шаг 8. Настройка рабочей среды управления моделями 
Создайте среду для развертывания веб-служб. Веб-службу можно запустить на локальном компьютере с помощью Docker. Ее также можно развернуть в кластере ACS для высокопроизводительных операций. 

```azure-cli
# Create new local operationalization environment
$ az ml env setup -l <supported Azure region> -n <env name>
# Once setup is complete, set your environment for current context
$ az ml env set -g <resource group name> -n <env name>
```

## <a name="step-9-create-a-model-management-account"></a>Шаг 9. Создание учетной записи управления моделями 
Для развертывания и отслеживания моделей в рабочей среде требуется учетная запись управления моделями. 

```azure-cli
$ az ml account modelmanagement create -n <model management account name> -g <resource group name> -l <supported Azure region>
```

## <a name="step-10-create-a-web-service"></a>Шаг 10. Создание веб-службы
Создайте веб-службу, которая возвращает прогноз с использованием развернутой модели. 

```azure-cli
$ az ml service create realtime -m asset_download/model.pkl -f score_iris.py -r python –n <web service name>
```

## <a name="step-11-run-the-web-service"></a>Шаг 11. Запуск веб-службы
Используя идентификатор веб-службы из выходных данных предыдущего шага, можно вызвать веб-службу и протестировать ее. 

```azure-cli
# Get web service usage infomration
$ az ml service usage realtime -i <web service id>

# Call the web service with the run command:
$ az ml service run realtime -i <web service id> -d <input data>
```

## <a name="step-12-deleting-all-the-resources"></a>Шаг 12. Удаление всех ресурсов 
Закончим работу с этим руководством, удалив все созданные ресурсы, если вы не хотите продолжать работать с ними. 

Для этого удалите группу ресурсов, содержащую ресурсы. 

```azure-cli
az group delete --name <resource group name>
```

## <a name="next-steps"></a>Дальнейшие действия
В этом руководстве было продемонстрировано, как использовать службу "Машинное обучение Azure" для: 
> [!div class="checklist"]
> * настройки учетной записи Экспериментирования и создания рабочей области;
> * создания проектов;
> * отправки эксперимента нескольким целевым объектам вычислений;
> * повышения уровня и регистрации обученной модели;
> * создания учетной записи управления моделями;
> * создания среды для развертывания веб-служб;
> * развертывания веб-службы и оценки по новым данным.
