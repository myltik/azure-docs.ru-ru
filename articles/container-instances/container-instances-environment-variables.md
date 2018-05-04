---
title: Настройка переменных среды в службе "Экземпляры контейнеров Azure"
description: Узнайте, как настроить переменные среды в службе "Экземпляры контейнеров Azure"
services: container-instances
author: david-stanford
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 03/13/2018
ms.author: dastanfo
ms.openlocfilehash: 37fde41b6dc2ea0a4d3b4b38a0e3df81a297c125
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
---
# <a name="set-environment-variables"></a>Настройка переменных среды

Используя переменные среды в экземплярах контейнеров, вы можете динамически изменять конфигурацию приложения или скрипта, запущенных в контейнере.

Сейчас переменные среды можно настроить из CLI и PowerShell. В обоих случаях, чтобы задать переменные среды, в командах используется флаг. Настройка переменных среды в службе "Экземпляры контейнеров Azure" аналогична установке аргумента командной строки `--env` для `docker run`. Например, если используется образ контейнера aci/microsoft-wordcount, вы можете изменить поведение, указав следующие переменные среды:

*NumWords* задает число слов для вывода в STDOUT.

*MinLength* обозначает минимальную длину учитываемых слов в символах. Если увеличить это значение, скрипт будет пропускать самые распространенные слова, например of и the.

## <a name="azure-cli-example"></a>Пример для Azure CLI

Чтобы просмотреть выходные данные контейнера по умолчанию, выполните следующую команду:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer1 \
    --image microsoft/aci-wordcount:latest \
    --restart-policy OnFailure
```

Если вы определите для контейнера переменные среды `NumWords=5` и `MinLength=8`, выходные данные в журнале контейнера изменятся.

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer2 \
    --image microsoft/aci-wordcount:latest \
    --restart-policy OnFailure \
    --environment-variables NumWords=5 MinLength=8
```

Когда состояние контейнера примет значение *Terminated* (Завершено), что можно проверить с помощью команды [az container show][az-container-show], просмотрите его журналы для оценки выходных данных.  Чтобы просмотреть выходные данные контейнера без переменных среды, задайте для параметра --name значение mycontainer1 вместо mycontainer2.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer2
```

## <a name="azure-powershell-example"></a>Пример для Azure PowerShell

Чтобы просмотреть выходные данные контейнера по умолчанию, выполните следующую команду:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer1 \
    --image microsoft/aci-wordcount:latest \
    --restart-policy OnFailure
```

Если вы определите для контейнера переменные среды `NumWords=5` и `MinLength=8`, выходные данные в журнале контейнера изменятся.

```azurepowershell-interactive
$envVars = @{NumWord=5;MinLength=8}
New-AzureRmContainerGroup `
    -ResourceGroupName myResourceGroup `
    -Name mycontainer2 `
    -Image microsoft/aci-wordcount:latest `
    -RestartPolicy OnFailure `
    -EnvironmentVariable $envVars
```

Когда состояние контейнера примет значение *Terminated* (Завершено), что можно проверить с помощью команды [Get-AzureRmContainerInstanceLog][azure-instance-log], просмотрите его журналы для оценки выходных данных. Чтобы просмотреть журналы контейнера без переменных среды, задайте для параметра ContainerGroupName значение mycontainer1 вместо mycontainer2.

```azurepowershell-interactive
Get-AzureRmContainerInstanceLog `
    -ResourceGroupName myResourceGroup `
    -ContainerGroupName mycontainer2
```

## <a name="example-output-without-environment-variables"></a>Пример выходных данных без переменных среды

```bash
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
```

## <a name="example-output-with-environment-variables"></a>Пример выходных данных с переменными среды

```bash
[('CLAUDIUS', 120),
 ('POLONIUS', 113),
 ('GERTRUDE', 82),
 ('ROSENCRANTZ', 69),
 ('GUILDENSTERN', 54)]
```

## <a name="next-steps"></a>Дополнительная информация

Теперь, когда вы знаете, как настроить входные данные для контейнера, ознакомьтесь со сведениями о том, как сохранить выходные данные контейнеров, которые выполняются до завершения.
> [!div class="nextstepaction"]
> [Подключение общего файлового ресурса Azure с помощью службы "Экземпляры контейнеров Azure"](container-instances-mounting-azure-files-volume.md)

<!-- LINKS Internal -->
[azure-cloud-shell]: ../cloud-shell/overview.md
[azure-cli-install]: /cli/azure/
[azure-powershell-install]: /powershell/azure/install-azurerm-ps
[azure-instance-log]: /powershell/module/azurerm.containerinstance/get-azurermcontainerinstancelog
[az-container-show]: /cli/azure/container?view=azure-cli-latest#az_container_show