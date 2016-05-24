<properties
   pageTitle="Установка Visual Studio и SSDT для хранилища данных SQL | Microsoft Azure"
   description="Установка Visual Studio и SQL Server Data Tools (SSDT) для хранилища данных SQL Azure."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="05/16/2016"
   ms.author="sonyama;barbkess"/>

# Установка Visual Studio 2015 и SSDT для хранилища данных SQL

При разработке приложений для хранилища данных SQL мы рекомендуем использовать Visual Studio 2015 с последней версией SQL Server Data Tools (SSDT). Также поддерживается Visual Studio 2013 с SSDT.

Кроме того, для запуска запросов из интегрированной среды разработки (IDE) Visual Studio необходимо **обновление Microsoft SQL Server для средств работы с базами данных**. После установки этого расширения вы сможете просматривать объекты базы данных в дереве обозревателя объектов и выполнять запросы к хранилищу данных SQL.

> [AZURE.NOTE] Хранилище данных SQL пока не поддерживает проекты базы данных Visual Studio. Эта функция будет добавлена в одной из следующих версий.

## Шаг 1. Установка Visual Studio 2015

Скачайте и установите Visual Studio 2015, используя приведенные ниже ссылки. Если приложение Visual Studio 2013 или 2015 уже установлено, можно перейти к шагу 2 и установить SSDT.

1. [Загрузите Visual Studio 2015][] с сайта Visual Studio Team Services.
2. Выполните установку, следуя инструкциям по [установке Visual Studio][] с сайта MSDN, а затем выберите конфигурации по умолчанию.

## Шаг 2. Установка SSDT

Чтобы установить SSDT для Visual Studio, проверьте наличие обновления SSDT в Visual Studio, выполнив следующие действия.

1. В Visual Studio последовательно щелкните **Сервис**, **Расширения и обновления**, **Обновления**.
2. Выберите **Обновления продукта** и поищите элемент **Обновление Microsoft SQL Server для средств работы с базами данных**.

Если обновление не найдено, у вас установлена последняя версия. Чтобы убедиться, что компонент SSDT установлен, последовательно щелкните **Справка**, **О Microsoft Visual Studio** и найдите в списке пункт SQL Server Data Tools.

## Дальнейшие действия

Теперь, когда у вас установлена последняя версия SSDT, можно приступать к [подключению][] к базе данных.

<!--Anchors-->

<!--Image references-->

<!--Articles-->
[подключению]: ./sql-data-warehouse-get-started-connect.md

<!--Other-->
[Загрузите Visual Studio 2015]: https://www.visualstudio.com/downloads/
[установке Visual Studio]: https://msdn.microsoft.com/library/e2h7fzkw.aspx

<!---HONumber=AcomDC_0518_2016-->