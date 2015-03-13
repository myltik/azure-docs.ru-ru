<properties 
	pageTitle="Использование серверной части .NET мобильных служб. Мобильные службы Azure" 
	description="Подробно изучите модель программирования серверной части .NET для мобильных служб Azure, включая способы работы с данными таблиц, интерфейсы API, аутентификацию и запланированные задания" 
	services="" 
	documentationCenter="windows" 
	authors="mattchenderson" 
	manager="dwrede" 
	editor="mollybos"/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-multiple" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="11/11/2014" 
	ms.author="mahender"/>
# Использование серверной части .NET мобильных служб

<div class="dev-center-tutorial-subselector"><a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-how-to-use/" title=".NET backend" class="current">Серверная часть .NET</a> | <a href="/ru-ru/documentation/articles/mobile-services-how-to-use-server-scripts/"  title="JavaScript backend">Серверная часть JavaScript</a></div>

Эта статья содержит подробные сведения о серверной части .NET в мобильных службах Azure и примеры работы с ней. Этот раздел содержит следующие параграфы:

+ [Введение](#intro)
+ [Операции с таблицами](#table-scripts)

##<a name="intro"></a>Введение

Серверная часть мобильных служб .NET позволяет создавать разнообразную бизнес-логику на стороне сервера базы данных с использованием [веб-API ASP.NET](http://www.asp.net/web-api) и любимого языка на платформе .NET. Мобильные службы предоставляют небольшую поверхность модели программирования в виде [пакетов NuGet](http://www.nuget.org/packages?q=%22mobile+services+.net+backend%22), которые обеспечивают непрерывный доступ к вашей службе из кроссплатформенных клиентских пакетов SDK мобильных служб для Windows, Android, iOS и т. д. Эти интерфейсы API также позволяют максимально упростить процесс добавления поддержки проверки подлинности и push-уведомлений. Однако модель программирования в основном основана на веб-API, и разработчики, знакомые с данным интерфейсом, должны чувствовать себя очень уверенно. 

##<a name="table-scripts"></a>Операции с таблицами

Серверная часть мобильных служб .NET предоставляет универсальную абстракцию "таблица", представляющую основанный на создании, чтении, обновлении и удалении (CRUD) интерфейс HTTP API для хранилища баз данных. Данная абстракция отделяет задачи хранения данных от вашей бизнес-логики и позволяет вашей мобильной службе предоставлять различные хранилища данных в согласованном телеграфном формате на основе JSON, который легко понимают кроссплатформенные клиентские пакеты SDK мобильных служб. 

В основе данной модели программирования лежит класс [**TableController<T>**](http://msdn.microsoft.com/library/dn643359.aspx), представляющий собой просто обычный [**ApiController**](http://msdn.microsoft.com/library/system.web.http.apicontroller.aspx) интерфейса веб-API, который настраивается для шаблона доступа к данным CRUD. Класс **TableController** может использовать различные хранилища данных, включая SQL (через [Entity Framework](http://msdn.microsoft.com/data/ef.aspx)), [хранилище таблиц Azure](http://azure.microsoft.com/documentation/services/storage/), [MongoDB](http://www.mongodb.org) или ваше собственное хранилище).

<!--HONumber=42-->
