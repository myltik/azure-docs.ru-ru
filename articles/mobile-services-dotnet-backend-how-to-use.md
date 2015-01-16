<properties urlDisplayName="Use the Mobile Services .NET Backend" pageTitle="Использование серверной части .NET мобильных служб. Мобильные службы Azure" metaKeywords="" description="Подробно изучите модель программирования серверной части .NET для мобильных служб Azure, включая способы работы с данными таблиц, интерфейсы API, аутентификацию и запланированные задания" metaCanonical="" services="" documentationCenter="Mobile" title="Use the Mobile Services .NET Backend" authors="mahender" solutions="" manager="dwrede" editor="mollybos" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="11/11/2014" ms.author="mahender" />
# Использование серверной части .NET мобильных служб

<div class="dev-center-tutorial-subselector"><a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-how-to-use/" title=".NET backend" class="current">Серверная часть .NET</a> | <a href="/ru-ru/documentation/articles/mobile-services-how-to-use-server-scripts/"  title="JavaScript backend">Серверная часть JavaScript</a></div>

В этой статье приведена подробная информация о работе с серверной частью .NET в мобильных службах Azure и продемонстрированы примеры такой работы. Этот раздел состоит из следующих подразделов:

+ [Введение](#intro)
+ [Операции с таблицами](#table-scripts)

##<a name="intro"></a>Введение

Серверная часть мобильных служб .NET позволяет создавать разнообразную бизнес-логику на стороне сервера базы данных с использованием [веб-API ASP.NET](http://www.asp.net/web-api) и любимого языка на платформе .NET. Мобильные службы предоставляют небольшую поверхность модели программирования в виде [пакетов NuGet](http://www.nuget.org/packages?q=%22mobile+services+.net+backend%22), которые обеспечивают непрерывный доступ к вашей службе из кроссплатформенных клиентских пакетов SDK мобильных служб для Windows, Android, iOS и т. д. Эти интерфейсы API также позволяют максимально упростить процесс добавления поддержки проверки подлинности и push-уведомлений. Но большая часть программной модели основана на веб-API, что поможет в освоении тем разработчикам, которые с ним знакомы. 

##<a name="table-scripts"></a>Операции с таблицами

Серверная часть мобильных служб .NET предоставляет универсальную абстракцию "таблица", представляющую основанный на создании, чтении, обновлении и удалении интерфейс HTTP API для хранилища баз данных. Эта абстракция отделяет логику хранения данных от бизнес-логики и позволяет мобильной службе работать с разными хранилищами в едином каркасном формате на основе JSON, который хорошо понимают кроссплатформенные клиентские пакеты SDK для мобильных служб. 

В основе этой программной модели лежит класс [**TableController<T>**](http://msdn.microsoft.com/library/dn643359.aspx), обычный [**ApiController**](http://msdn.microsoft.com/library/system.web.http.apicontroller.aspx) интерфейса веб-API, настроенный для шаблона доступа к данным на основе создания, чтения, обновления и удаления. **TableController** может использовать различные хранилища данных, включая SQL (через [Entity Framework](http://msdn.microsoft.com/data/ef.aspx)), [табличное хранилище Azure](http://azure.microsoft.com/documentation/services/storage/), [MongoDB](http://www.mongodb.org) или ваше пользовательское хранилище.
