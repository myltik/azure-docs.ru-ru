---
title: "Начало работы с инструментами эластичной базы данных — Azure | Документы Microsoft"
description: "Общее объяснение возможность гибко инструменты для баз данных базы данных SQL Azure, включая приложения для выполнения образца."
services: sql-database
documentationcenter: 
manager: jstrauss
author: anumjs
editor: CarlRabeler
ms.assetid: b6911f8d-2bae-4d04-9fa8-f79a3db7129d
ms.service: sql-database
ms.custom: scale out apps
ms.workload: On Demand
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/16/2017
ms.author: anjangsh
ms.openlocfilehash: 615e5b0bf299a844ea2d37476fc704c48e17b363
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/08/2017
---
# <a name="get-started-with-elastic-database-tools"></a>Начало работы с инструментами эластичной базы данных
В этом документе рассказывается об опыт разработки для [клиентской библиотеке эластичной базы данных](sql-database-elastic-database-client-library.md) , помогая запуска примера приложения. Пример приложения создается простое приложение сегментированных и рассматриваются ключевые возможности функции эластичной инструменты для баз данных базы данных SQL Azure. В этом документе описано, как [управлять сопоставлениями сегментов](sql-database-elastic-scale-shard-map-management.md), [настроить маршрутизацию, зависящую от данных](sql-database-elastic-scale-data-dependent-routing.md), и [создавать многосегментные запросы](sql-database-elastic-scale-multishard-querying.md). Клиентская библиотека для .NET, а также Java. 

## <a name="elastic-database-tools-for-java"></a>Инструменты для эластичных баз данных для Java
### <a name="prerequisites"></a>Технические условия
* Java Developer Kit (JDK), 1.8 или более поздней версии
* [Maven](http://maven.apache.org/download.cgi)
* Логический сервер в Azure или локальный экземпляр SQL Server

### <a name="download-and-run-the-sample-app"></a>Загрузка и запуск примера приложения
Чтобы построить JAR-файла и приступить к работе в пример проекта, выполните следующее: 
1. Клон [репозитории GitHub](https://github.com/Microsoft/elastic-db-tools-for-java) содержащий клиентской библиотеке, а также пример приложения. 

2. Изменить _./sample/src/main/resources/resource.properties_ файл, чтобы установить следующие:
    * TEST_CONN_USER;
    * TEST_CONN_PASSWORD;
    * TEST_CONN_SERVER_NAME.

3. Для построения образца проекта в _. / sample_ каталога, выполните следующую команду:

    ```
    mvn install
    ```
    
4. Чтобы запустить образец проекта в _. / sample_ каталога, выполните следующую команду: 
    
    ```
    mvn -q exec:java "-Dexec.mainClass=com.microsoft.azure.elasticdb.samples.elasticscalestarterkit.Program"
    ```
    
5. Дополнительные сведения о возможностях клиентской библиотеки Поэкспериментируйте с различными параметрами. Вы можете просмотреть код, чтобы узнать, как реализуется пример приложения.

    ![Описание процесса для Java][5]
    
Поздравляем! Вы успешно построения и запуска сегментированных первого приложения с помощью эластичных инструменты для баз данных в базе данных SQL Azure. Используйте Visual Studio или SQL Server Management Studio, чтобы подключиться к базе данных SQL, и ознакомьтесь с сегментами, созданными примером. Таким образом можно увидеть новые сегментированные базы данных и базу данных диспетчера сопоставлений сегментов, созданные демонстрационным приложением. 

Чтобы добавить клиентскую библиотеку Maven проекта, добавьте следующую зависимость в POM-файла:

```xml
<dependency> 
    <groupId>com.microsoft.azure</groupId> 
    <artifactId>elastic-db-tools</artifactId> 
    <version>1.0.0</version> 
</dependency> 
```

## <a name="elastic-database-tools-for-net"></a>Инструменты для эластичных баз данных для .NET 
### <a name="prerequisites"></a>Технические условия
* Visual Studio 2012 или более поздней версии с C#. Загрузите бесплатную версию на странице [Загрузок Visual Studio](http://www.visualstudio.com/downloads/download-visual-studio-vs.aspx).
* NuGet 2.7 или более поздней версии. Сведения о получении последней версии см. в разделе [Установка NuGet](http://docs.nuget.org/docs/start-here/installing-nuget).

### <a name="download-and-run-the-sample-app"></a>Загрузка и запуск примера приложения
Чтобы установить библиотеку, перейдите по ссылке [Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/). Эта библиотека устанавливается с примером приложения, описанным в следующем разделе.

Чтобы загрузить и запустить демонстрационное приложение, выполните следующие действия. 

1. Скачайте образец [Elastic DB Tools for Azure SQL — Getting Started](https://code.msdn.microsoft.com/windowsapps/Elastic-Scale-with-Azure-a80d8dc6) (Инструменты эластичной базы данных SQL Azure — приступая к работе) с сайта MSDN. Распакуйте пример в выбранное расположение.

2. Чтобы создать проект, откройте решение *ElasticScaleStarterKit.sln* из каталога *C#*.

3. В решении для примера проекта откройте файл *app.config*. Следуйте инструкциям в файле, чтобы добавить имя сервера базы данных SQL Azure и данные входа в систему (имя пользователя и пароль).

4. Создайте и запустите приложение. Когда вам будет предложено включите восстановления пакетов NuGet решения в Visual Studio. Это действие загружает последнюю версию клиентской библиотеке эластичной базы данных из NuGet.

5. Дополнительные сведения о возможностях клиентской библиотеки Поэкспериментируйте с различными параметрами. Обратите внимание, действия, которые использует приложение в консоли вывода и вы можете просматривать код в фоновом.
   
    ![Ход выполнения][4]

Поздравляем! Вы успешно построения и запуска сегментированных первого приложения с помощью эластичных инструменты для баз данных в базе данных SQL. Используйте Visual Studio или SQL Server Management Studio, чтобы подключиться к базе данных SQL, и ознакомьтесь с сегментами, созданными примером. Таким образом можно увидеть новые сегментированные базы данных и базу данных диспетчера сопоставлений сегментов, созданные демонстрационным приложением.

> [!IMPORTANT]
> Чтобы обеспечить синхронизацию с обновлениями Azure и базы данных SQL, рекомендуется всегда использовать последнюю версию Management Studio. [Обновите среду SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).
> 
> 

## <a name="key-pieces-of-the-code-sample"></a>Ключевые фрагменты программного кода демонстрационного приложения
* **Управление сегментов и сегментов сопоставляет**: кода показано, как работать с сегментами, диапазоны и сопоставления в *ShardManagementUtils.cs* файла. Дополнительные сведения см. в статье [Развертывание баз данных с использованием диспетчера карты сегментов](http://go.microsoft.com/?linkid=9862595).  

* **Маршрутизации в зависимости от данных**: маршрутизации транзакций в сегмент справа отображается в *DataDependentRoutingSample.cs* файла. Дополнительные сведения см. в статье [Маршрутизация, зависящая от данных](http://go.microsoft.com/?linkid=9862596). 

* **Запросы по нескольким сегментам**: запрос по сегментам проиллюстрирован в *MultiShardQuerySample.cs* файла. Дополнительные сведения см. в статье [Многосегментное формирование запросов](http://go.microsoft.com/?linkid=9862597).

* **Добавление пустой сегментов**: итеративный Добавление новых сегментов пустой выполняется код в *CreateShardSample.cs* файла. Дополнительные сведения см. в статье [Развертывание баз данных с использованием диспетчера карты сегментов](http://go.microsoft.com/?linkid=9862595).

## <a name="other-elastic-scale-operations"></a>Другие операции, относящиеся к эластичному масштабированию
* **Разделение существующего сегмента**: средство слияния разбиение предоставляется возможность разделения сегментов. Дополнительные сведения см. в статье [Перемещение данных между масштабируемыми облачными базами данных](sql-database-elastic-scale-overview-split-and-merge.md).

* **Слияние существующим сегментам**: слияний сегментов также выполняются с помощью средства разделения слияния. Дополнительные сведения см. в статье [Перемещение данных между масштабируемыми облачными базами данных](sql-database-elastic-scale-overview-split-and-merge.md).   

## <a name="cost"></a>Стоимость
Библиотека эластичной инструменты для баз данных предоставляется бесплатно. При использовании инструментов эластичной базы данных, при этом нет дополнительная плата за пределами затраты на использование Azure. 

Например, демонстрационное приложение создает новую базу данных. Затраты на эту возможность зависит от выбранной версии базы данных SQL и использования приложения Azure.

Сведения о ценах см. на [странице с ценами на базу данных SQL](https://azure.microsoft.com/pricing/details/sql-database/).

## <a name="next-steps"></a>Дальнейшие действия
Дополнительные сведения о средствах эластичных баз данных см. в следующих статьях:

* Примеры кода: 
  * Инструменты эластичных баз данных ([.NET](http://code.msdn.microsoft.com/Elastic-Scale-with-Azure-a80d8dc6?SRC=VSIDE), [Java](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-elasticdb-tools%22))
  * [Средства эластичной базы данных для SQL Azure — Entity Framework интеграции](http://code.msdn.microsoft.com/Elastic-Scale-with-Azure-bae904ba?SRC=VSIDE)
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
[5]: ./media/sql-database-elastic-scale-get-started/java-client-library.PNG

