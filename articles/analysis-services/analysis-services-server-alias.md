---
title: Псевдонимы сервера Azure Analysis Services | Документация Майкрософт
description: Описывается создание и использование псевдонимов сервера.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 04/12/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: c563eb97e093924076684dcf40ac3a8fe5f96880
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34596759"
---
# <a name="alias-server-names"></a>Псевдонимы сервера

С помощью псевдонима сервера пользователи могут подключаться к серверу Analysis Services Azure, используя более короткий *псевдоним* вместо имени сервера. При подключении из клиентского приложения псевдоним указывается как конечная точка в формате протокола **ссылка://**. Затем конечная точка возвращает настоящее имя сервера для подключения.

Псевдонимы сервера удобно использовать в следующих сценариях:

- Перенос моделей между серверами без влияния на пользователей. 
- Понятные имена серверов легче запоминать. 
- Направление пользователей на разные серверы в разное время суток. 
- Направление пользователей в разные регионы к экземплярам, которые расположены ближе к ним, как при использовании диспетчера трафика Azure. 

Любая конечная точка HTTPS, которая возвращает допустимое имя сервера Analysis Services Azure, может использоваться в качестве псевдонима. Эта конечная точка должна поддерживать протокол HTTPS и использовать порт 443, и этот порт не должен быть указан в универсальном коде ресурса (URI).

![Псевдоним в формате ссылки](media/analysis-services-alias/aas-alias-browser.png)

При подключении из клиента псевдоним сервера вводится в формате протокола **ссылка://**. Например, в Power BI Desktop это выглядит следующим образом.

![Подключение Power BI Desktop](media/analysis-services-alias/aas-alias-connect-pbid.png)

## <a name="create-an-alias"></a>Создание псевдонима

Чтобы создать конечную точку псевдонима, можно использовать любой метод, который возвращает допустимое имя сервера Azure Analysis Services. Например, можно указать ссылку на файл в хранилище BLOB-объектов Azure, содержащий настоящее имя сервера, или создать и опубликовать приложение веб-форм ASP.NET.

В этом примере в Visual Studio создается приложение веб-форм ASP.NET. Со страницы Default.aspx удаляются ссылка на главную страницу и пользовательский элемент управления. Страница Default.aspx просто содержит приведенную ниже директиву Page.

```
<%@ Page Title="Home Page" Language="C#" AutoEventWireup="true" CodeBehind="Default.aspx.cs" Inherits="FriendlyRedirect._Default" %>
```

Событие Page_Load в Default.aspx.cs использует метод Response.Write() для возвращения имени сервера Azure Analysis Services.

```
protected void Page_Load(object sender, EventArgs e)
{
    this.Response.Write("asazure://<region>.asazure.windows.net/<servername>");
}
```

## <a name="see-also"></a>См. также

[Клиентские библиотеки](analysis-services-data-providers.md)   
[Подключение из Power BI Desktop](analysis-services-connect-pbi.md)
