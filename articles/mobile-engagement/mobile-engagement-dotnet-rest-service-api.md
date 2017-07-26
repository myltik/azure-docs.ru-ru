---
title: "Использование интерфейса API REST для доступа к интерфейсам API службы Azure Mobile Engagement"
description: "В этой статье описывается, как использовать интерфейсы API REST Mobile Engagement для доступа к интерфейсам API службы Azure Mobile Engagement"
services: mobile-engagement
documentationcenter: mobile
author: wesmc7777
manager: erikre
editor: 
ms.assetid: e8df4897-55ee-45df-b41e-ff187e3d9d12
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 10/05/2016
ms.author: wesmc;ricksal
ms.translationtype: Human Translation
ms.sourcegitcommit: 8f987d079b8658d591994ce678f4a09239270181
ms.openlocfilehash: e381eef22b68e89d5407fb452c05eba089b98780
ms.contentlocale: ru-ru
ms.lasthandoff: 05/18/2017


---
# <a name="using-rest-to-access-azure-mobile-engagement-service-apis"></a>Использование REST для доступа к интерфейсам API службы Azure Mobile Engagement
Служба Azure Mobile Engagement предоставляет [интерфейс REST API для Azure Mobile Engagement](https://msdn.microsoft.com/library/azure/mt683754.aspx), который позволяет управлять устройствами, кампаниями охвата/продвижения и т. д.

Если вы не хотите использовать интерфейсы REST API, мы предлагаем [файл Swagger](https://github.com/Azure/azure-rest-api-specs/blob/master/arm-mobileengagement/2014-12-01/swagger/mobile-engagement.json), который можно использовать со средствами создания пакетов SDK для предпочитаемого языка. Мы рекомендуем использовать средство [AutoRest](https://github.com/Azure/AutoRest) для создания пакета SDK из нашего файла Swagger. Аналогичным образом мы создали пакет SDK для .NET, который позволяет взаимодействовать с этими интерфейсами API с помощью оболочки C# без самостоятельного согласования маркера проверки подлинности и обновления. Сведения об использовании пакета SDK для .NET для доступа к API см. в статье [Использование пакета SDK для .NET для доступа к интерфейсам API службы Azure Mobile Engagement](mobile-engagement-dotnet-sdk-service-api.md).

