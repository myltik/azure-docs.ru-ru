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
ms.sourcegitcommit: 13eb8ab1bf3c218f14b4c23ca1a46e9552d55b25
ms.openlocfilehash: d70b9f2c2a0cb1abe11dcfee9acb51dcb5552a60
ms.lasthandoff: 02/09/2017


---

# <a name="data-providers-for-connecting-to-azure-analysis-services"></a>Поставщики данных для подключения к службам Azure Analysis Services

Поставщики данных, также известные как клиентские библиотеки, требуются клиентским приложениям для подключения к серверам служб Analysis Services. 

Службы Analysis Services используют три типа поставщиков данных. ADOMD.NET и объекты управления служб Analysis Services — это управляемые поставщики данных. Поставщик OLE DB служб Analysis Services (MSOLAP DLL) — это собственный поставщик данных. Как правило, все три поставщика устанавливаются одновременно. Службы Azure Analysis Services требуют последней версии поставщиков данных. 

Клиентские приложения Майкрософт, такие как Power BI Desktop и Excel, устанавливают все три поставщика данных. Однако в зависимости от версии Excel или от того, обновляются ли новые версии Excel и Power BI Desktop ежемесячно, установленные поставщики данных могут не обновиться до последней версии, требуемой службой Azure Analysis Service. Это же касается и пользовательских приложений или других интерфейсов, таких как AsCmd, TOM, ADOMD.NET. Для этих приложений поставщики данных требуется устанавливать вручную. Поставщики данных, устанавливаемые вручную, включены в пакеты дополнительных компонентов SQL Server в качестве распространяемых пакетов. Однако они зависят от версии SQL Server, поэтому версия в пакетах может быть не последней.  

Поставщики данных для клиентских подключений отличаются от поставщиков данных, требуемых для подключения с сервера служб Analysis Services Azure к источнику данных. Дополнительные сведения о подключениях к источнику данных см. в [этой статье](analysis-services-datasource.md).
 
## <a name="download-the-latest-data-providers"></a>Скачивание самых новых поставщиков данных  

[MSOLAP (amd64)](https://go.microsoft.com/fwlink/?linkid=829576)</br>
[MSOLAP (x86)](https://go.microsoft.com/fwlink/?linkid=829575)</br>
[AMO](https://go.microsoft.com/fwlink/?linkid=829578)</br>
[ADOMD](https://go.microsoft.com/fwlink/?linkid=829577)</br>

## <a name="next-steps"></a>Дальнейшие действия
Установив самые новые поставщики данных, вы можете подключить свое клиентское приложение к серверу. Дополнительные сведения о подключении с клиента см. в статье [Получение данных из служб Azure Analysis Services](analysis-services-connect.md).

