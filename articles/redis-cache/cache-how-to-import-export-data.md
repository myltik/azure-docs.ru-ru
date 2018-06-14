---
title: Импорт и экспорт данных в кэше Redis для Azure | Документация Майкрософт
description: Вы можете узнать, как импортировать данные в хранилище BLOB-объектов и экспортировать их оттуда с использованием экземпляров кэша Redis для Azure категории "Премиум"
services: redis-cache
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.assetid: 4a68ac38-87af-4075-adab-569d37d7cc9e
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: article
ms.date: 07/31/2017
ms.author: wesmc
ms.openlocfilehash: db488f759752880a47a78dfeec13b14f65bd503c
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/19/2018
ms.locfileid: "27910093"
---
# <a name="import-and-export-data-in-azure-redis-cache"></a>Импорт и экспорт данных в кэше Redis для Azure
Импорта и экспорт являются операциями управления данными в кэше Redis для Azure, которые позволяют импортировать данные в кэш Redis для Azure или экспортировать их оттуда путем импорта и экспорта моментального снимка базы данных кэша Redis (RDB) из кэша уровня "Премиум" в большой двоичный объект в учетной записи хранения Azure. 

- **Экспорт**. Моментальные снимки в формате RDB из кэша Redis для Azure можно экспортировать в страничный BLOB-объект.
- **Импорт**. Моментальные снимки в формате RDB можно импортировать в кэш Redis для Azure из страничного или блочного BLOB-объекта.

Функция импорта/экспорта дает возможность переключаться между различными экземплярами кэша Redis для Azure или заполнять кэш данными перед использованием.

Эта статья содержит руководство по импорту и экспорту данных в кэше Redis для Azure, а также ответы на часто задаваемые вопросы.

> [!IMPORTANT]
> Функция импорта/экспорта находится на этапе предварительной версии и доступна только для кэшей [категории "Премиум"](cache-premium-tier-intro.md) .
>
>

## <a name="import"></a>Импорт
Импорт можно использовать для переноса RDB-файлов, совместимых с Redis, с сервера Redis, запущенного в любом облаке или любой среде, включая Redis в Linux, Windows, или у любого поставщика облачных служб, такого как Amazon Web Services или другого. Импорт данных позволяет легко создать кэш, предварительно заполненный данными. Во время импорта кэш Redis для Azure загружает RDB-файлы из службы хранилища Azure в память, а затем вставляет в кэш ключи.

> [!NOTE]
> Перед началом операции импорта убедитесь, что RDB-файл или RDB-файлы базы данных Redis переданы в страничные или блочные BLOB-объекты в службе хранилища Azure, размещенные в том же регионе и подписке, что и ваш экземпляр кэша Redis для Azure. Дополнительные сведения см. в статье [Приступая к работе с хранилищем BLOB-объектов Azure с помощью .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md). Если вы экспортировали RDB-файл с помощью функции [экспорта кэша Redis для Azure](#export), то этот файл уже находится в страничном BLOB-объекте и готов к импорту.
>
>

1. Чтобы импортировать один или несколько экспортированных больших двоичных объектов кэша, [перейдите к кэшу](cache-configure.md#configure-redis-cache-settings) на портале Azure и нажмите кнопку **Импорт данных** в **меню ресурсов**.

    ![Импорт данных][cache-import-data]
2. Щелкните **Выберите BLOB-объекты** и выберите учетную запись хранения, содержащую данные для импорта.

    ![Выбор учетной записи хранения][cache-import-choose-storage-account]
3. Щелкните контейнер, содержащий данные для импорта.

    ![Выберите контейнер][cache-import-choose-container]
4. Выберите один или несколько BLOB-объектов для импорта, щелкнув область слева от имени BLOB-объекта и выбрав пункт **Выбрать**.

    ![Выберите BLOB-объекты][cache-import-choose-blobs]
5. Щелкните **Импорт** для начала процесса импорта.

   > [!IMPORTANT]
   > Во время процедуры импорта кэш недоступен для клиентов кэша, а все существующие в нем данные удаляются.
   >
   >

    ![Импорт][cache-import-blobs]

    Ход выполнения операции импорта можно отслеживать, выбирая уведомления на портале Azure или просматривая события в [журнале аудита](../azure-resource-manager/resource-group-audit.md).

    ![Ход выполнения импорта][cache-import-data-import-complete]

## <a name="export"></a>экспорт.
Экспорт позволяет экспортировать данные, хранящиеся в кэше Redis для Azure в RDB-файлы, совместимые с Redis. Эту функцию можно использовать для перемещения данных из одного экземпляра кэша Redis для Azure в другой или на другой сервер Redis. Во время экспорта на виртуальной машине, где размещается экземпляр сервера кэша Redis для Azure, создается временный файл, который отправляется в заданную учетную запись хранения. После успешного или неудачного завершения операции экспорта этот временный файл удаляется.

1. Чтобы экспортировать текущее содержимое кэша в хранилище, [перейдите к кэшу](cache-configure.md#configure-redis-cache-settings) на портале Azure и нажмите кнопку **Экспорт данных** в **меню ресурсов**.

    ![Выберите контейнер хранилища][cache-export-data-choose-storage-container]
2. Щелкните **Выберите контейнер хранилища** и выберите требуемую учетную запись хранения. Эта учетная запись хранения должна относиться к той же подписке и тому же региону, что ваш кэш.

   > [!IMPORTANT]
   > Функция экспорта работает со страничными BLOB-объектами, которые поддерживаются как классическими учетными записями хранения, так и учетными записями хранения Resource Manager, но пока не поддерживаются [учетными записями хранения BLOB-объектов](../storage/common/storage-account-options.md#blob-storage-accounts).
   >
   >

    ![Учетная запись хранения][cache-export-data-choose-account]
3. Выберите нужный контейнер BLOB-объектов и нажмите кнопку **Выбрать**. Чтобы использовать новый контейнер, щелкните **Добавить контейнер** и выберите его из списка.

    ![Выберите контейнер хранилища][cache-export-data-container]
4. Введите значение **Префикс имени BLOB-объекта** и нажмите кнопку **Экспорт** для запуска процедуры экспорта. Префикс имени BLOB-объекта добавляется к именам файлов, создаваемых этой операцией экспорта.

    ![экспорт.][cache-export-data]

    Ход выполнения операции экспорта можно отслеживать, выбирая уведомления на портале Azure или просматривая события в [журнале аудита](../azure-resource-manager/resource-group-audit.md).

    ![Экспорт данных завершен][cache-export-data-export-complete]

    Во время экспорта кэши остаются доступными для использования.

## <a name="importexport-faq"></a>Часто задаваемые вопросы о функции импорта/экспорта
Этот раздел содержит часто задаваемые вопросы о функции импорта/экспорта.

* [В каких ценовых категориях можно функцию импорта/экспорта?](#what-pricing-tiers-can-use-importexport)
* [Можно ли импортировать данные с любого сервера Redis?](#can-i-import-data-from-any-redis-server)
* [Какие версии RDB-файлов можно импортировать?](#what-rdb-versions-can-i-import)
* [Доступен ли кэш во время операции импорта или экспорта?](#is-my-cache-available-during-an-importexport-operation)
* [Можно использовать функцию импорта/экспорта с кластером Redis?](#can-i-use-importexport-with-redis-cluster)
* [Как работает импорт и экспорт в базах данных с пользовательскими настройками?](#how-does-importexport-work-with-a-custom-databases-setting)
* [Чем отличается функция импорта/экспорта от сохраняемости Redis?](#how-is-importexport-different-from-redis-persistence)
* [Можно ли автоматизировать функцию импорта/экспорта с помощью PowerShell, интерфейса командной строки или других клиентов управления?](#can-i-automate-importexport-using-powershell-cli-or-other-management-clients)
* [Возникла ошибка времени ожидания во время операции импорта или экспорта. Что это означает?](#i-received-a-timeout-error-during-my-importexport-operation-what-does-it-mean)
* [При экспорте данных в хранилище BLOB-объектов Azure возникла ошибка. Что произошло?](#i-got-an-error-when-exporting-my-data-to-azure-blob-storage-what-happened)

### <a name="what-pricing-tiers-can-use-importexport"></a>В каких ценовых категориях можно функцию импорта/экспорта?
Функция импорта/экспорта доступна только в ценовой категории "Премиум".

### <a name="can-i-import-data-from-any-redis-server"></a>Можно ли импортировать данные с любого сервера Redis?
Да, в дополнение к импорту данных, экспортированных из экземпляров кэша Redis для Azure, можно импортировать RDB-файлы с любого сервера Redis, запущенного в любом облаке или любой среде, например в Linux, Windows или у поставщиков облачных решений, таких как Amazon Web Services. Для этого передайте RDB-файл с нужного сервера Redis в страничный или блочный BLOB-объект в учетной записи хранения Azure, а затем импортируйте его в экземпляр кэша Redis для Azure уровня "Премиум". Например, вы можете экспортировать данные из рабочего кэша и импортировать их в кэш, используемый в составе промежуточной среды для тестирования или переноса.

> [!IMPORTANT]
> Для успешного импорта данных, экспортированных с серверов Redis (не являющихся серверами кэша Redis для Azure), при использовании страничного BLOB-объекта его размер должен соответствовать границе в 512 байтов. Приме код для заполнения необходимых байтов приведен на странице [SamplePageBlobUpload](https://github.com/JimRoberts-MS/SamplePageBlobUpload).
> 
> 

### <a name="what-rdb-versions-can-i-import"></a>Какие версии RDB-файлов можно импортировать?

Кэш Redis для Azure поддерживает импорт RDB-файлов вплоть до RDB версии 7.

### <a name="is-my-cache-available-during-an-importexport-operation"></a>Доступен ли кэш во время операции импорта или экспорта?
* **Экспорт** — кэши остаются доступными, и во время операции экспорта можно продолжить работу с кэшем.
* **Импорт** — кэши становятся недоступными при запуске операции импорта, а по ее завершении вновь становятся доступными для использования.

### <a name="can-i-use-importexport-with-redis-cluster"></a>Можно использовать функцию импорта/экспорта с кластером Redis?
Да, и вы можете выполнять импорт/экспорт между кластеризованным и некластеризованный кэшами. Так как кластер Redis [поддерживает только базу данных 0](cache-how-to-premium-clustering.md#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering), данные в базах данных, отличных от 0, не импортируются. При импорте данных кластеризованного кэша ключи перераспределяются между сегментами кластера.

### <a name="how-does-importexport-work-with-a-custom-databases-setting"></a>Как работает импорт и экспорт в базах данных с пользовательскими настройками?
Некоторые ценовые категории имеют различные [ограничения для количества баз данных](cache-configure.md#databases), поэтому существуют определенные рекомендации по импорту, актуальные, если вы задали пользовательское значение для параметра `databases` во время создания кэша.

* При импорте в ценовую категорию с более низким ограничением для параметра `databases` , чем у категории, из которой выполняется импорт:
  * Если вы используете заданное по умолчанию количество `databases` (16 для всех ценовых категорий), то данные не теряются.
  * Если вы используете настраиваемое количество `databases` , находящееся в пределах категории, в которую выполняется импорт, данные не теряются.
  * Если экспортируемые данные включают в себя данные из баз данных, которые являются превышением ограничения для новой категории, данные из таких баз данных не импортируются.

### <a name="how-is-importexport-different-from-redis-persistence"></a>Чем отличается функция импорта/экспорта от сохраняемости Redis?
Сохраняемость кэша Redis для Azure обеспечивает целостность данных в кэше Redis посредством службы хранилища Azure. Если настроено постоянное хранение, кэш Redis для Azure будет сохранять моментальный снимок кэша Redis на диске в двоичном формате Redis в соответствии с настроенной частотой резервного копирования. В случае аварии, при которой становятся недоступными как основной экземпляр кэша, так и реплика кэша, данные кэша восстанавливаются автоматически из последнего моментального снимка. Дополнительные сведения см. в статье [Настройка постоянного хранения для кэша Redis для Azure уровня Премиум](cache-how-to-premium-persistence.md).

Функция импорта/экспорта позволяет перенести данные в кэш Redis для Azure или экспортировать их оттуда. Она не осуществляет настройку резервного копирования использует для восстановления механизм сохраняемости Redis.

### <a name="can-i-automate-importexport-using-powershell-cli-or-other-management-clients"></a>Можно ли автоматизировать функцию импорта/экспорта с помощью PowerShell, интерфейса командной строки или других клиентов управления?
Да, инструкции по использованию PowerShell см. в разделах, посвященных [импорту](cache-howto-manage-redis-cache-powershell.md#to-import-a-redis-cache) и [экспорту](cache-howto-manage-redis-cache-powershell.md#to-export-a-redis-cache) кэша Redis.

### <a name="i-received-a-timeout-error-during-my-importexport-operation-what-does-it-mean"></a>Во время операции импорта/экспорта возникла ошибка времени ожидания. Что это означает?
Если перед запуском операции вы находитесь в колонке **Импорт данных** или **Экспорт данных** дольше 15 минут, то появится сообщение об ошибке следующего вида:

    The request to import data into cache 'contoso55' failed with status 'error' and error 'One of the SAS URIs provided could not be used for the following reason: The SAS token end time (se) must be at least 1 hour from now and the start time (st), if given, must be at least 15 minutes in the past.

Чтобы устранить эту проблему, запустите операцию импорта или экспорта до истечения 15 минут.

### <a name="i-got-an-error-when-exporting-my-data-to-azure-blob-storage-what-happened"></a>При экспорте данных в хранилище BLOB-объектов возникает ошибка. Что произошло?
Функция экспорта работает только с RDB-файлами, сохраненными в виде страничных BLOB-объектов. Другие типы больших двоичных объектов пока не поддерживаются, включая учетные записи хранилища BLOB-объектов с "горячим" и "холодным" уровнями. Дополнительные сведения см. в разделе [Учетные записи хранения BLOB-объектов](../storage/common/storage-account-options.md#blob-storage-accounts).

## <a name="next-steps"></a>Дополнительная информация
Узнайте, как использовать расширенные функции кэша.

* [Знакомство с кэшем Redis для Azure уровня Премиум](cache-premium-tier-intro.md)    

<!-- IMAGES -->
[cache-settings-import-export-menu]: ./media/cache-how-to-import-export-data/cache-settings-import-export-menu.png
[cache-export-data-choose-account]: ./media/cache-how-to-import-export-data/cache-export-data-choose-account.png
[cache-export-data-choose-storage-container]: ./media/cache-how-to-import-export-data/cache-export-data-choose-storage-container.png
[cache-export-data-container]: ./media/cache-how-to-import-export-data/cache-export-data-container.png
[cache-export-data-export-complete]: ./media/cache-how-to-import-export-data/cache-export-data-export-complete.png
[cache-export-data]: ./media/cache-how-to-import-export-data/cache-export-data.png
[cache-import-data]: ./media/cache-how-to-import-export-data/cache-import-data.png
[cache-import-choose-storage-account]: ./media/cache-how-to-import-export-data/cache-import-choose-storage-account.png
[cache-import-choose-container]: ./media/cache-how-to-import-export-data/cache-import-choose-container.png
[cache-import-choose-blobs]: ./media/cache-how-to-import-export-data/cache-import-choose-blobs.png
[cache-import-blobs]: ./media/cache-how-to-import-export-data/cache-import-blobs.png
[cache-import-data-import-complete]: ./media/cache-how-to-import-export-data/cache-import-data-import-complete.png
