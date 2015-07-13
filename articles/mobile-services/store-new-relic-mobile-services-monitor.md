<properties 
	pageTitle="Хранение серверных скриптов в системе управления версиями — мобильные службы Azure" 
	description="Узнайте, как использовать надстройку New Relic для мониторинга своей мобильной службы." 
	documentationCenter="" 
	authors="stepsic-microsoft-com" 
	manager="carolz" 
	editor="" 
	services="mobile-services"/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="03/16/2015" 
	ms.author="stepsic"/>

# Использование New Relic для мониторинга мобильных служб

В этом разделе показано, как настроить стороннюю надстройку New Relic для работы с мобильными службами Azure в целях обеспечения расширенного мониторинга мобильной службы.

В этом учебнике рассмотрено выполнение следующих действий:

1. [Подписка на New Relic с использованием магазина Azure].
2. [Установка модуля New Relic].
3. [Включение аналитических средств для разработчиков New Relic для мобильной службы].
4. [Мониторинг мобильной службы в панели мониторинга New Relic].

Для прохождения данного учебника вы должны были уже создать мобильную службу в учебнике [Приступая к работе с мобильными службами] или [Приступая к работе с данными].

##<a name="sign-up"></a>Подписка на New Relic с использованием Магазина Azure

Первым шагом является покупка службы New Relic. В этом учебнике показано, как приобрести эту службу в магазине Azure. Мобильные службы поддерживают подписки New Relic, приобретенные за пределами магазина Azure.

1. Выполните вход на [портал управления Azure](https://manage.windowsazure.com).

2. В нижней части портала управления щелкните **Создать**.

3. Нажмите кнопку **Магазин**.

4. В диалоговом окне **Выберите надстройку** выберите **New Relic** и щелкните **Далее**.

5. В диалоговом окне **Настроить надстройку** выберите нужный план New Relic.

7. Введите имя службы New Relic, которое будет отображаться в параметрах Azure, или используйте значение по умолчанию **NewRelic**. Это имя должно быть уникальным в списке элементов Магазина Azure, на которые вы подписались.

8. Укажите регион, например **Запад США**.

9. Нажмите кнопку **Далее**.

10. В диалоговом окне **Просмотр приобретения** просмотрите информацию о плане и ценах, а также ознакомьтесь с условиями использования. Если вы согласны с данными условиями, щелкните **Купить**.

11. После нажатия кнопки **Купить** начнется процесс создания учетной записи New Relic. Состояние можно отслеживать на портале управления Azure.

##<a name="install-module"></a>Установка модуля New Relic

После регистрации в службе New Relic необходимо установить модуль Node.js New Relic в вашей мобильной службе. Для отправки этого модуля необходимо включить систему управления версиями для мобильной службы.

1. Если вы еще не сделали это, выполните действия, перечисленные в учебнике [Хранение кода проекта в системе управления версиями], чтобы включить систему управления версиями для своей мобильной службы, клонировать репозиторий и установить <a href="http://nodejs.org/" target="_blank">диспетчер пакетов Node (NPM)</a>.

2. Перейдите в папку `.\service` локального репозитория Git, а затем в командной строке введите следующую команду:

		npm install newrelic

	NPM устанавливает [модуль New Relic][newrelic] в подкаталог `\newrelic`.

3. Откройте программу командной строки Git, такую как **GitBash** (Windows) или **Bash** (оболочка Unix), и введите следующую команду в командной строке Git:

		$ git add .
		$ git commit -m "added newrelic module"
		$ git push origin master
		
	При этом новый модуль `newrelic` будет отправлен в вашу мобильную службу.

Далее потребуется включить мониторинг New Relic за мобильной службой в [портале управления][Azure Management Portal].

##<a name="enable-service"></a>Включение аналитических средств для разработчиков New Relic для мобильной службы

1. На [портале управления][Azure Management Portal] выберите свою мобильную службу и откройте вкладку **Настройка**.

	![][0]

2. Перейдите к расположенному ниже элементу **Аналитические средства для разработчиков** и выполните одно из следующих действий в зависимости от того, как вы купили подписку New Relic:

	+ Подписка приобретена в Магазине Azure.

		Щелкните **Надстройка**, выберите надстройку New Relic в списке **Выберите надстройку** и нажмите кнопку **Сохранить**.

		![][1]

	+ Подписка приобретена непосредственно у компании New Relic.

		Щелкните элемент **Настраиваемый**, выберите New Relic в списке **Поставщик**, введите ключ, а затем нажмите кнопку **Сохранить**.

		![][2]

		Ключ можно получить в панели мониторинга New Relic.

3. После завершения регистрации в поле **Параметры приложения** отобразится новое значение:

	![][3]

##<a name="monitor"></a>Отслеживание работы мобильной службы на панели мониторинга New Relic

1. Запустите клиентское приложение, чтобы создать запросы на чтение, создание, обновление и удаление для мобильной службы.

2. Подождите несколько минут до завершения обработки данных, а затем перейдите на панель мониторинга New Relic.

	Если подписка New Relic была приобретена в виде надстройки, выберите ее на [портале управления][Azure Management Portal] и нажмите кнопку **Управление**.

3. В New Relic щелкните **Приложения**, а затем — свою мобильную службу.

	![][4]

4. Щелкните **Веб-транзакции**, чтобы просмотреть последние запросы, выполненные для вашей мобильной службы.

	![][5]

##<a name="next-steps"> </a>Дальнейшие действия

+ Сведения об оптимизация производительности ваших мобильных приложений под управлением **iOS** или **Android** приведены на странице [New Relic Mobile].
+ Информацию по ценам см. в разделе [Страница New Relic в Магазине Azure].
+ Дополнительную информацию об использовании New Relic см. в разделе [Обзор приложений] документации по New Relic. 

<!-- Anchors. -->
[Подписка на New Relic с использованием магазина Azure]: #sign-up
[Установка модуля New Relic]: #install-module
[Включение аналитических средств для разработчиков New Relic для мобильной службы]: #enable-service
[Мониторинг мобильной службы в панели мониторинга New Relic]: #monitor
[Next steps]: #next-steps

<!-- Images. -->
[0]: ./media/store-new-relic-mobile-services-monitor/mobile-configure-tab.png
[1]: ./media/store-new-relic-mobile-services-monitor/mobile-configure-new-relic-monitoring.png
[2]: ./media/store-new-relic-mobile-services-monitor/mobile-configure-new-relic-monitoring-custom.png
[3]: ./media/store-new-relic-mobile-services-monitor/mobile-configure-new-relic-monitoring-complete.png
[4]: ./media/store-new-relic-mobile-services-monitor/mobile-new-relic-dashboard.png
[5]: ./media/store-new-relic-mobile-services-monitor/mobile-new-relic-dashboard-2.png

<!-- URLs. -->
[Source control]: http://msdn.microsoft.com/library/windowsazure/c25aaede-c1f0-4004-8b78-113708761643
[Work with server scripts in Mobile Services]: ../work-with-server-scripts.md

[Azure Management Portal]: https://manage.windowsazure.com/
[Node.js API Documentation: Modules]: http://nodejs.org/api/modules.html
[Хранение кода проекта в системе управления версиями]: /develop/mobile/tutorials/store-scripts-in-source-control/
[newrelic]: https://npmjs.org/package/newrelic
[Страница New Relic в Магазине Azure]: /gallery/store/new-relic/new-relic/
[Обзор приложений]: https://docs.newrelic.com/docs/applications-dashboards/applications-overview
[Приступая к работе с мобильными службами]: /develop/mobile/tutorials/get-started/
[Приступая к работе с данными]: /develop/mobile/tutorials/get-started-with-data-dotnet
[New Relic Mobile]: http://newrelic.com/mobile-monitoring

 

<!---HONumber=July15_HO1-->