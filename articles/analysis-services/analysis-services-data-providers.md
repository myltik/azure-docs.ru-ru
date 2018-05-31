---
title: Клиентские библиотеки, требуемые для подключения к службам Azure Analysis Services | Документы Майкрософт
description: Описываются клиентские библиотеки, необходимые для подключения клиентских приложений и средств к службам Azure Analysis Services
author: minewiskan
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: c6c92bdd2461a0f1147f15b5c1134c189c55a37c
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34193314"
---
# <a name="client-libraries-for-connecting-to-azure-analysis-services"></a>Клиентские библиотеки для подключения к службам Azure Analysis Services

Клиентские библиотеки требуются клиентским приложениям и средствам для подключения к серверам служб Analysis Services. 

## <a name="download-the-latest-client-libraries-windows-installer"></a>Скачивание последних версий клиентских библиотек (установщик Windows)  

|Загрузка  |Версия продукта  | 
|---------|---------|
|[MSOLAP (amd64)](https://go.microsoft.com/fwlink/?linkid=829576)    |    15.0.1.395      |
|[MSOLAP (x86)](https://go.microsoft.com/fwlink/?linkid=829575)     |    15.0.1.395      |
|[Объекты AMO](https://go.microsoft.com/fwlink/?linkid=829578)     |   15.0.4     |
|[ADOMD](https://go.microsoft.com/fwlink/?linkid=829577)     |    15.0.4     |

## <a name="amo-and-adomd-nuget-packages"></a>AMO и ADOMD (пакеты NuGet)

Клиентские библиотеки AMO (объекты управления Analysis Services) и ADOMD доступны в качестве устанавливаемых пакетов на сайте [NuGet.org](https://www.nuget.org/). Мы рекомендуем использовать ссылки на пакеты NuGet вместо установщика Windows. 

|Package  | Версия продукта  | 
|---------|---------|
|[Объекты AMO](https://www.nuget.org/packages/Microsoft.AnalysisServices.retail.amd64/)    |    15.0.2.0      |
|[ADOMD](https://www.nuget.org/packages/Microsoft.AnalysisServices.AdomdClient.retail.amd64/)     |   15.0.2.0      |

Сборки пакетов NuGet AssemblyVersion следуют семантической организации номера версии — "Главная.Второстепенная.Исправление". Ссылки на пакеты NuGet загружают ожидаемую версию, даже при наличии другой версии в глобальном кэше сборок (в результате установки MSI). Номер исправления увеличивается с каждым выпуском. Версии ADOMD и AMO синхронизируются.

## <a name="understanding-client-libraries"></a>Основные сведения о клиентских библиотеках

Службы Analysis Services используют три типа клиентских библиотек, именуемых также поставщиками данных. ADOMD.NET и объекты AMO (объекты управления Analysis Services) — это управляемые клиентские библиотеки. Поставщик Analysis Services OLE DB (MSOLAP DLL) — это собственная клиентская библиотека. Как правило, все три библиотеки устанавливаются одновременно. **Службы Azure Analysis Services поддерживают только последние версии всех трех библиотек**. 

Клиентские приложения Майкрософт, в частности Power BI Desktop и Excel, устанавливают все три клиентские библиотеки и обновляют их незамедлительно при выходе новых версий. Но в зависимости от версии или частоты обновлений могут устанавливаться устаревшие, а не новые версии клиентских библиотек, необходимые для служб Azure Analysis Services. Это же касается и пользовательских приложений или других интерфейсов, таких как AsCmd, TOM, ADOMD.NET. Для этих приложений клиентские библиотеки требуется устанавливать вручную или программным способом. Клиентские библиотеки, устанавливаемые вручную, включены в пакеты дополнительных компонентов SQL Server в качестве распространяемых пакетов. Однако они зависят от версии SQL Server, поэтому версия в пакетах может быть не последней.  

Клиентские библиотеки для клиентских подключений отличаются от поставщиков данных, требуемых для подключения с сервера служб Azure Analysis Services к источнику данных. Дополнительные сведения о подключениях к источнику данных см. в [этой статье](analysis-services-datasource.md).

## <a name="client-library-types"></a>Типы клиентских библиотек

### <a name="analysis-services-ole-db-provider-msolap"></a>Поставщик Analysis Services OLE DB (MSOLAP) 

 Поставщик Analysis Services OLE DB (MSOLAP) представляет собой собственную клиентскую библиотеку для подключения к базе данных служб Analysis Services. Он косвенно используется в ADOMD.NET и объектах AMO, делегируя запросы соединений поставщику данных. Также вы можете вызывать поставщик OLE DB напрямую из кода приложения.  
  
 Поставщик Analysis Services OLE DB устанавливается автоматически большинством средств и клиентских приложений, которые используются для доступа к базам данных служб Analysis Services. Его следует установить на всех компьютерах, с которых выполняется доступ к данным Analysis Services.  
  
 Поставщики OLE DB часто указываются в строках подключения. В строке подключения служб Analysis Services используется другой формат указания поставщика OLE DB: MSOLAP.\<version>.dll.

### <a name="amo"></a>AMO  

 AMO — это управляемая клиентская библиотека, которая используется для администрирования сервера и определения данных. Она устанавливается и используется средствами и клиентскими приложениями. Например, SQL Server Management Studio (SSMS) использует AMO для подключения к службам Analysis Services. Подключение с помощью объектов AMO предельно просто. Достаточно лишь указать `“data source=\<servername>”`. Когда подключение будет установлено, этот API можно использовать для работы с коллекциями баз данных и основными объектами. SSDT и SSMS используют объекты AMO для подключения к экземпляру служб Analysis Services.  

  
### <a name="adomd"></a>ADOMD

 ADOMD.NET — это управляемая клиентская библиотека, используемая для запросов к данным Analysis Services. Она устанавливается и используется средствами и клиентскими приложениями. 
  
 Во всех трех библиотеках используются похожие свойства строк подключения к базе данных. Практически любая строка подключения, созданная для ADOMD.NET с помощью [Microsoft.AnalysisServices.AdomdClient.AdomdConnection.ConnectionString](https://msdn.microsoft.com/library/microsoft.analysisservices.adomdclient.adomdconnection.connectionstring.aspx), подойдет и для объектов AMO и (или) поставщика Analysis Services OLE DB (MSOLAP). Дополнительные сведения см. в статье [Свойства строки подключения (службы Analysis Services)](https://docs.microsoft.com/sql/analysis-services/instances/connection-string-properties-analysis-services).  

  
##  <a name="bkmk_LibUpdate"></a>Как определить версию клиентской библиотеки   
  
### <a name="oleddb-msolap"></a>OLEDDB (MSOLAP)  
  
1.  Перейдите по пути C:\Program Files\Microsoft Analysis Services\AS OLEDB\. Если здесь несколько папок, выберите папку с самым большим номером.
  
2.  Щелкните правой кнопкой мыши файл **msolap.dll** и выберите  > **Свойства** > **Сведения**. Если файл называется msolap140.dll, это более ранняя версия. Его необходимо обновить.
    
    ![Сведения о клиентской библиотеке](media/analysis-services-data-providers/aas-msolap-details.png)
    
  
### <a name="amo"></a>AMO

1. Перейдите на сайт `C:\Windows\Microsoft.NET\assembly\GAC_MSIL\Microsoft.AnalysisServices\`. Если здесь несколько папок, выберите папку с самым большим номером.
2. Щелкните правой кнопкой мыши **Microsoft.AnalysisServices** > **Свойства** > **Сведения**.  

### <a name="adomd"></a>ADOMD

1. Перейдите на сайт `C:\Windows\Microsoft.NET\assembly\GAC_MSIL\Microsoft.AnalysisServices.AdomdClient\`. Если здесь несколько папок, выберите папку с самым большим номером.
2. Щелкните правой кнопкой мыши **Microsoft.AnalysisServices.AdomdClient** > **Свойства** > **Сведения**.  


## <a name="next-steps"></a>Дополнительная информация
[Подключение с помощью Excel](analysis-services-connect-excel.md)    
[Подключение с помощью Power BI](analysis-services-connect-pbi.md)
