<properties linkid="mobile-services-dotnet-backend-how-to-use" urlDisplayName="Use the Mobile Services .NET Backend" pageTitle="Use the Mobile Services .NET Backend - Azure Mobile Services" metaKeywords="" description="Learn the details of the .NET Backend programming model for Azure Mobile Services, including how to work with table data, APIs, authentication, and scheduled jobs" metaCanonical="" services="" documentationCenter="Mobile" title="Use the Mobile Services .NET Backend" authors="yavorg" solutions="" manager="" editor="mollybos" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="yavorg" />

# Использование серверной части .NET мобильных служб

<div class="dev-center-tutorial-subselector"><a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-how-to-use/" title="Серверная часть .NET" class="current">Серверная часть .NET</a> | <a href="/ru-ru/documentation/articles/mobile-services-how-to-use-server-scripts/"  title="Серверная часть JavaScript">Серверная часть JavaScript</a></div>

Эта статья содержит подробные сведения о серверной части .NET в мобильных службах Azure и примеры работы с ней. Этот раздел содержит следующие параграфы:

-   [Введение][Введение]
-   [Операции с таблицами][Операции с таблицами]

## <a name="intro"></a> Введение

Серверная часть .NET мобильных служб позволяет создавать многофункциональную серверную бизнес-логику с помощью интерфейса [Веб-API ASP.NET][Веб-API ASP.NET] и предпочтительного для вас языка .NET. Мобильные службы предоставляют небольшую поверхность модели программирования в виде [пакетов NuGet][пакетов NuGet], которые обеспечивают непрерывный доступ к вашей службе из кроссплатформенных клиентских пакетов SDK мобильных служб для Windows, Android, iOS и т. д. Эти интерфейсы API также позволяют максимально упростить процесс добавления поддержки проверки подлинности и push-уведомлений. Однако модель программирования в основном основана на веб-API, и разработчики, знакомые с данным интерфейсом, должны чувствовать себя очень уверенно.

## <a name="table-scripts"></a>Операции с таблицами

Серверная часть .NET мобильных служб предоставляет универсальную абстрактную «таблицу», которая представляет интерфейс API HTTP на основе CRUD для хранения базы данных. Данная абстракция отделяет задачи хранения данных от вашей бизнес-логики и позволяет вашей мобильной службе предоставлять различные хранилища данных в согласованном телеграфном формате на основе JSON, который легко понимают кроссплатформенные клиентские пакеты SDK мобильных служб.

В основе данной модели программирования лежит класс [**TableController<t>**][**TableController<t>**], представляющий собой просто обычный [**ApiController**][**ApiController**] интерфейса веб-API, который настраивается для шаблона доступа к данным CRUD. Класс **TableController** может использовать различные хранилища данных, включая SQL (через [Entity Framework][Entity Framework]), [хранилище таблиц Azure][хранилище таблиц Azure], [MongoDB][MongoDB] или ваше собственное хранилище).

  [Введение]: #intro
  [Операции с таблицами]: #table-scripts
  [Веб-API ASP.NET]: http://www.asp.net/web-api
  [пакетов NuGet]: http://www.nuget.org/packages?q=%22mobile+services+.net+backend%22
  [**TableController<t>**]: http://msdn.microsoft.com/library/dn643359.aspx
  [**ApiController**]: http://msdn.microsoft.com/library/system.web.http.apicontroller.aspx
  [Entity Framework]: http://msdn.microsoft.com/data/ef.aspx
  [хранилище таблиц Azure]: http://azure.microsoft.com/documentation/services/storage/
  [MongoDB]: http://www.mongodb.org
