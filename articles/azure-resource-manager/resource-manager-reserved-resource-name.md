---
title: Ошибки имен зарезервированных ресурсов Azure | Документация Майкрософт
description: Описывается, как устранить ошибки при предоставлении имени ресурса, который включает зарезервированное слово.
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 11/08/2017
ms.author: tomfitz
ms.openlocfilehash: b91a53d17d64afb0a56f745505f10e8cabbc22cc
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34357067"
---
# <a name="resolve-reserved-resource-name-errors"></a>Ошибки имен зарезервированных ресурсов Azure

В этой статье описаны ошибки, которые возникают при развертывании ресурса, в имени которого содержится зарезервированное слово.

## <a name="symptom"></a>Симптом

При развертывании ресурсов, которые доступны через другую общедоступную конечную точку, может появиться следующая ошибка:

```
Code=ReservedResourceName;
Message=The resource name <resource-name> or a part of the name is a trademarked or reserved word.
```

## <a name="cause"></a>Причина:

Ресурсы, которые имеют общедоступную конечную точку, не могут содержать зарезервированные слова или товарные знаки в имени.

Следующие слова являются зарезервированными:

* ACCESS;
* AZURE;
* BING;
* BIZSPARK;
* BIZTALK;
* CORTANA;
* DIRECTX;
* DOTNET;
* DYNAMICS;
* EXCEL;
* EXCHANGE;
* FOREFRONT;
* GROOVE;
* HOLOLENS;
* HYPERV;
* KINECT;
* LYNC;
* MSDN
* O365
* OFFICE;
* OFFICE365;
* ONEDRIVE;
* ONENOTE;
* OUTLOOK;
* POWERPOINT;
* SHAREPOINT;
* SKYPE;
* VISIO;
* VISUALSTUDIO.

Следующие слова нельзя использовать ни в качестве целого слова, ни как часть строки в имени:

* LOGIN;
* MICROSOFT;
* WINDOWS;
* XBOX.

## <a name="solution"></a>Решение

Предоставьте имя, которое не содержит ни одного из зарезервированных слов.