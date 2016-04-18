<properties
   pageTitle="API Power BI Embedded для .NET"
   description="API Power BI Embedded для .NET"
   services="power-bi-embedded"
   documentationCenter=""
   authors="dvana"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="power-bi-embedded"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="powerbi"
   ms.date="03/29/2016"
   ms.author="derrickv"/>

# API Power BI Embedded для .NET

Основное внимание в предварительной версии **Microsoft Power BI Embedded** уделяется предоставлению большинства существующих функций API Power BI в составе службы Azure. С помощью этих функций вы сможете разрабатывать свои приложения. Вы также сможете программно подготавливать, разрабатывать и развертывать необходимые ресурсы и содержимое Power BI.

Вы сможете создавать рабочие области содержимого Power BI и управлять ими с помощью **API Power BI**. С помощью API вы можете:

  - Импортировать файл рабочего стола Power BI (PBIX) в рабочую область с помощью проверки подлинности на основе ключа.
  - Изменять строки подключения набора данных.
  - Получить **отчеты** для интеграции в свое приложение.
  - Задать учетные данные для набора данных для связи с соответствующим источником данных.
  - Для получения дополнительных сведений об **API Power BI** обратитесь к разделу [Приступая к работе с Microsoft Power BI Embedded](power-bi-embedded-get-started.md).

См. [Справочник по Power BI на сайте MSDN](https://msdn.microsoft.com/library/mt669800.aspx).

Ниже перечислены классы и методы, используемые в **примере Power BI Embedded**:

## Пространство имен Microsoft.PowerBI.Api.Beta

### Класс ReportsExtensions
|Имя|Описание
|---|---
|[GetReports(IReports, String, String)](https://msdn.microsoft.com/library/microsoft.powerbi.api.beta.reportsextensions.getreports.aspx)|Получает список доступных отчетов в заданной рабочей области
|[GetReportsAsync(IReports, String, String, CancellationToken)](https://msdn.microsoft.com/library/microsoft.powerbi.api.beta.reportsextensions.getreportsasync.aspx)|Получает список доступных отчетов в заданной рабочей области

## Пространство имен Microsoft.PowerBI.Security

### Методы PowerBIToken
|Имя| Описание
|---|---
|[CreateDevToken(String, Guid)](https://msdn.microsoft.com/library/mt670215.aspx)|Создает маркер разработчика со сроком действия по умолчанию, используемый для доступа к службам платформы Power BI
|[CreateProvisionToken(String)](https://msdn.microsoft.com/library/mt670218.aspx)|Создает маркер подготовки со сроком действия по умолчанию, используемый для управления рабочими областями в коллекции рабочей области

## См. также

- [Что такое Microsoft Power BI Embedded](power-bi-embedded-what-is-power-bi-embedded.md)
- [Приступая к работе с предварительной версией Microsoft Power BI Embedded](power-bi-embedded-get-started.md)

<!---HONumber=AcomDC_0406_2016-->