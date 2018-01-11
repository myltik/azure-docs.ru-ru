---
title: "Имена серверов псевдоним Azure Analysis Services | Документы Microsoft"
description: "Описывает создание и использование псевдонимов сервера."
services: analysis-services
documentationcenter: 
author: minewiskan
manager: kfile
editor: 
ms.assetid: 
ms.service: analysis-services
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2017
ms.author: owend
ms.openlocfilehash: a947dde1551c653faa54f088c1712c41c7657aa0
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/14/2017
---
# <a name="alias-server-names"></a>Имена псевдонимов сервера

С помощью псевдонима имя сервера, пользователи могут подключаться к серверу служб Analysis Services Azure с более коротким *псевдоним* вместо имени сервера. При подключении из клиентского приложения, как конечную точку, используя указан псевдоним **ссылка: / /** формат протокола. Конечная точка затем возвращает имя реальной сервера для подключения.

Имена псевдонимов сервера подходят для:

- Миграция моделей между серверами, не оказывая влияние на пользователей. 
- Имена серверов понятное легче для запоминания. 
- Переход пользователей на разных серверах, в разное время суток. 
- Переход пользователей в разных регионах для экземпляров, которые расположены ближе друг от друга, как и при использовании диспетчера трафика Azure. 

Любой конечной точке HTTP, которая возвращает допустимое имя сервера служб Analysis Services Azure можно использовать в качестве псевдонима.

![Псевдоним, используя формат ссылки](media/analysis-services-alias/aas-alias-browser.png)

При подключении клиента, имя псевдонима сервера вводится с использованием **ссылка: / /** формат протокола. Например в Power BI Desktop:

![Подключение Power BI Desktop](media/analysis-services-alias/aas-alias-connect-pbid.png)

## <a name="create-an-alias"></a>Создать псевдоним

Чтобы создать конечную точку псевдоним, можно использовать любой метод, который возвращает допустимое имя сервера Azure Analysis Services. Например, ссылку на файл в хранилище Azure BLOB-объект, содержащий реальные сервера имя, или создать и опубликовать приложение веб-форм ASP.NET.

В этом примере в Visual Studio создается веб-приложения форм ASP.NET. На странице Default.aspx удаляются ссылки и пользовательского элемента управления главной страницы. Содержимое Default.aspx является просто страницы следующую директиву:

```
<%@ Page Title="Home Page" Language="C#" AutoEventWireup="true" CodeBehind="Default.aspx.cs" Inherits="FriendlyRedirect._Default" %>
```

Событие Page_Load в Default.aspx.cs метод Response.Write() возвращает имя сервера служб аналитики Azure.

```
protected void Page_Load(object sender, EventArgs e)
{
    this.Response.Write("asazure://<region>.asazure.windows.net/<servername>");
}
```

## <a name="see-also"></a>См. также

[Клиентские библиотеки](analysis-services-data-providers.md)   
[Подключение из Power BI Desktop](analysis-services-connect-pbi.md)
