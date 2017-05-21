---
title: "Создание первой функции с помощью Azure CLI | Документация Майкрософт"
description: "Узнайте, как создать первую функцию Azure, выполняемую без сервера, с помощью Azure CLI."
services: functions
keywords: 
author: ggailey777
ms.author: glenga
ms.assetid: 674a01a7-fd34-4775-8b69-893182742ae0
ms.date: 05/02/2017
ms.topic: hero-article
ms.service: functions
ms.devlang: azure-cli
manager: erikre
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: ef9d65ae5ad0792230e1b8c0d7ed123c129f0f59
ms.contentlocale: ru-ru
ms.lasthandoff: 05/10/2017

---

# <a name="create-your-first-function-using-the-azure-cli"></a>Создание первой функции с помощью Azure CLI

В этом кратком руководстве описано, как создать свою первую функцию с помощью Функций Azure. Используйте Azure CLI для создания приложения-функции, которое является независимой от сервера инфраструктурой, где размещается ваша функция. Код функции развертывается из примера репозитория GitHub.    

Выполните действия, приведенные ниже, с помощью компьютера Mac, Windows или Linux. На все это вам понадобится около 5 минут.

## <a name="prerequisites"></a>Предварительные требования 

Перед выполнением этого примера вам понадобится следующее:

+ Активная учетная запись [GitHub](https://github.com). 
+ [Установленный Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli). 
+ Активная подписка Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="log-in-to-azure"></a>Вход в Azure

Войдите в подписку Azure с помощью команды [az login](/cli/azure/#login) и следуйте инструкциям на экране. 

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Создание группы ресурсов

Создайте группу ресурсов с помощью команды [az group create](/cli/azure/group#create). Группа ресурсов Azure — это логический контейнер, в котором происходит развертывание ресурсов Azure (приложений-функций, баз данных и учетных записей хранения) и управление ими.

В следующем примере создается группа ресурсов `myResourceGroup`:

```azurecli
az group create --name myResourceGroup --location westeurope
```
## <a name="create-an-azure-storage-account"></a>Создание учетной записи хранения Azure

Функции используют учетную запись хранения Azure для сохранения состояния и других сведений о функции. Создайте учетную запись хранения в созданной вами группе ресурсов с помощью команды [az storage account create](/cli/azure/storage/account#create).

В следующей команде замените `<storage_name>` глобально уникальным именем своей учетной записи хранения везде, где встречается этот заполнитель. Имя учетной записи хранения должно содержать от 3 до 24 символов и состоять только из цифр и строчных букв.

```azurecli
az storage account create --name <storage_name> --location westeurope --resource-group myResourceGroup --sku Standard_LRS
```

После создания учетной записи хранения в Azure CLI отобразятся следующие сведения:

```json
{
  "creationTime": "2017-04-15T17:14:39.320307+00:00",
  "id": "/subscriptions/bbbef702-e769-477b-9f16-bc4d3aa97387/resourceGroups/myresourcegroup/...",
  "kind": "Storage",
  "location": "westeurope",
  "name": "myfunctionappstorage",
  "primaryEndpoints": {
    "blob": "https://myfunctionappstorage.blob.core.windows.net/",
    "file": "https://myfunctionappstorage.file.core.windows.net/",
    "queue": "https://myfunctionappstorage.queue.core.windows.net/",
    "table": "https://myfunctionappstorage.table.core.windows.net/"
  },
     ....
    // Remaining output has been truncated for readability.
}
```

## <a name="create-a-function-app"></a>Создание приложения-функции

Для выполнения функций вам понадобится приложение-функция, предоставляющее среду для выполнения кода функции без сервера. Это позволяет вам группировать функции в логические единицы и упростить развертывания и совместное использование ресурсов, а также управление ими. Создайте приложение-функцию с помощью команды [az functionapp create](/cli/azure/functionapp#create). 

В следующей команде замените `<app_name>` уникальным именем вашего приложения функции, а `<storage_name>` — именем учетной записи хранения. `<app_name>` используется по умолчанию в качестве домена DNS для приложения-функции. Поэтому это имя должно быть уникальным для всех приложений в Azure. 

```azurecli
az functionapp create --name <app_name> --storage-account  <storage_name>  --resource-group myResourceGroup --consumption-plan-location westeurope
```
Приложение-функция по умолчанию создается с планом потребления. Это означает, что ресурсы добавляются динамически по мере необходимости для вашей функции, а вы платите только при выполнении функций. Дополнительные сведения см. в статье [Выбор правильного плана обслуживания для Функций Azure](functions-scale.md). 

После создания приложения-функции в Azure CLI отображаются следующие сведения:

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "containerSize": 1536,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "quickstart.azurewebsites.net",
  "enabled": true,
  "enabledHostNames": [
    "quickstart.azurewebsites.net",
    "quickstart.scm.azurewebsites.net"
  ],
   ....
    // Remaining output has been truncated for readability.
}
```

Теперь, когда у вас есть приложение-функция, вы можете развернуть фактический код функции из примера репозитория GitHub.

## <a name="deploy-your-function-code"></a>Развертывание кода функции  

Существует несколько способов создания кода функции в новом приложении-функции. Этот раздел связан с примером репозитория Azure. В следующем коде замените заполнитель `<app_name>` именем созданного приложения-функции. 

```azurecli
az functionapp deployment source config --name <app_name> --resource-group myResourceGroup --repo-url https://github.com/Azure-Samples/functions-quickstart --branch master --manual-integration
```
После задания источника развертывания в Azure CLI появятся следующие сведения (значения null удалены для удобства):

```json
{
  "branch": "master",
  "deploymentRollbackEnabled": false,
  "id": "/subscriptions/bbbef702-e769-477b-9f16-bc4d3aa97387/resourceGroups/myResourceGroup/...",
  "isManualIntegration": true,
  "isMercurial": false,
  "location": "West Europe",
  "name": "quickstart",
  "repoUrl": "https://github.com/Azure-Samples/functions-quickstart",
  "resourceGroup": "myResourceGroup",
  "type": "Microsoft.Web/sites/sourcecontrols"
}
```

## <a name="test-the-function"></a>Проверка функции

Используйте cURL, чтобы проверить развернутую функцию на компьютере Mac или Linux, или Bash для компьютера с Windows. Выполните следующую команду cURL, заменив заполнитель `<app_name>` именем вашего приложения-функции. Добавьте строку запроса `&name=<yourname>` к URL-адресу.

```bash
curl http://<app_name>.azurewebsites.net/api/HttpTriggerJS1?name=<yourname>
```  

![Ответ функции в браузере](./media/functions-create-first-azure-function-azure-cli/functions-azure-cli-function-test-curl.png)  

Если у вас нет доступного cURL в командной строке, введите тот же URL-адрес в адресной строке браузера. Еще раз замените заполнитель `<app_name>` именем вашего приложения-функции, добавьте строку запроса `&name=<yourname>` к URL-адресу и выполните запрос. 

    http://<app_name>.azurewebsites.net/api/HttpTriggerJS1?name=<yourname>
   
![Ответ функции в браузере](./media/functions-create-first-azure-function-azure-cli/functions-azure-cli-function-test-browser.png)  

## <a name="clean-up-resources"></a>Очистка ресурсов

Другие краткие руководства в этой коллекции созданы на основе этого документа. Если вы планируете продолжать работу с этими руководствами по быстрому запуску или обычными руководствами, не удаляйте созданные ресурсы. Если вы не планируете продолжать работу, удалите все созданные ресурсы с помощью следующей команды:

```azurecli
az group delete --name myResourceGroup
```
При появлении запроса введите `y`.

## <a name="next-steps"></a>Дальнейшие действия

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

