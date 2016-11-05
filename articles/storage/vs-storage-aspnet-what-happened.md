---
title: Что произошло с моим проектом ASP.NET? | Microsoft Docs
description: Сведения о том, что происходит после добавления службы хранилища Azure в проект ASP.NET с помощью подключенных служб Visual Studio
services: storage
documentationcenter: ''
author: TomArcher
manager: douge
editor: ''

ms.service: storage
ms.workload: web
ms.tgt_pltfrm: vs-what-happened
ms.devlang: na
ms.topic: article
ms.date: 08/15/2016
ms.author: tarcher

---
# Что произошло с моим проектом ASP.NET (подключенными к службе хранилища Azure службами Visual Studio)?
## Добавлены ссылки
Пакет NuGet хранилища Azure был добавлен в проект Visual Studio. Этот пакет добавляет следующие ссылки .NET:

* **Microsoft.Data.Edm**
* **Microsoft.Data.OData**
* **Microsoft.Data.Services.Client**
* **Microsoft.WindowsAzure.Configuration**
* **Microsoft.WindowsAzure.Storage**
* **Newtonsoft.Json**
* **System.Data**
* **System.Spatial**

## Добавлена строка подключения к хранилищу Azure
В файле web.config проекта был создан элемент с ключом и строкой подключения выбранной учетной записи хранилища.

Дополнительные сведения см. в разделе [ASP.NET](http://www.asp.net).

<!---HONumber=AcomDC_0817_2016-->