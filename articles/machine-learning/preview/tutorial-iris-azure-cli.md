---
title: "Руководство по функциям предварительной версии служб машинного обучения Azure. Интерфейс командной строки | Документация Майкрософт"
description: "В этом руководстве рассматриваются все шаги, необходимые для выполнения полной классификации цветков ириса с помощью интерфейса командной строки."
services: machine-learning
author: ahgyger
ms.author: ahgyger, ritbhat
manager: haining
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 09/20/2017
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: c2a3b9702afd99c29b64133a05515a1b5f395130
ms.contentlocale: ru-ru
ms.lasthandoff: 09/25/2017

---

# <a name="tutorial-classifying-iris-using-the-command-line-interface"></a>Руководство по классификации цветков ириса с помощью интерфейса командной строки
Служба "Машинное обучение Azure" (предварительная версия) — это полнофункциональное интегрированное аналитическое решение для специалистов по обработке и анализу данных. Оно помогает подготавливать данные, разрабатывать эксперименты и развертывать модели в масштабе облака.

Из этого руководства вы узнаете, как использовать функции предварительной версии служб машинного обучения Azure с помощью интерфейса командной строки для: 
> [!div class="checklist"]
> * настройки учетной записи Экспериментирования и создания рабочей области;
> * Создание проекта
> * отправки эксперимента нескольким целевым объектам вычислений;
> * повышения уровня и регистрации обученной модели;
> * развертывания веб-службы для оценки новых данных.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования
- Вам понадобится доступ к подписке Azure и разрешение для создания в ней ресурсов. 
- Необходимо установить приложение Azure Machine Learing Workbench, следуя [краткому руководству по установке и созданию](quickstart-installation.md). 

  >[!NOTE]
  >Azure Machine Learning Workbench требуется установить в локальной среде. Нужно только выполнить шаги, описанные в разделах об установке Azure Machine Learning Workbench, поскольку создание учетной записи и создание новых этапов проекта будет выполняться с помощью командной строки, как описано в этой статье.
 
## <a name="getting-started"></a>Приступая к работе
Интерфейс командной строки (CLI) Машинного обучения Azure позволяет выполнять все задачи, необходимые для полного рабочего процесса обработки и анализа данных. Вы можете использовать средства интерфейса командной строки следующим образом.

### <a name="option-1-launch-azure-ml-cli-from-azure-ml-workbench-log-in-dialog-box"></a>Вариант 1. Запуск командной строки Машинного обучения Azure из диалогового окна входа в Azure ML Workbench
Если вы впервые выполняете вход в Azure ML Workbench и у вас нет доступа к учетной записи Экспериментирования, появится следующий экран:

![учетная запись не найдена](media/tutorial-iris-azure-cli/no_account_found.png)

Щелкните ссылку **окно командной строки** в диалоговом окне, чтобы открыть окно командной строки.

### <a name="option-2-launch-azure-ml-cli-from-azure-ml-workbench-app"></a>Вариант 2. Запуск командной строки Машинного обучения Azure из приложения Azure ML Workbench
Если вы уже получили доступ к учетной записи Экспериментирования, вы сможете успешно выполнить вход. Затем можно открыть окно командной строки, щелкнув меню **Файл** --> **Open Command Prompt** (Открыть командную строку).

### <a name="option-3-enable-azure-ml-cli-in-an-arbitrary-command-line-window"></a>Вариант 3. Включение командной строки Машинного обучения Azure в произвольном окне командной строки
Командную строку Машинного обучения Azure можно также включить в любом окне командной строки. Откройте окно командной строки и введите следующие команды:

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

Затем необходимо установить правильный контекст в вашем интерфейсе командной строки для доступа к ресурсам Azure и управления ими.
 
```bash
az login
az account set -s d128f140-94e6-1206-80a7-954b9d27d007
```

> [!TIP]
> Чтобы получить список всех подписок, выполните следующую команду: 
>```
>az account list -o table
>```

## <a name="step-2-create-a-new-azure-machine-learning-experimentation-account-and-workspace"></a>Шаг 2. Создание учетной записи Экспериментирования службы машинного обучения Azure и рабочей области
Начнем с создания учетной записи Экспериментирования и рабочей области. Дополнительные сведения об учетных записях Экспериментирования и рабочих областях см. в статье [Azure Machine Learning - Concepts](overview-general-concepts.md) (Основные понятия Машинного обучения Azure). 

> [!NOTE]
> Для учетных записей Экспериментирования требуется учетная запись хранения, которая используется для хранения выходных данных в результате выполнения эксперимента. Имя учетной записи хранения должно быть уникальным в Azure, так как отсутствует URL-адрес, связанный с ним. Имя учетной записи Экспериментирования используется (от вашего имени) для создания учетной записи хранения. Обязательно используйте уникальное имя, или появится сообщение об ошибке, например _Учетная запись хранения с именем amlsampleexp уже занята_. Кроме того, можно использовать аргумент `--storage`, чтобы воспользоваться существующей учетной записью хранения.

```bash
az group create --name amlsample --location eastus2
az ml account experimentation create --name amlsampleexp --resource-group amlsample
az ml account experimentation create --name amlsampleexp --resource-group amlsample --storage /subscriptions/6d48cffb-b787-47bd-8d20-1696afa33b67/resourceGroups/existing/providers/Microsoft.Storage/storageAccounts/mystorageacct
az ml workspace create --name amlsamplew --account amlsampleexp --resource-group amlsample
```

## <a name="step-2a-optional-share-a-workspace-with-co-worker"></a>Шаг 2.a. Предоставление общего доступа к рабочей области (необязательно)
Здесь вы узнаете, как предоставить коллеге доступ к рабочей области. Шаги для предоставления общего доступа к учетной записи Экспериментирования или проекту аналогичны. Отличается только способ получения идентификатора ресурса Azure.

```bash
az ml workspace show --name amlsamplew --account amlsampleexp --resource-group amlsample 
az role assignment create --assignee ahgyger@microsoft.com --role owner --scope "/subscriptions/d128f140-94e6-4175-87a7-954b9d27db16/resourceGroups/amlsample/providers/Microsoft.MachineLearningExperimentation/accounts/amlsampleexp/workspaces/amlsamplew"
```

> [!TIP]
> Необходимо использовать реальный адрес электронной почты сотрудника, а не псевдоним. 

## <a name="step-3-create-a-new-project"></a>Шаг 3. Создание нового проекта
Теперь необходимо создать проект. Это можно сделать несколькими способами.

### <a name="create-a-new-blank-project"></a>Создание пустого проекта

```bash
az ml project create --name 9_25_1 --workspace amlsamplew --account amlsampleexp --resource-group amlsample --path c:\Users\ahgyger\Documents\AMLworkbench_Demo\9_25\
```

### <a name="create-a-new-project-with-template-files"></a>Создание проекта с файлами шаблонов
Файлы шаблонов не являются примерами, но они дают оболочку для нового проекта. Проект предварительно заполнен двумя файлами: `train.py` и `score.py`.

```bash
az ml project create --name 9_25_1 --workspace amlsamplew --account amlsampleexp --resource-group amlsample --path c:\Users\ahgyger\Documents\AMLworkbench_Demo\ --template
```

### <a name="create-a-new-project-associated-with-a-cloud-git-repository"></a>Создание проекта, связанного с облачным репозиторием Git
Можно создать проект, связанный с облачным репозиторием Git. Каждый раз при отправке эксперимента будет создаваться мгновенный снимок содержимого проекта в виде фиксации Git в ветви журнала выполнения. Дополнительные сведения см. в статье [Использование репозитория Git в проекте Azure Machine Learning Workbench](using-git-ml-project.md).

> [!NOTE]
> Машинное обучение Azure поддерживает только пустой командный проект (VSTS) с Git в качестве системы управления версиями.

> [!TIP]
> При получении ошибки Repository url might be invalid or user might not have access (URL-адрес репозитория недействителен, или у пользователя нет доступа) вы можете создать токен безопасности в VSTS (Security, add personal access tokens (Безопасность > Добавить токены доступа)) и использовать аргумент __vststoken__ при создании своего проекта. 

```bash
az ml project create --name 9_25_2 --workspace amlsamplew --account amlsampleexp --resource-group amlsample --path c:\Users\ahgyger\Documents\AMLworkbench_Demo\ --repo https://ahgyger.visualstudio.com/AMLWorkbench/_git/9_25 --vststoken m2fholwwrcn7u4nrdqfxx007u5rztjfhgofnemvuvtue6pbwo3sa
```

### <a name="sample_create"></a>Создание проекта с помощью примера из сети
В этом примере мы используем шаблон из проекта github при создании нашего проекта. 

```bash
# List the project samples
az ml project sample list

# Create a new project from a sample
az ml project create --name 9_25_3 --workspace amlsamplew --account amlsampleexp --resource-group amlsample --path c:\Users\ahgyger\Documents\AMLworkbench_Demo\ --repo https://ahgyger.visualstudio.com/AMLWorkbench/_git/9_25 --template-url https://github.com/MicrosoftDocs/MachineLearningSamples-Iris
```

После создания проекта выберите его каталог, прежде чем перейти к следующему шагу.

```bash
cd c:\Users\ahgyger\Documents\AMLworkbench_Demo\9_25\9_25_1
```

## <a name="step-4-run-the-training-experiment"></a>Шаг 4. Выполнение обучающего эксперимента 
В приведенных ниже действиях предполагается, что у вас уже есть проект с примером классификации цветков ириса (см. раздел [Создание проекта с помощью примера из сети](#sample_create)).

### <a name="prepare-your-environment"></a>Подготовка среды 
Для примера классификации цветков ириса необходимо установить matplotlib.

```bash
pip install matplotlib
```

###  <a name="submit-the-experiment"></a>Отправка эксперимента

```bash
# Execute the file
az ml experiment submit --run-configuration local iris_sklearn.py
```

### <a name="iterate-on-your-experiment-with-descending-regularization-rates"></a>Выполнение итерации эксперимента с понижением показателей регуляризации
Используя творческий потенциал, можно легко объединить скрипт Python, который отправляет эксперименты с разными показателями регуляризации. (Может потребоваться изменить файл, чтобы указать путь к необходимому проекту.)

```bash
python run.py
```

## <a name="step-5-view-run-history"></a>Шаг 5. Просмотр журнала выполнения
Следующая команда перечисляет все предыдущие выполнения. 

```bash
az ml history list -o table
```
В результате отобразится список всех выполнений, относящихся к этому проекту. Вы увидите, что метрики точности и показатели регуляризации также указаны. Это позволяет легко идентифицировать лучшее выполнение в списке.

## <a name="step-5a-view-attachment-created-by-a-given-run"></a>Шаг 5.a. Просмотр вложения, созданного выбранным выполнением 
Чтобы просмотреть вложения, связанные с выбранным выполнением, можно использовать команду info журнала выполнения.

```bash
az ml history info --run 9_16_4_1505589545267 --artifact driver_log
```

Чтобы загрузить артефакты из выполнения, можно использовать следующую команду:

```bash
# Stream a given attachment 
az ml history info --run <run id> --artifact <artifact location>
```

## <a name="step-6-promote-artifacts-of-a-run"></a>Шаг 6. Повышение уровня артефактов выполнения 
Одно из выполнений имеет лучшее значение AUC, поэтому мы будем использовать его для создания оценочной веб-службы для развертывания в рабочей среде. Чтобы сделать это, необходимо сначала повысить уровень артефактов в ресурсе.

```bash
az ml history promote --run 9_25_1505346632975 --artifact-path outputs/model.pkl --name model.pkl
```

При этом будет создана папка __assets__ в каталоге проекта, содержащая pickle.link. Этот файл-ссылка используется для отслеживания версии файла повышенного уровня.

## <a name="step-7-download-the-files-to-be-operationalized"></a>Шаг 7. Скачивание файлов для использования
Теперь нужно скачать файлы с повышенным уровнем, чтобы их можно было использовать для создания веб-службы прогнозирования. 

```bash
az ml asset download --link-file assets\pickle.link -d asset_download
```

## <a name="step-8-setup-your-model-management-environment"></a>Шаг 8. Настройка рабочей среды управления моделями 
Создадим среду для развертывания веб-служб. Веб-службу можно запустить на локальном компьютере с помощью Docker. Ее также можно развернуть в кластере ACS для высокопроизводительных операций. 

```bash
# Create new environment
az ml env setup -l eastus2 -n amlsamplesenv
# Once setup is complete, set your environment for current context
az ml env set -g amlsamplesenvrg -n amlsamplesenv
```

## <a name="step-9-create-a-model-management-account"></a>Шаг 9. Создание учетной записи управления моделями 
Для развертывания и отслеживания моделей в рабочей среде требуется учетная запись управления моделями. 

```bash
az ml account modelmanagement create -n amlsamplesacct -g amlsamplesenvrg -l eastus2
```

## <a name="step-10-create-a-web-service"></a>Шаг 10. Создание веб-службы
Затем создадим веб-службу, которая возвращает прогноз с использованием развернутой модели. 

```bash
az ml service create realtime -m modelfilename -f score.py -r python –n amlsamplews
```

## <a name="step-10-run-the-web-service"></a>Шаг 10. Запуск веб-службы
Используя идентификатор веб-службы из выходных данных предыдущего шага, можно вызвать веб-службу и протестировать ее. 

```
# Get web service usage infomration
az ml service usage realtime -i <web service id>

# Call the web service with the run command:
az ml service run realtime -i <web service id> -d <input data>
```

## <a name="deleting-all-the-resources"></a>Удаление всех ресурсов 
Закончим работу с этим руководством, удалив все созданные ресурсы, если вы не хотите продолжать работать с ними. 

Чтобы сделать это, просто удалите группу ресурсов, содержащую все ресурсы. 

```bash
az group delete --name amlsample
az group delete --name amlsamplesenvrg
```

## <a name="next-steps"></a>Дальнейшие действия
В этом руководстве было продемонстрировано, как использовать функции предварительной версии служб машинного обучения Azure для: 
> [!div class="checklist"]
> * настройки учетной записи Экспериментирования и создания рабочей области;
> * создания проектов;
> * отправки эксперимента нескольким целевым объектам вычислений;
> * повышения уровня и регистрации обученной модели;
> * создания учетной записи управления моделями;
> * создания среды для развертывания веб-службы;
> * развертывания веб-службы и оценки по новым данным.


