---
title: Ограничения запросов Azure Resource Manager | Документация Майкрософт
description: В данной статье описывается использование регулирования запросов Azure Resource Manager при достижении ограничений подписки.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: e1047233-b8e4-4232-8919-3268d93a3824
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/10/2018
ms.author: tomfitz
ms.openlocfilehash: f3dcb0c5036b2cfc38ef2a6a16269a8697bbd9e6
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34358869"
---
# <a name="throttling-resource-manager-requests"></a>Регулирование запросов Resource Manager
Для каждой подписки и клиента Resource Manager ограничивает число запросов на чтение до 15 000 и запросов на запись — до 1200 в час. Эти ограничения применяются к каждому экземпляру Azure Resource Manager. В каждом регионе Azure существует несколько экземпляров, а Azure Resource Manager развертывается во всех регионах Azure.  На практике ограничений гораздо больше, чем указано выше, так как запросы пользователя обычно обслуживаются разными экземплярами.

Если приложение или сценарий достигает этих предельных значений, то требуется регулировать запросы. В этой статье показано, как определить, сколько осталось запросов до достижения ограничения, и как реагировать на достижение этого ограничения.

По достижении ограничения отображается код состояния HTTP **429 — слишком много запросов**.

Число запросов ограничивается либо подпиской, либо клиентом. Если в вашей подписке имеется несколько приложений, одновременно выполняющих запросы, то количество этих запросов суммируется, после чего определяется количество оставшихся запросов.

Запросы, ограничиваемые подпиской, включают в себя передачу идентификатора подписки (например, извлечение ресурсов в подписке). Запросы, ограничиваемые клиентом, не включают идентификатор подписки (например, извлечение действительных расположений Azure).

## <a name="remaining-requests"></a>Количество оставшихся запросов
Количество оставшихся запросов можно определить, проверяя заголовки ответов. Каждый запрос содержит значения количества оставшихся запросов на чтение и запись. В приведенной ниже таблице описаны заголовки ответов, в которых можно проверить эти значения.

| Заголовок ответа | ОПИСАНИЕ |
| --- | --- |
| x-ms-ratelimit-remaining-subscription-reads |Оставшееся число запросов на чтение для подписки. Это значение возвращается при операциях чтения. |
| x-ms-ratelimit-remaining-subscription-writes |Оставшееся число запросов на запись для подписки. Это значение возвращается при операциях записи. |
| x-ms-ratelimit-remaining-tenant-reads |Оставшееся количество запросов на чтение для клиента. |
| x-ms-ratelimit-remaining-tenant-writes |Оставшееся количество запросов на запись для клиента. |
| x-ms-ratelimit-remaining-subscription-resource-requests |Оставшееся количество запросов для типа ресурса для подписки.<br /><br />Это значение заголовка возвращается только в том случае, если служба переопределила ограничение по умолчанию. Resource Manager добавляет это значение вместо ограничения подписки на запросы на чтение или запись. |
| x-ms-ratelimit-remaining-subscription-resource-entities-read |Оставшееся количество запросов коллекции типов ресурсов для подписки.<br /><br />Это значение заголовка возвращается только в том случае, если служба переопределила ограничение по умолчанию. Это значение содержит число оставшихся запросов коллекции (вывод ресурсов). |
| x-ms-ratelimit-remaining-tenant-resource-requests |Оставшееся количество запросов для типа ресурса для клиента.<br /><br />Этот заголовок добавляется только для запросов уровня клиента и только в том случае, если служба переопределила ограничение по умолчанию. Resource Manager добавляет это значение вместо ограничения клиента на запросы на чтение или запись. |
| x-ms-ratelimit-remaining-tenant-resource-entities-read |Оставшееся количество запросов коллекции типов ресурсов для клиента.<br /><br />Этот заголовок добавляется только для запросов уровня клиента и только в том случае, если служба переопределила ограничение по умолчанию. |

## <a name="retrieving-the-header-values"></a>Получение значений заголовков
Получение этих значений заголовков в коде или сценарии ничем не отличается от извлечения любого другого значения заголовка. 

Например, приведенный ниже код **C#** извлекает значение заголовка из объекта **HttpWebResponse** с именем **response**.

```cs
response.Headers.GetValues("x-ms-ratelimit-remaining-subscription-reads").GetValue(0)
```

В **PowerShell** извлечь значение заголовка можно с помощью операции Invoke-WebRequest.

```powershell
$r = Invoke-WebRequest -Uri https://management.azure.com/subscriptions/{guid}/resourcegroups?api-version=2016-09-01 -Method GET -Headers $authHeaders
$r.Headers["x-ms-ratelimit-remaining-subscription-reads"]
```

Или, если требуется узнать количество оставшихся запросов для отладки, можно указать параметр **-Debug** в командлете **PowerShell**.

```powershell
Get-AzureRmResourceGroup -Debug
```

Он возвращает массу значений, включая следующее значение ответа.

```powershell
DEBUG: ============================ HTTP RESPONSE ============================

Status Code:
OK

Headers:
Pragma                        : no-cache
x-ms-ratelimit-remaining-subscription-reads: 14999
```

Чтобы получить ограничение на запись, используйте операцию записи: 

```powershell
New-AzureRmResourceGroup -Name myresourcegroup -Location westus -Debug
```

Будет возвращено множество значений, включая следующие:

```powershell
DEBUG: ============================ HTTP RESPONSE ============================

Status Code:
Created

Headers:
Pragma                        : no-cache
x-ms-ratelimit-remaining-subscription-writes: 1199
```

В **интерфейсе командной строки Azure** извлечь значение заголовка можно с помощью параметра подробного вывода.

```azurecli
az group list --verbose --debug
```

Будет возвращено множество значений, включая следующие:

```azurecli
msrest.http_logger : Response status: 200
msrest.http_logger : Response headers:
msrest.http_logger :     'Cache-Control': 'no-cache'
msrest.http_logger :     'Pragma': 'no-cache'
msrest.http_logger :     'Content-Type': 'application/json; charset=utf-8'
msrest.http_logger :     'Content-Encoding': 'gzip'
msrest.http_logger :     'Expires': '-1'
msrest.http_logger :     'Vary': 'Accept-Encoding'
msrest.http_logger :     'x-ms-ratelimit-remaining-subscription-reads': '14998'
```

Чтобы получить ограничение на запись, используйте операцию записи: 

```azurecli
az group create -n myresourcegroup --location westus --verbose --debug
```

Будет возвращено множество значений, включая следующие:

```azurecli
msrest.http_logger : Response status: 201
msrest.http_logger : Response headers:
msrest.http_logger :     'Cache-Control': 'no-cache'
msrest.http_logger :     'Pragma': 'no-cache'
msrest.http_logger :     'Content-Length': '163'
msrest.http_logger :     'Content-Type': 'application/json; charset=utf-8'
msrest.http_logger :     'Expires': '-1'
msrest.http_logger :     'x-ms-ratelimit-remaining-subscription-writes': '1199'
```

## <a name="waiting-before-sending-next-request"></a>Ожидание перед отправкой следующего запроса
По достижении предельного количества запросов Resource Manager возвращает в заголовке код состояния HTTP **429** и значение **Retry-After**. Значение **Retry-After** указывает период времени в секундах, в течение которого приложение ожидает (или находится в спящем режиме), прежде чем отправить следующий запрос. Если отправить запрос до истечения значения Retry-After, этот запрос не будет обработан, и будет возвращено новое значение Retry-After.

## <a name="next-steps"></a>Дополнительная информация

* Дополнительные сведения об ограничениях и квотах см. в статье [Подписка Azure, границы, квоты и ограничения службы](../azure-subscription-service-limits.md).
* Сведения об обработке асинхронных запросов REST см. в статье [Track asynchronous Azure operations](resource-manager-async-operations.md) (Отслеживание асинхронных операций Azure).
