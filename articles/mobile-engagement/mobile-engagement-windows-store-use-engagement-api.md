---
title: Как использовать API Engagement для универсальных приложений для Windows
description: Как использовать API Engagement для универсальных приложений для Windows
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: bb501fca-9cfe-4495-81df-b5efd6e0137b
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-store
ms.devlang: dotnet
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 4f8f211764646bc53319f435d74a16a026329039
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/30/2018
---
# <a name="how-to-use-the-engagement-api-on-windows-universal"></a>Как использовать API Engagement для универсальных приложений для Windows
> [!IMPORTANT]
> Срок действия Служб мобильного взаимодействия истекает 31.03.2018. Вскоре после этого страница будет удалена.
> 

Этот документ представляет собой дополнение к документу [Как интегрировать Engagement в универсальные приложения для Windows](mobile-engagement-windows-store-integrate-engagement.md). В нем предоставлены подробные сведения о том, как использовать API Engagement для сообщения статистики приложения.

Учтите, что если вам требуется, чтобы служба Engagement сообщала только о сеансах, действиях, сбоях и технической информации приложения, проще всего сделать так, чтобы все подклассы `Page` наследовались из класса `EngagementPage`.

Если вы хотите, чтобы служба делала что-то еще, например сообщала о событиях, ошибках и заданиях приложения или сообщала о действиях приложения способом, отличным от реализованного в классах `EngagementPage`, необходимо использовать API Engagement.

API Engagement предоставляется в классе `EngagementAgent` . К этим методам можно обращаться с помощью `EngagementAgent.Instance`.

Даже если модуль агента не инициализирован, каждый вызов API откладывается и выполняется повторно, когда агент становится доступен.

## <a name="engagement-concepts"></a>Основные понятия Engagement
В следующих подразделах дано более подробное объяснение [общих понятий Служб мобильного взаимодействия](mobile-engagement-concepts.md) для универсальной платформы для Windows.

### <a name="session-and-activity"></a>`Session` и `Activity`
*Действие*, как правило, связано с одной страницей приложения, т. е. *действие* начинается при отображении страницы и завершается при ее закрытии. Это происходит в том случае, если пакет SDK для Engagement интегрируется с помощью класса `EngagementPage`.

*Действиями* можно также управлять вручную с помощью API Engagement. Это позволяет разделить определенную страницу на несколько частей, чтобы получить дополнительную информацию о ее использовании (например, информацию о частоте и продолжительности использования диалоговых окон на этой странице).

## <a name="reporting-activities"></a>Уведомление о действиях
### <a name="user-starts-a-new-activity"></a>Запуск нового действия пользователем
#### <a name="reference"></a>Справочные материалы
            void StartActivity(string name, Dictionary<object, object> extras = null)

При каждом изменении пользовательского действия необходимо вызывать `StartActivity()` . При первом вызове этой функции начинается новый сеанс пользователя.

> [!IMPORTANT]
> Пакет SDK автоматически вызывает метод EndActivity при закрытии приложения. Таким образом, мы НАСТОЯТЕЛЬНО рекомендуем вызывать метод StartActivity при каждом изменении действия пользователя и НИКОГДА не вызывать метод EndActivity, так как это приводит к завершению текущего сеанса.
> 
> 

#### <a name="example"></a>Пример
            EngagementAgent.Instance.StartActivity("main", new Dictionary<object, object>() {{"example", "data"}});

### <a name="user-ends-his-current-activity"></a>Завершение текущего действия пользователем
#### <a name="reference"></a>Справочные материалы
            void EndActivity()

Это приводит к завершению действия и сеанса. Не следует вызывать этот метод, если не вполне ясно, что он делает.

#### <a name="example"></a>Пример
            EngagementAgent.Instance.EndActivity();

## <a name="reporting-jobs"></a>Уведомление о заданиях
### <a name="start-a-job"></a>Запустите задание
#### <a name="reference"></a>Справочные материалы
            void StartJob(string name, Dictionary<object, object> extras = null)

Задания можно использовать для отслеживания определенных задач в течение заданного периода времени.

#### <a name="example"></a>Пример
            // An upload begins...

            // Set the extras
            var extras = new Dictionary<object, object>();
            extras.Add("title", "avatar");
            extras.Add("type", "image");

            EngagementAgent.Instance.StartJob("uploadData", extras);

### <a name="end-a-job"></a>Конец задания
#### <a name="reference"></a>Справочные материалы
            void EndJob(string name)

Сразу же после завершения задачи, отслеживаемой с помощью задания, следует вызвать для этого задания метод EndJob, указав его имя.

#### <a name="example"></a>Пример
            // In the previous section, we started an upload tracking with a job
            // Then, the upload ends

            EngagementAgent.Instance.EndJob("uploadData");

## <a name="reporting-events"></a>Уведомление о событиях
Существует три типа событий:

* Изолированные события
* События сеанса
* События задания

### <a name="standalone-events"></a>Изолированные события
#### <a name="reference"></a>Справочные материалы
            void SendEvent(string name, Dictionary<object, object> extras = null)

Изолированные события могут происходить вне контекста сеанса.

#### <a name="example"></a>Пример
            EngagementAgent.Instance.SendEvent("event", extra);

### <a name="session-events"></a>События сеанса
#### <a name="reference"></a>Справочные материалы
            void SendSessionEvent(string name, Dictionary<object, object> extras = null)

События сеанса обычно используются для уведомления о действиях, выполняемых пользователем во время сеанса.

#### <a name="example"></a>Пример
**Без данных:**

            EngagementAgent.Instance.SendSessionEvent("sessionEvent");

            // or

            EngagementAgent.Instance.SendSessionEvent("sessionEvent", null);

**С данными:**

            Dictionary<object, object> extras = new Dictionary<object,object>();
            extras.Add("name", "data");
            EngagementAgent.Instance.SendSessionEvent("sessionEvent", extras);

### <a name="job-events"></a>События задания
#### <a name="reference"></a>Справочные материалы
            void SendJobEvent(string eventName, string jobName, Dictionary<object, object> extras = null)

События заданий обычно используются для уведомления о действиях, выполняемых пользователем во время задания.

#### <a name="example"></a>Пример
            EngagementAgent.Instance.SendJobEvent("eventName", "jobName", extras);

## <a name="reporting-errors"></a>Уведомление об ошибках
Существует три типа ошибок:

* Изолированные ошибки
* Ошибки сеанса
* Ошибки заданий

### <a name="standalone-errors"></a>Изолированные ошибки
#### <a name="reference"></a>Справочные материалы
            void SendError(string name, Dictionary<object, object> extras = null)

В отличие от ошибок сеанса изолированные ошибки могут возникать вне контекста сеанса.

#### <a name="example"></a>Пример
            EngagementAgent.Instance.SendError("errorName", extras);

### <a name="session-errors"></a>Ошибки сеанса
#### <a name="reference"></a>Справочные материалы
            void SendSessionError(string name, Dictionary<object, object> extras = null)

Ошибки сеанса обычно используются для уведомления об ошибках, влияющих на пользователя во время сеанса.

#### <a name="example"></a>Пример
            EngagementAgent.Instance.SendSessionError("errorName", extra);

### <a name="job-errors"></a>Ошибки заданий
#### <a name="reference"></a>Справочные материалы
            void SendJobError(string errorName, string jobName, Dictionary<object, object> extras = null)

Ошибки могут быть связаны с выполняемым заданием, а не с текущим сеансом пользователя.

#### <a name="example"></a>Пример
            EngagementAgent.Instance.SendJobError("errorName", "jobname", extra);

## <a name="reporting-crashes"></a>Сбои отчетов
Агент предоставляет два метода для обработки сбоев.

### <a name="send-an-exception"></a>Отправка исключения
#### <a name="reference"></a>Справочные материалы
            void SendCrash(Exception e, bool terminateSession = false)

#### <a name="example"></a>Пример
Исключение можно отправить в любое время, вызвав:

            EngagementAgent.Instance.SendCrash(aCatchedException);

Также можно использовать необязательный параметр завершения сеанса Engagement одновременно с отправкой сообщения о сбое. Для этого вызовите:

            EngagementAgent.Instance.SendCrash(new Exception("example"), terminateSession: true);

Если это сделать, сеанс и задания закрываются сразу после отправки сообщения о сбое.

### <a name="send-an-unhandled-exception"></a>Отправка необработанного исключения
#### <a name="reference"></a>Справочные материалы
            void SendCrash(Exception e)

Engagement также предоставляется метод отправки необработанных исключений при условии **отключения** автоматического сообщения о **сбоях** в Engagement. Это особенно удобно при использовании в приложении обработчика событий UnhandledException.

Этот метод будет **ВСЕГДА** завершать сеанс Engagement и задания после вызова.

#### <a name="example"></a>Пример
Вы можете использовать его для реализации собственного обработчика UnhandledExceptionEventArgs. Например, добавьте метод `Current_UnhandledException` файла `App.xaml.cs`:

            // In your App.xaml.cs file

            // Code to execute on Unhandled Exceptions
            void Current_UnhandledException(object sender, UnhandledExceptionEventArgs e)
            {
               EngagementAgent.Instance.SendCrash(e.Exception,false);
            }

В файле App.xaml.cs в Public App(){} добавьте следующее:

            Application.Current.UnhandledException += Current_UnhandledException;

## <a name="device-id"></a>Идентификатор устройства
            String EngagementAgent.Instance.GetDeviceId()

Идентификатор устройства Engagement можно получить, вызвав этот метод.

## <a name="extras-parameters"></a>Дополнительные параметры
Произвольные данные можно прикрепить к событию, ошибке, действию или заданию. Эти данные можно структурировать по словарю. Ключи и значения могут принадлежать к любому типу.

Вспомогательные данные сериализуются, поэтому если нужно вставить в них собственный тип, необходимо добавить контракт данных для этого типа.

### <a name="example"></a>Пример
Создадим новый класс Person.

            using System.Runtime.Serialization;

            namespace Microsoft.Azure.Engagement
            {
              [DataContract]
              public class Person
              {
                public Person(string name, int age)
                {
                  Age = age;
                  Name = name;
                }

                // Properties

                [DataMember]
                public int Age
                {
                  get;
                  set;
                }

                [DataMember]
                public string Name
                {
                  get;
                  set; 
                }
              }
            }

Затем добавим экземпляр `Person` во вспомогательные данные.

            Person person = new Person("Engagement Haddock", 51);
            var extras = new Dictionary<object, object>();
            extras.Add("people", person);

            EngagementAgent.Instance.SendEvent("Event", extras);

> [!WARNING]
> При добавлении других типов объектов убедитесь, что для них реализован метод ToString(), возвращающий удобочитаемую строку.
> 
> 

### <a name="limits"></a>Ограничения
#### <a name="keys"></a>ключей
Каждый ключ в объекте должен соответствовать следующему регулярному выражению:

`^[a-zA-Z][a-zA-Z_0-9]*$`

Это означает, что ключ должен содержать не менее одной буквы, за которой следуют буквы, цифры или символы подчеркивания (\_).

#### <a name="size"></a>Размер
Вспомогательные данные могут содержать не более **1024** знаков на вызов.

## <a name="reporting-application-information"></a>Уведомление о данных приложения
### <a name="reference"></a>Справочные материалы
            void SendAppInfo(Dictionary<object, object> appInfos)

С помощью функции SendAppInfo() можно вручную сообщить о данных отслеживания (или любых других данных о приложении).

Обратите внимание, что такие данные можно отправлять поэтапно, так как для заданного устройства будет сохраняться только последнее значение заданного ключа. Как и в случае со вспомогательными данными событий, используйте для присоединения информации формат Dictionary\<объект, объект\>.

### <a name="example"></a>Пример
            Dictionary<object, object> appInfo = new Dictionary<object, object>()
              {
                {"birthdate", "1983-12-07"},
                {"gender", "female"}
              };

            EngagementAgent.Instance.SendAppInfo(appInfo);

### <a name="limits"></a>Ограничения
#### <a name="keys"></a>ключей
Каждый ключ в объекте должен соответствовать следующему регулярному выражению:

`^[a-zA-Z][a-zA-Z_0-9]*$`

Это означает, что ключ должен содержать не менее одной буквы, за которой следуют буквы, цифры или символы подчеркивания (\_).

#### <a name="size"></a>Размер
Объем информации о приложении не может превышать **1024** символа на вызов.

В предыдущем примере длина JSON-файла, отправленного на сервер, составляет 44 знаков:

            {"birthdate":"1983-12-07","gender":"female"}

## <a name="logging"></a>Ведение журналов
### <a name="enable-logging"></a>Включение ведения журналов
Пакет SDK можно настроить для создания журналов тестирования в консоли интегрированной среды разработки.
По умолчанию эти журналы не активированы. Чтобы настроить это действие, обновите свойство `EngagementAgent.Instance.TestLogEnabled` до одного из значений, доступных в перечислении `EngagementTestLogLevel`, например:

            EngagementAgent.Instance.TestLogLevel = EngagementTestLogLevel.Verbose;
            EngagementAgent.Instance.Init();

