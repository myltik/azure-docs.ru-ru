<properties 
	pageTitle="Модель данных Application Insights" 
	description="Описание свойств, экспортируемых с помощью непрерывного экспорта в формате JSON и используемых в качестве фильтров." 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/11/2015" 
	ms.author="awills"/>

# Экспорт модели данных Application Insights

В этой таблице перечислены свойства телеметрии, отправляемой из различных пакетов SDK для [Application Insights](app-insights-overview.md) на портал. Вы увидите эти свойства в выходных данных [непрерывного экспорта](app-insights-export-telemetry.md). Также они отображаются в фильтрах свойств в [обозревателе метрик](app-insights-metrics-explorer.md) и [диагностическом поиске](app-insights-diagnostic-search.md).

Существует несколько [примеров](app-insights-export-telemetry.md#code-samples), показывающих, как их использовать.

&lt;telemetryType&gt; первого раздела — это заполнитель для имени любого типа телеметрии : представления, запроса и т. д.


## &lt;telemetryType&gt;

**<measurement>**

    KVPs <string, double> <telemetryType>.measurement      Max: 100
* 
    Контейнер свойств с парой "ключ-значение" (KVP), обеспечивающий расширяемость элементов телеметрии AppInsights для добавления пользовательских метрик. 

    *Формирование:* длина названий мер не может быть больше 100 знаков.

    *По умолчанию:* если имеется ключ без значения, то счетчик = 1, значение = 0, минимальное или максимальное значения = 0.

**<property>**

    KVPs <string, string> <telemetryType>.properties      Max: 100
* 
    Контейнер свойств с парой "ключ-значение" (KVP), обеспечивающий расширяемость элементов телеметрии AppInsights для добавления пользовательских свойств. Разработчик может предоставить список пар "ключ-значение", связанный с элементом телеметрии. Каждый ключ отслеживается, при этом из расчета на одно приложение AppInsights ikey можно предоставить до 200 уникальных ключей. Максимальная длина ключа может составлять 100 символов. Все значения обрабатываются как строка длиной не более 1000 символов. Каждое свойство первоначально классифицируется как данные длины, ширины или высоты, при этом возможно использование функций сегментации, зависимой от набора значений каждого свойства. Каждый набор значений отслеживается на количество элементов по ключу свойства. Когда количество элементов ключа превышает 100 уникальных значений, свойство классифицируется как атрибут. Можно выполнить поиск атрибута, однако он не может являться целью сегментации (агрегирования и группировки). 

    *Формирование:* длина имен свойств не может быть больше 100 знаков, а значения не могут превышать 1024 знака.

    *По умолчанию:* если ключ существует, но его значение отсутствует, тогда его значение — NULL.

**count**

    long <telemetryType>.count      
* 
    Количество элементов телеметрии.   

    *По умолчанию:* при значении NULL количество = 1.

**длительность**

    simpleMetric <telemetryType>.duration   ticks   
* 
    Длительность элемента телеметрии. Для запросов это значение указывает на время выполнения запроса. 

    *По умолчанию:* R1: для представления это поле не является обязательным.

**message**

    string <telemetrytype>.message      Max: 10240
* 
    Текстовое сообщение о типе телеметрии. Эта строка доступна для полнотекстового поиска. 

**name**

    string <telemetrytype>.name      Max: 250
* 
    Имя элемента телеметрии. Это имя не является уникальным в различных экземплярах и служит для группирования типов телеметрии. Для представлений это значение сбрасывается на значение по умолчанию URLData.base. Для событий оно представляет собой метку, создаваемую разработчиком. Для запросов оно представляет собой читаемую форму запроса, например controller\\action. 

    *Примеры*<br/> Имена представлений:<br/>Вопрос № 1 экзамена 70-486<br/>О моем приложении ASP.NET<br/><br/>Имена примеров запросов:<br/>POST /Components/WebHandlers/ItemCompare.ashx<br/>GET /signalr/poll<br/>GET /signalr/negotiate

**severity**

    string <telemetryType>.severity      Max: 100
* 
    Серьезность элемента телеметрии. Это свойство допустимо использовать для элементов телеметрии Trace и Exception 

**url**

    string <telemetrytype>.url      Max: 2048
* 
    URL-адрес представления страницы, события, запроса или RDD. Представляет собой полный URL-адрес и поддерживается для полнотекстового поиска и экспорта. Это поле поддерживает большие значения количества элементов и представляет собой атрибут. Оно разбивается на набор элементов данных urlData, которые могут использоваться для агрегирования средствами обозревателя метрик. 

    *По умолчанию:* R2: для remotedepencyType, если используется тип dependencyType = HTTP, это поле является обязательным<br/>, если используется тип clientperformanceType, это поле является обязательным.

    *Примеры:*<br/> https://icecream.contoso.com/main.aspx?etc=3&extraqs=%3fetc%3d3%26formid%3dc40d07a7-1cf1-4e1d-b00e-e61876d1284e&pagemode=iframe&pagetype=entityrecord<br/>http://fabrikam-oats.azurewebsites.net/index.htm

**urlData.base**

    string <telemetrytype>.urldata.base      Max: 2048
* 
    Часть элемента данных URL-адреса, за исключением узла и элементов запроса. Это корневой URI. Это значение может использоваться для сегментации и агрегации. 

    *Формирование:* см. приложение, посвященное преобразованию URL-адресов.

    *Примеры*<br/> /main.aspx?etc=3&extraqs=%3fetc%3d3%26formid%3dc40d07a7-1cf1-4e1d-b00e-e61876d1284e&pagemode=iframe&pagetype=entityrecord<br/>/default.aspx<br/>/Patients/Search/<br/>

**urldata.hashTag**

    string <telemetrytype>.urldata.hashtag      Max: 100
* 
    Текст хэштега элемента данных URL-адреса 

    *Формирование:* см. приложение, посвященное преобразованию URL-адресов.

**urlData.host**

    string <telemetrytype>.urldata.host      Max: 200
* 
    Узел элемента данных URL-адреса. Если элемент данных URL-адреса представляет собой локальный URI, то это значение отображается пустым 

    *Формирование:* см. приложение, посвященное преобразованию URL-адресов.

    *Примеры*<br/> www.fabrikam.com<br/>www.contoso.com<br/>bretwpc711.azurewebsites.net<br/>



## availability

**availability**

    simpleMetric availability.availability      
* 
    Индикатор успешности проверки доступности 

**dataSize**

    simpleMetric availability.datasize      
* 
    Размер текста сообщения &lt;telemetry&gt;.message. 

**result**

    enum (pass/fail) availability.result      
* 
    Указатель (вызов HTTP), предназначенный для извлечения подробных сведений веб-проверки на доступность 

**runLocation**

    string availability.runlocation      Max: 100
* 
    Место выполнения проверки доступности 

**testName**

    string availability.testname      Max: 1024
* 
    Имя проверки доступности 

**testRunId**

    string availability.testrunid      Max: 100
* 
    Уникальный идентификатор экземпляра запущенной проверки доступности 

**testTimeStamp**

    datetime availability.testtimestamp      
* 
    Отметка времени запуска экземпляра проверки доступности 


## basicexception

**assembly**

    string basicexception.assembly      Max: 100
**exceptionGroup**

    string basicException.exceptionGroup      
**exceptionType**

    string basicexception.exceptiontype      Max: 100
**failedUserCodeAssembly**

    string basicException.failedUserCodeAssembly      
**failedUserCodeMethod**

    string basicException.failedUserCodeMethod      
**handledAt**

    string basicexception.handledat      Max: 100
**innerMostExceptionMessage**

    string basicException.innermostExceptionMessage      
**innerMostExceptionThrownAtAssembly**

    string basicException.innermostExceptionThrownAtAssembly      
**innerMostExceptionThrownAtAssembly**

    string basicException.innermostExceptionThrownAtMethod      
**innerMostExceptionMessage**

    string basicException.innermostExceptionType      
**метод**

    string basicexception.method      Max: 100
**outerMostExceptionMessage**

    string basicException.outerExceptionMessage      
**innerMostExceptionThrownAtAssembly**

    string basicException.outerExceptionThrownAtAssembly      
**innerMostExceptionThrownAtAssembly**

    string basicException.outerExceptionThrownAtMethod      
**outerMostExceptionMessage**

    string basicException.outerExceptionType      
**problemid**

    string basicexception.problemid      Max: 100
* 
    *Формирование:* см. приложение, посвященное анализу стека вызовов. 

**Exceptions.Assembly**

    string basicexception.exceptions.assembly      Max: 100
**Exceptions.fileName**

    string basicexception.exceptions.filename      Max: 100
**Exceptions.hasFullStack**

    boolean basicexception.exceptions.hasfullstack      
**Exceptions.Level**

    string basicexception.exceptions.level      Max: 100
**Exceptions.Level**

    long basicexception.exceptions.line      
**Exceptions.Message**

    string basicexception.exceptions.message      Max: 10240
**Exceptions.Message**

    string basicexception.exceptions.method      Max: 100
**Exceptions.Message**

    long basicexception.exceptions.outerid      
**Exceptions.parsedStack**

    List<StackFrame> basicexception.exceptions.parsedstack      
**Exceptions.parsedStack**

    string basicexception.exceptions.stack      Max: 10240
**Exceptions.typeName**

    string basicexception.exceptions.typename      Max: 100
**parsedStackAssembly**

    string basicException.parsedStack.assembly      
**parsedStackFilename**

    string basicException.parsedStack.fileName      
**parsedStackLevel**

    string basicException.parsedStack.level      
**parsedStackLevel**

    string basicException.parsedStack.line      
**paseStackMethod**

    string basicException.parsedStack.method      

## clientperformance

**domProcessing**

    simpleMetric clientperformance.domprocessing   ms   
* 
    Часть времени perTotal. Этот отрезок указывает на время, в течение которого приложение выполняло обработку отклика. Для веб-клиента оно представляет собой время обработки модели DOM. Это время извлекается при помощи интерфейса API perfTiming, используемого в современных браузерах. 

**networkConnect**

    simpleMetric clientperformance.networkconnect   ms   
* 
    Часть времени perTotal. Этот отрезок указывает на время, в течение которого приложение выполняло сетевое подключение. Это время извлекается при помощи интерфейса API perfTiming, используемого в современных браузерах. 

**perfTotal**

    simpleMetric clientperformance.perftotal   ms   
* 
    Общее время загрузки представления. Для веб-клиентов это эквивалентно ""времени загрузки страницы"". Это время извлекается при помощи интерфейса API perfTiming, используемого в современных браузерах. 

**receiveResponse**

    simpleMetric clientperformance.receiveresponse   ms   
* 
    Часть времени perTotal. Этот отрезок указывает на время, в течение которого приложение получало отклик на запрос. Это время извлекается при помощи интерфейса API perfTiming, используемого в современных браузерах. 

**sendRequest**

    simpleMetric clientperformance.sendrequest   ms   
* 
    Часть времени perTotal. Этот отрезок указывает на время, в течение которого приложение отправляло запрос на сервер. Это время извлекается при помощи интерфейса API perfTiming, используемого в современных браузерах. 


## context

**applicationBuild**

    string context.application.build      Max: 100
* 
    Номер сборки приложения 

**applicationVersion**

    string context.application.version      Max: 100
* 
    Версия клиентского приложения. Недоступна, если задано значение «Unknown». 

    *Примеры:*<br/> 2015.5.21.3<br/>NokiaMailBye\_CD\_20150227.4

**telemetryType**

    string context.billing.telemetrytype      Max: 100
* 
    Это значение указывает на тип телеметрии, который используется в записи для выставления счетов. Оно применяется внутренне в виде сегментации элементов телеметрии, по которым взимается плата, для приложения 

**dataId**

    string context.data.id      Max: 100
* 
    Уникальный идентификатор элемента телеметрии. Назначается в конечной точке сбора данных. 

    *Формирование:* сформированный UUID4.

    *Пример:*<br/> edc6eaf3-3459-46a0-bb81-bedc24913864

**eventTime**

    datetime context.data.eventtime      
* 
    Время события телеметрии, записанное по всеобщему скоординированному времени (UTC). Как правило, оно указывается на клиенте. Если это поле отсутствует, оно заполняется в конечной точке сбора данных. Формат поля — ГГГГ-ММ-ДДTЧЧ:ММ:СС.сссZ.    

    *Пример:*<br/> 2015-05-20T04:00:46.8338283Z

**samplingRate**

    string context.data.samplerate      Max: 100
* 
    Частота выборки поставщика данных (SDK). Любое значение, отличное от 1, указывает на то, что метрики, связанные с данным элементом телеметрии, представляют собой отобранные значения. Таким образом, частота выборки в 0,05 приведет к извлечению любого одного элемента телеметрии с количеством 20. 

**браузер iPhone;**

    string context.device.browser      Max: 100
* 
    Браузер клиента 

    *По умолчанию:* если значение равно NULL, это значение устанавливается при помощи агента пользователя. См. приложение, посвященное анализу агента пользователя

    *Примеры:*<br/> Opera<br/>Mobile Safari<br/>Ovi Browser<br/>Chrome<br/>Firefox<br/>Internet Explorer

**browserVersion**

    string context.device.browserversion      Max: 100
* 
    Версия браузера клиента 

    *По умолчанию:* если значение равно NULL, это значение устанавливается при помощи агента пользователя. См. приложение, посвященное анализу агента пользователя

    *Примеры:*<br/> Opera 12.17<br/>Mobile Safari 8.0<br/>Ovi Browser 5.5<br/>Chrome 37.0<br/>Firefox 21.0<br/>Internet Explorer 7.0

**deploymentId**

    string context.device.deploymentid      Max: 100
* 
    Идентификатор развертывания сервера 


**deviceName**

    string context.device.name      Max: 100
* 
    Имя устройства, на котором выполняется приложение 

**locale**

    string context.device.locale      Max: 100
* 
    Языковые параметры приложения, установленного на клиенте. Если не указано явно для элемента телеметрии, это значение получается в результате обработки поля агента пользователя. 

    *Примеры:*<br/> ru<br/>ru-RU<br/>de-DE<br/>unknown

**machineName**

    string context.device.vmname      Max: 100
* 
    Имя компьютера сервера. Для виртуализованных вычислений этот элемент данных эквивалентен соответствующему узлу. Для выделенных вычислений здесь указывается имя компьютера. 


**operatingSystem**

    string context.device.os      Max: 100
* 
    Операционная система клиента 

    *По умолчанию:* если значение равно NULL, это значение устанавливается при помощи агента пользователя. См. приложение, посвященное анализу агента пользователя

    *Примеры:*<br/> Windows<br/>iOS iPad<br/>Nokia

**operatingSystemVersion**

    string context.device.osversion      Max: 100
* 
    Версия операционной системы клиента 

    *По умолчанию:* если значение равно NULL, это значение устанавливается при помощи агента пользователя. См. приложение, посвященное анализу агента пользователя

    *Примеры:*<br/> Windows XP<br/>iOS 8.3<br/>Nokia Series 40<br/>Windows 7<br/>Windows 8

**roleInstance**

    string context.device.roleinstance      Max: 100
* 
    Экземпляр вычислительного сервера. При облачном/виртуализованном сценарии этот элемент представляет собой виртуальное имя вычислительного экземпляра. В выделенном вычислительном экземпляре здесь указывается имя компьютера. Для облачных служб Azure это значение по умолчанию должно соответствовать имени экземпляра роли PaaS или имени виртуальной машины IaaS  

**roleName**

    string context.device.rolename      Max: 100
* 
    Логическое пространство имен для группирования экземпляров вычислительных серверов. Для служб, размещенных в Azure, роли PaaS должны по умолчанию иметь имя роли PaaS. Роли IaaS должны по умолчанию иметь имя виртуальной машины  

**screenHeight**

    string context.device.screenresolution.height      Max: 100
* 
    Высота экрана приложения на оборудовании клиента в момент записи телеметрии. Если эти данные не предоставляются явным образом, их источником является преобразование элемента данных screenresolution (разрешение экрана). 

    *Формирование:* извлекается из context.device.screenresolution, если такой параметр имеется.

    *Примеры:*<br/> 360<br/>1280<br/>1920

**screenResolution**

    string context.device.screenresolution      Max: 100
* 
    Разрешение экрана в момент захвата приложением элемента телеметрии. Экран может менять ориентацию на портретную или альбомную в течение одного сеанса. Когда этот атрибут переносится на уровень сеанса, в нем фиксируется первое значение разрешения экрана, которое будет использоваться в течение всего сеанса. 

    *Примеры:*<br/> Разрешение экрана, высота и ширина<br/>360X640<br/>1280X800<br/>1920x1080

**screenWidth**

    string context.device.screenresolution.width      Max: 100
* 
    Ширина экрана приложения на клиентском оборудовании в момент записи телеметрии. Если эти данные не предоставляются явным образом, их источником является преобразование элемента данных screenresolution (разрешение экрана). 

    *Формирование:* извлекается из context.device.screenresolution, если такой параметр имеется.

    *Примеры:*<br/> 640<br/>800<br/>1080


**aiAgentVersion**

    string context.internal.agentversion      Max: 100
* 
    Внутренний элемент данных, который указывает на версию агента пакета SDK, создавшего элемент телеметрии 

**city**

    string context.location.city      Max: 100
* 
    Город, в котором запущено приложение. Он может указываться напрямую в элементе телеметрии. Если он отсутствует, это значение заполняется по IPv4 в элементе телеметрии. Если данные IPv4 не предоставляются, это поле остается пустым. 

    *Примеры:*<br/> Minsk<br/>Haarlem

**clientIpAddress**

    ipv4 context.location.clientip      
* 
    IPv4-адрес клиента в формате xxx.xxx.xxx.xxx.

     Последний октет всегда имеет значение 0 для сохранения конфиденциальности.

    *По умолчанию:* если значение равно NULL, устанавливается в значение IP-адреса HTTP, полученное в конечной точке сбора данных.

    *Примеры*<br/> 186.123.63.0<br/>123.203.131.0

**continent**

    string context.location.continent      Max: 100
* 
    Континент, на котором выполняется сеанс приложения. Он может указываться напрямую в элементе телеметрии. Если он отсутствует, это значение заполняется по IPv4 в элементе телеметрии. Если данные IPv4 не предоставляются, это поле остается пустым. 

    *Примеры:*<br/> Europe<br/>North America

**country**

    string context.location.country      Max: 100
* 
    Страна, в которой выполняется сеанс приложения Он может указываться напрямую в элементе телеметрии. Если он отсутствует, это значение заполняется по IPv4 в элементе телеметрии. Если данные IPv4 не предоставляются, это поле остается пустым. 

    *Примеры:*<br/> Belarus<br/>Netherlands<br/>Germany


**state**

    string context.location.province      Max: 100
* 
    Область/провинция/регион сеанса приложения. Он может указываться напрямую в элементе телеметрии. Если он отсутствует, это значение заполняется по IPv4 в элементе телеметрии. Если данные IPv4 не предоставляются, это поле остается пустым. 

    *Примеры:*<br/> Minsk<br/>Oregon<br/>Central Serbia<br/>Provincia di Oristano

**operationId**

    string context.operation.id      Max: 100
* 
    operation.id представляет собой идентификатор корреляции, который может использоваться в различных элементах телеметрии. Например, идентификатор запроса может быть указан в идентификаторе operation.id для всех связанных элементов телеметрии, позволяя устанавливать связь между различными элементами телеметрии, такими как rdd, exception, events и т. п.  

**operationName**

    string context.operation.name      Max: 100
* 
    Имя операции в формате, удобном для чтения. См. раздел, посвященный идентификатору операции. Это позволяет группировать похожие идентификаторы операций, такие как "Покупка выполнена".   

**operationParentId**

     context.operation.parentid      
* 
    Родительский идентификатор по отношению к operation.id, позволяющий группировать идентификаторы с целью корреляции телеметрии.   

**syntheticSource**

    string context.data.syntheticsource      Max: 100
* 
    Если issynthetic = true, этот элемент данных представляет источник искусственных данных. 

    *По умолчанию:* если значение равно NULL, выполняется проверка агента пользователя на наличие известных источников искусственных данных (webcrawler и т. п.), и на основании этого может быть установлен источник.

**syntheticTransaction**

    boolean context.data.issynthetic      
* 
    Индикатор того, что элемент телеметрии был создан при помощи искусственных тестов в отличие от действий реального пользователя. 

    *По умолчанию:* если значение равно NULL, агент пользователя проверяется по списку известных синтетических агентов. При обнаружении совпадения устанавливается значение true.<br/>Если агент пользователя имеет значение NULL, то устанавливается значение false

**session.Id**

    String context.session.id      Max: 100
* 
    Уникальный идентификатор взаимодействия реальных пользователей с приложением. Такое взаимодействие представляет собой ""сеанс"". Вся телеметрия, создаваемая приложением с одним и тем же ключом iKey, должна содержать этот уникальный идентификатор. <br/><br/>Сеанс определяется как последовательность событий в рамках взаимодействия с одним конкретным пользователем. Период более 30 минут без событий телеметрии означает завершение сеанса.   

    *По умолчанию:* недопустимо для MetricType, BillingType.

    *Примеры*<br/> CFFC8B21-9828-4F56-AD7C-B6B5AC26B133

**accountAcquisitionDate**

    datetime context.user.accountAcquisitionDate  
    
**anonUserId**

    string context.user.anonId      Max: 100
* 
    Уникальный идентификатор, определяющий анонимного пользователя приложения. Когда используется пакет SDK, этот идентификатор создается автоматически и сохраняется в клиенте. Этот идентификатор не позволяет отличить реальных пользователей друг от друга, когда они используют одно и то же устройство под одним именем входа. Он позволяет отличить одного пользователя от другого при использовании различных имен входа и профилей, поддерживаемых в ОС. Он представляет собой наилучший прокси-сервер для уникальных пользователей, когда не используется проверка подлинности. 

    *Пример:*<br/> f23854a1b01c4b1fa79fa2d9a5768526

**anonymousUserAcquisitionDate**

    datetime context.user.anonAcquisitionDate      

**authenticatedUserAcquisitionDate**

    datetime context.user.authAcquisitionDate     
 
**authUserId**

    string context.user.authId      Max: 100
* 
    Уникальный идентификатор, определяющий пользователя, прошедшего проверку подлинности в приложении. Он предоставляется разработчиком. Преимущество использования проверки подлинности пользователей состоит в том, что идентификатор может перемещаться по различным устройствам, сохраняя при этом уникальность. 

**storeRegion**

    string context.user.storeregion      Max: 100
* 
    Область хранения, из которой было предоставлено приложение. 

**userAcountId**

    string context.user.accountId      Max: 100
* 
    Уникальный идентификатор, определяющий учетную запись в приложении. Он предоставляется разработчиком. 

### Настраиваемые метрики

    context.custom.metrics.<metric-name>

      double value
      double count
      double min
      double max
      double stdDev
      double sampledValue
      double sum


## remotedependency

**async**

    boolean remotedependency.async      
* 
    Индикатор того, был ли удаленный вызов зависимости асинхронным 

**commandName**

    string remotedependency.commandname      Max: 2048
* 
    Имя команды SQL удаленной зависимости, если удаленная зависимость является зависимостью SQL 

**dependencyTypeName**

    string remotedependency.dependencytypename      Max: 2048
* 
    Имя типа удаленной зависимости 

**remoteDependencyName**

    string remotedependency.remotedependencyname      Max: 2048
* 
    Имя удаленной зависимости 

    *Формирование:* стандартизация в соответствии с &lt;telemetryType.name&gt;.

**type**

    string remotedependency.remotedependencytype      Max: 100
* 
    Тип удаленной зависимости. Поддерживаются типы: SQL, ресурсы AZURE (хранилище, очередь и т. п.) и HTTP. 

**Успешное завершение**

    boolean remotedependency.success      
* 
    Индикатор того, был ли удаленный вызов зависимости успешным или неудачным 


## запрос

**hasdetaileddata**

    boolean request.hasdetaileddata      
* 
    Индикатор, определяющий, имеются ли связанные элементы телеметрии по идентификатору operation.id 

**httpMethod**

    string request.httpmethod      Max: 100
* 
    Метод HTTP, используемый в запросе.   

**id**

    string request.id      Max: 100
* 
    Уникальный идентификатор запроса, создаваемый пакетом SDK. Этот идентификатор может быть помещен в свойство "идентификатор операции", чтобы соотнести элементы телеметрии, полученные в рамках одного и того же запроса. 

**responseCode**

    long request.responsecode      
* 
    Код ответа на запрос 

**Успешное завершение**

    boolean request.success      
* 
    Индикатор, указывающий, был ли запрос выполнен успешно. Код ответа со значением в пределах второй сотни считается успешным. 

    *По умолчанию:* если значение равно NULL, устанавливается значение true.


## sessionmetric

**anonymousUserDurationSinceLastVisit**

    Long sessionmetric.anonymoususerdurationsincelastvisit      
* 
    Время с момента последнего визита этого идентификатора анонимного пользователя. При первом посещении это значение остается пустым. При каждом последующем посещении здесь отображается время между посещениями подневно. Значение 3 означает, что с момента экземпляра предыдущего сеанса до этого прошло три дня 

**anonymousUserSessionCount**

    Long sessionmetric.anonymoususersessioncount      
* 
    Счетчик посещений для анонимного пользователя. Это последовательный счетчик всех сеансов для этого уникального идентификатора анонимного пользователя. Каждый сеанс с этим идентификатором увеличивает показатель счетчика на единицу. Этот счетчик сбрасывается, если идентификатор пользователя не используется в течение 30-дневного периода, после чего счетчик снова сбрасывается, а при следующем посещении пользователя с этим идентификатором он будет считаться новым пользователем. 

**authenticatedAccountDurationSinceLastVisit**

    Long sessionmetric.authenticatedaccountdurationsincelastvisit      
* 
    Время с момента последнего визита этого идентификатора учетной записи. При первом посещении это значение остается пустым. При каждом последующем посещении здесь отображается время между посещениями подневно. Значение 3 означает, что с момента экземпляра предыдущего сеанса до этого прошло три дня 

**authenticatedAccountSessionCount**

    Long sessionmetric.authenticatedaccountsessioncount      
* 
    Счетчик посещений для идентификатора пользователя, прошедшего проверку подлинности. Он представляет собой счетчик всех сеансов для этого уникального идентификатора учетной записи. Каждый сеанс с этим идентификатором увеличивает показатель счетчика на единицу. Этот счетчик сбрасывается, если идентификатор пользователя не используется в течение 30-дневного периода, после чего счетчик снова сбрасывается, а при следующем посещении пользователя с этим идентификатором он будет считаться новым пользователем. 

**authenticatedUserDurationSinceLastVisit**

    Long sessionmetric.authenticateduserdurationsincelastvisit      
* 
    Время с момента последнего посещения при помощи этого идентификатора пользователя, прошедшего проверку подлинности. При первом посещении это значение остается пустым. При каждом последующем посещении здесь отображается время между посещениями подневно. Значение 3 означает, что с момента экземпляра предыдущего сеанса до этого прошло три дня 

**authenticatedUserSessionCount**

    Long sessionmetric.authenticatedusersessioncount      
* 
    Счетчик посещений для этого идентификатора пользователя, прошедшего проверку подлинности. Это последовательный счетчик всех сеансов для этого уникального идентификатора пользователя, прошедшего проверку подлинности. Каждый сеанс с этим идентификатором увеличивает показатель счетчика на единицу. Этот счетчик сбрасывается, если идентификатор пользователя не используется в течение 30-дневного периода, после чего счетчик снова сбрасывается, а при следующем посещении пользователя с этим идентификатором он будет считаться новым пользователем. 

**crashCount**

    Long sessionmetric.crashcount      
* 
    Количество сбоев, произошедших во время этого экземпляра сеанса. 

**длительность**

    timespan sessionmetric.duration      
* 
    Продолжительность экземпляра сеанса 

**entryEvent**

    string sessionmetric.entryevent      Max: 200
* 
    Первое событие сеанса. Это значение получается из имени события event.name и доступно в виде сегментации/агрегата для метрики sessionMetric 

    *Формирование:* из event.name.

**entryUrl**

    string sessionmetric.entryurl      Max: 2048
* 
    Первый URL-адрес сеанса. Это значение получается из urlData.base и доступно в виде сегментации/агрегата метрик sessionMetric 

    *Формирование:* из &lt;telemetryType&gt;.Url.

**eventCount**

    Long sessionmetric.eventcount      
* 
    Количество событий, произошедших в течение текущего экземпляра сеанса 

**exceptionCount**

    Long sessionmetric.exceptioncount      
* 
    Количество исключений, произошедших в течение текущего экземпляра сеанса 

**exitEvent**

    string sessionmetric.exitevent      Max: 200
* 
    Последнее событие сеанса. Это значение получается из имени события event.name и доступно в виде сегментации/агрегата для метрики sessionMetric 

    *Получение:* из event.name

**exitUrl**

    string sessionmetric.exiturl      Max: 2048
* 
    Последний URL-адрес сеанса. Это значение получается из urlData.base и доступно в виде сегментации/агрегата метрик sessionMetric 

    *Формирование:* из &lt;telemetryType&gt;.Url.

**pageBounceCount**

    boolean sessionmetric.pagebouncecount      
* 
    Количество сеансов возврата, которые представляет этом элемент телеметрии sessionMetric. Сеанс возврата представляет собой сеанс, создаваемый по элементу телеметрии для единичного просмотра. 

    *Формирование:* если sessionMetric.viewCount + sessionMetric.requestCount = 1, то 1, иначе 0.

**pageCount**

    Long sessionmetric.pagecount      
* 
    Количество просмотров, выполненных в ходе этого экземпляра сеанса 

**requestCount**

    Long sessionmetric.requestcount      
* 
    Количество запросов, выполненных в ходе этого экземпляра сеанса 

**sessionCount**

    Long sessionmetric.sessioncount      
* 
    Количество сеансов, которые представляет этот экземпляр телеметрии sessionMetric 


## trace

**context**

    string trace.context      Max: 100
* 
    Контекст capp в момент трассировки/исключения 

**exception**

    string trace.exception      Max: 10240
* 
    Исключение, связанное с элементом трассировки телеметрии 

**excerpt**

    string trace.excerpt      Max: 100
* 
    Краткое текстовое сообщение элемента трассировки телеметрии 

**formatMessage**

    string trace.formatmessage      Max: 100
* 
    Формат сообщения элемента трассировки телеметрии 

**formatProvider**

    string trace.formatprovider      Max: 100
* 
    Поставщик формата для текущего элемента трассировки телеметрии 

**hasStackTrace**

    boolean trace.hasstacktrace      
* 
    Индикатор того, включает ли элемент трассировки телеметрии трассировку стека 

**level**

    string trace.level      Max: 100
* 
    Уровень сообщения трассировки 

**loggerName**

    string trace.loggername      Max: 100
* 
    Имя средства ведения журнала трассировки 

**loggerShortName**

    string trace.loggershortname      Max: 100
* 
    Краткое имя средства ведения журнала 

**message**

    string trace.message      Max: 10240
* 
    Полное текстовое сообщение элемента трассировки телеметрии 

**messageId**

    string trace.messageId      Max: 100
* 
    Уникальный идентификатор элемента трассировки телеметрии 

**parameters**

    string trace.parameters      Max: 100
* 
    Здесь указываются параметры, предоставляемые средству записи трассировки для элемента трассировки телеметрии 

**processId**

    string trace.processId      Max: 100
* 
    Идентификатор процесса приложения в момент записи элемента телеметрии 

**sourceType**

    string trace.sourceType      Max: 100
* 
    Тип источника элемента трассировки телеметрии 

**sqquenceId**

    string trace.sequenceid      Max: 100
* 
    Идентификатор последовательности, который может использоваться поставщиком трассировки для записи последовательности элементов трассировки телеметрии 

**stacktrace**

    string trace.stacktrace      Max: 100
* 
    Трассировка стека извлекается для этого элемента трассировки телеметрии 

**threadId**

    string trace.threadId      Max: 100
* 
    Идентификатор threadid приложения с момента записи элемента телеметрии 

**userStackframe**

    string trace.userstackframe      Max: 100
* 
    Кадр стека пользователя для элемента трассировки телеметрии 

**userStackframNum**

    string trace.userstackframenum      Max: 100
* 
    Номер кадра стека пользователя для элемента трассировки телеметрии 


## представление

**referrerDataUrl**

    string view.referralurl      Max: 2048
* 
    Ссылающийся URL-адрес представления страницы. Представляет собой полный URL-адрес и поддерживается для полнотекстового поиска и экспорта. Это поле поддерживает большие значения количества элементов и представляет собой атрибут. Оно разбивается на набор элементов данных referralData, которые могут использоваться для агрегирования средствами обозревателя метрик. 

**referrerData.base**

    string view.referrerdata.base      Max: 2048
* 
    Часть ссылающего URL-адреса, за исключением узла и элементов запроса. Это корневой URI. Это значение может использоваться для сегментации и агрегации. 

    *Получение:* см. приложение, посвященное преобразованию URL-адреса

**referrerData.hashTag**

    string view.referrerdata.hashtag      Max: 100
* 
    Текст хэштега ссылающегося URL-адреса 

    *Формирование:* см. приложение, посвященное преобразованию URL-адресов.

**referrerData.host**

    string view.referrerdata.host      Max: 200
* 
    Узел ссылающегося URL-адреса. Если URL-адрес представляет собой локальный URI, то это значение отображается пустым 

    *Формирование:* см. приложение, посвященное преобразованию URL-адресов.

**referrerData.port**

    string view.referrerdata.port      Max: 100
* 
    Порт ссылающегося URL-адреса, если он указан в полном URL-адресе. В противном случае это свойство пустое. 

    *Формирование:* см. приложение, посвященное преобразованию URL-адресов.

**referrerData.protocol**

    string view.referrerdata.protocol      Max: 100
* 
    Протокол (HTTP, FTP и т. п.) ссылающегося URL-адреса 

    *Формирование:* см. приложение, посвященное преобразованию URL-адресов.

    *Примеры:*<br/> http<br/>https

**referrerData.queryParameters.parameter**

    string view.referrerdata.queryparameters.parameter      Max: 100
* 
    Массив имен параметров запроса ссылающегося URL-адреса 

    *Формирование:* см. приложение, посвященное преобразованию URL-адресов.

**referrerData.queryParameters.value**

    string view.referrerdata.queryparameters.value      Max: 100
* 
    Массив значений параметра запроса, полученных в результате анализа из referringData URL-адреса. 

    *Формирование:* см. приложение, посвященное преобразованию URL-адресов.



## См. также

* [Application Insights](app-insights-overview.md) 
* [Непрерывный экспорт](app-insights-export-telemetry.md)
* [Примеры кода](app-insights-export-telemetry.md#code-samples)

<!---HONumber=AcomDC_0302_2016-->