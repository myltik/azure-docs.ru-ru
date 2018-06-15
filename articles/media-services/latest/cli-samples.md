---
title: Примеры Azure CLI для Служб мультимедиа Azure | Документация Майкрософт
description: Примеры Azure CLI для Служб мультимедиа Azure
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: ''
ms.date: 04/15/2018
ms.author: juliako
ms.openlocfilehash: acc92662aa5b727656a8eda368ba6d78a87d9ecd
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34640895"
---
# <a name="azure-cli-examples-for-azure-media-services"></a>Примеры Azure CLI для Служб мультимедиа Azure

В следующей таблице содержатся ссылки на примеры Azure CLI для Служб мультимедиа Azure.

|  |  |
|---|---|
|**Учетная запись.**||
| [Создание учетной записи Служб мультимедиа](./scripts/cli-create-account.md) | Создает учетную запись Служб мультимедиа Azure, а также субъект-службу, с помощью которого можно получить доступ к API-интерфейсам и программным образом управлять учетной записью. |
| [Сброс данных учетной записи](./scripts/cli-reset-account-credentials.md)|Сброс данных учетной записи и восстановление параметров в файле app.config обратно.|
|**Ресурсы**||
| [Создание ресурсов](./scripts/cli-create-asset.md)|Создание ресурса служб мультимедиа для передачи содержимого.|
| [Передача файла](./scripts/cli-upload-file-asset.md)|Передача локального файла в контейнер хранилища.|
| [Публикация ресурса](./scripts/cli-publish-asset.md)| Создание указателя потоковой передачи и получение URL-адреса потоковой передачи. |
| **Преобразования** и **задания**||
| [Создание преобразований](./scripts/cli-create-transform.md)|Показано, как создавать преобразования. Преобразования описывают простой рабочий процесс задач для обработки видео- и аудиофайлов (его часто называют "рецептом").<br/> Всегда проверяйте, существует ли уже "рецепт" и преобразование с нужным именем. Если он существует, используйте его. |
| [Создание заданий](./scripts/cli-create-jobs.md)|Отправка задания для простого преобразования с кодированием с помощью URL-адреса HTTPs.|
| [Создание сетки событий](./scripts/cli-create-event-grid.md)|Создание подписки службы "Сетка событий" уровня учетной записи для изменения состояния заданий.|

## <a name="see-also"></a>См. также

[CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/ams?view=azure-cli-latest)
