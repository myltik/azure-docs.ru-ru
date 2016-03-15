<properties
   pageTitle="Установка Visual Studio и (или) SSDT для хранилища данных SQL | Microsoft Azure"
   description="Установка Visual Studio и (или) средств разработки SSDT для хранилища данных SQL Azure"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="twounder"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="03/03/2016"
   ms.author="mausher;barbkess;sonyama"/>

# Установка Visual Studio 2015 и (или) SSDT для хранилища данных SQL

При разработке приложений для хранилища данных SQL рекомендуется использовать Visual Studio 2013 или более поздней версии в сочетании с последней версией SQL Server Data Tools (SSDT).

Для запуска запросов из интегрированной среды разработки (IDE) Visual Studio необходимо установить только SSDT. Будет выполнена установка IDE Visual Studio и SSDT, и вы сможете использовать обозреватель объектов SQL Server для подключения к серверу Azure SQL Server. После этого вы сможете просматривать и выполнять запросы к базам данных в хранилище данных SQL.


## Шаг 1. Загрузите и установите Visual Studio.

Если вы решили установить Visual Studio, можно использовать Visual Studio 2013 или Visual Studio 2015 с хранилищем данных SQL. Если приложение Visual Studio 2013 или 2015 уже установлено, перейдите к шагу 2 и установите SSDT.

Чтобы установить Visual Studio 2015, сделайте следующее.

1. [Загрузите Visual Studio 2015](https://www.visualstudio.com/downloads) с сайта Visual Studio Team Services.
2. Выполните установку, следуя инструкциям [Установка Visual Studio](https://msdn.microsoft.com/library/e2h7fzkw.aspx) с сайта MSDN, а затем выберите конфигурации по умолчанию.

## Шаг 2. Загрузка и установка последней версии SQL Server Data Tools (SSDT)

Независимо от того, установлено ли приложение Visual Studio, необходимо установить последнюю версию SQL Server Data Tools (SSDT), поддерживающую хранилище данных SQL.

Чтобы установить последнюю версию SSDT, сделайте следующее.

1. [Загрузите предварительную версию SQL Server Data Tools](https://msdn.microsoft.com/library/mt204009.aspx) для Visual Studio 2013 или 2015.
2. Выполните установку, следуя инструкциям на сайте загрузки.

## Дальнейшие действия

Теперь, когда у вас установлена последняя версия SSDT, можно приступать к [подключению](./sql-data-warehouse-get-started-connect.md) к базе данных.

<!--Anchors-->

<!--Image references-->

<!---HONumber=AcomDC_0309_2016-->