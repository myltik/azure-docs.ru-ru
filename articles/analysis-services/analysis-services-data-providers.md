---
title: "Клиентские библиотеки, требуемые для подключения к службам Azure Analysis Services | Документы Майкрософт"
description: "Описываются клиентские библиотеки, необходимые для подключения клиентских приложений и средств к службам Azure Analysis Services"
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
ms.date: 04/14/2016
ms.author: owend
translationtype: Human Translation
ms.sourcegitcommit: 8c4e33a63f39d22c336efd9d77def098bd4fa0df
ms.openlocfilehash: c29a6627f712b9d89ac65e845f3ccb4fb87bf8fb
ms.lasthandoff: 04/20/2017


---

# <a name="client-libraries-for-connecting-to-azure-analysis-services"></a>Клиентские библиотеки для подключения к службам Azure Analysis Services

Клиентские библиотеки требуются клиентским приложениям и средствам для подключения к серверам служб Analysis Services. 

Службы Analysis Services используют три типа клиентских библиотек. ADOMD.NET и объекты управления служб Analysis Services — это управляемые клиентские библиотеки. Поставщик OLE DB служб Analysis Services (MSOLAP DLL) — это собственная клиентская библиотека. Как правило, все три библиотеки устанавливаются одновременно. Службам Azure Analysis Services требуются последние версии библиотек. 

Клиентские приложения Майкрософт, такие как Power BI Desktop и Excel, устанавливают все три клиентские библиотеки. Однако в зависимости от версии Excel или от того, обновляются ли новые версии Excel и Power BI Desktop ежемесячно, установленные клиентские библиотеки могут не обновиться до последней версии, требуемой службам Azure Analysis Services. Это же касается и пользовательских приложений или других интерфейсов, таких как AsCmd, TOM, ADOMD.NET. Для этих приложений клиентские библиотеки требуется устанавливать вручную. Клиентские библиотеки, устанавливаемые вручную, включены в пакеты дополнительных компонентов SQL Server в качестве распространяемых пакетов. Однако они зависят от версии SQL Server, поэтому версия в пакетах может быть не последней.  

Клиентские библиотеки для клиентских подключений отличаются от поставщиков данных, требуемых для подключения с сервера служб Azure Analysis Services к источнику данных. Дополнительные сведения о подключениях к источнику данных см. в [этой статье](analysis-services-datasource.md).

## <a name="download-the-latest-preview-client-libraries"></a>Скачивание последних **предварительных версий** клиентских библиотек  
Используйте перечисленные ниже клиентские библиотеки для получения последних исправлений ошибок и обновлений. Их рекомендуется использовать при подключении к службам Azure Analysis Services или SQL Server vNext Analysis Services.

[Предварительная версия MSOLAP (amd64)](http://download.microsoft.com/download/4/8/2/482E5799-9B8E-4724-8A4C-F301BAE788EE/14.0.500.170/amd64/SQL_AS_OLEDB.msi)</br>
[Предварительная версия MSOLAP (x86)](http://download.microsoft.com/download/4/8/2/482E5799-9B8E-4724-8A4C-F301BAE788EE/14.0.500.170/x86/SQL_AS_OLEDB.msi)</br>
[Предварительная версия AMO](http://download.microsoft.com/download/4/8/2/482E5799-9B8E-4724-8A4C-F301BAE788EE/14.0.500.170/SQL_AS_AMO.msi)</br>
[Предварительная версия ADOMD](http://download.microsoft.com/download/4/8/2/482E5799-9B8E-4724-8A4C-F301BAE788EE/14.0.500.170/SQL_AS_ADOMD.msi)</br>

## <a name="download-the-latest-rtm-client-libraries"></a>Скачивание последних **RTM-версий** клиентских библиотек  
Используйте перечисленные ниже клиентские библиотеки, если в рабочей среде требуются полностью выпущенные и поддерживаемые версии.

[MSOLAP (amd64)](https://go.microsoft.com/fwlink/?linkid=829576)</br>
[MSOLAP (x86)](https://go.microsoft.com/fwlink/?linkid=829575)</br>
[AMO](https://go.microsoft.com/fwlink/?linkid=829578)</br>
[ADOMD](https://go.microsoft.com/fwlink/?linkid=829577)</br>

## <a name="next-steps"></a>Дальнейшие действия
[Подключение к службам Azure Analysis Services](analysis-services-connect.md).

