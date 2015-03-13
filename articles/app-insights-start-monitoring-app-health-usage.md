<properties 
	pageTitle="Добавление пакета SDK Application Insights в веб-проект" 
	description="Анализ использования, доступности и производительности локального приложения или веб-приложения Microsoft Azure с помощью Application Insights." 
	services="application-insights" 
	authors="alancameronwills" 
	manager="kamrani"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="2015-02-03" 
	ms.author="awills"/>

# Application Insights - начните отслеживать работоспособность и использование приложения

*Служба Application Insights доступна в предварительной версии.*

С помощью Application Insights можно отслеживать следующие показатели работающего приложения:

* **Доступность** - мы будем тестировать ваши URL-адреса каждые несколько минут из разных точек мира.
* **Производительность**   - обнаружение и диагностика проблем с производительностью и исключений.
* **Использование** - узнайте, что пользователи делают с вашим приложением, чтобы улучшить его.

Альтернативные способы описаны в руководстве [Приступая к работе с Application Insights][start].

## <a name="add"></a>Добавление Application Insights в ваш проект

Вам потребуется [Visual Studio 2013, обновление 3](http://go.microsoft.com/fwlink/?linkid=397827&clcid=0x409) (или более поздняя версия) и учетная запись в [Microsoft Azure](http://azure.com).

### Добавление в новый проект

При создании нового проекта в Visual Studio 2013 установите флажок для компонента Application Insights. 


![Create an ASP.NET project](./media/appinsights/appinsights-01-vsnewp1.png)

Если вы делаете это впервые, вам будет предложено войти или зарегистрироваться в Microsoft Azure предварительной версии. (При этом создается учетная запись, не связанная с учетной записью Visual Studio Online.)

Если вы хотите, чтобы имя ресурса Azure отличалось от имени проекта или чтобы ресурс входил в ту же группу в качестве отдельного проекта, выберите **Настроить параметры**. 

*Нет параметра для добавления Application Insights? Убедитесь в том, что вы используете Visual Studio 2013, обновление 3, что инструменты Application Insights Tools включены в разделе "Расширения и обновления" и что вы создаете веб-проект либо проект для Магазина Windows или Windows Phone.*

### ... или, если это существующий проект

В обозревателе решений щелкните проект правой кнопкой мыши и выберите пункт Add Application Insights (Добавить Application Insights).

![Choose Add Application Insights](./media/appinsights/appinsights-03-addExisting.png)

*Чтобы настроить аналитику использования веб-сайта, нужно выполнить еще одно действие, но сначала давайте рассмотрим некоторые данные...*


### <a name="run"></a>2. Запустите свой проект

Запустите приложение, нажав клавишу F5, и попробуйте открыть разные страницы.

В Visual Studio вы увидите число полученных событий.

![](./media/appinsights/appinsights-09eventcount.png)

### <a name="monitor"></a>3. Просмотрите данные мониторинга

Откройте Application Insights из проекта.

![Right-click your project and open the Azure portal](./media/appinsights/appinsights-04-openPortal.png)


Просматривайте данные на плитках **Работоспособность приложения**. Сначала вы увидите только одну или две точки. Например: 

![Click through to more data](./media/appinsights/appinsights-41firstHealth.png)

Щелкните любую плитку для просмотра более подробных данных. Вы можете изменить данные, отображаемые в отчетах. [Подробнее о настройке отчетов о работоспособности приложения.][perf]


### <a name="deploy"></a>4. Разверните свое приложение

Разверните приложение и наблюдайте за тем, как накапливаются данные.



Когда приложение заработает, [настройте веб-тесты][availability], чтобы контролировать его работоспособность. 

![Example application monitor in Application Insights](./media/appinsights/appinsights-00-appblade.png)

### Изменение имени приложения на портале

Вы можете изменить ресурс, которому проект отправляет данные телеметрии. 

(Ресурс - это именованный модуль Application Insights, в котором отображаются результаты. Вы можете добавить несколько ресурсов в группу - например, если несколько проектов образуют одно бизнес-приложение.) 

В обозревателе решений щелкните правой кнопкой мыши на ApplicationInsights.config и выберите **Обновить Application Insights**. Откроется мастер, в котором можно выбрать другой существующий ресурс или создать новый.

После этого вернитесь на портал и удалите старый ресурс.

## <a name="video"></a>Видео

> [AZURE.VIDEO getting-started-with-application-insights]

## <a name="next"></a>Дальнейшие действия

[Отслеживание использования веб-приложения][usage]

[Отслеживание производительности в веб-приложениях][perf]

[Ведение журналов диагностики и поиск по ним][diagnostic]

[Устранение неполадок][qna]




[AZURE.INCLUDE [app-insights-learn-more](../includes/app-insights-learn-more.md)]



<!--HONumber=46--> 
