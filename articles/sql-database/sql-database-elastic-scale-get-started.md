---
title: "Приступая к работе с инструментами эластичных баз данных"
description: "Объяснение основных принципов инструментов эластичной базы данных SQL Azure, включая пример простого для запуска приложения."
services: sql-database
documentationcenter: 
manager: jhubbard
author: ddove
editor: CarlRabeler
ms.assetid: b6911f8d-2bae-4d04-9fa8-f79a3db7129d
ms.service: sql-database
ms.workload: sql-database
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2016
ms.author: ddove
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: a42f6ed3bce42cbd5841f241c2f735b87252cb3f


---
# <a name="get-started-with-elastic-database-tools"></a>Приступая к работе с инструментами эластичных баз данных
Настоящий документ выступает в качестве введения в процесс запуска примера приложения. В данном примере выполняется создание простого сегментированного приложения и исследуются основные возможности инструментов эластичной базы данных. В примере показаны функции [клиентской библиотеки эластичной базы данных](sql-database-elastic-database-client-library.md)

Чтобы установить библиотеку, перейдите по ссылке [Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/). Библиотека устанавливается с примером приложения, описанным ниже.

## <a name="prerequisites"></a>Предварительные требования
1. Требуется Visual Studio 2012 или выше с пакетом C#. Загрузите бесплатную версию на странице [Загрузок Visual Studio](http://www.visualstudio.com/downloads/download-visual-studio-vs.aspx).
2. Maven 2.7 или выше. раздел [Установка NuGet](http://docs.nuget.org/docs/start-here/installing-nuget)

## <a name="download-and-run-the-sample-app"></a>Загрузка и запуск примера приложения
Пример приложения из раздела **Эластичные базы данных SQL Azure — приступая к работе** позволяет познакомиться с наиболее важными аспектами процесса разработки сегментированных приложений с использованием инструментов эластичных баз данных SQL Azure. В нем описаны основные случаи использования для [управления сопоставлениями сегментов](sql-database-elastic-scale-shard-map-management.md), [маршрутизации, зависящей от данных](sql-database-elastic-scale-data-dependent-routing.md), и [многосегментного формирования запросов](sql-database-elastic-scale-multishard-querying.md). Чтобы загрузить и запустить демонстрационное приложение, выполните следующие действия. 

1. Откройте Visual Studio и выберите **Файл -> Создать -> Проект**.
2. В открывшемся диалоговом окне нажмите на пункт **В сети**.
   
    ![Новый проект>В сети][2]
3. Затем щелкните пункт **Visual C#** в разделе **Примеры**.
   
    ![Нажатие Visual C#][3]
4. Чтобы найти пример, в поле поиска введите **эластичная база данных**. Появится заголовок **Elastic DB Tools for Azure SQL - Getting Started** (Инструменты эластичной базы данных SQL Azure — приступая к работе).
   
    ![Поле поиска][1]
5. Выберите необходимый пример, укажите название и месторасположение для нового проекта, а затем нажмите кнопку **OK** , чтобы создать этот проект.
6. Откройте файл **app.config** в решении для данного демонстрационного проекта и следуйте приведенным в нем указаниям, чтобы добавить имя сервера базы данных SQL Azure и учетные данные (имя пользователя и пароль).
7. Создайте и запустите приложение. После соответствующего запроса разрешите Visual Studio восстановить пакеты NuGet из данного решения. В результате этого будет выполнена скачивание самых новых версий клиентских библиотек эластичной базы данных из NuGet.
8. Попробуйте устанавливать различные значения параметров, чтобы более подробно исследовать возможности клиентской библиотеки. Обратите внимание на то, какие действия выполняет приложение, отслеживая выводимые им в консоль сообщения, и ознакомьтесь с отвечающим за эти действия программным кодом.
   
    ![ход выполнения][4]

Поздравляем! Вы успешно создали и запустили свое первое сегментированное приложение с помощью инструментов эластичной базы данных SQL Azure. Ознакомьтесь с сегментами, созданными примером приложения, подключившись с помощью Visual Studio или SQL Server Management Studio к серверу базы данных Azure. Таким образом можно увидеть новые сегментированные базы данных и базу данных диспетчера сопоставлений сегментов, созданные демонстрационным приложением.

> [!IMPORTANT]
> Чтобы обеспечить синхронизацию с обновлениями Microsoft Azure и Базой данных SQL, рекомендуется всегда использовать последнюю версию Management Studio. [Обновите среду SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).
> 
> 

### <a name="key-pieces-of-the-code-sample"></a>Ключевые фрагменты программного кода демонстрационного приложения
1. **Управление сегментами и сопоставлениями сегментов.** Этот программный код, демонстрирующий, каким образом следует работать с сегментами, диапазонами и сопоставлениями, взят из файла **ShardMapManagerSample.cs**. Дополнительные сведения по этой теме см. здесь: [Развертывание баз данных с использованием диспетчера карты сегментов](http://go.microsoft.com/?linkid=9862595).  
2. **Маршрутизация, зависящая от данных.** Маршрутизация транзакций к необходимому сегменту демонстрируется в файле **DataDependentRoutingSample.cs**. Дополнительные сведения см. в статье [Маршрутизация, зависящая от данных](http://go.microsoft.com/?linkid=9862596). 
3. **Формирование запросов по нескольким сегментам.** Формирование запросов по сегментам демонстрируется в файле **MultiShardQuerySample.cs**. Дополнительные сведения приводятся в статье [Многосегментное формирование запросов](http://go.microsoft.com/?linkid=9862597).
4. **Добавление пустых сегментов.** Итеративное добавление новых пустых сегментов выполняется программным кодом, который приводится в файле **AddNewShardsSample.cs**. Подробные сведения по этому вопросу можно найти здесь: [Развертывание баз данных с использованием диспетчера карты сегментов](http://go.microsoft.com/?linkid=9862595).

### <a name="other-elastic-scale-operations"></a>Другие операции, относящиеся к эластичному масштабированию
1. **Разбиение имеющегося сегмента.** Возможность разбиения сегментов реализована с помощью **инструмента разбиения и объединения**. Дополнительные сведения об этом инструменте можно найти здесь: [Перемещение данных между масштабируемыми облачными базами данных](sql-database-elastic-scale-overview-split-and-merge.md).
2. **Объединение имеющихся сегментов.** Объединение сегментов также выполняется с помощью **инструмента разбиения и объединения**. Дополнительные сведения см. в следующей статье: [Перемещение данных между масштабируемыми облачными базами данных](sql-database-elastic-scale-overview-split-and-merge.md).   

## <a name="cost"></a>Стоимость
Инструменты эластичной базы данных предоставляются бесплатно. Использование инструментов эластичной базы данных не предполагает взимания каких-либо дополнительных платежей помимо оплаты за использование платформы Azure. 

Например, демонстрационное приложение создает новую базу данных. Оплачиваемая стоимость зависит от выбранной версии базы данных SQL Azure и от использования данным приложением платформы Azure.

Сведения о ценах см. на [странице с ценами на базу данных SQL](https://azure.microsoft.com/pricing/details/sql-database/).

## <a name="next-steps"></a>Дальнейшие действия
Дополнительную информацию об инструментах эластичной базы данных см. в следующих разделах:

* [Карта документации по инструментах эластичной базы данных](https://azure.microsoft.com/documentation/learning-paths/sql-database-elastic-scale/) 
* Примеры программного кода: 
  * [Эластичная база данных SQL Azure — приступая к работе](http://code.msdn.microsoft.com/Elastic-Scale-with-Azure-a80d8dc6?SRC=VSIDE)
  * [Эластичная база данных SQL Azure — интеграция с Entity Framework](http://code.msdn.microsoft.com/Elastic-Scale-with-Azure-bae904ba?SRC=VSIDE)
  * [Эластичность сегментов в Центре сценариев](https://gallery.technet.microsoft.com/scriptcenter/Elastic-Scale-Shard-c9530cbe)
* Блог: [Объявления, касающиеся эластичного масштабирования](https://azure.microsoft.com/blog/2014/10/02/introducing-elastic-scale-preview-for-azure-sql-database/)
* Microsoft Virtual Academy: [Implementing Scale-Out Using Sharding with the Elastic Database Client Library Video](https://mva.microsoft.com/en-US/training-courses/elastic-database-capabilities-with-azure-sql-db-16554?l=lWyQhF1fC_6306218965) (Видео. Реализация масштабирования с использованием сегментирования с помощью клиентской библиотеки эластичной базы данных) 
* Channel 9: [Видеообзор технологии эластичного масштабирования](http://channel9.msdn.com/Shows/Data-Exposed/Azure-SQL-Database-Elastic-Scale)
* Форум для обсуждений: [Форум по базам данных SQL Azure](http://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted)
* Измерение производительности: [Счетчики производительности для диспетчера карты сегментов](sql-database-elastic-database-client-library.md)

<!--Anchors-->
[Пример приложения для эластичного масштабирования]: #The-Elastic-Scale-Sample-Application
[Download and Run the Sample App]: #Download-and-Run-the-Sample-App
[Стоимость]: #Cost
[Дальнейшие действия]: #next-steps

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-get-started/newProject.png
[2]: ./media/sql-database-elastic-scale-get-started/click-online.png
[3]: ./media/sql-database-elastic-scale-get-started/click-CSharp.png
[4]: ./media/sql-database-elastic-scale-get-started/output2.png




<!--HONumber=Nov16_HO3-->


