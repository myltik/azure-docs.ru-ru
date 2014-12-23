<properties urlDisplayName="Use WebJobs to run background tasks in Microsoft Azure Websites" pageTitle="Использование веб-заданий для выполнения фоновых задач на веб-сайтах Microsoft Azure" metaKeywords="веб-сайты Microsoft Azure, веб-задания, фоновые задачи" description="Learn how to run background tasks in Microsoft Azure Websites." metaCanonical="" services="web-sites" documentationCenter="" title="Use WebJobs to run background tasks in Microsoft Azure Websites" authors="cephalin"  solutions="" writer="timamm" manager="wpickett" editor="mollybos"  />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/12/2014" ms.author="cephalin" />

#Использование веб-заданий для выполнения фоновых задач на веб-сайтах Microsoft Azure

Веб-сайты Microsoft Azure позволяют запускать программы или сценарии на веб-сайте одним из трех способов: по требованию, непрерывно или по расписанию. Для использования веб-заданий Microsoft Azure дополнительные затраты не требуются.

В данной статье показано, как развернуть веб-задания с помощью портала управления Azure. Сведения о развертывании с помощью Visual Studio или процесса непрерывной доставки см. в разделе [Способы развертывания веб-заданий Azure для веб-сайтов Azure](http://azure.microsoft.com/ru-ru/documentation/articles/websites-dotnet-deploy-webjobs).

Пакет SDK веб-заданий Azure упрощает многие задачи программирования для веб-заданий. Дополнительные сведения см. в разделе [Что такое пакет SDK веб-заданий](../websites-dotnet-webjobs-sdk).

## Оглавление ##
- [Применимые типы файлов для скриптов](#acceptablefiles)
- [Создание задачи по требованию](#CreateOnDemand)
- [Создание непрерывно выполняющейся задачи](#CreateContinuous)
- [Создание запланированной задачи](#CreateScheduled)
	- [Запланированные задания и планировщик Azure](#Scheduler)
- [Просмотр журнала заданий](#ViewJobHistory)
- [Примечания](#WHPNotes)
- [Дальнейшие действия](#NextSteps)

## <a name="acceptablefiles"></a>Допустимые типы файлов для скриптов и программ

Допустимы следующие типы файлов:

* .cmd, .bat, .exe (с использованием командной строки windows)
* .ps1 (с использованием powershell)
* .sh (с использованием bash)
* .PHP (с использованием php)
* .py (с использованием python)
* .js (с использованием node)

## <a name="CreateOnDemand"></a>Создание задачи по требованию

1. В командной строке на странице **Веб-задания** щелкните **Добавить**. Откроется диалоговое окно **Новое задание**.
	
	![On Demand Task][OnDemandWebJob]
	
2. В поле **Имя** укажите имя задачи. Имя должно начинаться с буквы или цифры и не может содержать специальные символы, кроме "-" и "_".
	
3. В поле **Содержимое (ZIP-файлы - максимум 100 Мбайт)** перейдите к ZIP-файлу, в котором содержится скрипт. ZIP-файл должен содержать исполняемый файл (.exe .cmd .bat .sh .php .py .js), а также все вспомогательные файлы, необходимые для запуска программы или скрипта.
	
4. В поле **Как выполнить** выберите пункт**Выполнять по требованию**.
	
5. Установите флажок в правой нижней части диалогового окна для передачи сценария на веб-сайт. В списке отображается имя, указанное для задачи:
	
	![Task List][WebJobsList]
	
6. Чтобы запустить скрипт, выберите его имя в списке и щелкните пункт **Запустить один раз** на панели команд в нижней части страницы портала.
	
	![Run Once][RunOnce]

## <a name="CreateContinuous"></a>Создание непрерывно выполняющейся задачи

1. Чтобы создать непрерывно выполняемую задачу, выполните действия по созданию задачи, которая запускается один раз, но в поле **Как выполнить** выберите значение **Выполнять непрерывно**.
	
	![New Continuous Task][NewContinuousJob]
	
2. Чтобы запустить или остановить непрерывно выполняемую задачу, выберите задачу в списке и щелкните на панели команд пункт **Запустить** или **Остановить**.

> [WACOM.NOTE] Если веб-сайт работает на нескольких экземплярах, непрерывно выполняемая задача будет работать на всех экземплярах. Задачи по требованию и запланированные задачи запускаются на отдельном экземпляре, выбранном Microsoft Azure для балансировки нагрузки.

> [WACOM.NOTE]
> Для непрерывно выполняемых задач рекомендуется включить параметр **Всегда включено** на странице "Настройки" для вашего веб-сайта. Функция "Всегда включено", доступная только в базовом и стандартном режимах, препятствует выгрузке веб-сайтов, даже если они были неактивны в течение некоторого времени. Если веб-сайт всегда загружен, непрерывно выполняемая задача может работать более стабильно. 

## <a name="CreateScheduled"></a>Создание запланированной задачи

1. Чтобы создать запланированную задачу, выполните те же действия, что и раньше, но в поле **Как выполнить** выберите **Выполнять по расписанию**.
	
	![New Scheduled Job][NewScheduledJob]
	
2. Выберите **Регион планировщика** для задания и нажмите кнопку со стрелкой в правом нижнем углу диалогового окна, чтобы перейти к следующему экрану.

3. В диалоговом окне **Создать задание** выберите требуемый тип **Повторения**: **Разовое задание** или **Повторяющееся задание**.
	
	![Schedule Recurrence][SchdRecurrence]
	
4. Кроме того, выберите время **запуска**: **Сейчас** или **В определенное время**.
	
	![Schedule Start Time][SchdStart]
	
5. Если необходимо начать в определенное время, выберите значения времени запуска в поле **Начало в**.
	
	![Schedule Start at a Specific Time][SchdStartOn]
	
6. Если выбрано повторяющееся задание, с помощью параметра **Повторять каждые** можно указать частоту повторения, а с помощью параметра **Заканчивается в** - время завершения.
	
	![Schedule Recurrence][SchdRecurEvery]
	
7. При выборе значения **Недели** можно выбрать поле **В определенном расписании** и указать дни недели, в которые должно запускаться это задание.
	
	![Schedule Days of the Week][SchdWeeksOnParticular]
	
8. При выборе значения **Месяцы** и поля **В определенном расписании** можно определить запуск задания в определенные, пронумерованные **дни** месяца. 
	
	![Schedule Particular Dates in the Month][SchdMonthsOnPartDays]
	
9. При выборе значения **Дни недели** можно выбрать те дни недели месяца, в которые требуется запускать задание.
	
	![Schedule Particular Week Days in a Month][SchdMonthsOnPartWeekDays]
	
10. Наконец, можно также использовать параметр **Вхождения**, чтобы выбрать неделю месяца (первая, вторая, третья и т.д.), в течение которой задание должно выполняться в указанные дни недели.
	
	![Schedule Particular Week Days on Particular Weeks in a Month][SchdMonthsOnPartWeekDaysOccurences]
	
11. Имена созданных заданий будут отображаться на вкладке "Веб-задания" вместе со сведениями об их состоянии, типе расписания и прочей информацией. Сохраняются журнальные данные для последних 30 выполненных задач.
	
	![Jobs list][WebJobsListWithSeveralJobs]
	
### <a name="Scheduler"></a>Запланированные задания и планировщик Azure

На портале планировщика Azure можно выполнить дополнительную настройку запланированных заданий.

1.	Чтобы перейти на страницу портала планировщика Azure, щелкните ссылку **расписание** для задания на странице "Веб-задания". 
	
	![Link to Azure Scheduler][LinkToScheduler]
	
2. Выберите задание на странице планировщика.
	
	![Job on the Scheduler portal page][SchedulerPortal]
	
3. Откроется страница **Действие задания**, на которой можно выполнить дополнительную настройку задания. 
	
	![Job Action PageInScheduler][JobActionPageInScheduler]
	
## <a name="ViewJobHistory"></a>Просмотр журнала заданий

1. Чтобы просмотреть журнал выполнения задания, включая задания, созданные с помощью SDK веб-заданий, щелкните соответствующую ссылку в столбце **Журналы**. (Можно также использовать значок буфера обмена для копирования URL-адреса страницы файла журнала в буфер обмена.)
	
	![Logs Link][WebJobLogs]
		
2. При щелчке ссылки открывается страница сведений о веб-заданиях для задачи. На этой странице отображаются имя выполненной команды, время ее последнего выполнения, а также сведения об успешности выполнения. Укажите время в разделе **Недавно выполненные задания**, чтобы просмотреть дополнительные сведения.
	
	![WebJobDetails][WebJobDetails]
	
3. Откроется страница **Сведения о выполнении веб-задания**. Щелкните **Переключить вывод**, чтобы просмотреть текст журнала. Журнал выводится в текстовом формате. 
	
	![Web job run details][WebJobRunDetails]
	
4. Чтобы просмотреть текст вывода в отдельном окне браузера, щелкните ссылку **загрузить**. Чтобы загрузить текст, щелкните ссылку правой кнопкой мыши и выберите команду браузера для сохранения содержимого файла.
	
	![Download log output][DownloadLogOutput]
	
5. Чтобы простым способом получить список веб-заданий на панели мониторинга журнала, можно воспользоваться ссылкой **Веб-задания** в верхней части страницы.
	
	![Link to web jobs list][WebJobsLinkToDashboardList]
	
	![List of jobs in history dashboard][WebJobsListInJobsDashboard]
	
	При щелчке одной из этих ссылок открывается страница "Сведения о веб-задании" для выбранного задания.


## <a name="WHPNotes"></a>Примечания
	
- По состоянию на март 2014 г. в бесплатном режиме веб-сайтов время ожидания может истекать, если в течение 20 минут не поступали запросы к сайту SCM (развертывание), а в Azure не был открыт портал веб-сайта. При этом такое поведение наблюдается даже при наличии запросов к действующему сайту.
- Код непрерывно выполняющегося задания должен выполняться в бесконечном цикле.
- Задания выполняются непрерывно только во время работы сайта.
- В базовом и стандартном режимах реализована функция "Всегда включено", которая позволяет предотвратить переход сайта в неактивное состояние.

## <a name="NextSteps"></a>Дальнейшие действия
 
Дополнительные сведения см. в разделе [Рекомендуемые ресурсы для веб-заданий Azure][WebJobsRecommendedResources].

[PSonWebJobs]:http://blogs.msdn.com/b/nicktrog/archive/2014/01/22/running-powershell-web-jobs-on-azure-websites.aspx
[WebJobsRecommendedResources]:http://go.microsoft.com/fwlink/?LinkId=390226

[OnDemandWebJob]: ./media/web-sites-create-web-jobs/01aOnDemandWebJob.png
[WebJobsList]: ./media/web-sites-create-web-jobs/02aWebJobsList.png
[NewContinuousJob]: ./media/web-sites-create-web-jobs/03aNewContinuousJob.png
[NewScheduledJob]: ./media/web-sites-create-web-jobs/04aNewScheduledJob.png
[SchdRecurrence]: ./media/web-sites-create-web-jobs/05SchdRecurrence.png
[SchdStart]: ./media/web-sites-create-web-jobs/06SchdStart.png
[SchdStartOn]: ./media/web-sites-create-web-jobs/07SchdStartOn.png
[SchdRecurEvery]: ./media/web-sites-create-web-jobs/08SchdRecurEvery.png
[SchdWeeksOnParticular]: ./media/web-sites-create-web-jobs/09SchdWeeksOnParticular.png
[SchdMonthsOnPartDays]: ./media/web-sites-create-web-jobs/10SchdMonthsOnPartDays.png
[SchdMonthsOnPartWeekDays]: ./media/web-sites-create-web-jobs/11SchdMonthsOnPartWeekDays.png
[SchdMonthsOnPartWeekDaysOccurences]: ./media/web-sites-create-web-jobs/12SchdMonthsOnPartWeekDaysOccurences.png
[RunOnce]: ./media/web-sites-create-web-jobs/13RunOnce.png
[WebJobsListWithSeveralJobs]: ./media/web-sites-create-web-jobs/13WebJobsListWithSeveralJobs.png
[WebJobLogs]: ./media/web-sites-create-web-jobs/14WebJobLogs.png
[WebJobDetails]: ./media/web-sites-create-web-jobs/15WebJobDetails.png
[WebJobRunDetails]: ./media/web-sites-create-web-jobs/16WebJobRunDetails.png
[DownloadLogOutput]: ./media/web-sites-create-web-jobs/17DownloadLogOutput.png
[WebJobsLinkToDashboardList]: ./media/web-sites-create-web-jobs/18WebJobsLinkToDashboardList.png
[WebJobsListInJobsDashboard]: ./media/web-sites-create-web-jobs/19WebJobsListInJobsDashboard.png
[LinkToScheduler]: ./media/web-sites-create-web-jobs/31LinkToScheduler.png
[SchedulerPortal]: ./media/web-sites-create-web-jobs/32SchedulerPortal.png
[JobActionPageInScheduler]: ./media/web-sites-create-web-jobs/33JobActionPageInScheduler.png
