<properties 
	pageTitle="Как использовать API Engagement для универсальных приложений для Windows" 
	description="Как использовать API Engagement для универсальных приложений для Windows"			
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="piyushjo" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="07/07/2015" 
	ms.author="piyushjo" />

#Как использовать API Engagement для универсальных приложений для Windows

Этот документ представляет собой дополнение к документу [Как интегрировать Engagement в универсальные приложения для Windows](../mobile-engagement-windows-store-integrate-engagement/). В нем предоставлена подробная информация о том, как использовать API Engagement для сообщения статистики приложения.

Учтите, что если вам требуется, чтобы служба Engagement сообщала только о сеансах, действиях, сбоях и технической информации приложения, проще всего сделать так, чтобы все подклассы `Page` наследовались из класса `EngagementPage`.

Если вы хотите, чтобы служба делала что-то еще, например сообщала о событиях, ошибках и заданиях приложения или сообщала о действиях приложения способом, отличным от реализованного в классах `EngagementPage`, необходимо использовать API Engagement.

API Engagement предоставляется в классе `EngagementAgent`. К этим методам можно обращаться с помощью `EngagementAgent.Instance`.

Даже если модуль агента не инициализирован, каждый вызов API откладывается и выполняется повторно, когда агент становится доступен.

##Основные понятия Engagement

В следующих подразделах дано более подробное объяснение [общих понятий Mobile Engagement](../mobile-engagement-concepts/) для универсальной платформы для Windows.

### `Session` и `Activity`

*Действие*, как правило, связано с одной страницей приложения, т. е. *действие* начинается при отображении страницы и завершается при ее закрытии. Это происходит в том случае, если пакет SDK для Engagement интегрируется с помощью класса `EngagementPage`.

*Действиями* можно также управлять вручную с помощью API Engagement. Это позволяет разделить эту страницу на несколько частей, чтобы получить дополнительную информацию об использовании этой страницы (например, информацию о частоте и продолжительности использования диалоговых окон на этой странице).

##Уведомление о действиях

### Запуск нового действия пользователем

#### Справочные материалы

			void StartActivity(string name, Dictionary<object, object> extras = null)

При каждом изменении пользовательского действия необходимо вызывать `StartActivity()`. При первом вызове этой функции начинается новый сеанс пользователя.

> [AZURE.IMPORTANT]Пакет SDK автоматически вызывает метод EndActivity при закрытии приложения. Таким образом, мы НАСТОЯТЕЛЬНО рекомендуем вызывать метод StartActivity при каждом изменении действия пользователя и НИКОГДА не вызывать метод EndActivity, так как это приводит к завершению текущего сеанса.

#### Пример

			EngagementAgent.Instance.StartActivity("main", new Dictionary<object, object>() {{"example", "data"}});

### Завершение текущего действия пользователем

#### Справочные материалы

			void EndActivity()

Это приводит к завершению действия и сеанса. Не следует вызывать этот метод, если не вполне ясно, что он делает.

#### Пример

			EngagementAgent.Instance.EndActivity();

##Уведомление о заданиях

### Запустите задание

#### Справочные материалы

			void StartJob(string name, Dictionary<object, object> extras = null)

Задания можно использовать для отслеживания определенных задач в течение заданного периода времени.

#### Пример

			// An upload begins...
			
			// Set the extras
			var extras = new Dictionary<object, object>();
			extras.Add("title", "avatar");
			extras.Add("type", "image");
			
			EngagementAgent.Instance.StartJob("uploadData", extras);

### Конец задания

#### Справочные материалы

			void EndJob(string name)

Сразу же после завершения задачи, отслеживаемой с помощью задания, следует вызвать для этого задания метод EndJob, указав его имя.

#### Пример

			// In the previous section, we started an upload tracking with a job
			// Then, the upload ends
			
			EngagementAgent.Instance.EndJob("uploadData");

##Уведомление о событиях

Существует три типа событий:

-   Изолированные события
-   События сеанса
-   События задания

### Изолированные события

#### Справочные материалы

			void SendEvent(string name, Dictionary<object, object> extras = null)

Изолированные события могут происходить вне контекста сеанса.

#### Пример

			EngagementAgent.Instance.SendEvent("event", extra);

### События сеанса

#### Справочные материалы

			void SendSessionEvent(string name, Dictionary<object, object> extras = null)

События сеанса обычно используются для уведомления о действиях, выполняемых пользователем во время сеанса.

#### Пример

**Без данных:**

			EngagementAgent.Instance.SendSessionEvent("sessionEvent");
			
			// or
			
			EngagementAgent.Instance.SendSessionEvent("sessionEvent", null);

**С данными:**

			Dictionary<object, object> extras = new Dictionary<object,object>();
			extras.Add("name", "data");
			EngagementAgent.Instance.SendSessionEvent("sessionEvent", extras);

### События задания

#### Справочные материалы

			void SendJobEvent(string eventName, string jobName, Dictionary<object, object> extras = null)

События заданий обычно используются для уведомления о действиях, выполняемых пользователем во время задания.

#### Пример

			EngagementAgent.Instance.SendJobEvent("eventName", "jobName", extras);

##Уведомление об ошибках

Существует три типа ошибок:

-   Изолированные ошибки
-   Ошибки сеанса
-   Ошибки заданий

### Изолированные ошибки

#### Справочные материалы

			void SendError(string name, Dictionary<object, object> extras = null)

В отличие от ошибок сеанса изолированные ошибки могут возникать вне контекста сеанса.

#### Пример

			EngagementAgent.Instance.SendError("errorName", extras);

### Ошибки сеанса

#### Справочные материалы

			void SendSessionError(string name, Dictionary<object, object> extras = null)

Ошибки сеанса обычно используются для уведомления об ошибках, влияющих на пользователя во время сеанса.

#### Пример

			EngagementAgent.Instance.SendSessionError("errorName", extra);

### Ошибки заданий

#### Справочные материалы

			void SendJobError(string errorName, string jobName, Dictionary<object, object> extras = null)

Ошибки могут быть связаны с выполняемым заданием, а не с текущим сеансом пользователя.

#### Пример

			EngagementAgent.Instance.SendJobError("errorName", "jobname", extra);

##Сбои отчетов

Агент предоставляет два метода для обработки сбоев.

### Отправка исключения

#### Справочные материалы

			void SendCrash(Exception e, bool terminateSession = false)

#### Пример

Исключение можно отправить в любое время, вызвав:

			EngagementAgent.Instance.SendCrash(aCatchedException);

Также можно использовать необязательный параметр завершения сеанса Engagement одновременно с отправкой сообщения о сбое. Для этого вызовите:

			EngagementAgent.Instance.SendCrash(new Exception("example"), terminateSession: true);

Если это сделать, сеанс и задания закрываются сразу после отправки сообщения о сбое.

### Отправка необработанного исключения

#### Справочные материалы

			void SendCrash(Exception e)

Engagement также предоставляется метод отправки необработанных исключений при условии **ОТКЛЮЧЕНИЯ** автоматического сообщения о **сбоях** в Engagement. Это особенно удобно при использовании в приложении обработчика событий UnhandledException.

Этот метод будет **ВСЕГДА** завершать сеанс Engagement и задания после вызова.

#### Пример

Вы можете использовать его для реализации собственного обработчика UnhandledExceptionEventArgs. Например, добавьте метод `Current_UnhandledException` файла `App.xaml.cs`:

			// In your App.xaml.cs file
			
			// Code to execute on Unhandled Exceptions
			void Current_UnhandledException(object sender, UnhandledExceptionEventArgs e)
			{
			   EngagementAgent.Instance.SendCrash(e.Exception,false);
			}

В файле App.xaml.cs в Public App(){} добавьте следующее:

			Application.Current.UnhandledException += Current_UnhandledException;

##Идентификатор устройства

			String EngagementAgent.Instance.GetDeviceId()

Идентификатор устройства Engagement можно получить, вызвав этот метод.

##Дополнительные параметры

Произвольные данные можно прикрепить к событию, ошибке, действию или заданию. Эти данные можно структурировать по словарю. Ключи и значения могут принадлежать к любому типу.

Вспомогательные данные сериализуются, поэтому если нужно вставить в них собственный тип, необходимо добавить контракт данных для этого типа.

### Пример

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

> [AZURE.WARNING]При добавлении других типов объектов убедитесь, что для них реализован метод ToString(), возвращающий удобочитаемую строку.

### Ограничения

#### ключей

Каждый ключ в объекте должен соответствовать следующему регулярному выражению:

`^[a-zA-Z][a-zA-Z_0-9]*$`

Это означает, что ключ должен содержать не менее одной буквы, за которой следуют буквы, цифры или символы подчеркивания (_).

#### Размер

Вспомогательные данные могут содержать не более **1024** знаков на вызов.

##Уведомление о данных приложения

### Справочные материалы

			void SendAppInfo(Dictionary<object, object> appInfos)

С помощью функции SendAppInfo() можно вручную сообщить о данных отслеживания (или любых других данных о приложении).

Обратите внимание, что такие данные можно отправлять поэтапно, так как для заданного устройства будет сохраняться только последнее значение заданного ключа. Как и в случае со вспомогательными данными событий, используйте для присоединения информации Dictionary<объект, объект>.

### Пример

			Dictionary<object, object> appInfo = new Dictionary<object, object>()
			  {
			    {"birthdate", "1983-12-07"},
			    {"gender", "female"}
			  };
			
			EngagementAgent.Instance.SendAppInfo(appInfo);

### Ограничения

#### ключей

Каждый ключ в объекте должен соответствовать следующему регулярному выражению:

`^[a-zA-Z][a-zA-Z_0-9]*$`

Это означает, что ключ должен содержать не менее одной буквы, за которой следуют буквы, цифры или символы подчеркивания (_).

#### Размер

Размер информации о приложении не может быть более **1024** знаков на вызов.

В предыдущем примере длина JSON-файла, отправленного на сервер, составляет 44 знаков:

			{"birthdate":"1983-12-07","gender":"female"}
 

<!---HONumber=July15_HO2-->