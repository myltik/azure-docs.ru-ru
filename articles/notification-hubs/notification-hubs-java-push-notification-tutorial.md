---
title: Использование концентраторов уведомлений с Java
description: Узнайте, как использовать центры уведомлений Azure из серверной части Java.
services: notification-hubs
documentationcenter: ''
author: dimazaid
manager: kpiteira
editor: spelluru
ms.assetid: 4c3f966d-0158-4a48-b949-9fa3666cb7e4
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: java
ms.devlang: java
ms.topic: article
ms.date: 04/14/2018
ms.author: dimazaid
ms.openlocfilehash: 88e3ab3cc03cc1e760672120bc5c484af1ba4722
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33778381"
---
# <a name="how-to-use-notification-hubs-from-java"></a>Использование концентраторов уведомлений из Java
[!INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]

В этой статье описаны основные функции нового, полностью поддерживаемого официального пакета SDK для Java Центра уведомлений Azure. Это проект с открытым кодом. Вы можете просмотреть весь код пакета SDK в [пакете SDK для Java]. 

Вы можете обращаться ко всем функциям центров уведомлений из серверной части Java/PHP/Python/Ruby, используя интерфейс REST в соответствии с описанием в разделе MSDN [Интерфейсы API REST центров уведомлений](http://msdn.microsoft.com/library/dn223264.aspx). Пакет SDK для Java предоставляет тонкую оболочку для этих интерфейсов REST на Java. 

В настоящее время пакет SDK поддерживает следующее:

* операции CRUD для центра уведомлений; 
* операции CRUD для регистрации;
* управление установкой;
* регистрацию импорта и экспорта;
* обычную отправку;
* запланированную отправку;
* асинхронные операции с использованием Java NIO;
* Поддерживаемые платформы: APNS (iOS), GCM (Android), WNS (приложения для Магазина Windows), MPNS (Windows Phone), ADM (Amazon Kindle Fire), Baidu (Android без служб Google). 

## <a name="sdk-usage"></a>Использование пакета SDK
### <a name="compile-and-build"></a>Компиляция и сборка
Использование [Maven]

для сборки:

    mvn package

## <a name="code"></a>Код
### <a name="notification-hub-cruds"></a>Операции CRUD в центрах уведомлений
**Создание NamespaceManager:**

    NamespaceManager namespaceManager = new NamespaceManager("connection string")

**Создание центра уведомлений:**

    NotificationHubDescription hub = new NotificationHubDescription("hubname");
    hub.setWindowsCredential(new WindowsCredential("sid","key"));
    hub = namespaceManager.createNotificationHub(hub);

 Или

    hub = new NotificationHub("connection string", "hubname");

**Получение центра уведомлений:**

    hub = namespaceManager.getNotificationHub("hubname");

**Обновление центра уведомлений:**

    hub.setMpnsCredential(new MpnsCredential("mpnscert", "mpnskey"));
    hub = namespaceManager.updateNotificationHub(hub);

**Удаление центра уведомлений:**

    namespaceManager.deleteNotificationHub("hubname");

### <a name="registration-cruds"></a>Операции CRUD для регистрации
**Создание клиента центра уведомлений:**

    hub = new NotificationHub("connection string", "hubname");

**Создание регистрации Windows:**

    WindowsRegistration reg = new WindowsRegistration(new URI(CHANNELURI));
    reg.getTags().add("myTag");
    reg.getTags().add("myOtherTag");    
    hub.createRegistration(reg);

**Создание регистрации iOS:**

    AppleRegistration reg = new AppleRegistration(DEVICETOKEN);
    reg.getTags().add("myTag");
    reg.getTags().add("myOtherTag");
    hub.createRegistration(reg);

Аналогичным образом можно создавать регистрации для Android (GCM), Windows Phone (MPNS) и Kindle Fire (ADM).

**Создание регистрации шаблона:**

    WindowsTemplateRegistration reg = new WindowsTemplateRegistration(new URI(CHANNELURI), WNSBODYTEMPLATE);
    reg.getHeaders().put("X-WNS-Type", "wns/toast");
    hub.createRegistration(reg);

**Создание регистраций с помощью создания registrationid и операции upsert**

Этот шаблон позволяет удалить повторы, образовавшиеся из-за потерянных ответов, если в устройстве хранятся идентификаторы регистраций.

    String id = hub.createRegistrationId();
    WindowsRegistration reg = new WindowsRegistration(id, new URI(CHANNELURI));
    hub.upsertRegistration(reg);

**Обновление регистраций:**

    hub.updateRegistration(reg);

**Удаление регистраций:**

    hub.deleteRegistration(regid);

**Запрос регистраций:**

* **Получение одной регистрации:**
  
        hub.getRegistration(regid);

* **Получение всех регистраций в центре:**
  
        hub.getRegistrations();

* **Получение регистраций с тегом:**
  
        hub.getRegistrationsByTag("myTag");

* **Получение регистраций по каналу:**
  
        hub.getRegistrationsByChannel("devicetoken");


Все запросы коллекций поддерживают маркеры $top и маркеры продолжения.

### <a name="installation-api-usage"></a>Использование API установки
API установки — это альтернативный механизм управления регистрацией. Вместо поддержки нескольких регистраций, что является непростой задачей, которую зачастую выполняют неправильно или неэффективно, теперь можно использовать ЕДИНЫЙ объект установки. Установка включает в себя все необходимые составляющие: канал отправки (маркер устройства), теги, шаблоны и вспомогательные плитки (для WNS и APNS). Вам больше не нужно вызывать службу, чтобы получить идентификатор. Просто создайте GUID или любой другой идентификатор, сохраните его в устройстве и отправьте в серверную часть вместе с каналом отправки (маркером устройства). На сервере нужно сделать всего один вызов: CreateOrUpdateInstallation. Он является полностью идемпотентным, поэтому при необходимости можно повторить попытку.

Вот пример для Amazon Kindle Fire.

    Installation installation = new Installation("installation-id", NotificationPlatform.Adm, "adm-push-channel");
    hub.createOrUpdateInstallation(installation);

Если нужно обновление: 

    installation.addTag("foo");
    installation.addTemplate("template1", new InstallationTemplate("{\"data\":{\"key1\":\"$(value1)\"}}","tag-for-template1"));
    installation.addTemplate("template2", new InstallationTemplate("{\"data\":{\"key2\":\"$(value2)\"}}","tag-for-template2"));
    hub.createOrUpdateInstallation(installation);

Для сложных сценариев можно выполнять частичное обновление, которое позволяет изменять только определенные свойства объекта установки. Частичное обновление является подмножеством операций исправления JSON, которые можно выполнить для объекта установки.

    PartialUpdateOperation addChannel = new PartialUpdateOperation(UpdateOperationType.Add, "/pushChannel", "adm-push-channel2");
    PartialUpdateOperation addTag = new PartialUpdateOperation(UpdateOperationType.Add, "/tags", "bar");
    PartialUpdateOperation replaceTemplate = new PartialUpdateOperation(UpdateOperationType.Replace, "/templates/template1", new InstallationTemplate("{\"data\":{\"key3\":\"$(value3)\"}}","tag-for-template1")).toJson());
    hub.patchInstallation("installation-id", addChannel, addTag, replaceTemplate);

Удаление установки:

    hub.deleteInstallation(installation.getInstallationId());

Операции CreateOrUpdate, Patch и Delete согласованы в конечном счете с операцией Get. Запрошенная операция просто добавляется в системную очередь во время вызова и затем выполняется в фоновом режиме. Операция Get не предназначена для использования в основном сценарии среды выполнения. Она предусмотрена только для отладки и устранения неполадок и строго регулируется службой.

Поток отправки для установки аналогичен потоку отправки для регистрации. Чтобы отправлять уведомления в конкретный объект установки, необходимо просто использовать тег InstallationId:{desired-id}. В этом случае используется приведенный ниже код.

    Notification n = Notification.createWindowsNotification("WNS body");
    hub.sendNotification(n, "InstallationId:{installation-id}");

Для одного из нескольких шаблонов:

    Map<String, String> prop =  new HashMap<String, String>();
    prop.put("value3", "some value");
    Notification n = Notification.createTemplateNotification(prop);
    hub.sendNotification(n, "InstallationId:{installation-id} && tag-for-template1");

### <a name="schedule-notifications-available-for-standard-tier"></a>Планирование уведомлений (доступно для уровня STANDARD)
Такие уведомления отправляются обычным способом с использованием дополнительного параметра — scheduledTime, который определяет, когда нужно доставить уведомление. Для отправки можно указать любое время в диапазоне «теперешнее время-5 минут» и «теперешнее время-7 дней».

**Планирование собственных уведомлений Windows:**

    Calendar c = Calendar.getInstance();
    c.add(Calendar.DATE, 1);    
    Notification n = Notification.createWindowsNotification("WNS body");
    hub.scheduleNotification(n, c.getTime());

### <a name="importexport-available-for-standard-tier"></a>Импорт или экспорт (доступно для уровня STANDARD)
Иногда требуется выполнить массовую операцию регистрации. Обычно это необходимо для интеграции с другой системой или для внесения значительных исправлений, например для обновления тегов. Не рекомендуется использовать процесс Get или Update, если речь идет о тысячах операций регистрации. Возможность импорта или экспорта разработана для поддержки сценария. В сущности, вы предоставляете доступ к определенному контейнеру больших двоичных объектов в вашей учетной записи хранения в качестве источника входящих данных и расположения для выходных данных.

**Отправка задания экспорта:**

    NotificationHubJob job = new NotificationHubJob();
    job.setJobType(NotificationHubJobType.ExportRegistrations);
    job.setOutputContainerUri("container uri with SAS signature");
    job = hub.submitNotificationHubJob(job);


**Отправка задания импорта:**

    NotificationHubJob job = new NotificationHubJob();
    job.setJobType(NotificationHubJobType.ImportCreateRegistrations);
    job.setImportFileUri("input file uri with SAS signature");
    job.setOutputContainerUri("container uri with SAS signature");
    job = hub.submitNotificationHubJob(job);

**Ожидание завершения задания:**

    while(true){
        Thread.sleep(1000);
        job = hub.getNotificationHubJob(job.getJobId());
        if(job.getJobStatus() == NotificationHubJobStatus.Completed)
            break;
    }       

**Получение всех заданий:**

    List<NotificationHubJob> jobs = hub.getAllNotificationHubJobs();

**URI с подписью SAS** — это URL-адрес определенного файла большого двоичного объекта или контейнера больших двоичных объектов в сочетании с набором параметров, таких как разрешения и сроки действия, и подписью всех этих элементов, выполненной с использованием ключа подписанного URL-адреса учетной записи. Пакет SDK для Java для службы хранилища Azure предоставляет широкие возможности, включая создание подобных универсальных кодов ресурса. В качестве простой альтернативы рассмотрим тестовый класс ImportExportE2E (из расположения github) с базовой компактной реализацией алгоритма подписывания.

### <a name="send-notifications"></a>Отправка уведомлений
Объект уведомления — это основной текст с заголовками. С помощью методов, предоставляемых некоторыми служебными программами, можно создать собственные и шаблонные объекты уведомлений.

* **Магазин Windows и Windows Phone 8.1 (без Silverlight)**
  
        String toast = "<toast><visual><binding template=\"ToastText01\"><text id=\"1\">Hello from Java!</text></binding></visual></toast>";
        Notification n = Notification.createWindowsNotification(toast);
        hub.sendNotification(n);
* **iOS**
  
        String alert = "{\"aps\":{\"alert\":\"Hello from Java!\"}}";
        Notification n = Notification.createAppleNotification(alert);
        hub.sendNotification(n);
* **Android**
  
        String message = "{\"data\":{\"msg\":\"Hello from Java!\"}}";
        Notification n = Notification.createGcmNotification(message);
        hub.sendNotification(n);
* **Windows Phone 8.0 и 8.1 Silverlight**
  
        String toast = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                    "<wp:Notification xmlns:wp=\"WPNotification\">" +
                       "<wp:Toast>" +
                            "<wp:Text1>Hello from Java!</wp:Text1>" +
                       "</wp:Toast> " +
                    "</wp:Notification>";
        Notification n = Notification.createMpnsNotification(toast);
        hub.sendNotification(n);
* **Kindle Fire**
  
        String message = "{\"data\":{\"msg\":\"Hello from Java!\"}}";
        Notification n = Notification.createAdmNotification(message);
        hub.sendNotification(n);
* **Отправка к тегам**
  
        Set<String> tags = new HashSet<String>();
        tags.add("boo");
        tags.add("foo");
        hub.sendNotification(n, tags);
* **Отправка к выражению тега**       
  
        hub.sendNotification(n, "foo && ! bar");
* **Отправка шаблонного уведомления**
  
        Map<String, String> prop =  new HashMap<String, String>();
        prop.put("prop1", "v1");
        prop.put("prop2", "v2");
        Notification n = Notification.createTemplateNotification(prop);
        hub.sendNotification(n);

После выполнения кода Java на целевом устройстве должно отобразиться уведомление.

## <a name="next-steps"></a>Дальнейшие действия
В этом разделе было показано, как создать простой клиент REST Java для службы "Центры уведомлений". На данном этапе можно сделать следующее.

* Скачайте полный [пакете SDK для Java], содержащий весь код пакета SDK. 
* Попробуйте разные примеры:
  * [Приступая к работе с центрами уведомлений]
  * [Отправка экстренных новостей]
  * [Отправка локализованных экстренных новостей]
  * [Отправка уведомлений проверенным пользователям]
  * [Отправка кроссплатформенных уведомлений проверенным пользователям]

[пакете SDK для Java]: https://github.com/Azure/azure-notificationhubs-java-backend
[Get started tutorial]: http://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started/
[Приступая к работе с центрами уведомлений]: http://www.windowsazure.com/manage/services/notification-hubs/getting-started-windows-dotnet/
[Отправка экстренных новостей]: http://www.windowsazure.com/manage/services/notification-hubs/breaking-news-dotnet/
[Отправка локализованных экстренных новостей]: http://www.windowsazure.com/manage/services/notification-hubs/breaking-news-localized-dotnet/
[Отправка уведомлений проверенным пользователям]: http://www.windowsazure.com/manage/services/notification-hubs/notify-users/
[Отправка кроссплатформенных уведомлений проверенным пользователям]: http://www.windowsazure.com/manage/services/notification-hubs/notify-users-xplat-mobile-services/
[Maven]: http://maven.apache.org/

