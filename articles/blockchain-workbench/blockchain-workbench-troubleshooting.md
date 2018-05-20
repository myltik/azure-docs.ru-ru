---
title: Устранение неполадок в Azure Blockchain Workbench
description: Сведения об устранении неполадок в приложении Azure Blockchain Workbench.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 4/21/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: 8a2715666c4fff490f5184b7b8719b412952b9bf
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/12/2018
---
# <a name="azure-blockchain-workbench-troubleshooting"></a>Устранение неполадок в Azure Blockchain Workbench

Мы создали скрипт PowerShell для отладки при разработке и технической поддержки. Этот скрипт формирует сводные данные и собирает подробные журналы для устранения неполадок. Собираются журналы следующих служб:

* сеть Blockchain, например Ethereum;
* микрослужбы Blockchain Workbench;
* Application Insights
* службы мониторинга Azure (OMS).

Эти сведения помогут вам определиться с дальнейшими действиями и выяснить основную причину возникших проблем. 

## <a name="troubleshooting-script"></a>Скрипт для устранения неполадок

Скрипт PowerShell для устранения неполадок доступен в репозитории GitHub. [Скачайте ZIP-файл](https://github.com/Azure-Samples/blockchain/archive/master.zip) или клонируйте пример с GitHub.

```
git clone https://github.com/Azure-Samples/blockchain.git
```

## <a name="run-the-script"></a>Запуск сценария
[!INCLUDE [sample-powershell-install](../../includes/sample-powershell-install.md)]

Запустите скрипт `collectBlockchainWorkbenchTroubleshooting.ps1`, чтобы собрать журналы и создать ZIP-файл, содержащий папку со сведениями для устранения неполадок. Например: 

``` powershell
collectBlockchainWorkbenchTroubleshooting.ps1 -SubscriptionID "<subscription_id>" -ResourceGroupName "workbench-resource-group-name"
```
Этот скрипт принимает следующие параметры.

| Параметр  | ОПИСАНИЕ | Обязательно |
|---------|---------|----|
| SubscriptionID | Идентификатор подписки, в которой создаются или используются ресурсы. | Yes |
| ResourceGroupName | Имя группы ресурсов Azure, в которой развернуто приложение Blockchain Workbench. | Yes |
| OutputDirectory | Путь для создания ZIP-файла с выходными данными. Если это значение не указано, по умолчанию используется текущий каталог. | Нет 
| OmsSubscriptionId | Идентификатор подписки, в которой развернута служба OMS. Предавайте этот параметр только в том случае, если OMS для сети блокчейн развернута за пределами группы ресурсов Blockchain Workbench.| Нет  |
| OmsResourceGroup |Группа ресурсов, в которой развернута служба OMS. Предавайте этот параметр только в том случае, если OMS для сети блокчейн развернута за пределами группы ресурсов Blockchain Workbench.| Нет  |
| OmsWorkspaceName | Имя рабочей области OMS. Предавайте этот параметр только в том случае, если OMS для сети блокчейн развернута за пределами группы ресурсов Blockchain Workbench. | Нет  |

## <a name="what-is-collected"></a>Какие данные собираются?

Результирующий ZIP-файл содержит выходные данные в следующей структуре папок:

| Папка \ файл | ОПИСАНИЕ  |
|---------|---------|
| \Summary.txt | Общие сведения о системе |
| \metrics\blockchain | Метрики сети блокчейн |
| \metrics\workbench | Метрики Workbench |
| \details\blockchain | Подробные журналы сети блокчейн |
| \details\workbench | Подробные журналы Workbench |

Файл общих сведений содержит моментальный снимок общего состояния и работоспособности приложения. В эту сводку входят рекомендуемые действия, самые частые ошибки и метаданные о запущенных службах.

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Архитектура Azure Blockchain Workbench](blockchain-workbench-architecture.md)