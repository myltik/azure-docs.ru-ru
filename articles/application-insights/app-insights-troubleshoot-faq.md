<properties 
	pageTitle="Устранение неполадок и ответы на вопросы по Application Insights" 
	description="Что-то в Visual Studio Application Insights непонятно или не работает? Попробуйте здесь." 
	services="application-insights" 
    documentationCenter=".net"
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/09/2015" 
	ms.author="awills"/>
 
# Устранение неполадок и ответы на вопросы по Application Insights для ASP.NET

## Можно ли использовать Application Insights с...?

См. статью о [платформах][platforms].

## Предоставляется ли бесплатно?

* Да, если выбрать [ценовую категорию](app-insights-pricing.md) Free. Вы получаете доступ к большинству функций и значительную квоту данных. 
* Чтобы зарегистрироваться в Microsoft Azure, вы должны предоставить данные кредитной карты, но плата будет взиматься только за пользование платными службами Azure или же в случае обновления до платной категории.
* Если приложение отправляет больше данных, чем месячная квота для категории Free, запись в журнал прекращается. В этом случае вы можете начать платить за пользование службой или подождать окончания месяца, пока квота не будет сброшена.
* Квота не распространяется на основные данные об использовании и сеансах.
* Предлагается также 30-дневная пробная версия: в течение этого времени вы будете бесплатно получать доступ к функциям ценовой категории Premium.
* Каждый ресурс приложения имеет отдельную квоту, и вы устанавливаете его ценовую категорию независимо от всех других.

#### Что я получу в случае оплаты?

* Расширенную [ежемесячную квоту данных](http://azure.microsoft.com/pricing/details/application-insights/).
* Есть возможность оплатить «превышение» для продолжения сбора данных сверх месячной квоты. Если данные превышают квоту, плата будет сниматься за каждый МБ.
* [Непрерывный экспорт](app-insights-export-telemetry.md).

## Добавление пакета SDK

#### <a name="q01"></a>Нигде нет команды на добавление Application Insights в мой проект в Visual Studio

+ Убедитесь в том, что установлена версия [Visual Studio 2013 с обновлением 3 или более поздним](http://go.microsoft.com/fwlink/?LinkId=397827). В состав этой версии входят предварительно установленные Средства Application Insights.
+ Хотя эти средства не поддерживают все типы приложений, вы, скорее всего, сможете добавить пакет SDK для Application Insights в проект вручную. Используйте [эту процедуру][windows]. 


#### <a name="q02"></a>Мой новый веб-проект был создан, но добавить Application Insights не удалось.

Это может произойти с следующих случаях.

* Сбой связи с порталом Application Insights.
* В вашей учетной записи возникли неполадки.
* [Доступ только на чтение к подписке или группе, в которой вы пытаетесь создать новый ресурс](app-insights-resources-roles-access-control.md).

Способ устранения.

+ Убедитесь, что вы предоставили правильные учетные данные для учетной записи Azure. В некоторых предыдущих версиях этих средств учетные данные Microsoft Azure, которые вы видите в диалоговом окне «Новый проект», могут отличаться от учетных данных Visual Studio Online, которые отображаются в правом верхнем углу окна Visual Studio.
+ В браузере убедитесь, что у вас есть доступ к [порталу Azure](https://portal.azure.com). Откройте параметры и проверьте, нет ли каких-либо ограничений.
+ [Добавьте Application Insights в существующий проект][start]. В обозревателе решений щелкните проект правой кнопкой мыши и выберите команду «Добавить Application Insights».
+ Если устранить проблему не удалось, добавьте ресурс на портал [вручную](app-insights-start-monitoring-app-health-usage.md), а затем добавьте в проект пакет SDK. 

#### <a name="emptykey"></a>Появляется сообщение об ошибке «ключ инструментирования не может быть пустым»

Вероятно, произошла ошибка при установке Application Insights или же адаптера ведения журнала.

В обозревателе решений щелкните правой кнопкой мыши `ApplicationInsights.config` и выберите **Обновить Application Insights**. Появится диалоговое окно с предложением войти в Azure и создать новый ресурс Application Insights или повторно использовать уже существующий.


#### <a name="q14"></a>Что Application Insights изменяет в моем проекте?

Подробности зависят от типа проекта. Для веб-приложения:


+ Добавляет в проект следующие файлы:

 + ApplicationInsights.config.
 + ai.js


+ Устанавливает следующие пакеты NuGet:

 -  *Application Insights API* — основной API

 -  *API Application Insights для веб-приложений* — используется для отправки данных телеметрии со стороны сервера

 -  *API Application Insights для приложений JavaScript* — используется для отправки данных телеметрии со стороны клиента

    Пакет включает эти сборки:

 - Microsoft.ApplicationInsights

 - Microsoft.ApplicationInsights.Platform

+ Вставляет элементы в:

 - Web.config

 - packages.config

+ (Только для новых проектов. Если нужно [добавить Application Insights в существующий проект][start], придется делать это вручную.) Вставляет фрагменты кода в код клиента и сервера для их инициализации с идентификатором ресурса Application Insights. Например, в приложении MVC код вставляется в файл главной страницы Views/Shared/\_Layout.cshtml.

####<a name="NuGetBuild"></a>На моем сервере сборки отображается сообщение «Отсутствуют пакеты NuGet», хотя на компьютере разработчика сборка выполняется без ошибок.

См. раздел [Восстановление пакетов NuGet](http://docs.nuget.org/Consume/Package-Restore) и [Автоматическое восстановление пакетов](http://docs.nuget.org/Consume/package-restore/migrating-to-automatic-package-restore).

####<a name="FailUpdate"></a>При попытке создания сборки после обновления пакетов NuGet до версии 0.17 или более поздней отображается сообщение «Проект ссылается на пакеты NuGet, отсутствующие на компьютере».

При появлении такой ошибки после обновления пакетов NuGet до версии 0.17 или более поздней необходимо изменить PROJ-файл и удалить неиспользуемые целевые объекты BCL.

Для этого:

1. Щелкните правой кнопкой мыши проект в обозревателе решений и выберите «Выгрузить проект».
2. Щелкните правой кнопкой мыши проект еще раз и выберите «Редактировать» для *yourProject.csproj* 
3. Перейдите в нижнюю часть файла проекта и удалите целевые объекты BCL, подобные следующим: ``` <Import Project="..\packages\Microsoft.Bcl.Build.1.0.14\tools\Microsoft.Bcl.Build.targets" Condition="Exists('..\packages\Microsoft.Bcl.Build.1.0.14\tools\Microsoft.Bcl.Build.targets')" />
	  
	  <Target Name="EnsureBclBuildImported" BeforeTargets="BeforeBuild" Condition="'$(BclBuildImported)' == ''">
	  
	    <Error Condition="!Exists('..\packages\Microsoft.Bcl.Build.1.0.14\tools\Microsoft.Bcl.Build.targets')" Text="This project references NuGet package(s) that are missing on this computer. Enable NuGet Package Restore to download them.  For more information, see http://go.microsoft.com/fwlink/?LinkID=317567." HelpKeyword="BCLBUILD2001" />
	    
	    <Error Condition="Exists('..\packages\Microsoft.Bcl.Build.1.0.14\tools\Microsoft.Bcl.Build.targets')" Text="The build restored NuGet packages. Build the project again to include these packages in the build. For more information, see http://go.microsoft.com/fwlink/?LinkID=317568." HelpKeyword="BCLBUILD2002" />
	    
	</Target> ```
4. Сохраните файл.
5. Щелкните правой кнопкой мыши проект и выберите «Перезагрузить» для *yourProject.csproj*.

## Как обновить предыдущие версии пакета SDK?

Информацию для пакета SDK, соответствующего типу вашего приложения, см. в разделе [Заметки о выпуске](app-insights-release-notes.md).


## Нет данных

#### <a name="q03"></a>Мне удалось успешно добавить Application Insights и запустить приложение, но данные не появились на портале.

+ На странице «Обзор» щелкните плитку «Поиск», чтобы открыть колонку «Поиск по журналу диагностики». Сначала данные отображаются здесь.
+ Нажмите кнопку «Обновить». Колонка периодически обновляется, однако ее можно обновить и вручную. При значительных значениях временного диапазона интервал обновления увеличивается.
+ На начальном экране Microsoft Azure посмотрите на карту состояния службы. Если есть какие-либо предупреждения, дождитесь возвращения всех модулей в состояние ОК, затем закройте и заново откройте модуль приложения Application Insights.
+ Кроме того, просмотрите [наш блог о состояниях](http://blogs.msdn.com/b/applicationinsights-status/).

#### После публикации приложения на сервере отсутствуют данные

+ В брандмауэре вам может потребоваться открыть TCP-порты 80 и 443 для исходящего трафика, идущего на сайты dc.services.visualstudio.com и f5.services.visualstudio.com.
+ Если для отправки из корпоративной сети необходимо использовать прокси-сервер, установите значение [defaultProxy](https://msdn.microsoft.com/library/aa903360.aspx) в файле Web.config.
+ Для Windows Server 2008. Убедитесь в том, что установлены следующие обновления: [KB2468871](https://support.microsoft.com/kb/2468871), [KB2533523](https://support.microsoft.com/kb/2533523), [KB2600217](https://support.microsoft.com/kb/2600217).

#### <a name="q04"></a>В разделе «Аналитика использования» моего веб-сайта нет данных.

+ Данные поступают из сценариев на веб-страницах. Если вы добавили Application Insights в существующий веб-проект, [нужно добавить сценарии вручную][start].
+ Убедитесь, что Internet Explorer не отображает ваш сайт в режиме совместимости.
+ Воспользуйтесь функцией отладки в обозревателе (в некоторых обозревателях нажмите клавишу F12 и выберите «Сеть»), чтобы убедиться, что данные отправляются по адресу dc.services.visualstudio.com.

#### <a name="q08"></a>Можно ли использовать Application Insights для мониторинга веб-сервера в интрасети?

Да, можно отслеживать состояние и использование сервера, если сервер может отправлять данные в общедоступный Интернет. Откройте в своем брандмауэре TCP-порты 80 и 443 для исходящего трафика на сайты dc.services.visualstudio.com и f5.services.visualstudio.com.

Если же вы хотите запускать веб-тесты для вашей службы, она должна быть доступна из Интернета через порт 80.

#### Можно ли отслеживать веб-сервер в интрасети без доступа к Интернету?

Для этого понадобиться настроить прокси-сервер, который может передавать вызовы HTTPS POST по адресу dc.services.visualstudio.com.

#### Ранее видимые данные перестали отображаться

* Проверьте [блог состояний](http://blogs.msdn.com/b/applicationinsights-status/).
* Вы достигли месячной квоты точек данных? Чтобы выяснить это, последовательно выберите «Параметры», «Квота и расценки». Если вы достигли квоты, вы можете изменить свой тарифный план или заплатить за дополнительную емкость. См. [схему ценообразования](http://azure.microsoft.com/pricing/details/application-insights/).

## Монитор состояний не работает

См. статью [Устранение неполадок монитора состояний](app-insights-monitor-performance-live-website-now.md#troubleshooting). Чаще всего проблема связана с портами брандмауэра.

## Портал

#### <a name="q05"></a>На экране открыта начальная панель предварительной версии Microsoft Azure. Как найти мои данные в Application Insights?

Одно из двух:

* Выберите Browse и Application Insights, затем выберите свой проект. Если там пока нет проектов, нужно добавить [Application Insights в ваш веб-проект в Visual Studio][start].

* В обозревателе решений Visual Studio щелкните правой кнопкой мыши проект веб-приложения и выберите «Открыть портал Application Insights».


#### <a name="update"></a>Как изменить ресурс Azure, в который проект отправляет данные?

В обозревателе решений щелкните правой кнопкой мыши `ApplicationInsights.config` и выберите **Обновить Application Insights**. Данные можно отправлять на существующий или новый ресурс в Azure. Мастер обновления изменяет ключ инструментирования в файле ApplicationInsights.config, который определяет, куда сервер SDK должен отправлять ваши данные. Если отменить выбор параметра «Обновить все», это также приведет к изменению ключа, отображаемого на ваших веб-страницах.


#### <a name="q06"></a>Отображается ли состояние моего приложения на карте на начальном экране предварительной версии Microsoft Azure?

Нет! Здесь отображается состояние службы Azure. Чтобы увидеть результаты веб-теста, выберите Обзор > Application Insights > (ваше приложение).


#### <a name="q07"></a>После добавления Application Insights в приложение и открытия портала Application Insights все выглядит совсем не так, как на снимках экрана.

Возможно, вы используете [более раннюю версию пакета SDK для Application Insights](http://msdn.microsoft.com/library/dn793604.aspx), которая подключается к версии Visual Studio Online.

Эти страницы справки относятся к [Application Insights для предварительной версии Microsoft Azure][start], которая поставляется с Visual Studio Update 2013 с обновлением 3 и более поздними обновлениями.

#### <a name="data"></a>Как долго данные хранятся на портале? Защищены ли они?

См. раздел [Хранение данных и конфиденциальность][data].

## Ведение журналов

#### <a name="post"></a>Как просмотреть данные POST в колонке «Поиск по журналу диагностики»?

Данные POST не регистрируются автоматически, но можно использовать вызов TrackTrace. Для этого добавьте данные в параметр сообщения. В нем можно добавить больше символов, чем в свойствах строк, но по нему невозможно выполнить фильтрацию.

## Безопасность

#### Защищены ли мои данные на портале? Как долго они хранятся?

См. раздел [Хранение данных и конфиденциальность][data].


## <a name="q17"></a>Все ли активировано в Application Insights?

<table border="1">
<tr><th>Что вы должны видеть</th><th>Как это получить</th><th>Для чего это нужно</th></tr>
<tr><td>Диаграммы доступности</td><td><a href="../app-insights-monitor-web-app-availability/">Веб-тесты</a></td><td>Узнать, что ваше веб-приложение работает</td></tr>
<tr><td>Производительность приложения на сервере: время отклика и т.д.
</td><td><a href="../app-insights-start-monitoring-app-health-usage/">Добавить Application Insights в свой проект</a><br/>или <br/><a href="../app-insights-monitor-performance-live-website-now/">Установить монитор состояний Application Insights на сервере</a> (или написать собственный код для <a href="../app-insights-api-custom-events-metrics/#track-dependency">отслеживания зависимостей</a>)</td><td>Выявить проблемы производительности</td></tr>
<tr><td>Телеметрия зависимостей</td><td><a href="../app-insights-monitor-performance-live-website-now/">Установить монитор состояний Application Insights на сервере</a></td><td>Выявить проблемы с базами данных или другими внешними компонентами</td></tr>
<tr><td>Получение данных трассировки стека из исключений</td><td><a href="../app-insights-search-diagnostic-logs/#exceptions">Вставьте вызовы TrackException в код</a> (некоторые выводятся автоматически)</td><td>Обнаружить и диагностировать исключения</td></tr>
<tr><td>Поиск по трассировкам журнала</td><td><a href="../app-insights-search-diagnostic-logs/">Добавьте адаптер ведения журнала</a></td><td>Выявить исключения, проблемы производительности</td></tr>
<tr><td>Основная информация об использовании клиента: просмотр страниц, сеансы и т.&#160;д.</td><td><a href="../app-insights-javascript/">Инициализатор JavaScript на веб-страницах</a></td><td>Аналитика использования</td></tr>
<tr><td>Настраиваемые метрики клиента</td><td><a href="../app-insights-api-custom-events-metrics/">Трассировка вызовов на веб-страницах</a></td><td>Расширить возможности для пользователя</td></tr>
<tr><td>Настраиваемые метрики сервера</td><td><a href="../app-insights-api-custom-events-metrics/">Отслеживание вызовов в коде сервера</a></td><td>Бизнес-аналитика</td></tr>
</table>

Если ваша веб-служба работает на виртуальной машине Azure, вы можете [получить данные диагностики][azurediagnostic] здесь.

## Автоматизация

Вы можете [написать сценарий PowerShell](app-insights-powershell-script-create-resource.md) для создания ресурса Application Insights.

## Другие ответы

* [Форум Application Insights](https://social.msdn.microsoft.com/Forums/vstudio/ru-RU/home?forum=ApplicationInsights)


<!--Link references-->

[azurediagnostic]: ../insights-how-to-use-diagnostics.md
[data]: app-insights-data-retention-privacy.md
[platforms]: app-insights-platforms.md
[start]: app-insights-get-started.md
[windows]: app-insights-windows-get-started.md

 

<!---HONumber=Sept15_HO3-->