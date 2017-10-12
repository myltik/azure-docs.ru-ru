---
title: "Приступая к работе с инструментами эластичных баз данных | Документы Майкрософт"
description: "Объяснение основных принципов инструментов эластичной базы данных SQL Azure, включая пример простого для запуска приложения."
services: sql-database
documentationcenter: 
manager: jhubbard
author: ddove
editor: CarlRabeler
ms.assetid: b6911f8d-2bae-4d04-9fa8-f79a3db7129d
ms.service: sql-database
ms.custom: scale out apps
ms.workload: sql-database
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/06/2017
ms.author: ddove
ms.openlocfilehash: 637463399593f4bc9ff5bfcbf67bf93b816efc7f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-elastic-database-tools"></a>Приступая к работе с инструментами эластичных баз данных
Настоящий документ выступает в качестве введения в процесс запуска примера приложения. В данном примере выполняется создание простого сегментированного приложения и исследуются основные возможности инструментов эластичной базы данных. В примере показаны функции [клиентской библиотеки эластичной базы данных](sql-database-elastic-database-client-library.md)

Чтобы установить библиотеку, перейдите по ссылке [Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/). Эта библиотека устанавливается с примером приложения, описанным в следующем разделе.

## <a name="prerequisites"></a>Предварительные требования
* Visual Studio 2012 или более поздней версии с C#. Загрузите бесплатную версию на странице [Загрузок Visual Studio](http://www.visualstudio.com/downloads/download-visual-studio-vs.aspx).
* NuGet 2.7 или более поздней версии. Сведения о получении последней версии см. в разделе [Установка NuGet](http://docs.nuget.org/docs/start-here/installing-nuget).

## <a name="download-and-run-the-sample-app"></a>Загрузка и запуск примера приложения
Пример приложения из раздела **Инструменты эластичных баз данных для Azure SQL — приступая к работе** позволяет познакомиться с наиболее важными аспектами процесса разработки сегментированных приложений, использующих инструменты эластичных баз данных. В нем описаны основные варианты использования для [управления сопоставлениями сегментов](sql-database-elastic-scale-shard-map-management.md), [маршрутизации, зависящей от данных](sql-database-elastic-scale-data-dependent-routing.md), и [многосегментного формирования запросов](sql-database-elastic-scale-multishard-querying.md). Чтобы загрузить и запустить демонстрационное приложение, выполните следующие действия. 

1. Скачайте образец [Elastic DB Tools for Azure SQL — Getting Started](https://code.msdn.microsoft.com/windowsapps/Elastic-Scale-with-Azure-a80d8dc6) (Инструменты эластичной базы данных SQL Azure — приступая к работе) с сайта MSDN. Распакуйте пример в выбранное расположение.

2. Чтобы создать проект, откройте решение **ElasticScaleStarterKit.sln** из каталога **C#**.

3. В решении для примера проекта откройте файл **app.config**. Следуйте приведенным там указаниям, чтобы добавить имя сервера базы данных SQL Azure и учетные данные (имя пользователя и пароль).

4. Создайте и запустите приложение. После соответствующего запроса разрешите Visual Studio восстановить пакеты NuGet для решения. В результате из NuGet скачивается самая новая версия клиентской библиотеки эластичной базы данных.

5. Попробуйте устанавливать различные значения параметров, чтобы более подробно исследовать возможности клиентской библиотеки. Обратите внимание на то, какие действия выполняет приложение, отслеживая выводимые им в консоль сообщения, и ознакомьтесь с отвечающим за эти действия программным кодом.
   
    ![Ход выполнения][4]

Поздравляем! Вы успешно создали и запустили свое первое сегментированное приложение с помощью инструментов эластичной базы данных SQL. Используйте Visual Studio или SQL Server Management Studio, чтобы подключиться к базе данных SQL, и ознакомьтесь с сегментами, созданными примером. Таким образом можно увидеть новые сегментированные базы данных и базу данных диспетчера сопоставлений сегментов, созданные демонстрационным приложением.

> [!IMPORTANT]
> Чтобы обеспечить синхронизацию с обновлениями Azure и базы данных SQL, рекомендуется всегда использовать последнюю версию Management Studio. [Обновите среду SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).
> 
> 

### <a name="key-pieces-of-the-code-sample"></a>Ключевые фрагменты программного кода демонстрационного приложения
* **Управление сегментами и сопоставлениями сегментов**. Этот программный код, демонстрирующий, каким образом следует работать с сегментами, диапазонами и сопоставлениями, взят из файла **ShardManagementUtils.cs**. Дополнительные сведения см. в статье [Развертывание баз данных с использованием диспетчера карты сегментов](http://go.microsoft.com/?linkid=9862595).  

* **Маршрутизация, зависящая от данных.** Маршрутизация транзакций к необходимому сегменту демонстрируется в файле **DataDependentRoutingSample.cs**. Дополнительные сведения см. в статье [Маршрутизация, зависящая от данных](http://go.microsoft.com/?linkid=9862596). 

* **Формирование запросов по нескольким сегментам.** Формирование запросов по сегментам демонстрируется в файле **MultiShardQuerySample.cs**. Дополнительные сведения см. в статье [Многосегментное формирование запросов](http://go.microsoft.com/?linkid=9862597).

* **Добавление пустых сегментов.** Итеративное добавление новых пустых сегментов выполняется программным кодом, который приведен в файле **CreateShardSample.cs**. Дополнительные сведения см. в статье [Развертывание баз данных с использованием диспетчера карты сегментов](http://go.microsoft.com/?linkid=9862595).

### <a name="other-elastic-scale-operations"></a>Другие операции, относящиеся к эластичному масштабированию
* **Разбиение имеющегося сегмента.** Возможность разбиения сегментов реализована с помощью **инструмента разбиения и объединения**. Дополнительные сведения см. в статье [Перемещение данных между масштабируемыми облачными базами данных](sql-database-elastic-scale-overview-split-and-merge.md).

* **Объединение имеющихся сегментов.** Объединение сегментов также выполняется с помощью **инструмента разбиения и объединения**. Дополнительные сведения см. в статье [Перемещение данных между масштабируемыми облачными базами данных](sql-database-elastic-scale-overview-split-and-merge.md).   

## <a name="cost"></a>Стоимость
Инструменты эластичной базы данных предоставляются бесплатно. Использование инструментов эластичной базы данных не предполагает взимания каких-либо дополнительных платежей, помимо оплаты за использование платформы Azure. 

Например, демонстрационное приложение создает новую базу данных. Взимаемая плата зависит от выбранной версии базы данных SQL и от использования данным приложением платформы Azure.

Сведения о ценах см. на [странице с ценами на базу данных SQL](https://azure.microsoft.com/pricing/details/sql-database/).

## <a name="next-steps"></a>Дальнейшие действия
Дополнительную информацию об инструментах эластичной базы данных см. в следующих разделах:

* Примеры кода: 
  * [Инструменты эластичной базы данных SQL Azure — приступая к работе](http://code.msdn.microsoft.com/Elastic-Scale-with-Azure-a80d8dc6?SRC=VSIDE)
  * [Инструменты эластичной базы данных SQL Azure — интеграция с Entity Framework](http://code.msdn.microsoft.com/Elastic-Scale-with-Azure-bae904ba?SRC=VSIDE).
  * [Эластичность сегментов в Центре сценариев](https://gallery.technet.microsoft.com/scriptcenter/Elastic-Scale-Shard-c9530cbe)
* Блог: [объявление, касающееся эластичного масштабирования](https://azure.microsoft.com/blog/2014/10/02/introducing-elastic-scale-preview-for-azure-sql-database/)
* Microsoft Virtual Academy: [Implementing Scale-Out Using Sharding with the Elastic Database Client Library Video](https://mva.microsoft.com/training-courses/elastic-database-capabilities-with-azure-sql-db-16554?l=lWyQhF1fC_6306218965) (Видео. Реализация масштабирования с использованием сегментирования с помощью клиентской библиотеки эластичной базы данных) 
* Channel 9: [видеообзор технологии эластичного масштабирования](http://channel9.msdn.com/Shows/Data-Exposed/Azure-SQL-Database-Elastic-Scale)
* Форум для обсуждений: [форум по базам данных SQL Azure](http://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted)
* Измерение производительности: [Счетчики производительности для диспетчера карты сегментов](sql-database-elastic-database-client-library.md)

<!--Anchors-->
[The Elastic Scale Sample Application]: #The-Elastic-Scale-Sample-Application
[Download and Run the Sample App]: #Download-and-Run-the-Sample-App
[Cost]: #Cost
[Next steps]: #next-steps

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-get-started/newProject.png
[2]: ./media/sql-database-elastic-scale-get-started/click-online.png
[3]: ./media/sql-database-elastic-scale-get-started/click-CSharp.png
[4]: ./media/sql-database-elastic-scale-get-started/output2.png

