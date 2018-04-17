---
title: Что произошло с моим проектом ASP.NET 5 (подключенные службы Visual Studio) | Документация Майкрософт
description: Сведения о том, что происходит после подключения к учетной записи хранения Azure в проекте Visual Studio ASP.NET 5 с помощью подключенных служб Visual Studio
services: storage
documentationcenter: ''
author: ghogen
manager: douge
editor: ''
ms.assetid: e7caa9fa-c780-45eb-a546-299fc1c68455
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: vs-what-happened
ms.devlang: na
ms.topic: article
ms.date: 12/02/2016
ms.author: ghogen
ms.openlocfilehash: 16da7e7a2d61cffb95d22a85669c8f91f28ae476
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/30/2018
---
# <a name="what-happened-to-my-aspnet-5-project-visual-studio-azure-storage-connected-services"></a>Что произошло с моим проектом ASP.NET 5 (подключенными к службе хранилища Azure службами Visual Studio)?
## <a name="references-added"></a>Добавлены ссылки
Пакет NuGet хранилища Azure был добавлен в проект Visual Studio.  
Этот пакет добавляет следующие ссылки .NET:

* **Microsoft.Data.Edm**
* **Microsoft.Data.OData**
* **Microsoft.Data.Services.Client**
* **Microsoft.WindowsAzure.Configuration**
* **Microsoft.WindowsAzure.Storage**
* **Newtonsoft.Json**
* **System.Data**
* **System.Spatial**

Кроме того, добавлен пакет NuGet **Microsoft.Framework.Configuration.Json** .

## <a name="connection-string-for-azure-storage-added"></a>Добавлена строка подключения к хранилищу Azure
В файле config.json проекта был создан элемент с ключом и строкой подключения выбранной учетной записи хранилища.

Дополнительные сведения см. на сайте [ASP.NET 5](http://www.asp.net/vnext).

