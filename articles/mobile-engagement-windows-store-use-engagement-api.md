<properties 
	pageTitle="API для расширенного добавления тегов в SDK для Магазина Windows для Azure Mobile Engagement" 
	description="Последние обновления и указания для пакета SDK для Магазина Windows для Azure Mobile Engagement" 					
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="kpiteira" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="" 
	ms.topic="article" 
	ms.date="02/12/2015" 
	ms.author="kapiteir" />

#Как использовать API Engagement в Windows

Этот документ представляет собой дополнение к документу [Как интегрировать Mobile Engagement в Windows](mobile-engagement-windows-store-integrate-engagement.md). В нем предоставлена подробная информация о том, как использовать API Engagement для сообщения статистики приложения.

Учтите, что если вам требуется, чтобы служба Engagement сообщала только о сеансах, действиях, сбоях и технической информации приложения, проще всего сделать так, чтобы все подклассы `Page` наследовались из класса `EngagementPage`.

Если вы хотите, чтобы служба делала что-то еще, например, сообщала о событиях, ошибках и заданиях приложения или сообщала о действиях приложения способом, отличным от реализованного в классах `EngagementPage`, необходимо использовать API Engagement.

API Engagement предоставляется в классе `EngagementAgent`. К этим методам можно обращаться с помощью `EngagementAgent.Instance`.

Даже если модуль агента не инициализирован, каждый вызов API откладывается и выполняется повторно, когда агент становится доступен.

##Основные понятия Engagement

В следующих подразделах дано более подробное объяснение [общих понятий Mobile Engagement](mobile-engagement-concepts.md) для платформы Windows.

### `Сеанс` и `действие`

 *Действие*, как правило, связано с одной страницей приложения, т. е. *действие* начинается при отображении страницы и завершается при ее закрытии. Именно в таком случае интегрируется пакет SDK для Engagement с помощью класса `EngagementPage`.

 *Действиями* можно также управлять вручную с помощью API Engagement. Это позволяет разделить эту страницу на несколько частей, чтобы получить дополнительную информацию об использовании этой страницы (например, информацию о частоте и продолжительности использования диалоговых окон на этой странице).

##Уведомление о действиях

### Запуск нового действия пользователем

#### материалы по

			void StartActivity(string name, Dictionary<object, object> extras = null)

При каждом изменении пользовательского действия необходимо вызывать `StartActivity()`. При первом вызове этой функции начинается новый сеанс пользователя.

> [AZURE.TIP] Метод `EndActivity()`не нужно вызывать после каждого вызова `StartActivity()`.

#### Пример

			EngagementAgent.Instance.StartActivity("main", new Dictionary<object, object>() {{"example", "data"}});

### Завершение текущего действия пользователем

#### материалы по

			void EndActivity()

Это приводит к завершению действия и сеанса. Не следует вызывать этот метод, если не вполне ясно, что он делает.

#### Пример

			EngagementAgent.Instance.EndActivity();

##Уведомление о заданиях

### Запустите задание

#### материалы по

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

#### материалы по

			void EndJob(string name)

Сразу же после завершения задачи, отслеживаемой с помощью задания, следует вызвать для этого задания метод EndJob, указав его имя.

#### Пример

			// In the previous section, we started an upload Отслеживание с помощью задания
			// Then, the upload ends
			
			EngagementAgent.Instance.EndJob("uploadData");

##Уведомление о событиях

Существует три типа событий:

-   Изолированные события
-   События сеанса
-   События задания

### Изолированные события

#### материалы по

			void SendEvent(string name, Dictionary<object, object> extras = null)

Изолированные события могут происходить вне контекста сеанса.

#### Пример

			EngagementAgent.Instance.SendEvent("event", extra);

### События сеанса

#### материалы по

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

#### материалы по

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

#### материалы по

			void SendError(string name, Dictionary<object, object> extras = null)

В отличие от ошибок сеанса изолированные ошибки могут возникать вне контекста сеанса.

#### Пример

			EngagementAgent.Instance.SendError("errorName", extras);

### Ошибки сеанса

#### материалы по

			void SendSessionError(string name, Dictionary<object, object> extras = null)

Ошибки сеанса обычно используются для уведомления об ошибках, влияющих на пользователя во время сеанса.

#### Пример

			EngagementAgent.Instance.SendSessionError("errorName", extra);

### Ошибки заданий

#### материалы по

			void SendJobError(string errorName, string jobName, Dictionary<object, object> extras = null)

Ошибки могут быть связаны с выполняемым заданием, а не с текущим сеансом пользователя.

#### Пример

			EngagementAgent.Instance.SendJobError("errorName", "jobname", extra);

##Сбои отчетов

Агент предоставляет два метода для обработки сбоев.

### Отправка исключения

#### материалы по

			void SendCrash(Exception e, bool terminateSession = false)

#### Пример

Исключение можно отправить в любое время, вызвав:

			EngagementAgent.Instance.SendCrash(aCatchedException);

Также можно использовать необязательный параметр завершения сеанса Engagement одновременно с отправкой сообщения о сбое. Для этого вызовите:

			EngagementAgent.Instance.SendCrash(new Exception("example"), terminateSession: true);

Если это сделать, сеанс и задания закрываются сразу после отправки сообщения о сбое.

### Отправка необработанного исключения

#### материалы по

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

> [AZURE.WARNING] При добавлении других типов объектов убедитесь, что для них реализован метод ToString(), возвращающий удобочитаемую строку.

### Ограничения

#### ключей

Каждый ключ в объекте должен соответствовать следующему регулярному выражению:

`^[a-zA-Z][a-zA-Z_0-9]*$`

Это означает, что ключ должен содержать не менее одной буквы, за которой будут идти буквы, цифры или символы подчеркивания (\_).

#### Размер

Вспомогательные данные могут содержать не более **1024** знаков на вызов.

##Уведомление о данных приложения

### материалы по

			void SendAppInfo(Dictionary<object, object> appInfos)

С помощью функции SendAppInfo() можно вручную сообщить о данных отслеживания (или любых других данных о приложении).

Обратите внимание, что такие данные можно отправлять поэтапно, так как для заданного устройства будет сохраняться только последнее значение заданного ключа. Как и в случае со вспомогательными данными событий, используйте для присоединения информацию Dictionary\<объект, объект\>.

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

Это означает, что ключ должен содержать не менее одной буквы, за которой будут идти буквы, цифры или символы подчеркивания (\_).

#### Размер

Информация о приложении не может превышать **1024** знака на вызов.

В предыдущем примере длина JSON-файла, отправленного на сервер, составляет 44 знаков:

			{"birthdate":"1983-12-07","gender":"female"}

<!--HONumber=47-->
