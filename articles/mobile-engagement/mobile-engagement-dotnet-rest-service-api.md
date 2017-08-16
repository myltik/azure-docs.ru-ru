---
title: "Использование REST API для доступа к интерфейсам API Служб мобильного взаимодействия Azure"
description: "В этой статье описывается, как использовать интерфейсы REST API Служб мобильного взаимодействия для доступа к интерфейсам API Служб мобильного взаимодействия Azure"
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
ms.translationtype: HT
ms.sourcegitcommit: caaf10d385c8df8f09a076d0a392ca0d5df64ed2
ms.openlocfilehash: 4b21bca6fee7012ce1dba96950ae075eded414f8
ms.contentlocale: ru-ru
ms.lasthandoff: 08/08/2017

---
# <a name="using-rest-to-access-azure-mobile-engagement-service-apis"></a>Использование REST для доступа к интерфейсам API Служб мобильного взаимодействия Azure
Службы мобильного взаимодействия Azure предоставляют [REST API Служб мобильного взаимодействия Azure](https://msdn.microsoft.com/library/azure/mt683754.aspx), который позволяет управлять устройствами, кампаниями охвата/продвижения и т. д.

> [!NOTE]
> Мы прекратим использование Служб мобильного взаимодействия Azure в марте 2018 года. Сейчас они доступны только существующим клиентам. Дополнительные сведения см. в статье [Службы мобильного взаимодействия](https://azure.microsoft.com/en-us/services/mobile-engagement/).

Если вы не хотите использовать интерфейсы REST API, мы предлагаем [файл Swagger](https://github.com/Azure/azure-rest-api-specs/blob/master/arm-mobileengagement/2014-12-01/swagger/mobile-engagement.json), который можно использовать со средствами создания пакетов SDK для предпочитаемого языка. Мы рекомендуем использовать средство [AutoRest](https://github.com/Azure/AutoRest) для создания пакета SDK из нашего файла Swagger. Аналогичным образом мы создали пакет SDK для .NET, который позволяет взаимодействовать с этими интерфейсами API с помощью оболочки C# без самостоятельного согласования маркера проверки подлинности и обновления. Сведения об использовании пакета SDK для .NET для доступа к API см. в статье [Использование пакета SDK для .NET для доступа к интерфейсам API службы Azure Mobile Engagement](mobile-engagement-dotnet-sdk-service-api.md).

