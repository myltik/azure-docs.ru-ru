<properties 
	pageTitle="Обновление старой версии Visual Studio Online для Application Insights" 
	description="Обновить существующие проекты"
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
	ms.date="06/19/2015" 
	ms.author="awills"/>
 
# Обновление старой версии Visual Studio Online для Application Insights

Этот документ представляет интерес только в том случае, если имеется проект, по-прежнему использующий старую версию Application Insights, которая входила в состав Visual Studio Online. Эта версия будет отключена в установленном порядке, и поэтому рекомендуется выполнить обновление до новой версии, которая является службой в Microsoft Azure.

## Какая версия у меня?

Если вы добавили Application Insights в проект с помощью обновления 3 для Visual Studio 2013 или более поздней версии, скорее всего, используется новая версия Azure.

Откройте файл ApplicationInsights.config. Если присутствуют узлы `ActiveProfile` и `Profiles`, значит, это старая версия и ее следует обновить.

Или откройте свой проект в обозревателе решений Visual Studio и в разделе "Ссылки" выберите Microsoft.ApplicationInsights. В окне "Свойства" найдите версию. Если она меньше 0.12, значит, следует выполнить обновление.

## Если у вас проект Visual Studio...

1. Откройте проект в версии Visual Studio 2013 обновлением 3 или более поздней версии.
2. Удалите файл ApplicationInsights.config. 
3. Удалите пакеты NuGet Application Insights из проекта. Щелкните правой кнопкой мыши проект в обозревателе решений и выберите «Управление пакетами NuGet».

    ![](./media/app-insights-upgrade-vso-azure/nuget.png)
4. Удалите папку AppInsightsAgent и файлы, которые в ней находятся.

    ![](./media/app-insights-upgrade-vso-azure/folder.png)

5. Удалите все названия и значения параметров Microsoft.AppInsights из ServiceDefinition.csdef и ServiceConfiguration.csfg

    ![](./media/app-insights-upgrade-vso-azure/csdef.png)
4. Пакет SDK. Щелкните правой кнопкой мыши проект и [выберите пункт «Добавить Application Insights»][greenbrown]. Пакет SDK будет добавлен в проект, а в Azure будет создан новый ресурс Application Insights.
5. Ведение журнала. Если код содержит вызовы старого API, например LogEvent(), вы обнаружите их при попытке построить решение. Обновить их, чтобы [использовать новый API][track].
6. Веб-страницы. Если проект содержит веб-страницы, замените сценарии в <head> разделах. Обычно существует только одна копия на главной странице, например Views\Shared_Layout.cshtml. [Получите новый скрипт из колонки "Быстрый запуск" в ресурсе Application Insights в Azure][usage]. Если веб-страницы содержат вызовы телеметрии, например методы logEvent или logPage, [обновите их для использования нового API][api].
7. Монитор сервера. Если приложение является службой на сервере IIS, удалите Microsoft Monitoring Agent с сервера, а затем [установите монитор состояния Application Insights][redfield].
8. Веб-тесты. При использовании тестов доступности веб-сайта [заново создайте их на новом портале][availability], включая оповещения для них.
9. Оповещения. Настройте [оповещения для метрик][alerts] на портале Azure.


## Если у вас веб-служба Java...

1. В серверном компьютере отключите старый агент путем удаления ссылок на агента APM из файла загрузки веб-службы. На сервере TomCat измените файл Catalina.bat. На сервере JBoss измените файл Run.bat. 
2. Перезапустите веб-приложение.
3. На портале Microsoft Azure [добавьте новый ресурс Application Insights][java]. На компьютере разработки добавьте [пакет Java SDK][java] для веб-проекта.
4. Замените сценарии в <head> разделах веб-страниц. (В SSI может быть только одна копия.) [Получите новый сценарий из колонки "Быстрый запуск" в новом ресурсе Application Insights в Azure][usage]. Если веб-страницы содержат вызовы телеметрии, например методы logEvent или logPage, [обновите их для использования нового API][track].
8. Веб-тесты. При использовании тестов доступности веб-сайта [заново создайте их на новом портале][availability], включая оповещения для них.
9. Оповещения. Настройте [оповещения для метрик][alerts] на портале Azure.



<!--Link references-->

[alerts]: app-insights-alerts.md
[api]: app-insights-api-custom-events-metrics.md
[availability]: app-insights-monitor-web-app-availability.md
[greenbrown]: app-insights-start-monitoring-app-health-usage.md
[java]: app-insights-java-get-started.md
[redfield]: app-insights-monitor-performance-live-website-now.md
[track]: app-insights-custom-events-metrics-api.md
[usage]: app-insights-web-track-usage.md

 

<!---HONumber=July15_HO4-->