---
title: "Поставщики данных, требуемые для подключения клиентов к службам Azure Analysis Services | Документация Майкрософт"
description: "Содержит сведения о поставщиках данных, требуемых для подключения клиентов к службам Azure Analysis Services"
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 02/27/2016
ms.author: owend
translationtype: Human Translation
ms.sourcegitcommit: 538f282b28e5f43f43bf6ef28af20a4d8daea369
ms.openlocfilehash: 197e32058908533bc46b2ea2e38ff521a4a1fa3f
ms.lasthandoff: 04/07/2017


---

# <a name="data-providers-for-connecting-to-azure-analysis-services"></a>Поставщики данных для подключения к службам Azure Analysis Services

Поставщики данных, также известные как клиентские библиотеки, требуются клиентским приложениям для подключения к серверам служб Analysis Services. 

Службы Analysis Services используют три типа поставщиков данных. ADOMD.NET и объекты управления служб Analysis Services — это управляемые поставщики данных. Поставщик OLE DB служб Analysis Services (MSOLAP DLL) — это собственный поставщик данных. Как правило, все три поставщика устанавливаются одновременно. Службы Azure Analysis Services требуют последней версии поставщиков данных. 

Клиентские приложения Майкрософт, такие как Power BI Desktop и Excel, устанавливают все три поставщика данных. Однако в зависимости от версии Excel или от того, обновляются ли новые версии Excel и Power BI Desktop ежемесячно, установленные поставщики данных могут не обновиться до последней версии, требуемой службой Azure Analysis Service. Это же касается и пользовательских приложений или других интерфейсов, таких как AsCmd, TOM, ADOMD.NET. Для этих приложений поставщики данных требуется устанавливать вручную. Поставщики данных, устанавливаемые вручную, включены в пакеты дополнительных компонентов SQL Server в качестве распространяемых пакетов. Однако они зависят от версии SQL Server, поэтому версия в пакетах может быть не последней.  

Поставщики данных для клиентских подключений отличаются от поставщиков данных, требуемых для подключения с сервера служб Analysis Services Azure к источнику данных. Дополнительные сведения о подключениях к источнику данных см. в [этой статье](analysis-services-datasource.md).

## <a name="download-the-latest-preview-data-providers"></a>Скачивание самых новых поставщиков данных (**предварительные версии**)  
Используйте следующие поставщики данных для получения последних исправлений ошибок и обновлений. Рекомендуется использовать эти поставщики данных при подключении к предварительной версии служб Azure Analysis Services или к службам SQL Server vNext Analysis Services.

[Предварительная версия MSOLAP (amd64)](http://download.microsoft.com/download/4/8/2/482E5799-9B8E-4724-8A4C-F301BAE788EE/14.0.500.170/amd64/SQL_AS_OLEDB.msi)</br>
[Предварительная версия MSOLAP (x86)](http://download.microsoft.com/download/4/8/2/482E5799-9B8E-4724-8A4C-F301BAE788EE/14.0.500.170/x86/SQL_AS_OLEDB.msi)</br>
[Предварительная версия AMO](http://download.microsoft.com/download/4/8/2/482E5799-9B8E-4724-8A4C-F301BAE788EE/14.0.500.170/SQL_AS_AMO.msi)</br>
[Предварительная версия ADOMD](http://download.microsoft.com/download/4/8/2/482E5799-9B8E-4724-8A4C-F301BAE788EE/14.0.500.170/SQL_AS_ADOMD.msi)</br>

## <a name="download-the-latest-rtm-data-providers"></a>Скачивание самых новых поставщиков данных (**RTM**)  
Используйте следующие поставщики данных, если в рабочей среде требуются полностью выпущенные и поддерживаемые версии.

[MSOLAP (amd64)](https://go.microsoft.com/fwlink/?linkid=829576)</br>
[MSOLAP (x86)](https://go.microsoft.com/fwlink/?linkid=829575)</br>
[AMO](https://go.microsoft.com/fwlink/?linkid=829578)</br>
[ADOMD](https://go.microsoft.com/fwlink/?linkid=829577)</br>

## <a name="next-steps"></a>Дальнейшие действия
Установив самые новые поставщики данных, вы можете подключить свое клиентское приложение к серверу. Дополнительные сведения о подключении с клиента см. в статье [Получение данных из служб Azure Analysis Services](analysis-services-connect.md).

