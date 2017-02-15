---
title: "Перемещение ресурсов веб-приложений в другую группу ресурсов."
description: "Описываются сценарии, в которых вы можете перемещать веб-приложения и службы приложений из одной группы ресурсов в другую."
services: app-service
documentationcenter: 
author: ZainRizvi
manager: wpickett
editor: 
ms.assetid: 22f97607-072e-4d1f-a46f-8d500420c33c
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/04/2016
ms.author: zarizvi
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 08b8953b91a87a131f70309e41be3a9ce0123299


---
# <a name="supported-move-configurations"></a>Поддерживаемые конфигурации перемещения
Ресурсы веб-приложений Azure можно перемещать с помощью [API перемещения ресурсов ARM](../resource-group-move-resources.md).

Веб-приложения Azure в настоящее время поддерживают следующие сценарии перемещения.

* Перемещение всего содержимого группы ресурсов (веб-приложений, планов обслуживания приложений и сертификатов) в другую группу ресурсов. 
  * Примечание. В этом сценарии конечная группа ресурсов не может содержать никаких ресурсов Microsoft.Web.
* Перемещение отдельных веб-приложений в другую группу ресурсов с сохранением их размещения в текущем плане службы приложений (план службы приложений остается в старой группе ресурсов).




<!--HONumber=Nov16_HO3-->


