<properties linkid="develop-mobile-tutorials-dotnet-backend-get-started-with-data-javascript-vs2013" urlDisplayName="Get Started with Data" pageTitle="Get started with data (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to get started using Mobile Services to leverage data in your Windows Store app." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with data in Mobile Services" authors="wesmc" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="09/23/2014" ms.author="wesmc"></tags>

# Приступая к работе с данными в мобильных службах

[WACOM.INCLUDE [mobile-services-selector-get-started-data][mobile-services-selector-get-started-data]]

В этом разделе показано, как использовать мобильные службы Azure в качестве серверного источника данных для приложения Магазина Windows. В этом учебнике вам предстоит загрузить проект Visual Studio 2013 для приложения, хранящего данные в памяти, создать новую мобильную службу, интегрировать мобильную службу с приложением и просмотреть изменения, внесенные в данные во время работы приложения.

В этом учебнике речь идет о создании серверной мобильной службы Microsoft .NET. Серверная часть .NET позволяет использовать языки .NET и Visual Studio для серверной бизнес-логики в мобильной службе, и вы можете запускать и отлаживать мобильную службу на локальном компьютере. Сведения о создании мобильной службы, которая позволяет реализовать серверную бизнес-логику в JavaScript, см. в подразделе "Серверная версия JavaScript" этого раздела.

> [WACOM.NOTE]В этом разделе показано, как использовать средства Visual Studio Professional 2013 с обновлением 3 для подключения новой мобильной службы к универсальному приложению Windows. Если вы не можете выполнить обновление до Visual Studio Professional 2013 с обновлением 3 или предпочитаете вручную добавить проект мобильной службы в приложение Магазина Windows, см. [эту версию][эту версию] раздела.

В этом учебнике рассматриваются следующие основные действия:

1. [Загрузка проекта приложения для Магазина Windows][Загрузка проекта приложения для Магазина Windows]
2. [Создание мобильной службы в Visual Studio][Создание мобильной службы в Visual Studio]
3. [Локальное тестирование проекта мобильной службы][Локальное тестирование проекта мобильной службы]
4. [Обновление приложения для использования мобильной службы][Обновление приложения для использования мобильной службы]
5. [Публикация мобильной службы в Azure][Публикация мобильной службы в Azure]
6. [Тестирование приложения со службой, размещенной в Azure][Тестирование приложения со службой, размещенной в Azure]
7. [Просмотр данных, хранящихся в базе данных SQL][Просмотр данных, хранящихся в базе данных SQL]

Для работы с этим учебником требуется:

* Активная учетная запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure][Бесплатная пробная версия Azure].
* [Visual Studio Professional 2013][Visual Studio Professional 2013]. Доступна бесплатная пробная версия.

## <a name="download-app"></a> Загрузка проекта GetStartedWithData

[WACOM.INCLUDE [mobile-services-windows-universal-javascript-download-project][mobile-services-windows-universal-javascript-download-project]]

## <a name="create-service"></a> Создание мобильной службы в Visual Studio

[WACOM.INCLUDE [mobile-services-dotnet-backend-create-new-service-vs2013][mobile-services-dotnet-backend-create-new-service-vs2013]]

<ol start="8"><li><p>В обозревателе решений перейдите во вложенную папку <strong>services\mobileService\scripts</strong>, откройте файл скрипта service.js и обратите внимание на новую глобальную переменную, которая выглядит, как в следующем примере:</p> 

		<pre><code>var todolistClient = new WindowsAzure.MobileServiceClient(
                "https://todolist.azure-mobile.net/",
		        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX");</code></pre>

	<p>Этот код предоставляет доступ к новой мобильной службе в вашем приложении с помощью глобальной переменной. Клиент создается с указанием URI и ключа приложения новой мобильной службы. Так как ссылка на этот скрипт была добавлена в файл default.html, эта переменная доступна для всех файлов скриптов, на которые также указывает ссылка на этой странице.</p>
</li>
<li><p>Откройте файл проекта default.html, найдите ссылку на новый файл скрипта service.js и убедитесь в том, что путь ссылки выглядит следующим образом:</p>
<pre><code>&lt;script src="/services/mobileServices/scripts/todolist.js"&gt;</script></code></pre>
<p>В настоящее время в Visual Studio есть ошибка, из-за которой создается путь с неправильным именем папки.</p></li>
<li><p>Щелкните проект приложения Windows Phone правой кнопкой мыши, выберите пункт <strong>Добавить</strong>, щелкните <strong>Подключенная служба...</strong>, выберите только что созданную мобильную службу и нажмите кнопку <strong>ОК</strong>. </p>
<p>Тот же новый файл с кодом добавляется в проект приложения Магазина Windows Phone. Не забудьте также исправить путь ссылки, добавленный в файл default.html.</p></li>
</ol>

Теперь приложение Магазина Windows и приложение Магазина Windows Phone подключены к новой мобильной службе. Следующий шаг — тестирование нового проекта мобильной службы.

## <a name="test-the-service-locally"></a> Локальное тестирование проекта мобильной службы

[WACOM.INCLUDE [mobile-services-dotnet-backend-test-local-service-api-documentation][mobile-services-dotnet-backend-test-local-service-api-documentation]]

## <a name="update-app"></a> Обновление приложения для использования мобильной службы

В этом разделе вы обновите универсальное приложение Windows, чтобы использовать мобильную службу в качестве серверной службы для приложения. Изменения необходимо внести только в файл проекта default.js в папке проекта GetStartedWithData.Shared.

[WACOM.INCLUDE [mobile-services-windows-javascript-update-data-app][mobile-services-windows-javascript-update-data-app]]

## <a name="publish-mobile-service"></a> Публикация мобильной службы в Azure

[WACOM.INCLUDE [mobile-services-dotnet-backend-publish-service][mobile-services-dotnet-backend-publish-service]]

## <a name="test-azure-hosted"></a> Тестирование мобильной службы, размещенной в Azure

Теперь мы можем протестировать обе версии универсального приложения Windows с мобильной службой, размещенной в Azure.

[WACOM.INCLUDE [mobile-services-windows-universal-test-app][mobile-services-windows-universal-test-app]]

## <a name="view-stored-data"></a> Просмотр данных, хранящихся в базе данных SQL

[WACOM.INCLUDE [mobile-services-dotnet-backend-view-sql-data][mobile-services-dotnet-backend-view-sql-data]]

Это заключительный шаг учебника **Приступая к работе с данными**.

## <a name="next-steps"> </a> Дальнейшие действия

В этом учебнике содержатся базовые сведения о том, как включить в проекте универсального приложения Windows возможности работы с данными в мобильных службах. Далее рассмотрите выполнение одного из следующих учебников, которые основаны на приложении GetStartedWithData, созданном в этом учебнике:

* [Проверка и изменение данных с помощью скриптов][Проверка и изменение данных с помощью скриптов]
  <br/>Дополнительные сведения об использовании серверных скриптов в мобильных службах для проверки и изменения данных, отправляемых из приложения.

* [Уточнение запросов посредством разбиения по страницам][Уточнение запросов посредством разбиения по страницам]
  <br/>Сведения об использовании разбиения по страницам в запросах для управления объемом данных, обрабатываемых в одном запросе.

После завершения серии учебников по работе с данными попробуйте один из следующих учебников:

* [Приступая к работе с проверкой подлинности][Приступая к работе с проверкой подлинности]
  <br/>Дополнительные сведения о проверке подлинности пользователей приложения.

* [Приступая к работе с push-уведомлениями][Приступая к работе с push-уведомлениями]
  <br/>Сведения об отправке в приложение простейших push-уведомлений.

* [Справочник принципов использования мобильных служб .NET][Справочник принципов использования мобильных служб .NET]
  <br/>Дополнительные сведения об использовании мобильных служб с HTML и JavaScript.



<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

[mobile-services-selector-get-started-data]: ../includes/mobile-services-selector-get-started-data.md
[эту версию]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data
[Загрузка проекта приложения для Магазина Windows]: #download-app
[Создание мобильной службы в Visual Studio]: #create-service
[Локальное тестирование проекта мобильной службы]: #test-the-service-locally
[Обновление приложения для использования мобильной службы]: #update-app
[Публикация мобильной службы в Azure]: #publish-mobile-service
[Тестирование приложения со службой, размещенной в Azure]: #test-azure-hosted
[Просмотр данных, хранящихся в базе данных SQL]: #view-stored-data
[Бесплатная пробная версия Azure]: http://azure.microsoft.com/en-us/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-windows-universal-javascript-get-started-data%2F
[Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=257546
[mobile-services-windows-universal-javascript-download-project]: ../includes/mobile-services-windows-universal-javascript-download-project.md
[mobile-services-dotnet-backend-create-new-service-vs2013]: ../includes/mobile-services-dotnet-backend-create-new-service-vs2013.md
[mobile-services-dotnet-backend-test-local-service-api-documentation]: ../includes/mobile-services-dotnet-backend-test-local-service-api-documentation.md
[mobile-services-windows-javascript-update-data-app]: ../includes/mobile-services-windows-javascript-update-data-app.md
[mobile-services-dotnet-backend-publish-service]: ../includes/mobile-services-dotnet-backend-publish-service.md
[mobile-services-windows-universal-test-app]: ../includes/mobile-services-windows-universal-test-app.md
[mobile-services-dotnet-backend-view-sql-data]: ../includes/mobile-services-dotnet-backend-view-sql-data.md
[Проверка и изменение данных с помощью скриптов]: /en-us/develop/mobile/tutorials/validate-modify-and-augment-data-js
[Уточнение запросов посредством разбиения по страницам]: /en-us/develop/mobile/tutorials/add-paging-to-data-js
[Приступая к работе с проверкой подлинности]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users/
[Приступая к работе с push-уведомлениями]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push/
[Справочник принципов использования мобильных служб .NET]: /ru-ru/documentation/articles/mobile-services-html-how-to-use-client-library/
