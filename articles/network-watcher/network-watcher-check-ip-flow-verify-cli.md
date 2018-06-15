---
title: Проверка трафика с использованием проверки потока IP-адресов с помощью Наблюдателя за сетями (Azure CLI) | Документация Майкрософт
description: В этой статье описывается, как проверить состояние передачи входящего и исходящего трафика виртуальной машины (разрешен или запрещен) с помощью Azure CLI.
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: 92b857ed-c834-4c1b-8ee9-538e7ae7391d
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: b7122b632dca99eba6fae058beb644f945f67872
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/24/2018
ms.locfileid: "31809702"
---
# <a name="check-if-traffic-is-allowed-or-denied-to-or-from-a-vm-with-ip-flow-verify-a-component-of-azure-network-watcher"></a>Проверка состояния входящего и исходящего трафика виртуальной машины (разрешен или запрещен) путем проверки потока IP-адресов (компонент Наблюдателя за сетями Azure)

> [!div class="op_single_selector"]
> - [портал Azure](network-watcher-check-ip-flow-verify-portal.md)
> - [PowerShell](network-watcher-check-ip-flow-verify-powershell.md)
> - [Интерфейс командной строки 1.0](network-watcher-check-ip-flow-verify-cli-nodejs.md)
> - [CLI 2.0](network-watcher-check-ip-flow-verify-cli.md)
> - [Azure REST API](network-watcher-check-ip-flow-verify-rest.md)


Проверка IP-потока — это компонент Наблюдателя за сетями, позволяющий определить состояние (разрешен или запрещен) входящего и исходящего трафика виртуальной машины. В этом сценарии можно получить сведения о текущем состоянии взаимодействия виртуальной машины с внешним ресурсом или сервером. Проверка потока IP-адресов позволяет убедиться, что правила группы безопасности сети настроены правильно, и устранить неполадки потоков, заблокированных правилами NSG. Такая проверка также гарантирует, что NSG будет соответствующим образом блокировать трафик, который нужно заблокировать.

В этой статье мы используем наш новейший интерфейс командной строки для модели развертывания с помощью Resource Manager, а именно Azure CLI 2.0. Этот инструмент доступен для Windows, Mac и Linux.

Для выполнения действий, описанных в этой статье, требуется [установить интерфейс командной строки Azure для Mac, Linux и Windows (Azure CLI)](https://docs.microsoft.com/cli/azure/install-az-cli2).

## <a name="before-you-begin"></a>Перед началом работы

В этом сценарии предполагается, что у вас уже есть Наблюдатель за сетями или что вы создали его в соответствии с инструкциями в статье [Create a Network Watcher](network-watcher-create.md) (Создание Наблюдателя за сетями). Предполагается также, что у вас имеется группа ресурсов с допустимой виртуальной машиной.

## <a name="scenario"></a>Сценарий

В этом сценарии проверка потока IP-адресов используется, чтобы определить, может ли виртуальная машина обратиться к Bing по известному IP-адресу. Если трафик отклоняется, возвращается правило безопасности, блокирующее трафик. Дополнительные сведения о проверке потока IP-адресов см. в [этом обзоре](network-watcher-ip-flow-verify-overview.md).

## <a name="get-a-vm"></a>Получение виртуальной машины

При проверке IP-потока тестируется входящий и исходящий трафик виртуальной машины по IP-адресу в удаленное расположение и из него. Для командлета требуется идентификатор виртуальной машины. Если вы уже знаете идентификатор виртуальной машины, этот шаг можно пропустить.

```azurecli
az vm show --resource-group MyResourceGroup5431 --name MyVM-Web
```

## <a name="get-the-nics"></a>Получение сетевых карт

Требуется IP-адрес сетевого адаптера на виртуальной машине. Получить данные сетевых адаптеров, подключенных к виртуальной машине, можно с помощью приведенной ниже команды. Если вы уже знаете IP-адрес, который необходимо протестировать на виртуальной машине, этот шаг можно пропустить.

```azurecli
az network nic show --resource-group MyResourceGroup5431 --name MyNic-Web
```

## <a name="run-ip-flow-verify"></a>Выполнение проверки IP-потока

Выполните командлет `az network watcher test-ip-flow` для проверки трафика. В этом примере используется первый IP-адрес первого сетевого адаптера.

```azurecli
az network watcher test-ip-flow --resource-group resourceGroupName --direction directionInboundorOutbound --protocol protocolTCPorUDP --local ipAddressandPort --remote ipAddressandPort --vm vmNameorID --nic nicNameorID
```

> [!NOTE]
> Для проверки IP-потока требуется выделение ресурса виртуальной машины для выполнения.

## <a name="review-results"></a>Просмотр результатов

После выполнения командлет `az network watcher test-ip-flow` вернет результаты. Они представлены в следующем примере.

```azurecli
{
    "access": "Allow",
    "ruleName": "defaultSecurityRules/AllowInternetOutBound"
}
```

## <a name="next-steps"></a>Дополнительная информация

Если трафик блокируется, чего не должно быть, см. статью [Управление группами безопасности сети с помощью портала](../virtual-network/manage-network-security-group.md). В ней содержатся сведения об отслеживании группы безопасности сети и определенных правил безопасности.

Узнайте, как выполнить аудит параметров NSG, в статье [Auditing Network Security Groups (NSG) with Network Watcher](network-watcher-nsg-auditing-powershell.md) (Выполнение аудита групп безопасности сети с помощью Наблюдателя за сетями).

[1]: ./media/network-watcher-check-ip-flow-verify-portal/figure1.png
[2]: ./media/network-watcher-check-ip-flow-verify-portal/figure2.png
