<properties 
	pageTitle="Справочник по политикам Azure API Management" 
	description="Сведения о политиках, доступных для настройки службы управления API." 
	services="api-management" 
	documentationCenter="" 
	authors="steved0x" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="api-management" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="2/11/2015" 
	ms.author="sdanie"/>

# Application Insights - начните отслеживать работоспособность и использование приложения

*Служба Application Insights доступна в предварительной версии.*

С помощью Application Insights можно отслеживать следующие показатели работающего приложения:

* **доступность** - мы будем тестировать ваши URL-адреса каждые несколько минут из разных точек мира;
* **производительность** - обнаружение и диагностика проблем с производительностью и исключений;
* **использование** - узнайте, что пользователи делают с вашим приложением, чтобы улучшить его.

Настроить службу очень легко, так что вы увидите результаты через несколько минут. На сегодняшний день мы поддерживаем веб-приложения ASP.NET (на ваших собственных серверах или в Azure).


## Приступая к работе

Начните с любого сочетания и любого порядка точек входа, приведенных в левой части диаграммы. Выберите требуемые маршруты. При разработке веб-приложения ASP.NET следует начать с добавления Application Insights в веб-проект. Остальные инструменты будет несложно добавить позже.

Вам потребуется учетная запись в [Microsoft Azure](http://azure.com) (если только вы не используете версию VSO).

<table >
<tr valign="top"><th>Что вам нужно</th><th colspan="2">Что следует делать</th><th>Что вы получаете</th></tr>
<tr valign="top"><td>Получить аналитику производительности и использования для моего приложения ASP.NET</td><td colspan="2"><a href="../app-insights-start-monitoring-app-health-usage/">Добавить Application Insights в свой веб-проект</a></td><td>Метрики производительности: счетчики загрузки, время отклика...</td></tr>
<tr valign="top"><td></td><td></td><td><a href="../app-insights-web-track-usage-custom-events-metrics/">Отправить события и метрику из своего серверного кода</a></td><td>Пользовательская бизнес-аналитика</td></tr>
<tr valign="top"><td></td><td></td><td><a href="../app-insights-search-diagnostic-logs/">Отправить данные телеметрии по трассировке и исключениям со своего сервера или записывать данные сторонних журналов.</td><td>Диагностика серверного приложения. Поиск и фильтрация данных журнала.</a></td></tr>
<tr valign="top"><td>Получить аналитику использования моих веб-страниц (для любой платформы)</td><td colspan="2"><a href="../app-insights-web-track-usage/">Вставить скрипт AI на свои веб-страницы</a></td><td>Аналитика использования: просмотры страниц, вернувшиеся пользователи, счетчики сеансов</td></tr>
<tr valign="top"><td></td><td>&nbsp;&nbsp;</td><td><a href="../app-insights-web-track-usage-custom-events-metrics/">Добавить вызовы метрик и событий в скрипты своей веб-страницы</a></td><td>Настраиваемая аналитика взаимодействия с пользователем</td></tr>
<tr valign="top"><td></td><td></td><td><a href="../app-insights-search-diagnostic-logs/">Добавить вызовы метрик и событий в скрипты своей веб-страницы</a></td><td>Поиск и фильтрация данных журнала.</td></tr>
<tr valign="top"><td>Диагностировать проблемы в приложении ASP.NET, работающем на моем веб-сервере</td><td colspan="2"><a href="../app-insights-monitor-performance-live-website-now/">Установить монитор состояния на свой веб-сервер</a></td><td>Продолжительность и счетчики вызовов зависимостей; счетчики ЦП, памяти и сети; счетчики загрузки, время отклика</td></tr>
<tr valign="top"><td>Наблюдать за доступностью любой веб-страницы</td><td colspan="2"><a href="../app-insights-monitor-web-app-availability/">Настроить веб-тесты в Application Insights</a></td><td>Данные о доступности и оповещения</td></tr>
<tr valign="top"><td>Получать аналитику производительности и использования для приложений Windows Phone, магазина Windows или веб-сайтов Java</td><td colspan="2"><a href="http://msdn.microsoft.com/library/dn481095.aspx">Сейчас следует использовать более раннюю версию VSO Application Insights</a></td><td>Аналитика использования и производительности. <a href="http://msdn.microsoft.com/library/dn793604.aspx">Мы постепенно добавляем функции к новой версии Azure.</a></td></tr>
</table>


## <a name="video"></a>Видео

####  Введение

> [AZURE.VIDEO application-insights-introduction]

#### Приступая к работе

> [AZURE.VIDEO getting-started-with-application-insights]




[AZURE.INCLUDE [app-insights-learn-more](../../includes/app-insights-learn-more.md)]



<!--HONumber=46--> 
 