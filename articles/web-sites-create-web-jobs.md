<properties 
	pageTitle="Выполнение фоновых задач с веб-заданиями" 
	description="Узнайте, как выполнять фоновые задачи в веб-приложениях Azure." 
	services="app-service\web" 
	documentationCenter="" 
	authors="tdykstra" 
	writer="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/24/2015" 
	ms.author="tdykstra"/>

# Выполнение фоновых задач с веб-заданиями

## Обзор

Программы или скрипты можно запускать в веб-заданиях в веб-приложении [службы приложений](http://go.microsoft.com/fwlink/?LinkId=529714) тремя способами: по требованию, непрерывно или по расписанию. Для использования веб-заданий дополнительные затраты не требуются.

В данной статье показано, как развернуть веб-задания с помощью [портала Azure](http://go.microsoft.com/fwlink/?LinkId=529715). Информацию о развертывании с помощью Visual Studio или процесса непрерывной доставки см. в разделе [Развертывание веб-заданий Azure в веб-приложениях](websites-dotnet-deploy-webjobs.md).

Пакет SDK веб-заданий Azure упрощает многие задачи программирования для веб-заданий. Дополнительную информацию см. в разделе [Информация о пакете SDK веб-заданий](websites-dotnet-webjobs-sdk.md).

## <a name="acceptablefiles"></a>Допустимые типы файлов для сценариев и программ

Допустимы следующие типы файлов:

* .cmd, .bat, .exe (с использованием командной строки windows)
* .ps1 (с использованием powershell)
* .sh (с использованием bash)
* .PHP (с использованием php)
* .py (с использованием python)
* .js (с использованием node)

## <a name="CreateOnDemand"></a>Создание веб-задания по запросу на портале

1. В колонке **Веб-приложение** на [портале Azure](http://portal.azure.com) щелкните **Все параметры > Веб-задания** для отображения колонки **Веб-задания**.
	
	![WebJob blade](./media/web-sites-create-web-jobs/wjblade.png)
	
5. Щелкните **Добавить**. Появится диалоговое окно **Добавление веб-задания**.
	
	![Add WebJob blade](./media/web-sites-create-web-jobs/addwjblade.png)
	
2. В поле **Имя** укажите имя задачи. Имя должно начинаться с буквы или цифры и не может содержать специальные символы, отличные от "-" и "_".
	
4. В поле **Как выполнить** выберите пункт **Выполнять по требованию**.
	
3. В поле **Отправка файла** щелкните значок папки и перейдите к ZIP-файлу, который содержит сценарий. ZIP-файл должен содержать исполняемый файл (.exe .cmd .bat .sh .php .py .js), а также все вспомогательные файлы, необходимые для запуска программы или скрипта.
	
5. Поставьте флажок **Создать**, чтобы загрузить сценарий в веб-приложение. 
	
	Имя, которое вы присвоили веб-заданию, появится в списке колонки **Веб-задания**.
	
6. Чтобы запустить веб-задание, щелкните правой кнопкой мыши его имя в списке и нажмите кнопку **Запустить**.
	
	![Run WebJob](./media/web-sites-create-web-jobs/runondemand.png)
	
## <a name="CreateContinuous"></a>Создание непрерывно выполняющегося веб-задания

1. Чтобы создать непрерывно выполняющееся веб-задание, следуйте инструкциям по созданию задания, которое запускается один раз, только в поле **Как выполнять** выберите значение **Непрерывно**.

2. Чтобы запустить или остановить непрерывное веб-задание, щелкните правой кнопкой мыши веб-задание в списке и нажмите кнопку **Запустить** или **Остановить**.
	
> [AZURE.NOTE] Если веб-приложение работает на нескольких экземплярах, непрерывно выполняемое веб-задание будет работать на всех экземплярах. Веб-задания по требованию и запланированные задачи запускаются на отдельном экземпляре, выбранном Microsoft Azure для балансировки нагрузки.
	
> [AZURE.NOTE] Для непрерывно выполняемых веб-заданий рекомендуется включить параметр **Всегда включено** для веб-приложения. Функция "Всегда включено", доступная только в базовом и стандартном режимах, препятствует выгрузке веб-приложений, даже если они были неактивны в течение некоторого времени. Если веб-приложение всегда загружено, непрерывно выполняемое веб-задание может работать более стабильно. 

## <a name="CreateScheduled"></a>Создание запланированного веб-задания

На портале управления Azure еще нет возможности создавать запланированное веб-задание. Но пока эта функция не добавлена, вы можете использовать для создания запланированного веб-задания [старый портал](http://manage.windowsazure.com).

1. На [старом портале](http://manage.windowsazure.com) перейдите на страницу веб-задания и нажмите кнопку **Добавить**.

1. В поле **Как выполнить** выберите пункт **Выполнять по расписанию**.
	
	![New Scheduled Job][NewScheduledJob]
	
2. Выберите **Регион планировщика** для задания и нажмите кнопку со стрелкой в правом нижнем углу диалогового окна, чтобы перейти к следующему экрану.

3. В диалоговом окне **Создать задание** выберите нужный тип **Повторения**: **Разовое задание** или **Повторяющееся задание**.
	
	![Schedule Recurrence][SchdRecurrence]
	
4. Кроме того, выберите время **запуска**: **Сейчас** или **В определенное время**.
	
	![Schedule Start Time][SchdStart]
	
5. Если необходимо начать в определенное время, выберите значения времени запуска в поле **Начало в**.
	
	![Schedule Start at a Specific Time][SchdStartOn]
	
6. Если выбрано повторяющееся задание, с помощью параметра **Повторять каждые** можно указать частоту повторения, а с помощью параметра **Заканчивается** - время завершения.
	
	![Schedule Recurrence][SchdRecurEvery]
	
7. При выборе значения **Недели** можно выбрать поле **В определенном расписании** и указать дни недели, в которые должно запускаться это задание.
	
	![Schedule Days of the Week][SchdWeeksOnParticular]
	
8. При выборе значения **Месяцы** и выбора поля **В определенном расписании** можно определить запуск задания в определенные, пронумерованные **дни** месяца. 
	
	![Schedule Particular Dates in the Month][SchdMonthsOnPartDays]
	
9. При выборе значения **Дни недели** можно выбрать, в какой день или в какие дни недели месяца необходимо запускать задание.
	
	![Schedule Particular Week Days in a Month][SchdMonthsOnPartWeekDays]
	
10. Наконец, можно также использовать параметр **Вхождения** для выбора недели месяца (первая, вторая, третья и т. д.), во время которой задание должно выполняться в указанные дни недели.
	
	![Schedule Particular Week Days on Particular Weeks in a Month][SchdMonthsOnPartWeekDaysOccurences]
	
11. Имена созданных заданий будут отображаться на вкладке "Веб-задания" вместе с данными об их состоянии, типе расписания и прочей информацией. Сохраняются журнальные данные для последних 30 выполненных веб-заданий.
	
	![Jobs list][WebJobsListWithSeveralJobs]
	
### <a name="Scheduler"></a>Запланированные задания и планировщик Azure

На страницах планировщика Azure на [старом портале](http://manage.windowsazure.com) можно выполнить дополнительную настройку запланированных заданий.

1.	Чтобы перейти на страницу портала планировщика Azure, щелкните **расписание** задания на странице "Веб-задания". 
	
	![Link to Azure Scheduler][LinkToScheduler]
	
2. Выберите задание на странице планировщика.
	
	![Job on the Scheduler portal page][SchedulerPortal]
	
3. Откроется страница **Действие задания**, на которой можно выполнить дополнительную настройку задания. 
	
	![Job Action PageInScheduler][JobActionPageInScheduler]
	
## <a name="ViewJobHistory"></a>Просмотр журнала заданий

1. Чтобы просмотреть журнал выполнения задания, включая задания, созданные с помощью SDK для веб-заданий, щелкните соответствующую ссылку в столбце **Журналы** колонки "Веб-задания". Можно также использовать значок буфера обмена для копирования URL-адреса страницы файла журнала в буфер обмена.
	
	![Logs link](./media/web-sites-create-web-jobs/wjbladelogslink.png)
		
2. При щелчке ссылки открывается страница информации о веб-задании. На этой странице отображаются имя выполненной команды, время ее последнего выполнения, а также сведения об успешности выполнения. Укажите время в разделе **Недавно выполненные задания**, чтобы просмотреть дополнительные сведения.
	
	![WebJobDetails][WebJobDetails]
	
3. Откроется страница **Подробности о выполнении WebJob**. Щелкните **Переключить вывод**, чтобы просмотреть текст журнала. Журнал выводится в текстовом формате. 
	
	![Web job run details][WebJobRunDetails]
	
4. Чтобы просмотреть текст вывода в отдельном окне браузера, щелкните ссылку **скачать**. Чтобы загрузить текст, щелкните ссылку правой кнопкой мыши и выберите команду для сохранения содержимого файла в контекстном меню браузера.
	
	![Download log output][DownloadLogOutput]
	
5. Чтобы получить список заданий веб-заданий на панели мониторинга журнала, вы можете воспользоваться ссылкой **Веб-задания** вверху страницы.
	
	![Link to WebJobs list][WebJobsLinkToDashboardList]
	
	![List of WebJobs in history dashboard][WebJobsListInJobsDashboard]
	
	При щелчке одной из этих ссылок открывается страница "Сведения о веб-задании" для выбранного задания.


## <a name="WHPNotes"></a>Примечания
	
- По состоянию на март 2014 г. в бесплатном режиме веб-приложений время ожидания может истекать, если в течение 20 минут не поступали запросы к сайту SCM (развертывание), а в Azure не открыт портал веб-приложения. При этом такое поведение наблюдается даже при наличии запросов к фактическому сайту.
- Код непрерывно выполняющегося задания должен выполняться в бесконечном цикле.
- Задания выполняются непрерывно только во время работы веб-приложения.
- В базовом и стандартном режимах реализована функция "Всегда включено", которая позволяет предотвратить переход веб-приложений в неактивное состояние.

>[AZURE.NOTE] Если вы хотите начать работу со службой приложений Azure еще до создания учетной записи Azure, перейдите на страницу [ознакомительной версии службы приложений](http://go.microsoft.com/fwlink/?LinkId=523751). Там вы сможете сразу создать свое первое веб-приложение, правда срок его службы будет ограничен. Никаких кредитных карт и обязательств.

## <a name="NextSteps"></a>Дальнейшие действия
 
Дополнительную информацию см. в разделе [Рекомендуемые ресурсы веб-заданий Azure][WebJobsRecommendedResources].

## Изменения
* Сведения об изменении веб-сайтов на службу приложений см. в статье [Служба приложений Azure и ее влияние на существующие службы Azure](http://go.microsoft.com/fwlink/?LinkId=529714)
* Сведения о переходе со старого портала на новый см. в статье [Справочная информация о навигации по предварительной версии портала](http://go.microsoft.com/fwlink/?LinkId=529715).

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

<!--HONumber=49-->