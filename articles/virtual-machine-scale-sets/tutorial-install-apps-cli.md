---
title: Руководство. Установка приложений в масштабируемом наборе с помощью Azure CLI 2.0 | Документация Майкрософт
description: Узнайте, как с помощью Azure CLI 2.0 устанавливать приложения в масштабируемых наборах виртуальных машин с использованием расширения настраиваемых скриптов.
services: virtual-machine-scale-sets
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 5db044471c324a3707198ab57ee9b9b6528e121d
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2018
ms.locfileid: "30246633"
---
# <a name="tutorial-install-applications-in-virtual-machine-scale-sets-with-the-azure-cli-20"></a>Руководство. Установка приложений в масштабируемом наборе виртуальных машин с помощью Azure CLI 2.0
Для запуска приложений в экземплярах виртуальных машин в масштабируемом наборе необходимо сначала установить компоненты и необходимые файлы этих приложений. В предыдущем руководстве описано, как создать и использовать настраиваемый образ виртуальной машины для развертывания экземпляров виртуальных машин. При создании настраиваемого образа установка и настройка приложения выполнялись вручную. Кроме того, можно автоматизировать установку приложений в масштабируемом наборе после развертывания каждого экземпляра виртуальной машины или обновления приложения, которое уже запущено в масштабируемом наборе. Из этого руководства вы узнали, как выполнять такие задачи:

> [!div class="checklist"]
> * автоматически устанавливать приложения в масштабируемый набор;
> * использовать расширения настраиваемых скриптов;
> * обновлять приложения, работающие в масштабируемом наборе.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Чтобы установить и использовать интерфейс командной строки локально для работы с этим руководством, вам понадобится Azure CLI 2.0.29 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0]( /cli/azure/install-azure-cli). 


## <a name="what-is-the-azure-custom-script-extension"></a>Что такое расширение пользовательских скриптов Azure?
Расширение настраиваемых сценариев скачивает и выполняет сценарии на виртуальных машинах Azure. Это расширение можно использовать для настройки после развертывания, установки программного обеспечения и других задач настройки или управления. Сценарии можно скачать из службы хранилища Azure или GitHub или передать на портал Azure во время выполнения расширения.

Расширение настраиваемых скриптов интегрируется с шаблонами Azure Resource Manager и может использоваться с интерфейсом командной строки Azure CLI 2.0, Azure PowerShell, порталом Azure или REST API. Дополнительные сведения см. в статье [Расширение Custom Script в ОС Windows](../virtual-machines/linux/extensions-customscript.md).

Чтобы использовать расширение настраиваемых сценариев с Azure CLI, создайте JSON-файл, который определяет получаемые файлы и выполняемые команды. Эти определения JSON можно многократно использовать в крупномасштабных развертываниях масштабируемых наборов, чтобы обеспечить согласованную установку приложений.


## <a name="create-custom-script-extension-definition"></a>Создание определения расширения настраиваемых скриптов
Чтобы увидеть расширение настраиваемых скриптов в действии, создайте масштабируемый набор, который устанавливает веб-сервер NGINX и отображает имя узла экземпляра виртуальной машины масштабируемого набора. Приведенное ниже определение расширения настраиваемых скриптов скачивает пример скрипта из репозитория GitHub, устанавливает необходимые пакеты, а затем записывает имя узла экземпляра виртуальной машины на базовую HTML-страницу.

В текущей оболочке создайте файл *customConfig.json* и вставьте в него следующую конфигурацию. Например, создайте файл в Cloud Shell, не на локальном компьютере. Вы можете использовать любой редактор. В Cloud Shell введите `sensible-editor cloudConfig.json`, чтобы создать файл и просмотреть список доступных редакторов.

```json
{
  "fileUris": ["https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/automate_nginx.sh"],
  "commandToExecute": "./automate_nginx.sh"
}
```


## <a name="create-a-scale-set"></a>Создание масштабируемого набора
Создайте группу ресурсов с помощью команды [az group create](/cli/azure/group#create). В следующем примере создается группа ресурсов с именем *myResourceGroup* в расположении *eastus*.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Создайте масштабируемый набор виртуальных машин с помощью команды [az vmss create](/cli/azure/vmss#create). В следующем примере создаются масштабируемый набор *myScaleSet* и ключи SSH, если они еще не созданы.

```azurecli-interactive
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --admin-username azureuser \
  --generate-ssh-keys
```

Создание и настройка всех ресурсов и виртуальных машин масштабируемого набора занимает несколько минут.


## <a name="apply-the-custom-script-extension"></a>Применение расширения настраиваемых скриптов
Примените конфигурацию расширения настраиваемых сценариев к экземплярам виртуальных машин в своем масштабируемом наборе командой [az vmss extension set](/cli/azure/vmss/extension#set). В следующем примере применяется конфигурация *customConfig.json* к экземплярам виртуальных машин набора *myScaleSet* в группе ресурсов *myResourceGroup*.

```azurecli-interactive
az vmss extension set \
  --publisher Microsoft.Azure.Extensions \
  --version 2.0 \
  --name CustomScript \
  --resource-group myResourceGroup \
  --vmss-name myScaleSet \
  --settings @customConfig.json
```

Каждый экземпляр виртуальной машины в масштабируемом наборе скачивает и запускает скрипт из репозитория GitHub. В более сложном примере можно установить несколько компонентов и файлов приложения. Если масштабируемый набор развернут, новые экземпляры виртуальных машин автоматически будут применять одно и то же расширение настраиваемых скриптов и устанавливать нужное приложение.


## <a name="test-your-scale-set"></a>Проверка масштабируемого набора
Чтобы разрешить передачу трафика в веб-сервер, создайте правило подсистемы балансировки нагрузки с помощью команды [az network lb rule create](/cli/azure/network/lb/rule#create). В следующем примере создается правило *myLoadBalancerRuleWeb*.

```azurecli-interactive
az network lb rule create \
  --resource-group myResourceGroup \
  --name myLoadBalancerRuleWeb \
  --lb-name myScaleSetLB \
  --backend-pool-name myScaleSetLBBEPool \
  --backend-port 80 \
  --frontend-ip-name loadBalancerFrontEnd \
  --frontend-port 80 \
  --protocol tcp
```

Чтобы посмотреть, как работает веб-сервер, получите общедоступный IP-адрес подсистемы балансировки нагрузки с помощью команды [az network public-ip show](/cli/azure/network/public-ip#show). Следующий пример получает IP-адрес для *myScaleSetLBPublicIP*, созданного ранее вместе с масштабируемым набором.

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroup \
  --name myScaleSetLBPublicIP \
  --query [ipAddress] \
  --output tsv
```

Введите в браузере общедоступный IP-адрес подсистемы балансировки нагрузки. Подсистема балансировки нагрузки передаст запрос на один из экземпляров виртуальной машины, как показано в следующем примере:

![Базовая веб-страница NGINX](media/tutorial-install-apps-cli/running-nginx.png)

Не закрывайте веб-браузер, чтобы увидеть обновленную версию на следующем шаге.


## <a name="update-app-deployment"></a>Обновление развертывания приложения
На протяжении жизненного цикла масштабируемого набора может понадобиться развернуть обновленную версию приложения. Расширение пользовательских скриптов позволяет ссылаться на обновленный скрипт развертывания и повторно применять расширение к масштабируемому набору. Когда на предыдущем шаге создавался масштабируемый набор, для параметра `--upgrade-policy-mode` было задано значение *automatic*. Этот параметр позволяет экземплярам виртуальных машин в масштабируемом наборе автоматически обновлять и применять последнюю версию приложения.

В текущей оболочке создайте файл *customConfigv2.json* и вставьте в него следующую конфигурацию. Это определение позволяет запустить обновленную версию *v2* скрипта установки приложения.

```json
{
  "fileUris": ["https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/automate_nginx_v2.sh"],
  "commandToExecute": "./automate_nginx_v2.sh"
}
```

Примените конфигурацию расширения настраиваемых скриптов к экземплярам виртуальных машин в своем масштабируемом наборе. Для этого выполните команду [az vmss extension set](/cli/azure/vmss/extension#set). Этот файл *customConfigv2.json* используется для применения обновленной версии приложения.

```azurecli-interactive
az vmss extension set \
    --publisher Microsoft.Azure.Extensions \
    --version 2.0 \
    --name CustomScript \
    --resource-group myResourceGroup \
    --vmss-name myScaleSet \
    --settings @customConfigv2.json
```

Все экземпляры виртуальной машины в масштабируемом наборе автоматически обновляются до последней версии примера веб-страницы. Чтобы просмотреть обновленную версию, обновите веб-сайт в браузере:

![Обновленная веб-страница NGINX](media/tutorial-install-apps-cli/running-nginx-updated.png)


## <a name="clean-up-resources"></a>Очистка ресурсов
Чтобы удалить масштабируемый набор и дополнительные ресурсы, удалите группу ресурсов и все входящие в нее ресурсы с помощью команды [az group delete](/cli/azure/group#az_group_delete). При использовании параметра `--no-wait` управление возвращается в командную строку без ожидания завершения операции. Параметр `--yes` подтверждает, что вы хотите удалить ресурсы без дополнительного запроса.

```azurecli-interactive
az group delete --name myResourceGroup --no-wait --yes
```


## <a name="next-steps"></a>Дополнительная информация
Из этого руководства вы узнали, как автоматически устанавливать и обновлять приложения в масштабируемом наборе с помощью Azure CLI 2.0. Вы узнали, как выполнять такие задачи:

> [!div class="checklist"]
> * автоматически устанавливать приложения в масштабируемый набор;
> * использовать расширения настраиваемых скриптов;
> * обновлять приложения, работающие в масштабируемом наборе.

Перейдите к следующему руководству, чтобы узнать, как автоматически масштабировать масштабируемый набор.

> [!div class="nextstepaction"]
> [Автоматическое масштабирование масштабируемого набора](tutorial-autoscale-cli.md)