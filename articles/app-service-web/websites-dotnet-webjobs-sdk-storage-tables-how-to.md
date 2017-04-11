---
title: "Как использовать табличное хранилище Azure с пакетом SDK для WebJob"
description: "Информация об использовании табличного хранилища Azure с пакетом SDK для WebJob Создавайте таблицы, добавляйте в них сущности и считывайте существующие таблицы."
services: app-service\web, storage
documentationcenter: .net
author: ggailey777
manager: erikre
editor: jimbe
ms.assetid: 451432cc-c780-4310-85d3-84f44fe48afe
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 06/01/2016
ms.author: glenga
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 47db68afb8ea938a9861765f9e60c78436569110
ms.lasthandoff: 11/17/2016


---
# <a name="how-to-use-azure-table-storage-with-the-webjobs-sdk"></a>Как использовать табличное хранилище Azure с пакетом SDK для WebJob
## <a name="overview"></a>Обзор
Это руководство содержит примеры кода C#, в которых показано, как выполнять чтение и запись таблиц службы хранилища Azure с использованием [пакета SDK для веб-заданий](websites-dotnet-webjobs-sdk.md) версии 1 или более поздней версии.

В этом руководстве предполагается, что вы уже знаете, [как создать проект веб-задания в Visual Studio со строками подключения, указывающими на вашу учетную запись хранения](websites-dotnet-webjobs-sdk-get-started.md) или [несколько учетных записей хранения](https://github.com/Azure/azure-webjobs-sdk/blob/master/test/Microsoft.Azure.WebJobs.Host.EndToEndTests/MultipleStorageAccountsEndToEndTests.cs).

В некоторых фрагментах кода демонстрируется использование атрибута `Table` в функциях, [вызванных вручную](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#manual), т. е. без использования атрибутов триггера. 

## <a id="ingress"></a> Как добавить сущность в таблицу
Чтобы добавить сущности в таблицу, используйте атрибут `Table` с параметром `ICollector<T>` или `IAsyncCollector<T>`, где `T` указывает схему сущностей, которые нужно добавить. Конструктор атрибута принимает строковый параметр, который указывает имя таблицы. 

Следующий пример кода добавляет сущности `Person` в таблицу с именем *Ingress*.

        [NoAutomaticTrigger]
        public static void IngressDemo(
            [Table("Ingress")] ICollector<Person> tableBinding)
        {
            for (int i = 0; i < 100000; i++)
            {
                tableBinding.Add(
                    new Person() { 
                        PartitionKey = "Test", 
                        RowKey = i.ToString(), 
                        Name = "Name" }
                    );
            }
        }

Как правило, тип, используемый с `ICollector`, является производным от `TableEntity` или реализует `ITableEntity`, но не во всех случаях. Один из следующих классов `Person` работает с кодом, показанным в предыдущем методе `Ingress`.

        public class Person : TableEntity
        {
            public string Name { get; set; }
        }

        public class Person
        {
            public string PartitionKey { get; set; }
            public string RowKey { get; set; }
            public string Name { get; set; }
        }

Если требуется работать непосредственно с API службы хранилища Azure, можно добавить параметр `CloudStorageAccount` в сигнатуру метода.

## <a id="monitor"></a> Мониторинг в реальном времени
Поскольку функции входящих данных часто обрабатывают тома данных большого размера, на панели мониторинга пакета SDK для заданий WebJob доступны данные мониторинга в реальном времени. В разделе **Журнал вызова** указывается, запущена ли еще функция.

![Запуск функции входа](./media/websites-dotnet-webjobs-sdk-storage-tables-how-to/ingressrunning.png)

На странице **Подробности о вызове** отображаются данные о ходе выполнения функции (количество записанных сущностей) и предоставляется возможность ее прервать. 

![Запуск функции входа](./media/websites-dotnet-webjobs-sdk-storage-tables-how-to/ingressprogress.png)

По завершении выполнения функции на странице **Подробности о вызове** отображается количество записанных строк.

![Функция входа выполнена](./media/websites-dotnet-webjobs-sdk-storage-tables-how-to/ingresssuccess.png)

## <a id="multiple"></a> Как выполнять чтение нескольких сущностей из таблицы
Для чтения таблиц используйте атрибут `Table` с параметром `IQueryable<T>`, где тип `T` является производным от `TableEntity` или реализует `ITableEntity`.

Следующий пример кода считывает и записывает все строки таблицы `Ingress`:

        public static void ReadTable(
            [Table("Ingress")] IQueryable<Person> tableBinding,
            TextWriter logger)
        {
            var query = from p in tableBinding select p;
            foreach (Person person in query)
            {
                logger.WriteLine("PK:{0}, RK:{1}, Name:{2}", 
                    person.PartitionKey, person.RowKey, person.Name);
            }
        }

### <a id="readone"></a> Как выполнять чтение одной сущности из таблицы
Конструктор атрибута `Table` с двумя дополнительными параметрами позволяет указать ключ раздела и ключ строки, если необходимо выполнить привязку к одной сущности таблицы.

Следующий пример кода считывает строку таблицы для сущности `Person` на основе значений ключа раздела и ключа строки, полученных в сообщении очереди:  

        public static void ReadTableEntity(
            [QueueTrigger("inputqueue")] Person personInQueue,
            [Table("persontable","{PartitionKey}", "{RowKey}")] Person personInTable,
            TextWriter logger)
        {
            if (personInTable == null)
            {
                logger.WriteLine("Person not found: PK:{0}, RK:{1}",
                        personInQueue.PartitionKey, personInQueue.RowKey);
            }
            else
            {
                logger.WriteLine("Person found: PK:{0}, RK:{1}, Name:{2}",
                        personInTable.PartitionKey, personInTable.RowKey, personInTable.Name);
            }
        }


В этом примере класс `Person` не должен реализовывать `ITableEntity`.

## <a id="storageapi"></a> Как использовать API хранилища .NET непосредственно для работы с таблицей
Чтобы сделать работу с таблицей более гибкой, можно также использовать атрибут `Table` для объекта `CloudTable`.

В следующем примере кода объект `CloudTable` используется для добавления одной сущности в таблицу *Ingress* . 

        public static void UseStorageAPI(
            [Table("Ingress")] CloudTable tableBinding,
            TextWriter logger)
        {
            var person = new Person()
                {
                    PartitionKey = "Test",
                    RowKey = "100",
                    Name = "Name"
                };
            TableOperation insertOperation = TableOperation.Insert(person);
            tableBinding.Execute(insertOperation);
        }

Дополнительную информацию об использовании объекта `CloudTable` см. в статье [Приступая к работе с хранилищем таблиц Azure с помощью .NET](../storage/storage-dotnet-how-to-use-tables.md). 

## <a id="queues"></a>Связанные разделы, которые описаны в практическом руководстве по работе с очередями
Дополнительную информацию об обработке таблиц, которая инициируется сообщением очереди, а также несвязанные с обработкой таблиц сценарии для пакета SDK для веб-заданий см. в статье [Использование пакета SDK веб-заданий для работы с хранилищем очередей Azure](websites-dotnet-webjobs-sdk-storage-queues-how-to.md). 

В этой статье рассматриваются следующие вопросы:

* Асинхронные функции
* Выполнение на нескольких экземплярах
* Корректное завершение работы
* Использование атрибутов пакета SDK для заданий WebJob очереди в теле функции
* Установка строк подключения пакета SDK в коде.
* Установка значений параметров конструктора пакета SDK для заданий WebJob в коде
* Вызов функции вручную
* Запись журналов

## <a id="nextsteps"></a> Дальнейшие действия
В этом руководстве предоставлены примеры кода обработки обычных сценариев для работы с таблицами Azure. Дополнительную информацию об использовании веб-заданий Azure и пакета SDK для веб-заданий см. в [рекомендуемых ресурсах для веб-заданий Azure](http://go.microsoft.com/fwlink/?linkid=390226).


