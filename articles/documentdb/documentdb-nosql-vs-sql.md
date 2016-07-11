<properties
	pageTitle="Когда следует использовать NoSQL и SQL | Microsoft Azure"
	description="Сравните преимущества использования нереляционных решений NoSQL и решений SQL. Узнайте, какое решение в наибольшей степени соответствует вашим потребностям — службы Microsoft Azure NoSQL или SQL Server."
	keywords="NoSQL и SQL, когда следует использовать NoSQL, SQL и NoSQL"
	services="documentdb"
	documentationCenter=""
	authors="mimig1"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="documentdb"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article" 
	ms.date="06/24/2016"
	ms.author="mimig"/>

# Базы данных NoSQL и SQL

Сервер SQL Server и реляционные базы данных (реляционные СУБД) считаются самыми надежными уже более 20 лет. Однако возросшая потребность в быстрой обработке больших объемов данных разных типов привела к изменению самого характера требований к хранилищам данных со стороны разработчиков приложений. В ходе реализации этого сценария стали популярными базы данных NoSQL, которые обеспечивают хранение неструктурированных и разнородных данных в нужном масштабе.

NoSQL — это категория баз данных, которые заметно отличаются от баз данных SQL. NoSQL часто используется для описания систем управления данными, которые не относятся к SQL, или подхода к управлению данными, который предусматривает использование не только SQL. Существует ряд технологий категории NoSQL, включая базы данных документов, хранилища пар "ключ — значение", хранилища семейств столбцов, а также графовые базы данных, которые часто используются в играх, приложениях для работы с социальными сетями и приложениях IoT.

![NoSQL и SQL: обзорная схема с распространенными сценариями и моделями данных](./media/documentdb-nosql-vs-sql/nosql-vs-sql-overview.png)

В этой статье описаны различия между базами данных SQL и NoSQL, а также изложены общие сведения о предложениях SQL и NoSQL от Майкрософт.

## Когда используются базы данных NoSQL?

Предположим, вы создаете новую социальную сеть (сайт). Пользователи могут создавать записи, а также добавлять к ним изображения, видео и музыку. Другие пользователи могут комментировать эти записи, а также оценивать их с помощью отметок "Нравится". На целевой странице будет расположен канал записей, которыми пользователи смогут делиться и с которыми они смогут взаимодействовать.

Так как вы будете хранить эти данные? Если вы знакомы с SQL, возможно, вы уже начали строить подобную схему:

![NoSQL и SQL: схема с реляционной моделью данных для социальной сети (сайта)](./media/documentdb-nosql-vs-sql/nosql-vs-sql-social.png)

Пока что все выглядит неплохо, но теперь представьте себе структуру отдельной записи, а также способ ее отображения. Если вы хотите отобразить запись и связанные изображения, аудио, видео, комментарии, отметки и сведения о пользователе на веб-сайте или в приложении, только для получения содержимого вам нужно выполнить запрос к восьми объединениям таблиц. А теперь представьте поток записей, которые динамически загружаются и отображаются на экране, и вы поймете, что для выполнения этой задачи необходимы тысячи запросов к большому количеству объединений.

Теперь для хранения данных вы можете использовать реляционные решения, например, SQL Server. Но есть еще один вариант, который упрощает этот подход. Это база данных NoSQL. Когда вы преобразуете запись в документ JSON, как показано ниже, сохраняя его в DocumentDB, службе базы данных документов Azure NoSQL, вы можете повысить производительность и получить запись полностью с помощью одного запроса и без объединений. Это гораздо более простой и эффективный способ.

    {
        "id":"ew12-res2-234e-544f",
        "title":"post title",
        "date":"2016-01-01",
        "body":"this is an awesome post stored on NoSQL",
        "createdBy":User,
        "images":["http://myfirstimage.png","http://mysecondimage.png"],
        "videos":[
            {"url":"http://myfirstvideo.mp4", "title":"The first video"},
            {"url":"http://mysecondvideo.mp4", "title":"The second video"}
        ],
        "audios":[
            {"url":"http://myfirstaudio.mp3", "title":"The first audio"},
            {"url":"http://mysecondaudio.mp3", "title":"The second audio"}
        ]
    }

Кроме того, эти данные можно секционировать по идентификатору записи, что обеспечивает естественное масштабирование данных с учетом характеристик NoSQL. Также системы NoSQL позволяют разработчикам ослаблять согласованность, предоставляя приложения с более высоким уровнем доступности. Наконец, это решение не обязывает разработчиков определять, администрировать и обслуживать схемы на уровне данных, обеспечивая быструю итерацию.

Это решение также можно создать с помощью других служб Azure.

- [Служба поиска Аzure](https://azure.microsoft.com/services/search/) может использоваться для предоставления пользователям возможности поиска записей через веб-приложения.
- [Службы приложений Аzure](https://azure.microsoft.com/services/app-service/) могут использоваться для размещения приложений и фоновых процессов.
- [Хранилище BLOB-объектов Azure](https://azure.microsoft.com/services/storage/) может использоваться для хранения полных профилей пользователя, включая образы.
- [База данных SQL Аzure](https://azure.microsoft.com/services/sql-database/) позволяет хранить большие объемы данных, включая учетные данные и данные для аналитики использования.
- [Машинное обучение Аzure](https://azure.microsoft.com/services/machine-learning/) позволяет накапливать знания и аналитику, которые могут использоваться для предоставления отзывов и доставки соответствующего содержимого соответствующим пользователям.

Такая социальная сеть, представленная в виде сайта, является всего лишь одним из примеров использования базы данных NoSQL в качестве модели данных. Дополнительные сведения об этом сценарии и о моделировании данных для использования DocumentDB в приложениях социальных сетей, см. в статье [Going social with DocumentDB](documentdb-social-media-apps.md) (Создание социальных сетей с помощью DocumentDB).

## Сравнение баз данных NoSQL и SQL

В следующей таблице приведены основные различия между базами данных NoSQL и SQL.

![NoSQL и SQL: схема, описывающая использование NoSQL и SQL NoSQL и SQL: сравнение](./media/documentdb-nosql-vs-sql/nosql-vs-sql-comparison.png)

Если база данных NoSQL соответствует вашим требованиям, перейдите к следующему разделу, чтобы получить дополнительные сведения о службах NoSQL, доступных в Azure. Если же вам больше подходят базы данных SQL, перейдите к разделу с описанием [предложений Microsoft SQL](#what-are-the-microsoft-sql-offerings).

## Предложения NoSQL, доступные в Microsoft Azure

Azure предоставляет четыре полностью управляемые службы NoSQL.

- [Azure DocumentDB](https://azure.microsoft.com/services/documentdb/)
- [Хранилище таблиц Azure](https://azure.microsoft.com/services/storage/)
- [Azure HBase как часть HDInsight](https://azure.microsoft.com/services/hdinsight/)
- [кэш Azure Redis](https://azure.microsoft.com/services/cache/)

В таблице ниже приведены основные отличия каждой службы. Какая служба соответствует потребностям вашего приложения?

![NoSQL и SQL: схема, описывающая использование предложений NoSQL в Microsoft Azure (DocumentDB, хранилище таблиц, HBase как часть HDInsight, кэш Redis)](./media/documentdb-nosql-vs-sql/nosql-vs-sql-documentdb-storage-hbase-hdinsight-redis-cache.png)

Если для вашего приложения можно использовать одну или несколько служб, ознакомьтесь со следующими ресурсами.

- [Схема обучения для DocumentDB](https://azure.microsoft.com/documentation/learning-paths/documentdb/) и [Распространенные примеры использования DocumentDB](documentdb-use-cases.md)
- [Приступая к работе с хранилищем таблиц Azure с помощью .NET](../storage/storage-dotnet-how-to-use-tables.md)
- [Что такое HBase в HDInsight](../hdinsight/hdinsight-hbase-overview.md)
- [Схема обучения для кэша Redis](https://azure.microsoft.com/documentation/learning-paths/redis-cache/)

Затем перейдите к разделу [Дальнейшие действия](#next-steps), чтобы узнать о бесплатной пробной версии.

## Предложения Microsoft SQL

Доступны пять предложений Microsoft SQL:

- [база данных SQL Azure;](https://azure.microsoft.com/services/sql-database/)
- [SQL Server на виртуальных машинах Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/)
- [SQL Server](https://www.microsoft.com/server-cloud/products/sql-server-2016/)
- [Хранилище данных SQL (предварительная версия)](https://azure.microsoft.com/services/sql-data-warehouse/)
- [Система Analytics Platform System (на локальном устройстве)](https://www.microsoft.com/ru-RU/server-cloud/products/analytics-platform-system/)

Дополнительные сведения о разнице в использовании SQL Server на виртуальной машине и базы данных SQL см. в статье [Вы можете выбрать компонент SQL Server в облаке: база данных SQL Azure (PaaS) или SQL Server на виртуальных машинах Azure (IaaS)](../sql-database/data-management-azure-sql-database-and-sql-server-iaas.md).

Если вы склоняетесь к использованию SQL, перейдите [сюда](https://www.microsoft.com/server-cloud/products/), чтобы получить дополнительные сведения о продуктах и службах Microsoft SQL.

Затем перейдите к разделу [Дальнейшие действия](#next-steps), в котором приведены ссылки на бесплатную пробную и ознакомительную версии.

## Дальнейшие действия

Предлагаем вам ознакомиться с нашими продуктами SQL и NoSQL, поработав с ними бесплатно.

- Как и в случае с другими службами Azure, вы можете зарегистрироваться для получения [бесплатной пробной версии сроком на один месяц](https://azure.microsoft.com/pricing/free-trial/). Кроме того, вы получите 200 долларов США на использование любой из служб Azure.
    - [Azure DocumentDB](https://azure.microsoft.com/services/documentdb/)
    - [Azure HBase как часть HDInsight](https://azure.microsoft.com/services/hdinsight/)
    - [кэш Azure Redis](https://azure.microsoft.com/services/cache/)
    - [Хранилище данных SQL (предварительная версия)](https://azure.microsoft.com/services/sql-data-warehouse/)
    - [база данных SQL Azure;](https://azure.microsoft.com/services/sql-database/)
    - [Хранилище таблиц Azure](https://azure.microsoft.com/services/storage/)

- Вы можете запустить [ознакомительную версию SQL Server 2016 на виртуальной машине](https://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2016ctp33evaluationwindowsserver2012r2/) или скачать [ознакомительную версию SQL Server](https://www.microsoft.com/ru-RU/evalcenter/evaluate-sql-server-2016).
    - [SQL Server](https://www.microsoft.com/server-cloud/products/sql-server-2016/)
    - [SQL Server на виртуальных машинах Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/)

<!---HONumber=AcomDC_0629_2016-->