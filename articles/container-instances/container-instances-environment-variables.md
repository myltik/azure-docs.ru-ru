---
title: Настройка переменных среды в службе "Экземпляры контейнеров Azure"
description: Узнайте, как настроить переменные среды в контейнерах, выполняемых в службе "Экземпляры контейнеров Azure"
services: container-instances
author: mmacy
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 05/16/2018
ms.author: marsma
ms.openlocfilehash: 1a025ce647cb3c071a6549a433e6505b85409fdc
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34199013"
---
# <a name="set-environment-variables"></a>Настройка переменных среды

Используя переменные среды в экземплярах контейнеров, вы можете динамически изменять конфигурацию приложения или скрипта, запущенных в контейнере. Чтобы установить переменные среды в контейнере, укажите их при создании экземпляра контейнера. Переменные среды можно задать при запуске контейнера с помощью [Azure CLI](#azure-cli-example), [Azure PowerShell](#azure-powershell-example) и на [портале Azure](#azure-portal-example).

Например, если используется образ контейнера [aci/microsoft-wordcount][aci-wordcount], вы можете изменить поведение, указав следующие переменные среды:

*NumWords* задает число слов для вывода в STDOUT.

*MinLength* обозначает минимальную длину учитываемых слов в символах. Если увеличить это значение, скрипт будет пропускать самые распространенные слова, например of и the.

## <a name="azure-cli-example"></a>Пример для Azure CLI

Для просмотра выходных данных по умолчанию контейнера [aci/microsoft-wordcount][aci-wordcount] сначала запустите его с помощью команды [az container create][az-container-create] (переменные среды не заданы):

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer1 \
    --image microsoft/aci-wordcount:latest \
    --restart-policy OnFailure
```

Чтобы изменить выходные данные, запустите второй контейнер с добавленным аргументом `--environment-variables`, указав значения для переменных *NumWords* и *MinLength*:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer2 \
    --image microsoft/aci-wordcount:latest \
    --restart-policy OnFailure \
    --environment-variables NumWords=5 MinLength=8
```

Как только состояние обоих контейнеров станет *Terminated* (Завершен) (используйте [az container show][az-container-show] для проверки состояния), откройте их журналы с помощью [az container logs][az-container-logs], чтобы просмотреть выходные данные.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer1
az container logs --resource-group myResourceGroup --name mycontainer2
```

Выходные данные контейнеров показывают, как вы изменили поведение скрипта второго контейнера, установив переменные среды.

```console
azureuser@Azure:~$ az container logs --resource-group myResourceGroup --name mycontainer1
[('the', 990),
 ('and', 702),
 ('of', 628),
 ('to', 610),
 ('I', 544),
 ('you', 495),
 ('a', 453),
 ('my', 441),
 ('in', 399),
 ('HAMLET', 386)]

azureuser@Azure:~$ az container logs --resource-group myResourceGroup --name mycontainer2
[('CLAUDIUS', 120),
 ('POLONIUS', 113),
 ('GERTRUDE', 82),
 ('ROSENCRANTZ', 69),
 ('GUILDENSTERN', 54)]
```

## <a name="azure-powershell-example"></a>Пример для Azure PowerShell

Установка переменных среды в PowerShell аналогична действиям в CLI, за исключением использования аргумента командной строки `-EnvironmentVariable`.

Сначала запустите контейнер [microsoft/aci-wordcount][aci-wordcount] в конфигурации по умолчанию с помощью команды [New-AzureRmContainerGroup][new-azurermcontainergroup]:

```azurepowershell-interactive
New-AzureRmContainerGroup `
    -ResourceGroupName myResourceGroup `
    -Name mycontainer1 `
    -Image microsoft/aci-wordcount:latest
```

Теперь выполните следующую команду [New AzureRmContainerGroup][new-azurermcontainergroup]. Она задает переменные среды *NumWords* и *MinLength* после заполнения переменной массива `envVars`:

```azurepowershell-interactive
$envVars = @{NumWords=5;MinLength=8}
New-AzureRmContainerGroup `
    -ResourceGroupName myResourceGroup `
    -Name mycontainer2 `
    -Image microsoft/aci-wordcount:latest `
    -RestartPolicy OnFailure `
    -EnvironmentVariable $envVars
```

Как только состояние обоих контейнеров станет *Terminated* (Завершен) (используйте команду [Get-AzureRmContainerInstanceLog ][azure-instance-log] для проверки состояния), извлеките их журналы с помощью команды [Get-AzureRmContainerInstanceLog][azure-instance-log].

```azurepowershell-interactive
Get-AzureRmContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer1
Get-AzureRmContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer2
```

Выходные данные каждого контейнера показывают, как вы изменили скрипт, выполняемый контейнером, установив переменные среды.

```console
PS Azure:\> Get-AzureRmContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer1
[('the', 990),
 ('and', 702),
 ('of', 628),
 ('to', 610),
 ('I', 544),
 ('you', 495),
 ('a', 453),
 ('my', 441),
 ('in', 399),
 ('HAMLET', 386)]

Azure:\
PS Azure:\> Get-AzureRmContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer2
[('CLAUDIUS', 120),
 ('POLONIUS', 113),
 ('GERTRUDE', 82),
 ('ROSENCRANTZ', 69),
 ('GUILDENSTERN', 54)]

Azure:\
```

## <a name="azure-portal-example"></a>Пример на портале Azure

Чтобы установить переменные среды при запуске контейнера на портале Azure, укажите их на странице **Конфигурация** при создании контейнера.

При развертывании с помощью портала на данный момент вы не можете указать более трех переменных и должны ввести их в таком формате: `"variableName":"value"`

Чтобы увидеть пример, запустите контейнер [microsoft/aci-wordcount][aci-wordcount] с переменными *NumWords* и *MinLength*.

1. В разделе **Конфигурация** установите для параметра **Политика перезапуска** значение *При сбое*.
2. Введите `"NumWords":"5"` в качестве первой переменной, выберите **Да** для параметра **Add additional environment variables** (Добавить дополнительные переменные среды) и введите `"MinLength":"8"` в качестве второй переменной. Нажмите кнопку **ОК** для выполнения проверки, а затем разверните контейнер.

![Страница портала, отображающая переменную среды, кнопку "Включить" и текстовые поля][portal-env-vars-01]

Чтобы просмотреть журналы контейнера, в разделе **Параметры** выберите **Контейнеры**, а затем **Журналы**. Подобно тому, как это было показано в предыдущих разделах для CLI и PowerShell, вы можете увидеть, как поведение скрипта было изменено после указания переменных среды. Отображаются только пять слов, каждое из которых имеет длину не менее восьми символов.

![Выходные данные журналов контейнера на портале][portal-env-vars-02]

## <a name="next-steps"></a>Дополнительная информация

В скриптах на основе задач, таких как пакетная обработка большого набора данных с несколькими контейнерами, могут использоваться пользовательские переменные среды во время выполнения. Дополнительные сведения о запуске контейнеров на основе задач см. статье [Выполнение задачи-контейнера в службе "Экземпляры контейнеров Azure"](container-instances-restart-policy.md).

<!-- IMAGES -->
[portal-env-vars-01]: ./media/container-instances-environment-variables/portal-env-vars-01.png
[portal-env-vars-02]: ./media/container-instances-environment-variables/portal-env-vars-02.png

<!-- LINKS - External -->
[aci-wordcount]: https://hub.docker.com/r/microsoft/aci-wordcount/

<!-- LINKS Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[azure-cli-install]: /cli/azure/
[azure-instance-log]: /powershell/module/azurerm.containerinstance/get-azurermcontainerinstancelog
[azure-powershell-install]: /powershell/azure/install-azurerm-ps
[new-azurermcontainergroup]: /powershell/module/azurerm.containerinstance/new-azurermcontainergroup
[portal]: https://portal.azure.com