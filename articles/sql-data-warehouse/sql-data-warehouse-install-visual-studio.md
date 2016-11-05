---
title: Установка Visual Studio и SSDT для хранилища данных SQL | Microsoft Docs
description: Установка Visual Studio и SQL Server Data Tools (SSDT) для хранилища данных SQL Azure.
services: sql-data-warehouse
documentationcenter: NA
author: sonyam
manager: barbkess
editor: ''

ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 08/16/2016
ms.author: sonyama;barbkess

---
# Установка Visual Studio 2015 и SSDT для хранилища данных SQL
При разработке приложений для хранилища данных SQL мы рекомендуем использовать Visual Studio 2015 с последней версией SQL Server Data Tools (SSDT). Также для обратной совместимости поддерживается Visual Studio 2013 Update 5 с SSDT.

С помощью Visual Studio с SSDT вы сможете использовать обозреватель объектов SQL Server для визуального исследования таблиц, представлений, хранимых процедур и многих других объектов в хранилище данных SQL, а также выполнения запросов.

> [!NOTE]
> Хранилище данных SQL пока не поддерживает проекты базы данных Visual Studio. Эта функция будет добавлена в одной из следующих версий.
> 
> 

## Шаг 1. Установка Visual Studio 2015
Скачайте и установите Visual Studio 2015, используя приведенные ниже ссылки. Если приложение Visual Studio 2013 или 2015 уже установлено, можно перейти к шагу 2 и установить SSDT.

1. [Скачайте Visual Studio 2015][Скачайте Visual Studio 2015].
2. Выполните установку, следуя инструкциям по [установке Visual Studio][установке Visual Studio] с сайта MSDN, а затем выберите настройки по умолчанию.

## Шаг 2. Установка SSDT
Чтобы установить SSDT для Visual Studio, проверьте наличие обновления SSDT в Visual Studio, выполнив следующие действия.

1. В Visual Studio последовательно щелкните **Сервис**, **Расширения и обновления**, **Обновления**.
2. Выберите **Обновления продукта** и поищите элемент **Обновление Microsoft SQL Server для средств работы с базами данных**.

Если обновление не найдено, у вас установлена последняя версия. Чтобы убедиться, что компонент SSDT установлен, последовательно щелкните **Справка**, **О Microsoft Visual Studio** и найдите в списке пункт SQL Server Data Tools. Последняя версия SSDT: 14.0.60525.0. Если команда установки недоступна в Visual Studio, также можно посетить страницу [Скачивание последней версии SQL Server Data Tools][Скачивание последней версии SQL Server Data Tools], чтобы скачать и установить SSDT вручную.

## Дальнейшие действия
Теперь, когда у вас установлена последняя версия SSDT, можно [подключиться][подключиться] к хранилищу данных SQL.

<!--Anchors-->

.<!--Image references-->

.<!--Articles-->

[подключиться]: ./sql-data-warehouse-query-visual-studio.md

<!--Other-->
[Скачайте Visual Studio 2015]: https://www.visualstudio.com/downloads/
[установке Visual Studio]: https://msdn.microsoft.com/library/e2h7fzkw.aspx
[Скачивание последней версии SQL Server Data Tools]: https://msdn.microsoft.com/library/mt204009.aspx

<!---HONumber=AcomDC_0817_2016-->