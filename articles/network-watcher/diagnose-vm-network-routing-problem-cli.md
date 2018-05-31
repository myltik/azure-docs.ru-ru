---
title: Диагностика проблемы с маршрутизацией в сети виртуальной машины с помощью Azure CLI | Документы Майкрософт
description: Из этой статьи вы узнаете, как диагностировать проблему с маршрутизацией в сети виртуальной машине с помощью возможности следующего прыжка в Наблюдателе за сетями Azure.
services: network-watcher
documentationcenter: network-watcher
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: I need to diagnose virtual machine (VM) network routing problem that prevents communication to different destinations.
ms.assetid: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: network-watcher
ms.workload: infrastructure
ms.date: 04/20/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: fcb7ec2e40b5c0e8794d2f4d70395dcbecca019c
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32182164"
---
# <a name="diagnose-a-virtual-machine-network-routing-problem---azure-cli"></a>Диагностика проблем с маршрутизацией в сети виртуальной машины с помощью Azure CLI

В этой статье вы развернете виртуальную машину и проверите доступ к IP-адресу и URL-адресу. Затем вы определите причину проблем с подключением и найдете способ ее устранения.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Если вы решили установить и использовать интерфейс командной строки локально, для работы с этой статьей вам понадобится Azure CLI 2.0.28 или более поздней версии. Выполните командлет `az --version`, чтобы узнать установленную версию. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0](/cli/azure/install-azure-cli). После проверки версии CLI запустите `az login` для создания подключения к Azure. Команды CLI в этой статье отформатированы для выполнения в оболочке Bash.

## <a name="create-a-vm"></a>Создание виртуальной машины

Перед созданием виртуальной машины необходимо создать группу ресурсов, которая будет содержать эту виртуальную машину. Создайте группу ресурсов с помощью команды [az group create](/cli/azure/group#az_group_create). В следующем примере создается группа ресурсов с именем *myResourceGroup* в расположении *eastus*.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Создайте виртуальную машину с помощью команды [az vm create](/cli/azure/vm#az_vm_create). Также команда создает ключи SSH, если они не существуют в расположении ключей по умолчанию. Чтобы использовать определенный набор ключей, используйте параметр `--ssh-key-value`. В следующем примере создается виртуальная машина с именем *myVM*:

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm \
  --image UbuntuLTS \
  --generate-ssh-keys
```

Создание виртуальной машины занимает несколько минут. Переходите к остальным шагам только после того, как завершится создание виртуальной машины и CLI вернет выходные данные.

## <a name="test-network-communication"></a>Тестирование взаимодействия по сети

Чтобы проверить сетевое подключение с помощью Наблюдателя за сетями, сначала включите Наблюдатель за сетями в регионе, где размещается тестируемая виртуальная машина. Затем примените в Наблюдателе за сетями возможность следующего прыжка.

### <a name="enable-network-watcher"></a>Включение Наблюдателя за сетями

Если в регионе "Восточная часть США" уже включена служба "Наблюдатель за сетями", перейдите к разделу по [использованию следующего прыжка](#use-next-hop). С помощью команды [az network watcher configure](/cli/azure/network/watcher#az-network-watcher-configure) создайте Наблюдателя за сетями в регионе "Восточная часть США":

```azurecli-interactive
az network watcher configure \
  --resource-group NetworkWatcherRG \
  --locations eastus \
  --enabled
```

### <a name="use-next-hop"></a>Использование следующего прыжка

Azure автоматически создает маршруты к пунктам назначения по умолчанию. Вы можете создать настраиваемые маршруты, которые переопределяют маршруты по умолчанию. В некоторых случаях настраиваемые маршруты могут привести к сбою связи. Чтобы проверить маршрутизацию из виртуальной машины, выполните команду [az network watcher show-next-hop](/cli/azure/network/watcher?view=azure-cli-latest#az-network-watcher-show-next-hop) для определения следующего прыжка маршрутизации при поступлении трафика на определенный адрес.

Проверьте исходящее подключение с виртуальной машины по любому из IP-адресов сайта www.bing.com.

```azurecli-interactive
az network watcher show-next-hop \
  --dest-ip 13.107.21.200 \
  --resource-group myResourceGroup \
  --source-ip 10.0.0.4 \
  --vm myVm \
  --nic myVmVMNic \
  --out table
```

Через несколько секунд вы получите результат, где указано, что **nextHopType** имеет значение **Интернет**, а **routeTableId** — **Системный маршрут**. Из этого результата вы узнаете, что существует допустимый системный маршрут к назначению.

Проверьте исходящее подключение виртуальной машины к адресу 172.31.0.100:

```azurecli-interactive
az network watcher show-next-hop \
  --dest-ip 172.31.0.100 \
  --resource-group myResourceGroup \
  --source-ip 10.0.0.4 \
  --vm myVm \
  --nic myVmVMNic \
  --out table
```

Возвращенный результат сообщает, что **nextHopType** имеет значение **Нет**, а **routeTableId** — **Системный маршрут**. Так вы узнаете, что, хотя есть допустимый системный маршрут к месту назначения, следующий прыжок для маршрутизации трафика в пункт назначения отсутствует.

## <a name="view-details-of-a-route"></a>Просмотр сведений о маршруте

Чтобы проанализировать маршрутизацию дальше, просмотрите действующие маршруты для сетевого интерфейса, выполнив команду [az network nic show-effective-route-table](/cli/azure/network/nic#az-network-nic-show-effective-route-table):

```azurecli-interactive
az network nic show-effective-route-table \
  --resource-group myResourceGroup \
  --name myVmVMNic
```

В возвращенные данные включен следующий текст:

```azurecli
{
  "additionalProperties": {
    "disableBgpRoutePropagation": false
  },
  "addressPrefix": [
    "0.0.0.0/0"
  ],
  "name": null,
  "nextHopIpAddress": [],
  "nextHopType": "Internet",
  "source": "Default",
  "state": "Active"
},
```

При выполнении команды `az network watcher show-next-hop` для проверки исходящего подключения по IP-адресу 13.107.21.200 в разделе [Использование следующего прыжка](#use-next-hop) маршрут с **addressPrefix** 0.0.0.0/0** использовался для маршрутизации трафика на адрес, так как другие маршруты в выходных данных не включали адрес. По умолчанию все адреса, не указанные в префиксе адреса другого маршрута, маршрутизируются в Интернет.

Однако при выполнении команды `az network watcher show-next-hop` для проверки исходящего подключения по IP-адресу 172.31.0.100 вы узнали, что тип следующего прыжка отсутствовал. В возвращенные данные также включен следующий текст:

```azurecli
{
  "additionalProperties": {
    "disableBgpRoutePropagation": false
      },
  "addressPrefix": [
    "172.16.0.0/12"
  ],
  "name": null,
  "nextHopIpAddress": [],
  "nextHopType": "None",
  "source": "Default",
  "state": "Active"
},
```

Как можно увидеть в выходных данных выполнения команды `az network watcher nic show-effective-route-table`, хотя есть маршрут по умолчанию к префиксу 172.16.0.0/12, который включает адрес 172.31.0.100, **nextHopType** имеет значение **Нет**. Azure создает маршрут по умолчанию к 172.16.0.0/12, но не указывает тип следующего прыжка, пока для этого не возникнут основания. Если, например, добавить диапазон адресов 172.16.0.0/12 в адресное пространство виртуальной сети, Azure изменит **nextHopType** для маршрута на **Виртуальная сеть**. Затем проверка отобразит **Виртуальная сеть** в качестве значения **nextHopType**.

## <a name="clean-up-resources"></a>Очистка ресурсов

Чтобы удалить ненужную группу ресурсов и все содержащиеся в ней ресурсы, выполните команду [az group delete](/cli/azure/group#az_group_delete).

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Дополнительная информация

В этой статье вы создали виртуальную машину и выполнили диагностику сетевой маршрутизации из виртуальной машины. Вы узнали, что Azure создает несколько маршрутов по умолчанию и тестирует маршрутизацию в два разных пункта назначения. Узнайте больше о [маршрутизации в Azure](../virtual-network/virtual-networks-udr-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) и способах [создания пользовательских маршрутов](../virtual-network/manage-route-table.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#create-a-route).

Для исходящих подключений виртуальной машины можно определить задержку, разрешенный и запрещенный сетевой трафик между виртуальной машиной и конечной точкой с помощью функции [устранения неполадок с подключениями](network-watcher-connectivity-cli.md) Наблюдателя за сетями. Вы можете отслеживать обмен данными между виртуальной машиной и конечной точкой, представленных IP-адресом или URL-адресом, с течением времени, используя возможность мониторинга подключения Наблюдателя за сетями. Инструкции см. в статье о [мониторинге сетевых подключений](connection-monitor.md).