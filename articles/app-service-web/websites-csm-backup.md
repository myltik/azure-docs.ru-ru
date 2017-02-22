---
title: "Использование REST для резервного копирования и восстановления приложений службы приложений"
description: "Узнайте, как использовать вызовы API REST для запуска резервного копирования и восстановления приложения в службе приложений Azure."
services: app-service
documentationcenter: 
author: NKing92
manager: wpickett
editor: 
ms.assetid: f94d0aea-edc1-43ab-9b51-ea7375859276
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/10/2016
ms.author: nicking
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 1ad2911f809a17e4a6c0f2fe9087e1d9eb2da39e


---
# <a name="use-rest-to-back-up-and-restore-app-service-apps"></a>Использование REST для резервного копирования и восстановления приложений службы приложений
> [!div class="op_single_selector"]
> * [PowerShell](../app-service/app-service-powershell-backup.md)
> * [ИНТЕРФЕЙС REST API](websites-csm-backup.md)
> 
> 

[приложений службы приложений](https://azure.microsoft.com/services/app-service/web/) можно создавать резервную копию в виде больших двоичных объектов в хранилище Azure. Резервная копия может включать также базы данных приложения. Если приложение будет случайно удалено или потребуется восстановить его предыдущую версию, можно восстановить любую из резервных копий. Резервное копирование может выполняться по запросу в любое время или по расписанию с нужными интервалами.

В этой статье объясняется, как выполнять архивировать и восстанавливать приложений с помощью запросов RESTful API. Если вы хотите создавать резервные копии приложений и управлять ими через графический интерфейс на портале Azure, ознакомьтесь со статьей [Резервное копирование веб-приложений в службе приложений Azure](web-sites-backup.md)

<a name="gettingstarted"></a>

## <a name="getting-started"></a>Приступая к работе
Для отправки запросов REST необходимо знать **имя** приложения, его **группу ресурсов** и **идентификатор подписки**. Эти сведения можно найти, щелкнув приложение в колонке **Служба приложений** на [портале Azure](https://portal.azure.com). В примерах в этой статье мы настраиваем веб-сайт **backuprestoreapiexamples.azurewebsites.net**. Это приложение хранится в группе ресурсов с именем Default-Web-WestUS и выполняется в подписке с идентификатором 00001111-2222-3333-4444-555566667777.

![Пример информации о веб-сайте][SampleWebsiteInformation]

<a name="backup-restore-rest-api"></a>

## <a name="backup-and-restore-rest-api"></a>REST API архивации и восстановления
Теперь мы в нескольких примерах рассмотрим, как использовать API REST для резервного копирования и восстановления приложений. Каждый пример содержит URL-адрес и текст HTTP-запроса. URL-адрес в примере будет содержать заполнители, заключенные в фигурные скобки, например {subscription-id}. Вам нужно заменить их соответствующими значениями для своего приложения. Для справки приводится описание всех заполнителей, которые встречаются в URL-адресах наших примеров:

* subscription-id — идентификатор подписки Azure, которая содержит приложение;
* resource-group-name — имя группы ресурсов, которая содержит приложение;
* name — имя приложения Azure;
* backup-id — идентификатор резервной копии приложения.

Полную документацию по API, в том числе описание необязательных параметров, которые можно включать в HTTP-запрос, вы найдете в [обозревателе ресурсов Azure](https://resources.azure.com/).

<a name="backup-on-demand"></a>

## <a name="backup-an-app-on-demand"></a>Резервное копирование приложения по требованию
Чтобы немедленно создать резервную копию приложения, отправьте запрос **POST** на адрес **https://management.azure.com/subscriptions/{ИД_подписки}/resourceGroups/{имя_группы_ресурсов}/providers/Microsoft.Web/sites/{имя}/backup/**.

Например, так выглядит URL-адрес для сайта из нашего примера. **https://management.azure.com/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/providers/Microsoft.Web/sites/backuprestoreapiexamples/backup/**

Передайте в теле запроса объект JSON и укажите в нем учетную запись хранения, в которой будет сохранена резервная копия. Объект JSON должен иметь свойство с именем **storageAccountUrl**, которое содержит [URL-адрес SAS](../storage/storage-dotnet-shared-access-signature-part-1.md) , предоставляющий доступ с правом на запись к контейнеру службы хранилища Azure, в котром хранится большой двоичный объект резервной копии. Если вы хотите создать резервную копию баз данных, в запрос следует включить список, содержащий имена, типы и строки подключения для каждой копируемой базы данных.

```
{
    "properties":
    {
        "storageAccountUrl": “https://account.blob.core.windows.net/backups?sv=2015-02-21&sr=c&sig=DzlkBl7h32C8qCv%2BifdBRxE63r4iv0kZ9L7E0qP16sY%3D&se=2016-09-15T22%3A46%3A54Z&sp=rwdl”,
        “databases”: [
            {
                “databaseType”: “SqlAzure”,
                “name”: “MyDatabase1”,
                "connectionString": "<your database connection string>"
            }
        ]
    }
}
```

Архивация приложения начинается немедленно при получении запроса. Процесс резервного копирования может занять много времени. Ответ HTTP-ответ содержит идентификатор, который можно использовать в другом запросе, чтобы проверить состояние архивации. Ниже приведен пример текста HTTP-ответа на запрос резервного копирования.

```
{
    "id": "/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/providers/Microsoft.Web/sites/backuprestoreapiexamples",
    "name": " backuprestoreapiexamples ",
    "type": "Microsoft.Web/sites",
    "location": "WestUS",
    "properties":    {
        "id": 1,
        "storageAccountUrl": “https://account.blob.core.windows.net/backups?sv=2015-02-21&sr=c&sig=DzlkBl7h32C8qCv%2BifdBRxE63r4iv0kZ9L7E0qP16sY%3D&se=2016-09-15T22%3A46%3A54Z&sp=rwdl”,
        "blobName": “backup_201509291825.zip”,
        "name": "backup_201509291825",
        "status": 4,
        "sizeInBytes": 0,
        "created": "2015-09-29T18:25:26.3992707Z",
        "log": null,
        "databases": [
            {
                "databaseType": "SqlAzure",
                "name": "MyDatabase1",
                "connectionString": "<your database connection string>"
            }
        ],
        "scheduled": false,
        "correlationId": "ea730f4d-dd06-4f7f-a090-9aa09k662h36",
    }
}
```

> [!NOTE]
> Сообщения об ошибках можно найти в свойстве log в HTTP-ответе.
> 
> 

<a name="schedule-automatic-backups"></a>

## <a name="schedule-automatic-backups"></a>Планирование автоматического резервного копирования
Резервное копирование приложения можно запускать не только по требованию, но и автоматически по расписанию.

### <a name="set-up-a-new-automatic-backup-schedule"></a>Создание расписания автоматического резервного копирования
Чтобы задать расписание архивации, отправьте запрос **PUT** на адрес **https://management.azure.com/subscriptions/{ИД_подписки}/resourceGroups/{имя_группы_ресурсов}/providers/Microsoft.Web/sites/{имя}/config/backup/**.

Например, так выглядит URL-адрес для сайта из нашего примера. **https://management.azure.com/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/providers/Microsoft.Web/sites/backuprestoreapiexamples/config/backup**

Текст запроса должен содержать объект JSON, в котором указана конфигурация резервного копирования. Ниже приведен пример запроса со всеми обязательными параметрами.

```
{
    "location": "WestUS",
    "properties": // Represents an app restore request
    {
        "backupSchedule": { // Required for automatically scheduled backups
            "frequencyInterval": "7",
            "frequencyUnit": "Day",
            "keepAtLeastOneBackup": "True",
            "retentionPeriodInDays": "10",
        },
        "enabled": "True", // Must be set to true to enable automatic backups
        "name": “mysitebackup”,
        "storageAccountUrl": "https://account.blob.core.windows.net/backups?sv=2015-02-21&sr=c&sig=DzlkBl7h32C8qCv%2BifdBRxE63r4iv0kZ9L7E0qP16sY%3D&se=2016-09-15T22%3A46%3A54Z&sp=rwdl"
    }
}
```

Запрос из этого примера создает расписание для автоматической архивации приложения каждые семь дней. Параметры **frequencyInterval** и **frequencyUnit** вместе определяют частоту архивации. Допустимые значения для **frequencyUnit** — **hour** (час) и **day** (день). Например, чтобы запускать резервное копирование каждые 12 часов, укажите для frequencyInterval значение 12, а для frequencyUnit — значение hour.

Старые резервные копии будут автоматически удаляться из учетной записи хранения. Вы можете изменить срок хранения резервных копий, установив параметр **retentionPeriodInDays** . Если вы хотите, чтобы независимо от срока хранения всегда была сохранена хотя бы одна резервная копия, установите для параметра **keepAtLeastOneBackup** значение true.

### <a name="get-the-automatic-backup-schedule"></a>Получение расписания автоматического резервного копирования
Чтобы получить конфигурацию архивации приложения, отправьте запрос **POST** на URL-адрес **https://management.azure.com/subscriptions/{ИД_подписки}/resourceGroups/{имя_группы_ресурсов}/providers/Microsoft.Web/sites/{имя}/config/backup/list**.

В нашем примере URL-адрес следующий: **https://management.azure.com/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/providers/Microsoft.Web/sites/backuprestoreapiexamples/config/backup/list**.

<a name="get-backup-status"></a>

## <a name="get-the-status-of-a-backup"></a>Получение состояния резервного копирования
В зависимости от размера приложения резервное копирование может занять некоторое время. Более того, процесс копирования может завершиться ошибкой, прекратиться по истечении времени ожидания или оказаться частично выполненным. Чтобы просмотреть состояние всех резервных копий приложения, отправьте запрос **GET** на URL-адрес **https://management.azure.com/subscriptions/{ИД_подписки}/resourceGroups/{имя_группы_ресурсов}/providers/Microsoft.Web/sites/{имя}/backups**.

Чтобы просмотреть состояние определенной резервной копии приложения, отправьте запрос GET на URL-адрес **https://management.azure.com/subscriptions/{ИД_подписки}/resourceGroups/{имя_группы_ресурсов}/providers/Microsoft.Web/sites/{имя}/backups/{ИД_резервной_копии}**.

Например, так выглядит URL-адрес для сайта из нашего примера. **https://management.azure.com/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/providers/Microsoft.Web/sites/backuprestoreapiexamples/backups/1**

Текст ответа содержит объект JSON следующего вида.

```
{
    "properties":  {
        "id": 1,
        "storageAccountUrl": "https://account.blob.core.windows.net/backups?sv=2015-02-21&sr=c&sig=DzlkBl7h32C8qCv%2BifdBRxE63r4iv0kZ9L7E0qP16sY%3D&se=2016-09-15T22%3A46%3A54Z&sp=rwdl",
        "blobName": "backup_201509291734.zip",
        "name": "backup_201509291734",
        "status": 2,
        "sizeInBytes": 151973,
        "created": "2015-09-29T17:34:57.2091605",
        "scheduled": false,
        "lastRestoreTimeStamp": null,
        "finishedTimeStamp": "2015-09-29T17:35:11.3293602",
        "correlationId": "2379fc13-418a-4b9c-920d-d06e73c5028d",
        "websiteSizeInBytes": 209920
    }
}
```

Состояние резервного копирования кодируется числовым значением. Вот все возможные значения состояния:

* 0 — InProgress: резервное копирование запущено, но еще не завершено;
* 1 — Failed: не удалось выполнить резервное копирование;
* 2 — Succeeded: резервное копирование успешно завершено;
* 3 — TimedOut: архивация прекращена, так как не была завершена в установленный срок;
* 4 — Created: запрос резервного копирования помещен в очередь, но процесс еще не запущен;
* 5 — пропущено. Резервное копирование не выполнено, так как расписание активировало слишком много процессов резервного копирования.
* 6 — частично выполнено. Резервное копирование выполнено успешно, но некоторые файлы не скопированы, так как их не удалось считать. Это обычно происходит из-за установки монопольной блокировки на файлы.
* 7 — выполняется удаление. Удаление резервной копии начато, но еще не завершено.
* 8 — ошибка удаления. Не удалось удалить резервное копирование. Возможно, истек срок действия URL-адреса SAS, который использовался для создания резервного копирования.
* 9 – Deleted: резервная копия успешно удалена.

<a name="restore-app"></a>

## <a name="restore-an-app-from-a-backup"></a>Восстановление приложения из резервной копии
Если приложение было случайно удалено или вы хотите вернуться к предыдущей версии приложения, вы можете восстановить его из резервной копии. Чтобы вызвать восстановление, отправьте запрос **POST** на URL-адрес **https://management.azure.com/subscriptions/{ИД_подписки}/resourceGroups/{имя_группы_ресурсов}/providers/Microsoft.Web/sites/{имя}/backups/{ИД_резервной_копии}/restore**.

Например, так выглядит URL-адрес для сайта из нашего примера. **https://management.azure.com/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/providers/Microsoft.Web/sites/backuprestoreapiexamples/backups/1/restore**

В тексте запроса отправьте объект JSON, который содержит свойства для операции восстановления. Вот пример, содержащий все обязательные свойства:

```
{
    "location": "WestUS",
    "properties":
    {
        "blobName": "backup_201509280431.zip",
        "databases": [ // Not required unless you’re restoring databases
            {
                “databaseType”: “SqlAzure”,
                “name”: “MyDatabase1”
        }],
        "overwrite": "true",
        "storageAccountUrl": "https://account.blob.core.windows.net/backups?sv=2015-02-21&sr=c&sig=DzlkBl7h32C8qCv%2BifdBRxE63r4iv0kZ9L7E0qP16sY%3D&se=2016-09-15T22%3A46%3A54Z&sp=rwdl" // SAS URL to storage container containing your website backup
    }
}
```

### <a name="restore-to-a-new-app"></a>Восстановление в виде нового приложения
Иногда при восстановлении резервной копии может потребоваться создать новое приложение, а не перезаписывать уже существующее. Чтобы сделать это, измените URL-адрес запроса так, чтобы он указывал на новое приложение, а также установите в коде JSON для свойства **overwrite** значение **false**.

<a name="delete-app-backup"></a>

## <a name="delete-an-app-backup"></a>Удаление резервной копии приложения
Чтобы удалить резервную копию, отправьте запрос **DELETE** на URL-адрес **https://management.azure.com/subscriptions/{ИД_подписки}/resourceGroups/{имя_группы_ресурсов}/providers/Microsoft.Web/sites/{имя}/backups/{ИД_резервной_копии}**.

Например, так выглядит URL-адрес для сайта из нашего примера. **https://management.azure.com/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/providers/Microsoft.Web/sites/backuprestoreapiexamples/backups/1**

<a name="manage-sas-url"></a>

## <a name="manage-a-backups-sas-url"></a>Управление URL-адресом SAS резервной копии
Служба приложений Azure пытается удалить резервную копию из хранилища Azure, используя SAS URL-адрес, который был предоставлен при создании этой резервной копии. Если этот URL-адрес SAS уже недействителен, резервную копию не удастся удалить через REST API. Однако можно обновить URL-адрес SAS, связанный с резервной копий, отправив запрос **POST** на URL-адрес **https://management.azure.com/subscriptions/{ИД_подписки}/resourceGroups/{имя_группы_ресурсов}/providers/Microsoft.Web/sites/{имя}/backups/{ИД_резервной_копии}/list**.

Например, так выглядит URL-адрес для сайта из нашего примера. **https://management.azure.com/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/providers/Microsoft.Web/sites/backuprestoreapiexamples/backups/1/list**

В тексте запроса отправьте объект JSON, который содержит новый URL-адрес SAS. Ниже приведен пример.

```
{
    "properties":
    {
        "storageAccountUrl": "https://account.blob.core.windows.net/backups?sv=2015-02-21&sr=c&sig=DzlkBl7h32C8qCv%2BifdBRxE63r4iv0kZ9L7E0qP16sY%3D&se=2016-09-15T22%3A46%3A54Z&sp=rwdl"
    }
}
```

> [!NOTE]
> В целях безопасности URL-адрес SAS, связанный с резервной копией, не возвращается в ответе на запрос GET для определенной резервной копии. Если вы хотите просмотреть, какой URL-адрес SAS связан с резервной копией, отправьте на указанный выше адрес запрос POST. Добавьте пустой объект JSON в текст запроса. Ответ от сервера будет содержать полную информацию о резервной копии, включая связанный URL-адрес SAS.
> 
> 

<!-- IMAGES -->
[SampleWebsiteInformation]: ./media/websites-csm-backup/01siteconfig.png



<!--HONumber=Dec16_HO2-->


