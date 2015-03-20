<properties 
	pageTitle="Приступая к работе с Azure Mobile Engagement" 
	description="Информация об использовании службы push-уведомлений и аналитики совместно с Azure Mobile Engagement."
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="kpiteira" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="02/11/2015" 
	ms.author="kapiteir" />
	
# Приступая к работе с Mobile Engagement

<div class="dev-center-tutorial-selector sublanding"><a href="/documentation/articles/mobile-engagement-windows-store-dotnet-get-started/" title="Windows Store"  class="current">Магазин Windows</a><a href="/documentation/articles/mobile-engagement-windows-phone-get-started/" title="Windows Phone">Windows Phone</a><a href="/documentation/articles/mobile-engagement-ios-get-started/" title="iOS">iOS</a><a href="/documentation/articles/mobile-engagement-android-get-started/" title="Android">Android</a></div>

В этом разделе показано, как использовать Azure Mobile Engagement, чтобы понимать как приложение использует и отправляет push-уведомления сегментированным пользователям приложения Магазина Windows. 
В этом учебнике описывается простой сценарий вещания с использованием Mobile Engagement. Здесь вы создадите пустое приложение Магазина Windows, которое будет собирать основные данные и получать push-уведомления, используя службу push-уведомлений Windows (WPNS). По завершении вы сможете рассылать push-уведомления на все устройства или взять за цель конкретных пользователей на основе свойств их устройств. Рекомендуем вам изучить следующий учебник, чтобы узнать об использовании Mobile Engagement для охвата определенных пользователей и групп устройств.


Для работы с данным учебником требуется следующее:

+ Visual Studio 2013
+ [Пакет SDK для Магазина Windows для Mobile Engagement].

> [AZURE.IMPORTANT] Перед изучением остальных учебников по Mobile Engagement для приложений магазина Windows необходимо сначала ознакомиться с этим учебником, и для его полного прохождения требуется активная учетная запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе <a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fru-ru%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Бесплатная пробная версия Azure</a>.

##<a id="setup-azme"></a>Настройка Mobile Engagement для вашего приложения

1. Войдите на Портал управления Azure, а затем нажмите кнопку **+СОЗДАТЬ** в нижней части экрана.

2. Щелкните **Службы приложений**, **Mobile Engagement**, а затем - **Создать**.

   	![][7]

3. Во всплывающем окне введите следующую информацию:
 
   	![][8]

	1. **Имя приложения** - введите имя своего приложения. Вы можете использовать любые символы.
	2. **Платформа**: выберите целевую платформу для этого приложения (если приложение нацелено на несколько платформ, изучите этот учебник для каждой платформы).
	3. **Имя ресурса приложения** - это имя, по которому приложение можно будет открывать через API и по URL-адресам. Мы рекомендуем использовать обычные символы URL-адреса: автоматически созданное имя должно предоставлять твердую основу. Мы также рекомендуем добавить имя платформы, чтобы избежать конфликта имен, так как это имя должно быть уникальным.
	4. **Расположение**: выберите центр обработки данных, в котором будет размещено это приложение (и, что еще важнее, его коллекция, информацию о ней см. ниже).
	5. **Коллекция**: Если вы уже создали приложение, выберите созданную ранее коллекцию, а если нет, выберите "Создать коллекцию".
	6. **Имя коллекции**. Представляет собой группу приложений. Если задать это имя, все приложения будут находиться в одной группе, что позволит выполнять общие вычисления. Настоятельно рекомендуем использовать название вашей компании или отдела.


	По окончании нажмите кнопку с галочкой, чтобы завершить создание приложения.

4. Теперь выберите приложение, созданное на вкладке **Приложение**.
 
   	![][9]

5. Затем щелкните **Информация о подключении**, чтобы отобразить параметры подключения, которые следует использовать при интеграции пакета SDK.
 
   	![][10]

6. Наконец, введите значение в поле **Строка подключения**, которая понадобится для определения этого приложения в коде приложения.

   	![][11]

	>[AZURE.TIP] Для удобства вы можете использовать значок "Копировать" справа от поля "Строка подключения", чтобы скопировать название в буфер обмена.

##<a id="connecting-app"></a>Подключение приложения к серверной части Mobile Engagement

В этом учебнике описаны действия по базовой интеграции, т. е. минимуму, требуемому для сбора данных и отправки push-уведомлений. Информацию о полной интеграции см. в [документации по пакету SDK для Магазина Windows для Mobile Engagement].

Мы создадим базовое приложение в Visual Studio, чтобы продемонстрировать интеграцию.

###Создание нового проекта для Магазина Windows

Если у вас уже есть приложение и вы ознакомлены с разработкой для Windows, вы можете пропустить этот шаг.

1. Запустите Visual Studio и на начальном экране выберите **Создать проект...**.

   	![][12]

2. Во всплывающем окне (1) выберите `Магазин Windows`, а затем (2) выберите `Пустое приложение (XAML)`. Также (3) заполните в приложении поля `Имя` и (4) `Имя решения`, а затем нажмите кнопку **ОК**.

   	![][13]

Проект создан с помощью демонстрационной версии приложения, в которое будет интегрирована служба Mobile Engagement.

###Включение библиотеки пакета SDK в проект

Скачайте и интегрируйте библиотеку пакета SDK.

1. Скачайте [пакет SDK для Магазина Windows для Mobile Engagement].
2. Извлеките ZIP-файл в папку на компьютере.
3. Последовательно выберите `PROJECT` и команду "Управление пакетами NuGet...".
4. Во всплывающем окне щелкните "Параметры".
5. Затем нажмите кнопку "+", чтобы создать новый источник.

	![][17]

6. Нажмите кнопку `...`  внизу, чтобы выбрать источник, и перейдите к папке lib в извлеченном скачанном пакете SDK (см. шаг 2), а затем щелкните `Выбрать`.

	![][18]

7. Пакет SDK будет добавлен в качестве источника. Просто нажмите кнопку `ОК`. Затем выберите пакет в списке "сетевых" пакетов и щелкните "Установить".

	![][19]


###Подключение приложения к серверной части Mobile Engagement с помощью строки подключения

1. Откройте `Package.appxmanifest` и убедитесь в наличии следующих объявлений (добавьте их, если они отсутствуют):
		
		Internet (Client)

	![][20]

2. В этом же файле откройте вкладку `Объявления`:
	1. В разделе `Доступные объявления` добавьте `Сопоставления типов файлов`
	2. Затем в разделе `Свойства` справа задайте в поле `Имя` значение `engagement_log_file`.
	3. Затем в разделе `Свойства` справа задайте в поле `Тип файла` значение `.set`.
	4. Наконец в разделе `Доступные объявления` добавьте `Средство обновления кэшированных файлов`.

		![][21]
 
3. Вернитесь на страницу портала Azure **Информация о подключении** для вашего приложения и скопируйте **строку подключения**.

	![][11]

4. Вставьте ее в файл `Resources\EngagementConfiguration.xml` между тегами `<connectionString>` и `</connectionString>` :

	![][22]

5. В файле `App.xaml.cs`:
	1. Добавьте оператор `using`.

			using Microsoft.Azure.Engagement;

	2. Инициализируйте пакет SDK в методе `OnLaunched`:
			
			protected override void OnLaunched(LaunchActivatedEventArgs args)
			{
			  EngagementAgent.Instance.Init(args);
			
			  //... rest of the code
			}

	3. Вставьте следующий код в `Application_Activated` (эту операцию нужно добавить, если ее еще нет).

			protected override void OnActivated(IActivatedEventArgs args)
			{
			  EngagementAgent.Instance.Init(args);
			
			  //... rest of the code
			}

###Отправка экрана в Mobile Engagement

Чтобы начать отправку данных и убедиться, что пользователи активны, отправьте по крайней мере один экран (действие) в серверную часть Mobile Engagement. Для этого можно создать подкласс `MainPage` с помощью `EngagementPage` в пакете SDK для Mobile Engagement.

1. Чтобы сделать это, замените суперкласс `MainPage`, который расположен перед `Page`, с помощью EngagementPage, как показано ниже:

	![][23]

	>[AZURE.NOTE] Не забудьте разрешить класс, если он подчеркнут красным цветом, добавив `using Microsoft.Azure.Engagement;` в предложениях `using`.

2. В файле `MainPage.xaml`:
	1. Добавьте в объявления пространств имен:

			xmlns:engagement="using:Microsoft.Azure.Engagement"
	2. В имени тега XML замените `Страница` на `engagement:EngagementPage`

##<a id="monitor"></a>Как проверить, что приложение подключено с помощью функции мониторинга в реальном времени

В этом разделе показано, как сделать так, чтобы приложение подключалось к серверной части Mobile Engagement с помощью функции мониторинга в реальном времени.

1. Перейдите на портал Mobile Engagement.

	На портале Azure убедитесь, что вы используете требуемое приложение для этого проекта, и нажмите кнопку **Использование** внизу.

	![][26]

2. Вы перейдете на страницу параметров вашего приложения на портале Engagement. На этой странице щелкните вкладку **Монитор**, как показано ниже.
![][30]

3. Здесь в режиме реального времени может отобразиться любое устройство, которое запустит ваше приложение.
![][31]

4. Чтобы запустить приложение в Visual Studio в эмуляторе или в подключенном устройстве, щелкните зеленый треугольник и выберите устройство.

5. Если вы выполнили все приведенные выше действия, в разделе "Монитор" отобразится один сеанс. 
![][33]

**Поздравляем!** Вы успешно выполнили первый шаг этого учебника, суть которого заключалась в подключении приложения к серверной части Mobile Engagement, которая уже отправляет данные.


##<a id="integrate-push"></a>Включение push-уведомлений

Mobile Engagement позволяет взаимодействовать и связываться с пользователями с помощью push-уведомлений и сообщений в приложении в контексте кампаний. На портале Mobile Engagement этот модуль называется МОДУЛЕМ ОБРАБОТКИ РЕКЛАМНЫХ КАМПАНИЙ.
В следующих разделах описаны действия по настройке приложения для приема уведомлений и сообщений.

###Настройка приложения для приема push-уведомлений WNS

1. Добавьте новое `сопоставление типов файлов` в свой файл `Package.appxmanifest` на панели "Объявления":

		Name: engagement_reach_content
		File type: .txt

	![][34]

2. В том же файле `Package.appxmanifest` на вкладке `Application` в разделе `Уведомления` выберите `Да` для параметра `Всплывающие уведомления:`:

	![][35]

###Запуск пакета SDK для REACH

1. В `App.xaml.cs` вызовите `EngagementReach.Instance.Init(args);` в функции `OnLaunched` сразу после инициализации агента:

		protected override void OnLaunched(LaunchActivatedEventArgs args)
		{
		   EngagementAgent.Instance.Init(args);
		   EngagementReach.Instance.Init(args);
		}

2. В `App.xaml.cs` вызовите `EngagementReach.Instance.OnActivated(args);` в функции `Application_Activated` сразу после инициализации агента:

		protected override void OnActivated(IActivatedEventArgs args)
		{
		   EngagementAgent.Instance.OnActivated(args);
		   EngagementReach.Instance.OnActivated(args);
		}

3. Запустите приложение. 
Все готово к отправке всплывающего уведомления. Осталось только проверить, правильно ли выполнена эта базовая интеграция.

###Предоставление доступа к Mobile Engagement для отправки уведомлений

1. Затем необходимо связать приложение с Магазином Windows, чтобы получить `Package security identifier (SID)` и `секретный ключ` (*секрет клиента*).

2. После этого перейдите к параметрам портала Mobile Engagement и щелкните раздел `Системное push-уведомление` слева.

3. Нажмите кнопку `Изменить`, чтобы ввести свой `идентификатор безопасности пакета (SID)` и `секретный ключ` как показано ниже:

	![][36]

##<a id="send"></a>Отправка уведомления в приложение

Теперь мы создадим простую кампанию push-уведомлений, которая будет отправлять push-уведомления в приложение.

1. Перейдите на вкладку **РЕКЛАМНАЯ КАМПАНИЯ** на портале Mobile Engagement.
2. Щелкните **Создать объявление**, чтобы создать кампанию push-уведомлений.
![][37]

3. Выполните следующие шаги для настройки кампании:
![][38]

	1. Присвойте кампании любое имя.
	2. Выберите для параметра **Время доставки** значение *Любое*, чтобы приложение могло принимать уведомления независимо от того, запущено оно или нет.
	3. В тексте уведомления введите заголовок, которой будет отображаться полужирным шрифтом в push-уведомлении.
	4. Затем введите текст сообщения.

4. Прокрутите вниз и выберите в разделе содержимого **Только уведомления**.
![][39]

5. Вы закончили настройку самой простой кампании. Теперь снова прокрутите вниз, создайте кампанию и сохраните ее.
![][40]

6. И, наконец, активируйте свою кампанию.
![][41]

На вашем устройстве отобразится уведомление **Поздравляем!**.



<!-- URLs. -->
[Документация по пакету SDK для Магазина Windows для Mobile Engagement]: ../mobile-engagement-windows-store-integrate-engagement/
[Пакет SDK для Магазина Windows для Mobile Engagement]: http://go.microsoft.com/?linkid=9864592

<!-- Images. -->
[7]: ./media/mobile-engagement-windows-store-get-started/create-mobile-engagement-app.png
[8]: ./media/mobile-engagement-windows-store-get-started/aux-create-app.png
[9]: ./media/mobile-engagement-windows-store-get-started/aux-select-app.png
[10]: ./media/mobile-engagement-windows-store-get-started/app-main-page-select-connection-info.png
[11]: ./media/mobile-engagement-windows-store-get-started/app-connection-info-page.png
[12]: ./media/mobile-engagement-windows-phone-get-started/new-project.png
[13]: ./media/mobile-engagement-windows-store-get-started/project-properties.png
[17]: ./media/mobile-engagement-windows-phone-get-started/manage-nuget-settings-new-source.png
[18]: ./media/mobile-engagement-windows-store-get-started/manage-nuget-settings-new-source-add-lib.png
[19]: ./media/mobile-engagement-windows-store-get-started/manage-nuget-settings-new-source-added.png
[20]: ./media/mobile-engagement-windows-store-get-started/manifest-capabilities.png
[21]: ./media/mobile-engagement-windows-store-get-started/manifest-declarations.png
[22]: ./media/mobile-engagement-windows-store-get-started/add-connection-info.png
[23]: ./media/mobile-engagement-windows-store-get-started/subclass-page.png
[26]: ./media/mobile-engagement-windows-store-get-started/engage-button.png
[27]: ./media/mobile-engagement-windows-store-get-started/engagement-portal.png

[28]: ./media/mobile-engagement-windows-store-get-started/native-push-settings.png
[29]: ./media/mobile-engagement-windows-store-get-started/api-key.png
[30]: ./media/mobile-engagement-windows-store-get-started/clic-monitor-tab.png
[31]: ./media/mobile-engagement-windows-store-get-started/monitor.png
[32]: ./media/mobile-engagement-windows-store-get-started/launch.png
[33]: ./media/mobile-engagement-windows-phone-get-started/monitor.png
[34]: ./media/mobile-engagement-windows-store-get-started/manifest-declarations-reach.png
[35]: ./media/mobile-engagement-windows-store-get-started/manifest-toast.png
[36]: ./media/mobile-engagement-windows-store-get-started/enter-credentials.png
[37]: ./media/mobile-engagement-windows-store-get-started/new-announcement.png
[38]: ./media/mobile-engagement-windows-phone-get-started/campaign-first-params.png
[39]: ./media/mobile-engagement-windows-store-get-started/campaign-content.png
[40]: ./media/mobile-engagement-windows-store-get-started/campaign-create.png
[41]: ./media/mobile-engagement-windows-store-get-started/campaign-activate.png

<!--HONumber=47-->
