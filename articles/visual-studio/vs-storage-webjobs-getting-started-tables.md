---
title: Приступая к работе с подключенными службами хранилища Azure и Visual Studio (проекты веб-заданий)
description: Как приступить к работе, используя табличное хранилище Azure в проекте веб-задания Azure в Visual Studio после подключения к учетной записи хранения с помощью подключенных служб Visual Studio.
services: storage
author: ghogen
manager: douge
ms.assetid: 061a6c46-0592-4e5d-aced-ab7498481cde
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ms.openlocfilehash: 78ef7d98d9e11ba9b2c52bfc88d6ecb050cafa84
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2018
ms.locfileid: "31793306"
---
# <a name="getting-started-with-azure-storage-azure-webjob-projects"></a>Начало работы со службой хранилища Azure (проекты веб-заданий Azure)
[!INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Обзор
Эта статья содержит примеры кода C#, в которых показано, как использовать пакет SDK для веб-заданий Azure версии 1.x со службой хранилища таблиц Azure. В примерах кода используется [пакет SDK для веб-заданий](https://github.com/Azure/azure-webjobs-sdk/wiki) версии 1.x.

В службе хранилища таблиц Azure можно хранить большие объемы структурированных данных. Эта служба — хранилище данных NoSQL, которое принимает вызовы внутри и снаружи облака Azure с проверкой подлинности. Таблицы Azure идеально подходят для хранения нереляционных структурированных данных.  Дополнительные сведения см. в разделе [Приступая к работе с хранилищем таблиц Azure с помощью .NET](../cosmos-db/table-storage-how-to-use-dotnet.md#create-a-table).

В некоторых фрагментах кода демонстрируется использование атрибута **Table** в функциях, вызванных вручную, т. е. без использования атрибутов активации.

## <a name="how-to-add-entities-to-a-table"></a>Как добавить сущность в таблицу
Чтобы добавить сущности в таблицу, используйте атрибут **Table** с параметром **ICollector<T>** или **IAsyncCollector<T>**, где **T** обозначает схему сущностей, которые нужно добавить. Конструктор атрибута принимает строковый параметр, который указывает имя таблицы.

Следующий пример кода добавляет сущности **Person** в таблицу с именем *Ingress*.

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

Как правило, тип, используемый с **ICollector**, является производным от **TableEntity** или реализует **ITableEntity**, но не во всех случаях. Один из следующих классов **Person** работает с кодом, показанным в предыдущем методе **Ingress**.

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

При работе непосредственно с API хранилища Azure можно также добавить параметр **CloudStorageAccount** в сигнатуру метода.

## <a name="real-time-monitoring"></a>Мониторинг в реальном времени
Поскольку функции входящих данных часто обрабатывают тома данных большого размера, на панели мониторинга пакета SDK для заданий WebJob доступны данные мониторинга в реальном времени. В разделе **Журнал вызова** указывается, запущена ли еще функция.

![Запуск функции входа](./media/vs-storage-webjobs-getting-started-tables/ingressrunning.png)

На странице **Подробности о вызове** отображаются данные о ходе выполнения функции (количество записанных сущностей) и предоставляется возможность ее прервать.

![Запуск функции входа](./media/vs-storage-webjobs-getting-started-tables/ingressprogress.png)

По завершении выполнения функции на странице **Подробности о вызове** отображается количество записанных строк.

![Функция входа выполнена](./media/vs-storage-webjobs-getting-started-tables/ingresssuccess.png)

## <a name="how-to-read-multiple-entities-from-a-table"></a>Как выполнять чтение нескольких сущностей из таблицы
Для чтения таблиц используйте атрибут **Table** с параметром **IQueryable<T>**, где тип **T** является производным от **TableEntity** или реализует **ITableEntity**.

Следующий пример кода считывает и записывает все строки таблицы **Ingress** .

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

### <a name="how-to-read-a-single-entity-from-a-table"></a>Как выполнять чтение одной сущности из таблицы
Конструктор атрибута **Table** с двумя дополнительными параметрами позволяет указать ключ раздела и ключ строки, если необходимо выполнить привязку к одной сущности таблицы.

Следующий пример кода считывает строку таблицы для сущности **Person** на основе значений ключа раздела и ключа строки, полученных в сообщении очереди.  

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


В этом примере класс **Person** не обязательно должен реализовывать **ITableEntity**.

## <a name="how-to-use-the-net-storage-api-directly-to-work-with-a-table"></a>Как использовать API службы хранения .NET непосредственно для работы с таблицей
Чтобы сделать работу с таблицей более гибкой, можно также использовать атрибут **Table** для объекта **CloudTable**.

В следующем примере кода объект **CloudTable** используется для добавления одной сущности в таблицу *Ingress* .

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

Дополнительную информацию об использовании объекта **CloudTable** см. в статье [Приступая к работе с хранилищем таблиц Azure с помощью .NET](../storage/storage-dotnet-how-to-use-tables.md).

## <a name="related-topics-covered-by-the-queues-how-to-article"></a>Связанные разделы, которые описаны в практическом руководстве по работе с очередями
Дополнительную информацию об управлении обработкой таблиц, которая инициируется сообщением очереди, а также несвязанные с обработкой таблиц сценарии применения пакета SDK для веб-заданий см. в статье [Приступая к работе с подключенными службами хранилища очередей Azure и Visual Studio (проекты веб-заданий)](../storage/vs-storage-webjobs-getting-started-queues.md).

## <a name="next-steps"></a>Дополнительная информация
В этой статье предоставлены примеры кода обработки обычных сценариев для работы с таблицами Azure. Дополнительная информация об использовании веб-заданий Azure и пакета SDK для веб-заданий доступна в [ресурсах с документацией по веб-заданиям Azure](http://go.microsoft.com/fwlink/?linkid=390226).

