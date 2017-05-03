---
title: "Общие сведения об устранении неполадок ресурсов в Наблюдателе за сетями Azure | Документация Майкрософт"
description: "Эта страница содержит обзор возможностей Наблюдателя за сетями по устранению неполадок."
services: network-watcher
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
ms.assetid: c1145cd6-d1cf-4770-b1cc-eaf0464cc315
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: b0c27ca561567ff002bbb864846b7a3ea95d7fa3
ms.openlocfilehash: 9c7ce71674a851d598ef48eb430127c9a6bddb84
ms.lasthandoff: 04/25/2017

---

# <a name="introduction-to-resource-troubleshooting-in-azure-network-watcher"></a>Общие сведения об устранении неполадок ресурсов в Наблюдателе за сетями Azure

Шлюзы виртуальной сети обеспечивают связь между локальными ресурсами и другими виртуальными сетями в Azure. Мониторинг этих шлюзов и их подключений крайне важен для обеспечения бесперебойной связи. Наблюдатель за сетями дает возможность устранять неполадки шлюзов виртуальной сети и их подключений. Эту функцию можно вызывать с помощью PowerShell, интерфейса командной строки или REST API. При вызове Наблюдатель за сетями диагностирует работоспособность шлюза виртуальной сети или подключения и возвращает соответствующие результаты. Этот запрос является долго выполняющейся транзакцией, результаты которой возвращаются после завершения диагностики.

## <a name="results"></a>Результаты

Полученные предварительные результаты дают общее представление о работоспособности ресурса. Можно получить более подробную информацию о ресурсах, как показано в следующем разделе.

В следующем списке приведены значения, возвращаемые API устранения неполадок.

* **startTime** — это значение представляет собой время начала вызова API устранения неполадок.
* **endTime** — это значение представляет собой время завершения устранения неполадок.
* **code** — имеет значение UnHealthy в случае обнаружения отдельной ошибки при диагностике.
* **results** — результаты представляют собой набор данных, возвращаемых для подключения или шлюза виртуальной сети.
    * **id** — это значение представляет собой тип ошибки.
    * **summary** — это значение представляет собой сводку по ошибке.
    * **detailed** — это значение содержит подробное описание ошибки.
    * **recommendedActions** — это свойство представляет собой набор рекомендуемых действий.
      * **actionText** — это значение содержит текст, описывающий, какие действия следует предпринять.
      * **actionUri** — это значение содержит универсальный код ресурса (URI) для документации о том, как действовать.
      * **actionUriText** — это значение представляет собой краткое описание действий.

В следующих таблицах показаны различные типы ошибок (идентификатор в результатах из приведенного выше списка), которые могут произойти, и указано, приводит ли ошибка к созданию журнала.

### <a name="gateway"></a>Шлюз

| Тип ошибки | Причина | Журнал|
|---|---|---|
| NoFault | Ошибка не обнаружена. |Да|
| GatewayNotFound | Не удается найти шлюз или шлюз не подготовлен. |Нет|
| PlannedMaintenance |  Выполняется обслуживание экземпляра шлюза.  |Нет|
| UserDrivenUpdate | Идет обновление, инициированное пользователем. Это может быть операция изменения размера. | Нет |
| VipUnResponsive | Не удается связаться с первичным экземпляром шлюза. Это происходит при сбое пробы работоспособности. | Нет |
| PlatformInActive | Существует проблема с платформой. | Нет|
| ServiceNotRunning | Базовая служба не выполняется. | Нет|
| NoConnectionsFoundForGateway | У шлюза нет подключений. Это всего лишь предупреждение.| Нет|
| ConnectionsNotConnected | Подключения не установлены. Это всего лишь предупреждение.| Да|
| GatewayCPUUsageExceeded | Текущее использование ЦП шлюза превышает 95 %. | Да |

### <a name="connection"></a>Подключение

| Тип ошибки | Причина | Журнал|
|---|---|---|
| NoFault | Ошибка не обнаружена. |Да|
| GatewayNotFound | Не удается найти шлюз или шлюз не подготовлен. |Нет|
| PlannedMaintenance | Выполняется обслуживание экземпляра шлюза.  |Нет|
| UserDrivenUpdate | Идет обновление, инициированное пользователем. Это может быть операция изменения размера.  | Нет |
| VipUnResponsive | Не удается связаться с первичным экземпляром шлюза. Это происходит при сбое пробы работоспособности. | Нет |
| ConnectionEntityNotFound | Отсутствует конфигурация подключения. | Нет |
| ConnectionIsMarkedDisconnected | Подключение отмечено как "разъединенное". |Нет|
| ConnectionNotConfiguredOnGateway | Для базовой службы не настроено подключение. | Да |
| ConnectionMarkedStandy | Базовая служба помечена как ждущая.| Да|
| Аутентификация | Несоответствие предварительного ключа. | Да|
| PeerReachability | Одноранговый шлюз недоступен. | Да|
| IkePolicyMismatch | У однорангового шлюза имеются политики IKE, которые не поддерживаются в Azure. | Да|
| WfpParse Error | Ошибка при анализе журнала WFP. |Да|


## <a name="log-files"></a>Файлы журналов

После того, как устранение неполадок ресурсов завершено, файлы журнала устранения неполадок ресурсов сохраняются в учетной записи хранения. На следующем рисунке приведен пример содержимого вызова, который завершается ошибкой.

![ZIP-файл][1]

> [!NOTE]
> В некоторых случаях только подмножество файлов журнала записывается в хранилище.

Инструкции по скачиванию файлов из учетных записей хранения Azure см. в статье [Приступая к работе с хранилищем BLOB-объектов Azure с помощью .NET](../storage/storage-dotnet-how-to-use-blobs.md). Кроме того, можно использовать такое средство, как Storage Explorer. Дополнительные сведения о Storage Explorer можно найти по следующей ссылке: [Storage Explorer](http://storageexplorer.com/).

### <a name="connectionstatstxt"></a>ConnectionStats.txt

Файл **ConnectionStats.txt** содержит общую статистику подключения, включая количество полученных и отправленных байтов, состояние подключения и время, когда подключение было установлено.

> [!NOTE]
> Если вызов к API устранения неполадок возвращает значение, указывающее на работоспособное состояние, то в ZIP-файле возвращается только файл **ConnectionStats.txt**.

Содержимое этого файла имеет следующий вид.

```
Connectivity State : Connected
Remote Tunnel Endpoint :
Ingress Bytes (since last connected) : 288 B
Egress Bytes (Since last connected) : 288 B
Connected Since : 2/1/2017 8:22:06 PM
```

### <a name="cpustatstxt"></a>CPUStats.txt

Файл **CPUStats.txt** содержит данные об использовании ЦП и памяти, доступных во время тестирования.  Пример содержимого этого файла приведен ниже.

```
Current CPU Usage : 0 % Current Memory Available : 641 MBs
```

### <a name="ikeerrorstxt"></a>IKEErrors.txt

Файл **IKEErrors.txt** содержит все ошибки протокола IKE, обнаруженные во время мониторинга.

В следующем примере показано содержимое файла IKEErrors.txt. Ошибки могут быть разными в зависимости от проблемы.

```
Error: Authentication failed. Check shared key. Check crypto. Check lifetimes. 
     based on log : Peer failed with Windows error 13801(ERROR_IPSEC_IKE_AUTH_FAIL)
Error: On-prem device sent invalid payload. 
     based on log : IkeFindPayloadInPacket failed with Windows error 13843(ERROR_IPSEC_IKE_INVALID_PAYLOAD)
```

### <a name="scrubbed-wfpdiagtxt"></a>Scrubbed-wfpdiag.txt

Файл журнала **Scrubbed-wfpdiag.txt** содержит журнал WFP. В этом журнале регистрируется удаление пакетов, а также сбои протокола IKE и AuthIP.

В следующем примере показано содержимое файла Scrubbed-wfpdiag.txt. В данном примере общий ключ подключения неправилен, как видно в третьей строке снизу. Ниже приведен только фрагмент журнала, так как журнал может быть большим, в зависимости от проблемы.

```
...
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|Deleted ICookie from the high priority thread pool list
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|IKE diagnostic event:
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|Event Header:
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Timestamp: 1601-01-01T00:00:00.000Z
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Flags: 0x00000106
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|    Local address field set
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|    Remote address field set
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|    IP version field set
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  IP version: IPv4
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  IP protocol: 0
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Local address: 13.78.238.92
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Remote address: 52.161.24.36
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Local Port: 0
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Remote Port: 0
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Application ID:
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  User SID: <invalid>
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|Failure type: IKE/Authip Main Mode Failure
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|Type specific info:
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Failure error code:0x000035e9
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|    IKE authentication credentials are unacceptable
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Failure point: Remote
...
```

### <a name="wfpdiagtxtsum"></a>wfpdiag.txt.sum

Файл **wfpdiag.txt.sum** — это журнал, содержащий информацию о буферах и обработанных событиях.

Ниже приведен пример содержимого файла wfpdiag.txt.sum.
```
Files Processed:
    C:\Resources\directory\924336c47dd045d5a246c349b8ae57f2.GatewayTenantWorker.DiagnosticsStorage\2017-02-02T17-34-23\wfpdiag.etl
Total Buffers Processed 8
Total Events  Processed 2169
Total Events  Lost      0
Total Format  Errors    0
Total Formats Unknown   486
Elapsed Time            330 sec
+-----------------------------------------------------------------------------------+
|EventCount    EventName            EventType   TMF                                 |
+-----------------------------------------------------------------------------------+
|        36    ikeext               ike_addr_utils_c844  a0c064ca-d954-350a-8b2f-1a7464eef8b6|
|        12    ikeext               ike_addr_utils_c857  a0c064ca-d954-350a-8b2f-1a7464eef8b6|
|        96    ikeext               ike_addr_utils_c832  a0c064ca-d954-350a-8b2f-1a7464eef8b6|
|         6    ikeext               ike_bfe_callbacks_c133  1dc2d67f-8381-6303-e314-6c1452eeb529|
|         6    ikeext               ike_bfe_callbacks_c61  1dc2d67f-8381-6303-e314-6c1452eeb529|
|        12    ikeext               ike_sa_management_c5698  7857a320-42ee-6e90-d5d9-3f414e3ea2d3|
|         6    ikeext               ike_sa_management_c8447  7857a320-42ee-6e90-d5d9-3f414e3ea2d3|
|        12    ikeext               ike_sa_management_c494  7857a320-42ee-6e90-d5d9-3f414e3ea2d3|
|        12    ikeext               ike_sa_management_c642  7857a320-42ee-6e90-d5d9-3f414e3ea2d3|
|         6    ikeext               ike_sa_management_c3162  7857a320-42ee-6e90-d5d9-3f414e3ea2d3|
|        12    ikeext               ike_sa_management_c3307  7857a320-42ee-6e90-d5d9-3f414e3ea2d3|
```

## <a name="next-steps"></a>Дальнейшие действия

Узнайте, как диагностировать VPN-шлюзы и подключения с помощью PowerShell, посетив страницу [Troubleshoot Virtual Network Gateway and Connections using Azure Network Watcher PowerShell](network-watcher-troubleshoot-manage-powershell.md) (Устранение неполадок шлюза виртуальной сети и подключений с помощью Наблюдателя за сетями Azure и PowerShell).
<!--Image references-->

[1]: ./media/network-watcher-troubleshoot-overview/GatewayTenantWorkerLogs.png

