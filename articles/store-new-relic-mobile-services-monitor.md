<properties urlDisplayName="Use New Relic to monitor Mobile Services" pageTitle="Хранение серверных сценариев в системе управления версиями - Мобильные службы Azure" metaKeywords="" description="Learn how to use the New Relic add-on to monitor your mobile service." metaCanonical="" disqusComments="1" umbracoNaviHide="0" documentationCenter="Mobile" title="Use New Relic to monitor Mobile Services" authors="new relic" manager="carolz" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="11/25/2014" ms.author="stepsic" />

# Использование New Relic для мониторинга мобильных служб

В этом разделе показано, как настроить стороннюю надстройку New Relic для работы с мобильными службами Azure в целях обеспечения расширенного мониторинга мобильной службы. 

В этом учебнике рассмотрено выполнение следующих действий:

1. [Подписка на New Relic с использованием магазина Azure].
2. [Установка модуля New Relic].
3. [Включение аналитических средств для разработчиков New Relic для мобильной службы].
4. [Мониторинг мобильной службы на панели мониторинга New Relic].

Для прохождения данного учебника вы должны были уже создать мобильную службу в учебнике [Приступая к работе с мобильными службами] или [Приступая к работе с данными].

##<a name="sign-up"></a>Подписка на New Relic с использованием Магазина Azure

Первым шагом является покупка службы New Relic. В этом учебнике показано, как приобрести эту службу в магазине Azure. Мобильные службы поддерживают подписки New Relic, приобретенные за пределами магазина Azure.

1. Войдите на [портал управления Azure](https://manage.windowsazure.com).

2. В нижней части портала управления щелкните **Создать**.

3. Нажмите кнопку **Магазин**.

4. В диалоговом окне **Выберите надстройку** выберите **New Relic** и щелкните **Далее**.

5. В диалоговом окне **Настроить надстройку** выберите требуемый план New Relic.

7. Введите имя службы New Relic, которое будет отображаться в параметрах Azure, или используйте значение по умолчанию **NewRelic**. Это имя должно быть уникальным в списке элементов Магазина Azure, на которые вы подписаны.

8. Выберите значение для региона, например **Запад США**.

9. Нажмите кнопку **Далее**.

10. В диалоговом окне **Просмотреть приобретение** просмотрите сведения о плане и ценах, а также ознакомьтесь с условиями. Если вы согласны с данными условиями, щелкните **Купить**.

11. После нажатия кнопки **Купить** начнется процесс создания учетной записи New Relic. Состояние можно отслеживать на портале управления Azure.

##<a name="install-module"></a>Установка модуля New Relic

После регистрации в службе New Relic необходимо установить модуль Node.js New Relic в вашей мобильной службе. Для отправки этого модуля необходимо включить систему управления версиями для мобильной службы.

1. Если вы это еще не сделали, выполните действия из учебника [Хранение серверных сценариев в системе управления версиями], чтобы включить систему управления версиями для своей мобильной службы, клонировать репозиторий и установить <a href="http://nodejs.org/" target="_blank">диспетчер пакетов Node (NPM)</a>.

2. Перейдите к папке .\service локального репозитория Git, а затем в командной строке выполните следующую команду:

		npm install newrelic

	NPM устанавливает [модуль New Relic][newrelic] в подкаталог \newrelic. 

3. Откройте программу командной строки Git, такую как **GitBash** (Windows) или **Bash** (оболочка Unix), и введите следующую команду в командной строке Git: 

		$ git add .
		$ git commit -m "added newrelic module"
		$ git push origin master
		
	При этом новый модуль newrelic будет отправлен в вашу мобильную службу. 

Далее потребуется включить мониторинг New Relic за мобильной службой на [портале управления][Azure Management Portal]. 

##<a name="enable-service"></a>Включение аналитических средств для разработчиков New Relic для мобильной службы

1. На [портале управления][Azure Management Portal] выберите мобильную службу и откройте вкладку **Настройка**.

	![][0]

2. Выполните прокрутку вниз до элемента **Аналитические средства для разработчиков** и выполните одно из следующих действий в зависимости способа приобретения подписки New Relic:

	+ Подписка приобретена в Магазине Azure.

		Щелкните **Надстройка**, выберите надстройку New Relic в списке **Выберите надстройку** и нажмите кнопку **Сохранить**.

		![][1]

	+ Подписка приобретена непосредственно у компании New Relic. 

		Щелкните элемент **Настраиваемый**, выберите New Relic в списке **Поставщик**, введите ключ, а затем нажмите кнопку **Сохранить**.

		![][2]

		Ключ можно получить в панели мониторинга New Relic.

3. После завершения регистрации вы увидите новое значение в поле **Параметры приложения**:

	![][3] 

##<a name="monitor"></a>Мониторинг мобильной службы в панели мониторинга New Relic

1. Запустите клиентское приложение, чтобы создать запросы на чтение, создание, обновление и удаление для мобильной службы.

2. Подождите несколько минут до завершения обработки данных, а затем перейдите на панель мониторинга New Relic.

	Если подписка New Relic была приобретена в виде надстройки, выберите ее на [портале управления][Azure Management Portal] и нажмите кнопку **Управление**.

3. В разделе New Relic щелкните элемент **Приложения** и выберите свою мобильную службу.

	![][4]

4. Чтобы просмотреть последние запросы, которые вы выполнили к своей мобильной службе, щелкните **Веб-транзакции**:

	![][5]

##<a name="next-steps"> </a>Дальнейшие действия

+ Чтобы оптимизировать производительность ваших мобильных приложений под управлением **iOS** или **Android**, см. раздел [New Relic Mobile].
+ Информацию по ценам см. в разделе [Страница New Relic в Магазине Azure].
+ Дополнительную информацию об использовании New Relic см. в разделе [Обзор приложений] в документации по New Relic. 

<!-- Anchors. -->
[Подписка на New Relic с использованием магазина Azure]: #sign-up
[Установка модуля New Relic]: #install-module
[Включение аналитических средств для разработчиков New Relic для мобильной службы]: #enable-service
[Мониторинг мобильной службы на панели мониторинга New Relic]: #monitor
[Дальнейшие действия]: #next-steps

<!-- Images. -->
[0]: ./media/store-new-relic-mobile-services-monitor/mobile-configure-tab.png
[1]: ./media/store-new-relic-mobile-services-monitor/mobile-configure-new-relic-monitoring.png
[2]: ./media/store-new-relic-mobile-services-monitor/mobile-configure-new-relic-monitoring-custom.png
[3]: ./media/store-new-relic-mobile-services-monitor/mobile-configure-new-relic-monitoring-complete.png
[4]: ./media/store-new-relic-mobile-services-monitor/mobile-new-relic-dashboard.png
[5]: ./media/store-new-relic-mobile-services-monitor/mobile-new-relic-dashboard-2.png

<!-- URLs. -->
[Система управления версиями]: http://msdn.microsoft.com/ru-ru/library/windowsazure/c25aaede-c1f0-4004-8b78-113708761643
[Работа с серверными сценариями в мобильных службах]: /ru-ru/develop/mobile/how-to-guides/work-with-server-scripts.md

[Портал управления Azure]: https://manage.windowsazure.com/
[Документация по API node.js. Модули]: http://nodejs.org/api/modules.html
[Хранение серверных сценариев в системе управления версиями]: /ru-ru/develop/mobile/tutorials/store-scripts-in-source-control/
[newrelic]: https://npmjs.org/package/newrelic
[Страница New Relic в Магазине Azure]: /ru-ru/gallery/store/new-relic/new-relic/
[Обзор приложений]: https://docs.newrelic.com/docs/applications-dashboards/applications-overview
[Приступая к работе с мобильными службами]: /ru-ru/develop/mobile/tutorials/get-started/
[Приступая к работе с данными]: /ru-ru/develop/mobile/tutorials/get-started-with-data-dotnet
[New Relic Mobile]: http://newrelic.com/mobile-monitoring


<!--HONumber=35.1-->
