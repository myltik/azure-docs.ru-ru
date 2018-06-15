---
title: Установка Visual Studio и SSDT для хранилища данных SQL | Документация Майкрософт
description: Установка Visual Studio и SQL Server Data Tools (SSDT) для хранилища данных SQL Azure.
services: sql-data-warehouse
author: kavithaj
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: consume
ms.date: 04/17/2018
ms.author: kavithaj
ms.reviewer: igorstan
ms.openlocfilehash: a2f01424dedb977000d0e4150f4a31c1a9a21cfb
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2018
ms.locfileid: "31790650"
---
# <a name="install-visual-studio-and-ssdt-for-sql-data-warehouse"></a>Установка Visual Studio и SSDT для хранилища данных SQL
При разработке приложений для хранилища данных SQL мы рекомендуем использовать последнюю версию Visual Studio с последней версией SQL Server Data Tools (SSDT).  Также для обратной совместимости поддерживается Visual Studio 2013 Update 5 с SSDT.  

С помощью Visual Studio с SSDT вы сможете использовать обозреватель объектов SQL Server для визуального исследования таблиц, представлений, хранимых процедур и многих других объектов в хранилище данных SQL, а также выполнения запросов.

> [!NOTE]
> Хранилище данных SQL пока не поддерживает проекты базы данных Visual Studio. Для получения периодических обновлений об этой функции проголосуйте на сайте [UserVoice].
> 
> 

## <a name="step-1-install-visual-studio"></a>Шаг 1. Установка Visual Studio
Скачайте и установите Visual Studio, используя приведенные ниже ссылки. Если приложение Visual Studio 2013 или его более поздняя версия уже установлена, можно перейти к шагу 2 и установить SSDT.

1. [Скачайте Visual Studio][].
2. Выполните установку, следуя инструкциям по [установке Visual Studio][Installing Visual Studio] с сайта MSDN, а затем выберите настройки по умолчанию.

## <a name="step-2-install-ssdt"></a>Шаг 2. Установка SSDT
Чтобы установить SSDT для Visual Studio, сначала проверьте наличие обновления SSDT в Visual Studio, выполнив следующие действия.

1. В Visual Studio щелкните **Сервис** / **Расширения и обновления…** / **Обновления**
2. Выберите **Обновления продукта** и найдите элемент **Обновление Microsoft SQL Server для средств работы с базами данных**.

Если обновление не найдено, у вас установлена последняя версия.  Чтобы убедиться, что компонент SSDT установлен, выберите **Справка** / **О Microsoft Visual Studio** и найдите в списке SQL Server Data Tools. Последняя версия SSDT: 14.0.60525.0. Если команда установки недоступна в Visual Studio, также можно посетить страницу [Скачать SQL Server Data Tools (SSDT)][SSDT Download], чтобы скачать и установить SSDT вручную.

## <a name="next-steps"></a>Дополнительная информация
Теперь, когда у вас установлена последняя версия SSDT, можно [подключиться][connect] к хранилищу данных SQL.

<!--Anchors-->

<!--Image references-->

<!--Articles-->
[connect]: ./sql-data-warehouse-query-visual-studio.md

<!--Other-->
[Скачайте Visual Studio]: https://www.visualstudio.com/downloads/
[Installing Visual Studio]: https://msdn.microsoft.com/library/e2h7fzkw.aspx
[SSDT Download]: https://msdn.microsoft.com/library/mt204009.aspx
[UserVoice]: https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/13313247-database-project-from-visual-studio-to-support-azu
