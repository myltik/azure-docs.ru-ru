---
title: Создание функции в Linux из пользовательского образа (предварительная версия) | Документация Майкрософт
description: Узнайте, как создавать функции Azure под управлением пользовательского образа Linux.
services: functions
keywords: ''
author: ggailey777
ms.author: glenga
ms.date: 11/15/2017
ms.topic: tutorial
ms.service: functions
ms.custom: mvc
ms.devlang: azure-cli
manager: cfowler
ms.openlocfilehash: 758906126b42c103853e0047bb19d2e96a84fae6
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/23/2018
---
# <a name="create-a-function-on-linux-using-a-custom-image-preview"></a>Создание функции в Linux из пользовательского образа (предварительная версия)

Решение "Функции Azure" позволяет размещать в Linux собственные функции в пользовательском контейнере. Кроме того, вы можете [использовать для размещения контейнер по умолчанию в службе приложений Azure](functions-create-first-azure-function-azure-cli-linux.md). Эта функция сейчас доступна в режиме в предварительной версии. Для ее работы требуется [среда выполнения функций 2.0](functions-versions.md), которая также доступна в режиме предварительной версии.

Из этого руководства вы узнаете, как развернуть приложение-функцию в виде пользовательского образа Docker. Этот метод полезен, если вам нужно настроить встроенный образ контейнера службы приложений. Пользовательский образ может быть удобен, если вы используете конкретную версию языка или особые зависимости и (или) конфигурации, не поддерживаемые в рамках встроенного образа.

Из этого руководства вы узнаете, как с помощью решения "Функции Azure" создать пользовательский образ и отправить его в концентратор Docker. Затем этот образ станет источником для развертывания приложения-функции на платформе Linux. Сборка и отправка образа выполняются с помощью Docker. Чтобы создать приложение-функцию и развернуть образ из концентратора Docker, используйте Azure CLI. 

Из этого руководства вы узнаете, как выполнять такие задачи:

> [!div class="checklist"]
> * Сборка пользовательского образа с помощью Docker.
> * Публикация пользовательского образа в реестре контейнеров. 
> * Создание учетной записи хранения Azure. 
> * Создание плана службы приложений Linux. 
> * Развертывание приложения-функции из концентратора Docker.
> * Добавление параметров приложения в приложение-функцию. 

Описанные далее действия можно выполнять на компьютерах с Mac, Windows или Linux.  

## <a name="prerequisites"></a>предварительным требованиям

Для работы с этим учебником необходимы указанные ниже компоненты.

* [Git.](https://git-scm.com/downloads)
* Активная [подписка Azure](https://azure.microsoft.com/pricing/free-trial/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* [Docker](https://docs.docker.com/get-started/#setup)
* [Учетная запись Docker Hub](https://docs.docker.com/docker-id/).

[!INCLUDE [Free trial note](../../includes/quickstarts-free-trial-note.md)]

## <a name="download-the-sample"></a>Скачивание примера приложения

В окне терминала выполните следующую команду, чтобы клонировать репозиторий с примером приложения на локальный компьютер, а затем перейдите в каталог, в котором содержится образец кода.

```bash
git clone https://github.com/Azure-Samples/functions-linux-custom-image.git --config core.autocrlf=input
cd functions-linux-custom-image
```

## <a name="build-the-image-from-the-docker-file"></a>Создание образа на основе файла Docker

В репозитории Git откройте файл _Dockerfile_. В этом файле описана среда для запуска приложения-функции на платформе Linux. 

```docker
# Base the image on the built-in Azure Functions Linux image.
FROM microsoft/azure-functions-runtime:2.0.0-jessie
ENV AzureWebJobsScriptRoot=/home/site/wwwroot

# Add files from this repo to the root site folder.
COPY . /home/site/wwwroot 
```
>[!NOTE]
> Когда вы размещаете образ в частном реестре контейнеров, не забудьте добавить в приложение-функцию параметры подключения, указав переменные среды (**ENV**) в Dockerfile. Мы не можем быть уверены, что для работы с руководством вы используете частный реестр, поэтому из соображений безопасности файл настроен таким образом, что параметры подключения нужно [добавить через Azure CLI после развертывания ](#configure-the-function-app).   

### <a name="run-the-build-command"></a>Запустите команду сборки.
Чтобы создать образ Docker, выполните команду `docker build`, указав имя `mydockerimage` и тег `v1.0.0`. Замените `<docker-id>` идентификатором вашей учетной записи Docker Hub.

```bash
docker build --tag <docker-id>/mydockerimage:v1.0.0 .
```

После выполнения команды должен появиться результат, аналогичный приведенному ниже.

```bash
Sending build context to Docker daemon  169.5kB
Step 1/3 : FROM microsoft/azure-functions-runtime:v2.0.0-jessie
v2.0.0-jessie: Pulling from microsoft/azure-functions-runtime
b178b12f7913: Pull complete
2d9ce077a781: Pull complete
4775d4ba55c8: Pull complete
Digest: sha256:073f45fc167b3b5c6642ef4b3c99064430d6b17507095...
Status: Downloaded newer image for microsoft/azure-functions-runtime:v2.0.0-jessie
 ---> 217799efa500
Step 2/3 : ENV AzureWebJobsScriptRoot /home/site/wwwroot
 ---> Running in 528fa2077d17
 ---> 7cc6323b8ae0
Removing intermediate container 528fa2077d17
Step 3/3 : COPY . /home/site/wwwroot
 ---> 5bdac9878423
Successfully built 5bdac9878423
Successfully tagged ggailey777/mydockerimage:v1.0.0
```

### <a name="test-the-image-locally"></a>Локальное тестирование образа
Чтобы проверить работоспособность образа, выполните образ Docker в локальном контейнере. Выполните команду [​​docker run](https://docs.docker.com/engine/reference/commandline/run/), указав имя и тег образа. Обязательно укажите порт с помощью аргумента `-p`.

```bash
docker run -p 8080:80 -it <docker-ID>/mydockerimage:v1.0.0
```

Пока пользовательский образ выполняется в локальном контейнере Docker, откройте в браузере адрес <http://localhost:8080> и убедитесь, что приложение-функция и контейнер работают правильно.

![Выполните локальное тестирование приложения-функции.](./media/functions-create-function-linux-custom-image/run-image-local-success.png)

Когда вы закончите проверку приложения-функции, остановите обработку в контейнере. Теперь этот пользовательский образ следует передать в учетную запись концентратора Docker.

## <a name="push-the-custom-image-to-docker-hub"></a>Отправка пользовательского образа в концентратор Docker

Реестр — это приложение, которое содержит образы и предоставляет образ служб и службы контейнеров. Чтобы сделать образ общедоступным, необходимо передать его в реестр. Docker Hub — это реестр образов Docker, который позволяет размещать ваши собственные репозитории, как общедоступные, так и частные. 

Чтобы отправить образ, вам нужно выполнить вход в концентратор Docker с помощью команды [docker login](https://docs.docker.com/engine/reference/commandline/login/). Вместо `<docker-id>` укажите имя вашей учетной записи, а когда в консоли появится запрос, введите пароль. Другие возможности ввода пароля для концентратора Docker описаны в [документации по команде docker login](https://docs.docker.com/engine/reference/commandline/login/).

```bash
docker login --username <docker-id> 
```

При успешном выполнении входа появится сообщение "Вход выполнен". После успешного входа примените команду [docker push](https://docs.docker.com/engine/reference/commandline/push/), чтобы передать образ в концентратор Docker.

```bash
docker push <docker-id>/mydockerimage:v1.0.0
```

Убедитесь, что передача завершилось успешно, проверив выходные данные команды.

```bash
The push refers to a repository [docker.io/<docker-id>/mydockerimage:v1.0.0]
24d81eb139bf: Pushed
fd9e998161c9: Mounted from microsoft/azure-functions-runtime
e7796c35add2: Mounted from microsoft/azure-functions-runtime
ae9a05b85848: Mounted from microsoft/azure-functions-runtime
45c86e20670d: Mounted from microsoft/azure-functions-runtime
v1.0.0: digest: sha256:be080d80770df71234eb893fbe4d... size: 2422
```
Теперь вы можете использовать этот образ для развертывания приложения-функции в Azure. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Если вы решили установить и использовать интерфейс командной строки локально, для работы с этим руководством вам понадобится Azure CLI 2.0.21 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0]( /cli/azure/install-azure-cli). 

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-linux-app-service-plan"></a>Создание плана службы приложений Linux

Сейчас размещение функций на платформе Linux не поддерживается в планах потребления. Необходимо использовать только план службы приложений для Linux Дополнительные сведения о размещении вы найдете в статье [Сравнение планов размещения для решения "Функции Azure"](functions-scale.md). 

[!INCLUDE [app-service-plan-no-h](../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]


## <a name="create-and-deploy-the-custom-image"></a>Создание и развертывание пользовательского образа

Выполнение функций происходит с помощью приложения-функции. Чтобы создать приложение-функцию из образа, размещенного в концентраторе Docker, используйте команду [az functionapp create](/cli/azure/functionapp#az_functionapp_create). 

В следующей команде замените `<app_name>` уникальным именем вашего приложения функции, а `<storage_name>` — именем учетной записи хранения. `<app_name>` используется по умолчанию в качестве домена DNS для приложения-функции. Поэтому это имя должно быть уникальным для всех приложений в Azure. Как и раньше, `<docker-id>` обозначает имя учетной записи Docker.

```azurecli-interactive
az functionapp create --name <app_name> --storage-account  <storage_name>  --resource-group myResourceGroup \
--plan myAppServicePlan --deployment-container-image-name <docker-id>/mydockerimage:v1.0.0 
```
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

Параметр _deployment-container-image-name_ определяет образ, размещенный в концентраторе Docker, из которого нужно создать приложение-функцию. 


## <a name="configure-the-function-app"></a>Настройка приложения-функции

Функции нужно предоставить строку подключения для подключения к учетной записи хранения по умолчанию. Если вы публикуете пользовательский образ в учетной записи частного контейнера, задайте эти параметры приложения через переменные среды в файле Dockerfile, используя [инструкции ENV](https://docs.docker.com/engine/reference/builder/#env) или аналогичный механизм. 

В нашем случае `<storage_account>` — это имя созданной учетной записи хранения. Получите строку подключения, выполнив команду [az storage account show-connection-string](/cli/azure/storage/account#show-connection-string). Добавьте эти параметры приложения в приложение-функцию с помощью команды [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings#az_functionapp_config_appsettings_set).

```azurecli-interactive
storageConnectionString=$(az storage account show-connection-string \
--resource-group myResourceGroup --name <storage_account> \
--query connectionString --output tsv)

az functionapp config appsettings set --name <function_app> \
--resource-group myResourceGroup \
--settings AzureWebJobsDashboard=$storageConnectionString \
AzureWebJobsStorage=$storageConnectionString
```

Теперь вы можете проверить, как выполняются ваши функции на платформе Linux в Azure.

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>Дополнительная информация

Из этого руководства вы узнали, как выполнить следующие задачи:

> [!div class="checklist"]
> * Сборка пользовательского образа с помощью Docker.
> * Публикация пользовательского образа в реестре контейнеров. 
> * Создание учетной записи хранения Azure. 
> * Создание плана службы приложений Linux. 
> * Развертывание приложения-функции из концентратора Docker.
> * Добавление параметров приложения в приложение-функцию.

Дополнительные сведения о локальной разработке функций Azure с помощью основных инструментов решения "Функции Azure".

> [!div class="nextstepaction"] 
> [Как программировать и тестировать функции Azure в локальной среде](functions-run-local.md)
