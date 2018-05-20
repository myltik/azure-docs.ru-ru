---
title: Руководство по интеграции функций Azure со службой Azure SignalR | Документация Майкрософт
description: В этом руководстве вы узнаете, как использовать функции Azure со службой Azure SignalR
services: signalr
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: signalr
ms.workload: tbd
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/24/2018
ms.author: wesmc
ms.openlocfilehash: b1bb6b3fe545d5a42fa985ab85bd7a914128e56b
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
---
# <a name="tutorial-integrate-azure-functions-with-azure-signalr-service"></a>Руководство по интеграции функций Azure со службой Azure SignalR

Это руководство основывается на приложении комнаты чата, созданного в предыдущих руководствах. Если вы еще не закончили работу с [руководством по созданию чата со службой SignalR](signalr-quickstart-dotnet-core.md) и [руководством по аутентификации в службе Azure SignalR](./signalr-authenticate-oauth.md), сначала выполните эти упражнения. 

Обычный сценарий с приложениями, выполняющимися в режиме реального времени, — обновления содержимого происходят с сервера и публикуются в веб-клиентах. [Функции Azure](../azure-functions/functions-overview.md) отлично подходят для создания этих обновлений содержимого. Ключевым преимуществом использования функций Azure является то, что вы можете запускать свой код по требованию, не беспокоясь об архитектуре целого приложения или инфраструктуре для его запуска. Вы платите только за время, когда ваш код фактически выполняется.  

В этом сценарии могут возникать проблемы при попытке использовать SignalR, так как эта служба пытается поддерживать соединение между клиентом и сервером для передачи обновлений содержимого. Код выполняется только по запросу, поэтому соединение не будет поддерживаться. Однако служба Azure SignalR может поддерживать этот сценарий, так как она управляет подключениями во время выполнения.

В этом руководстве вы будете использовать функции Azure для формирования сообщений с использованием функции таймера в начале каждой минуты. Функция будет публиковать сообщения всем клиентам чата, созданного в предыдущих руководствах. Дополнительные сведения о функциях таймера см. в [этой статье](../azure-functions/functions-create-scheduled-function.md).

Вы можете использовать любой редактор кода для выполнения шагов в этом кратком руководстве. Однако [Visual Studio Code](https://code.visualstudio.com/) является отличным вариантом, доступным на платформах Windows, MacOS и Linux.

Код для этого руководства можно загрузить из [репозитория примеров AzureSignalR на GitHub](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/Timer).

![Приложение чата с сообщениями сервера](./media/signalr-integrate-functions/signalr-functions-complete.png)

Из этого руководства вы узнаете, как выполнять такие задачи:

> [!div class="checklist"]
> * Создание функции таймера с использованием решения "Функции Azure" и Azure CLI.
> * Настройка функции таймера для развертывания в локальном репозитории Git.
> * Подключение таймера к службе SignalR, чтобы каждую минуту передавать обновления.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>предварительным требованиям

Для работы с этим руководством необходимо следующее:

* [Git.](https://git-scm.com/)
* [Базовый пакет SDK для .NET](https://www.microsoft.com/net/download/windows) 
* [Настроенный Azure Cloud Shell.](https://docs.microsoft.com/azure/cloud-shell/quickstart)
* Скачанный или клонированный репозиторий [примеров AzureSignalR](https://github.com/aspnet/AzureSignalR-samples), доступный на GitHub.


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-function-app"></a>Создание приложения-функции

Нужно создать приложение-функцию для определения среды выполнения ваших функций. Приложение-функция также позволяет группировать несколько функций в логические единицы для упрощения управления, развертывания и совместного использования ресурсов. Дополнительные сведения см. в статье [Создание первой функции с помощью Azure CLI](../azure-functions/functions-create-first-azure-function-azure-cli.md).

В этом разделе вы будете использовать Azure Cloud Shell для создания приложения-функции Azure, настроенного для развертывания из локального репозитория Git. 

Ресурсы приложения-функции нужно создать в той же группе ресурсов, которая использовалась в предыдущих руководствах. Этот подход упрощает управление всеми ресурсами, используемыми в руководствах.

Скопируйте скрипт ниже в текстовый редактор и замените значения параметров-переменных значениями ваших ресурсов. Скопируйте и вставьте обновленный скрипт в Azure Cloud Shell и нажмите клавишу **ВВОД**, чтобы создать учетную запись хранения и приложение-функцию.

```azurecli-interactive
#====================================================================
#=== Update these variables to match your values from previous    === 
#=== tutorials.                                                   ===
#====================================================================
ResourceGroupName=SignalRTestResources
location=eastus

#====================================================================
#=== Update these variables for the new function app and storage  ===
#=== account.                                                     ===
#====================================================================
functionappName=mysignalfunctionapp
storageAccountName=mystorageaccount

# Create a storage account to hold function app code and settings
az storage account create --resource-group $ResourceGroupName \
--name $storageAccountName \
--location $location --sku Standard_LRS

# Create the function app
az functionapp create --resource-group $ResourceGroupName \
--name $functionappName \
--consumption-plan-location $location \
--storage-account $storageAccountName

```

| Параметр | ОПИСАНИЕ |
| -------------------- | --------------- |
| ResourceGroupName | Это имя группы ресурсов было предложено в предыдущих руководствах. Рекомендуется группировать все ресурсы, используемые в этих руководствах, вместе. Используйте ту же группу ресурсов, что и в предыдущих руководствах. | 
| location | Укажите для этой переменной то же расположение, которое вы использовали для создания группы ресурсов в предыдущих руководствах. | 
| functionappName | Укажите для этой переменной уникальное имя вашего нового приложения-функции. Например, signalrfunctionapp22665120. | 
| storageAccountName | Введите имя для новой учетной записи хранения, в которой будут храниться код и параметры приложения-функции. | 



## <a name="configure-the-function-app"></a>Настройка приложения-функции

В этом разделе вы настроите приложение-функцию, указав параметр приложения, содержащий строку подключения ресурса службы Azure SignalR. Код функции будет использовать этот параметр для подключения и публикации сообщений в чате. Вы также настроите приложение-функцию для развертывания из локального репозитория Git.

Скопируйте скрипт ниже и замените значения параметров. Вставьте обновленный скрипт в Azure Cloud Shell и нажмите клавишу **ВВОД**.

```azurecli-interactive
#====================================================================
#=== Update these variables to match your resources.              === 
#====================================================================
ResourceGroupName=SignalRTestResources
functionappName=mysignalfunctionapp

#========================================================================
#=== Replace this value with the connection string for your           ===
#=== SignalR Service resource.                                        ===
#========================================================================
connstring="Endpoint=<service_endpoint>;AccessKey=<access_key>;"

# Add the SignalR Service connection string app setting
az functionapp config appsettings set --resource-group $ResourceGroupName \
    --name $functionappName \
    --setting "Azure:SignalR:ConnectionString=$connstring"

# configure for deployment from a local Git repository
az functionapp deployment source config-local-git --name $functionappName \
    --resource-group $ResourceGroupName

```

| Параметр | ОПИСАНИЕ |
| -------------------- | --------------- |
| ResourceGroupName | Это имя группы ресурсов было предложено в предыдущих руководствах. Рекомендуется группировать все ресурсы, используемые в этих руководствах, вместе. Используйте ту же группу ресурсов, что и в предыдущих руководствах. | 
| functionappName | Укажите для этой переменной уникальное имя вашего нового приложения-функции. Например, signalrfunctionapp22665120. | 
| connstring | Введите строку подключения для ресурса службы SignalR. Вы можете получить эту строку подключения со страницы ресурсов службы SignalR на портале Azure, щелкнув **Ключи** в разделе **Параметры**. | 



Запишите URL-адрес развертывания Git, возвращенный из последней команды. Вы будете использовать этот URL-адрес для развертывания кода функции.


## <a name="the-timer-function"></a>Функция таймера

Пример функции таймера расположен в каталоге */samples/Timer* вашего пакета скачивания или клоне репозитория [AzureSignalR-sample](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/Timer) на GitHub. Код функции таймера находится в файле *TimerFunction.cs*. Этот код использует строку подключения, хранящуюся с ключом конфигурации по умолчанию (*Azure:SignalR:ConnectionString*), чтобы создать прокси-сервер для концентратора. Так как код функции работает на стороне сервера, нет причин требовать, чтобы он аутентифицировался как обычный клиент. Код является доверенным и может использовать строку подключения. Используя этот прокси-сервер, код функции может вызывать любой из методов, определенных вами в своем концентраторе. Код вызывает метод `BroadcastMessage` для публикации сообщения, содержащего текущее время запуска триггера.

Триггером для кода функции является *timerTrigger*, определенный в привязках в файле *TimerFunction/function.json*. Он содержит [выражение CRON](https://wikipedia.org/wiki/Cron#CRON_expression), активирующее срабатывание триггера таймера в начале каждой минуты.

```json
{
  "bindings": [
    {
      "type": "timerTrigger",
      "schedule": "0 * * * * *",
      "useMonitor": true,
      "runOnStartup": false,
      "name": "myTimer"
    }
  ],
  "disabled": false,
  "scriptFile": "../Timer.dll",
  "entryPoint": "Timer.TimerFunction.Run"
}
```


## <a name="building-the-timer-function"></a>Создание функции таймера

Используйте [.NET Core CLI](https://docs.microsoft.com/dotnet/core/tools/) в следующих шагах, чтобы создать функцию и подготовить ее для развертывания:

1. Перейдите в подкаталог */samples/Timer* вашего пакета скачивания или клон репозитория [AzureSignalR-sample](https://github.com/aspnet/AzureSignalR-samples) на GitHub.

2. Восстановите пакеты NuGet, используя следующую команду:

        dotnet restore

3. Создайте приложение-функцию *таймера*, используя следующую команду:

        msbuild /p:Configuration=Release


## <a name="create-and-deploy-the-local-git-repo"></a>Создание и развертывание локального репозитория Git

1. В оболочке Git перейдите в подкаталог сборки */samples/Timer/bin/Release/net461*.

        cd ./AzureSignalR-samples/samples/Timer/bin/Release/net461

2. Инициализируйте каталог как новый репозиторий Git, используя следующую команду:

        git init

3. Добавьте новую фиксацию для всех файлов в каталоге сборки.

        git add -A
        git commit -v -a -m "Initial Timer function commit"        

4. Добавьте удаленную конечную точку для URL-адреса развертывания Git, который вы сохранили во время настройки вашего приложения-функции:

        git remote add Azure <enter your Git deployment URL>

5. Развертывание приложения-функции

        git push Azure master

   Как только код будет развернут, таймер немедленно начнет срабатывать каждую минуту, чтобы выполнить код.

## <a name="test-the-chat-app"></a>Тестирование приложения чата

Перейдите в приложение чата. Теперь только что созданная функция таймера будет сообщать о времени в начале каждой минуты.

![Приложение чата с сообщениями сервера](./media/signalr-integrate-functions/signalr-functions-complete.png)



## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы хотите продолжить тестирование приложения, вы можете сохранить созданные ресурсы.

В противном случае, если вы закончили работу с примером приложения, вы можете удалить ресурсы Azure, чтобы избежать ненужных расходов. 

> [!IMPORTANT]
> Удаление группы ресурсов — необратимая операция, и все соответствующие ресурсы удаляются окончательно. Будьте внимательны, чтобы случайно не удалить не ту группу ресурсов или не те ресурсы. Если ресурсы для размещения этого примера созданы в существующей группе ресурсов, содержащей ресурсы, которые следует сохранить, можно удалить каждый ресурс отдельно в соответствующих колонках вместо удаления группы ресурсов.
> 
> 

Войдите на [портал Azure](https://portal.azure.com) и щелкните **Группы ресурсов**.

Введите имя группы ресурсов в текстовое поле **Фильтровать элементы…**. В инструкциях в этой статье использовалась группа ресурсов с именем *SignalRTestResources*. В своей группе ресурсов в списке результатов щелкните **...**, а затем **Удалить группу ресурсов**.

   
![Delete](./media/signalr-integrate-functions/signalr-delete-resource-group.png)


Подтвердите операцию удаления группы ресурсов. Введите имя группы ресурсов и нажмите кнопку **Удалить**.
   
Через некоторое время группа ресурсов и все ее ресурсы будут удалены.


## <a name="next-steps"></a>Дополнительная информация

В этом руководстве вы узнали, как выполнять интеграцию с функциями Azure, чтобы передавать обновления для клиентов на основе триггеров функций Azure. Чтобы узнать больше об использовании сервера Azure SignalR, перейдите к примерам Azure CLI для службы SignalR.

> [!div class="nextstepaction"]
> [Примеры CLI для Azure SignalR](./signalr-cli-samples.md)



