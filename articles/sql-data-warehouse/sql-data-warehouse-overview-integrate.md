<properties
   pageTitle="Создание интегрированных решений с помощью хранилища данных SQL | Microsoft Azure"
   description="Инструменты и партнерские решения, которые интегрируются с хранилищем данных SQL"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="02/01/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

#Интеграция других служб с хранилищем данных SQL
Помимо базовых возможностей, хранилище данных SQL позволяет пользователям дополнительно использовать многие другие службы в Azure. В частности, мы приняли меры по глубокой интеграции со следующими службами:

+ Power BI
+ Фабрика данных Azure
+ Машинное обучение Azure
+ Azure Stream Analytics

Мы работаем над возможностями подключения к большему количеству служб в экосистеме Azure.

##Power BI
Интеграция с Power BI позволяет использовать вычислительные ресурсы хранилища данных SQL с возможностью создания динамических отчетов и визуализацией Power BI. На данный момент интеграция с Power BI включает:

+ **Прямое соединение**: расширенное соединение с логической опорой на хранилище данных SQL. Это обеспечивает более быстрый анализ в большем масштабе.
+ **Открыть в Power BI**: кнопка "Открыть в Power BI" передает сведения об экземпляре в Power BI, повышая надежность соединения. 

Дополнительные сведения можно найти в разделе [Использование Power BI с хранилищем данных SQL](./sql-data-warehouse-integrate-power-bi.md) или в [документации по Power BI](http://blogs.msdn.com/b/powerbi/archive/2015/06/24/exploring-azure-sql-data-warehouse-with-power-bi.aspx).

##Фабрика данных Azure
Фабрика данных Azure предоставляет пользователям управляемую платформу для создания сложных конвейеров извлечения и загрузки. Интеграция хранилища данных SQL с фабрикой данных Azure включает следующее.

+ **Хранимые процедуры**: управление выполнением хранимых процедур в хранилище данных SQL.

Дополнительные сведения можно найти в разделе [Работа с фабрикой данных Azure и хранилищем данных SQL](./sql-data-warehouse-integrate-azure-data-factory.md) или в [документации по фабрике данных Azure](https://azure.microsoft.com/documentation/services/data-factory/).

##Машинное обучение Azure
Машинное обучение Azure — это полностью управляемая аналитическая служба, позволяющая пользователям создавать сложные модели с использованием крупного набора прогнозируемых инструментов. Хранилище данных SQL поддерживается в качестве источника и цели для этих моделей со следующими возможностями.

+ **Чтение данных:** масштабное использование моделей с помощью T-SQL в хранилище данных SQL. 
+ **Запись данных:** применение изменений любой модели в хранилище данных SQL.

Дополнительные сведения можно найти в разделе [Использование машинного обучения Azure с хранилищем данных SQL](./sql-data-warehouse-integrate-azure-machine-learning.md) или в [документации по машинному обучению Azure](https://azure.microsoft.com/services/machine-learning/).

##Azure Stream Analytics
Azure Stream Analytics — это сложная полностью управляемая инфраструктура для обработки и потребления данных о событиях, создаваемых концентратором событий Azure. Интеграция с хранилищем данных SQL обеспечивает эффективную обработку и хранение потоковых данных наряду с реляционными данными, предоставляя более глубокий и расширенный анализ.

+ **Выходные данные заданий:** отправка выходных данных заданий Stream Analytics напрямую в хранилище данных SQL.

Дополнительные сведения можно найти в разделе об [интеграции со службой Azure Stream Analytics](./sql-data-warehouse-integrate-azure-stream-analytics.md) или в [документации по Azure Stream Analytics](https://azure.microsoft.com/documentation/services/stream-analytics/).

<!--Image references-->

<!--Article references-->
[development overview]: sql-data-warehouse-overview-develop/

[Azure Data Factory]: sql-data-warehouse-integrate-azure-data-factory.md
[Azure Machine Learning]: sql-data-warehouse-integrate-azure-machine-learning.md
[Azure Stream Analytics]: sql-data-warehouse-integrate-azure-stream-analytics.md
[Power BI]: sql-data-warehouse-integrate-power-bi.md
[Partners]: sql-data-warehouse-integrate-solution-partners.md

<!--MSDN references-->

<!--Other Web references-->

<!---HONumber=AcomDC_0204_2016-->