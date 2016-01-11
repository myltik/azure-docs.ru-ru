<properties
	pageTitle="Использование REST для резервного копирования и восстановления приложений службы приложений"
	description="Узнайте, как использовать вызовы API REST для запуска резервного копирования и восстановления приложения в службе приложений Azure."
	services="app-service"
	documentationCenter=""
	authors="nking92"
	manager="edlauare"
    editor="" />

<tags
	ms.service="app-service"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/18/2015"
	ms.author="nicking"/>

# Использование REST для резервного копирования и восстановления приложений службы приложений
Для [приложений службы приложений](https://azure.microsoft.com/services/app-service/web/) можно создавать резервную копию в виде больших двоичных объектов в хранилище Azure. Резервная копия может включать также базы данных приложения. Если приложение будет случайно удалено или потребуется восстановить его предыдущую версию, можно восстановить любую из резервных копий. Резервное копирование может выполняться по запросу в любое время или по расписанию с нужными интервалами.

В этой статье объясняется, как выполнять резервное копирование и восстановление приложений с помощью запросов REST API. Если вы хотите создавать резервные копии приложений и управлять ими через графический интерфейс на портале Azure, ознакомьтесь со статьей [Резервное копирование веб-приложений в службе приложений Azure](web-sites-backup.md).

<a name="gettingstarted"></a>
## Приступая к работе
Для отправки запросов REST необходимо знать **имя** приложения, его **группу ресурсов** и **идентификатор подписки**. Эти сведения можно найти, щелкнув приложение в колонке **Служба приложений** на [портале Azure](https://portal.azure.com). В примерах в этой статье мы будем настраивать веб-сайт `backuprestoreapiexamples.azurewebsites.net`. Это приложение хранится в группе ресурсов с именем Default-Web-WestUS и выполняется в подписке с идентификатором 00001111-2222-3333-4444-555566667777.

![Пример информации о веб-сайте][SampleWebsiteInformation]

<a name="backup-restore-rest-api"></a>
## REST API архивации и восстановления
Теперь мы в нескольких примерах рассмотрим, как использовать API REST для резервного копирования и восстановления приложений. Каждый пример содержит URL-адрес и текст HTTP-запроса. URL-адреса в примерах будут содержать заполнители, заключенные в фигурные скобки, например {subscriptionId}. Вам нужно заменить их соответствующими сведениями о приложении. Для справки приводится описание всех заполнителей, которые встречаются в URL-адресах наших примеров:

* subscriptionId — идентификатор подписки Azure, которая содержит приложение;
* resourceGroupName — имя группы ресурсов, которая содержит приложение;
* sitemane — имя приложения Azure;
* backupId — идентификатор резервной копии приложения.

Полную документацию по API, в том числе описание необязательных параметров, которые можно включать в HTTP-запрос, вы найдете в [обозревателе ресурсов Azure](https://resources.azure.com/).

<a name="backup-on-demand"></a>
## Резервное копирование приложения по требованию
Чтобы немедленно создать резервную копию приложения, отправьте запрос **POST** на адрес `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Web/sites/{sitename}/backup/`.

Например, так выглядит URL-адрес для сайта из нашего примера. `https://management.azure.com/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/providers/Microsoft.Web/sites/backuprestoreapiexamples/backup/`

В теле запроса нужно передать объект JSON и указать в нем учетную запись хранения, в которой будет сохранена резервная копия. Объект JSON должен иметь свойство с именем **storageAccountUrl**, которое содержит [URL-адрес SAS](../storage/storage-dotnet-shared-access-signature-part-1.md), предоставляющий доступ с правом на запись к контейнеру хранилища Azure. В этом контейнере будет создан большой двоичный объект для резервной копии. Если вы хотите создать резервную копию баз данных, в запрос следует включить список, содержащий имена, типы и строки подключения для каждой копируемой базы данных.

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

Резервное копирование приложения начинается немедленно при получении запроса. Процесс резервного копирования может занять много времени. HTTP-ответ будет содержать идентификатор, который можно использовать в других запросах для проверки состояния резервного копирования. Ниже приведен пример текста HTTP-ответа на запрос резервного копирования.

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

>[AZURE.NOTE]Сообщения об ошибках можно найти в свойстве log в HTTP-ответе.

<a name="schedule-automatic-backups"></a>
## Планирование автоматического резервного копирования
Резервное копирование приложения можно запускать не только по требованию, но и автоматически по расписанию.

### Создание расписания автоматического резервного копирования
Чтобы настроить расписание резервного копирования, отправьте запрос **PUT** на адрес `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Web/sites/{name}/config/backup`.

Например, так выглядит URL-адрес для сайта из нашего примера: `https://management.azure.com/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/providers/Microsoft.Web/sites/backuprestoreapiexamples/config/backup`

Текст запроса должен содержать объект JSON, в котором указана конфигурация резервного копирования. Вот как выглядит запрос со всеми обязательными параметрами:

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

Запрос из этого примера создает расписание для автоматического резервного копирования приложения каждые 7 дней. Параметры **frequencyInterval** и **frequencyUnit** вместе определяют частоту резервного копирования. Допустимые значения для **frequencyUnit** — **hour** (час) и **day** (день). Например, чтобы запускать резервное копирование каждые 12 часов, укажите для frequencyInterval значение 12, а для frequencyUnit — значение hour.

Старые резервные копии будут автоматически удаляться из учетной записи хранилища. Вы можете изменить срок хранения резервных копий, установив параметр **retentionPeriodInDays**. Если вы хотите, чтобы независимо от срока хранения всегда была сохранена хотя бы одна резервная копия, установите для параметра **keepAtLeastOneBackup** значение true.

### Получение расписания автоматического резервного копирования
Чтобы получить актуальные настройки резервного копирования приложения, отправьте запрос **POST** на URL-адрес ` https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Web/sites/{name}/config/backup/list`.

URL-адрес для сайта из нашего примера: `https://management.azure.com/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/providers/Microsoft.Web/sites/backuprestoreapiexamples/config/backup/list`.

<a name="get-backup-status"></a>
## Получение состояния резервного копирования
В зависимости от размера приложения резервное копирование может занять некоторое время. Более того, процесс копирования может завершиться ошибкой, прекратиться по истечении времени ожидания или оказаться частично выполненным. Чтобы увидеть состояние всех резервных копий приложения, отправьте запрос **GET** на URL-адрес `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Web/sites/{name}/backups`.

Чтобы увидеть состояние конкретного процесса, отправьте запрос GET на URL-адрес `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Web/sites/{name}/backups/{backupId}`.

Например, так выглядит URL-адрес для сайта из нашего примера: `https://management.azure.com/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/providers/Microsoft.Web/sites/backuprestoreapiexamples/backups/1`

Текст ответа будет содержать объект JSON следующего вида:

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
* 3 — TimedOut: резервное копирование прекращено, так как не было завершено в установленный срок;
* 4 — Created: запрос резервного копирования помещен в очередь, но процесс еще не запущен;
* 5 — Skipped: резервное копирование пропущено, так как расписание создало слишком много процессов копирования;
* 6 — PartiallySucceeded: резервное копирование выполнено успешно, но некоторые файлы не были сохранены, так как их не удалось прочитать (это обычно происходит из-за монопольной блокировки на доступ к файлам);
* 7 – DeleteInProgress: получен запрос на удаление резервной копии, но она еще не удалена;
* 8 – DeleteFailed: не удалось удалить резервную копию (это скорее всего означает, что истек срок действия URL-адреса SAS, который был использован для создания резервной копии);
* 9 – Deleted: резервная копия успешно удалена.

<a name="restore-app"></a>
## Восстановление приложения из резервной копии
Если приложение было случайно удалено или вы хотите вернуться к предыдущей версии приложения, вы можете восстановить его из резервной копии. Чтобы запустить восстановление, отправьте запрос **POST** на URL-адрес `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Web/sites/{name}/backups/{id}/restore`.

Например, так выглядит URL-адрес для сайта из нашего примера: `https://management.azure.com/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/providers/Microsoft.Web/sites/backuprestoreapiexamples/backups/1/restore`

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

### Восстановление в виде нового приложения
Иногда при восстановлении резервной копии может потребоваться создать новое приложение, а не перезаписывать уже существующее. Чтобы сделать это, измените URL-адрес запроса так, чтобы он указывал на новое приложение, а также установите в объекте JSON для свойства **overwrite** значение **false**.

<a name="delete-app-backup"></a>
## Удаление резервной копии приложения
Если вы хотите удалить резервную копию, отправьте запрос **DELETE** на URL-адрес `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Web/sites/{name}/backups/{backupId}`.

Например, так выглядит URL-адрес для сайта из нашего примера: `https://management.azure.com/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/providers/Microsoft.Web/sites/backuprestoreapiexamples/backups/1`

<a name="manage-sas-url"></a>
## Управление URL-адресом SAS резервной копии
Служба приложений Azure пытается удалить резервную копию из хранилища Azure, используя SAS URL-адрес, который был предоставлен при создании этой резервной копии. Если этот URL-адрес SAS уже недействителен, резервную копию не удастся удалить через REST API. Но вы можете обновить URL-адрес SAS, связанный с резервной копией, отправив запрос **POST** на URL-адрес `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Web/sites/{name}/backups/{id}/list`.

Например, так выглядит URL-адрес для сайта из нашего примера: `https://management.azure.com/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/providers/Microsoft.Web/sites/backuprestoreapiexamples/backups/1/list`

В тексте запроса отправьте объект JSON, который содержит новый URL-адрес SAS. Ниже приведен пример.

```
{
    "properties":
    {
        "storageAccountUrl": "https://account.blob.core.windows.net/backups?sv=2015-02-21&sr=c&sig=DzlkBl7h32C8qCv%2BifdBRxE63r4iv0kZ9L7E0qP16sY%3D&se=2016-09-15T22%3A46%3A54Z&sp=rwdl"
    }
}
```

>[AZURE.NOTE]В целях безопасности URL-адрес SAS, связанный с резервной копией, не возвращается в ответе на запрос GET для определенной резервной копии. Если вы хотите просмотреть, какой URL-адрес SAS связан с резервной копией, отправьте на указанный выше адрес запрос POST, в теле которого передайте пустой объект JSON. Ответ от сервера будет содержать полную информацию о резервной копии, включая связанный URL-адрес SAS.

<!-- IMAGES -->
[SampleWebsiteInformation]: ./media/websites-csm-backup/01siteconfig.png

<!---HONumber=AcomDC_1223_2015-->