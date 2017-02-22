---
title: "Установка Visual Studio и SSDT для хранилища данных SQL | Документация Майкрософт"
description: "Установка Visual Studio и SQL Server Data Tools (SSDT) для хранилища данных SQL Azure."
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: 
ms.assetid: 0ed9b406-9b42-4fe6-b963-fe0a5b48aac1
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 10/31/2016
ms.author: barbkess
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 9c44f417d465aedcbbc731757282e18985b0435c


---
# <a name="install-visual-studio-2015-and-ssdt-for-sql-data-warehouse"></a>Установка Visual Studio 2015 и SSDT для хранилища данных SQL
При разработке приложений для хранилища данных SQL мы рекомендуем использовать Visual Studio 2015 с последней версией SQL Server Data Tools (SSDT).  Также для обратной совместимости поддерживается Visual Studio 2013 Update 5 с SSDT.  

С помощью Visual Studio с SSDT вы сможете использовать обозреватель объектов SQL Server для визуального исследования таблиц, представлений, хранимых процедур и многих других объектов в хранилище данных SQL, а также выполнения запросов.

> [!NOTE]
> Хранилище данных SQL пока не поддерживает проекты базы данных Visual Studio.  Эта функция будет добавлена в одной из следующих версий.
> 
> 

## <a name="step-1-install-visual-studio-2015"></a>Шаг 1. Установка Visual Studio 2015
Скачайте и установите Visual Studio 2015, используя приведенные ниже ссылки. Если приложение Visual Studio 2013 или 2015 уже установлено, можно перейти к шагу 2 и установить SSDT.

1. [Скачайте Visual Studio 2015][Download Visual Studio 2015].
2. Выполните установку, следуя инструкциям по [установке Visual Studio][Installing Visual Studio] с сайта MSDN, а затем выберите настройки по умолчанию.

## <a name="step-2-install-ssdt"></a>Шаг 2. Установка SSDT
Чтобы установить SSDT для Visual Studio, проверьте наличие обновления SSDT в Visual Studio, выполнив следующие действия.

1. В Visual Studio щелкните **Сервис** / **Расширения и обновления…** / **Обновления**.
2. Выберите **Обновления продукта** и найдите элемент **Обновление Microsoft SQL Server для средств работы с базами данных**.

Если обновление не найдено, у вас установлена последняя версия.  Чтобы убедиться, что компонент SSDT установлен, выберите **Справка** / **О Microsoft Visual Studio** и найдите в списке SQL Server Data Tools.  Последняя версия SSDT: 14.0.60525.0.  Если команда установки недоступна в Visual Studio, также можно посетить страницу [Скачать SQL Server Data Tools (SSDT)][SSDT Download], чтобы скачать и установить SSDT вручную.

## <a name="next-steps"></a>Дальнейшие действия
Теперь, когда у вас установлена последняя версия SSDT, можно [подключиться][connect] к хранилищу данных SQL.

<!--Anchors-->

<!--Image references-->

<!--Articles-->
[connect]: ./sql-data-warehouse-query-visual-studio.md

<!--Other-->
[Download Visual Studio 2015]: https://www.visualstudio.com/downloads/
[Installing Visual Studio]: https://msdn.microsoft.com/library/e2h7fzkw.aspx
[SSDT Download]: https://msdn.microsoft.com/library/mt204009.aspx



<!--HONumber=Dec16_HO2-->


