---
title: "Что произошло с моим проектом облачных служб? | Документация Майкрософт"
description: "Сведения о том, что происходит в проекте облачных служб после подключения к учетной записи хранения Azure с помощью подключенных служб Visual Studio"
services: storage
documentationcenter: 
author: kraigb
manager: ghogen
editor: 
ms.assetid: ca0ea68d-f417-4ce8-9413-40d76f69cdea
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: vs-what-happened
ms.devlang: na
ms.topic: article
ms.date: 12/02/2016
ms.author: kraigb
ms.translationtype: HT
ms.sourcegitcommit: 5b6c261c3439e33f4d16750e73618c72db4bcd7d
ms.openlocfilehash: 4c9de56f6daf07097c0f593db37d14dce3bce05f
ms.contentlocale: ru-ru
ms.lasthandoff: 08/28/2017

---
# <a name="what-happened-to-my-cloud-services-project-visual-studio-azure-storage-connected-service"></a>Что произошло с моим проектом облачных служб (подключенными к службе хранилища Azure службами Visual Studio)?
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

## <a name="connection-string-for-azure-storage-added"></a>Добавлена строка подключения к хранилищу Azure
Созданы элементы с ключом и строкой подключения выбранной учетной записи хранилища. Внесены изменения в следующие файлы:

* **ServiceDefinition.csdef**
* **ServiceConfiguration.Cloud.cscfg**
* **ServiceConfiguration.Local.cscfg**


