<properties title="Azure RemoteApp FAQ" pageTitle="Azure RemoteApp: вопросы и ответы" description="Узнайте об удаленном приложении Azure RemoteApp." metaKeywords="" services="" solutions="" documentationCenter="" authors="elizapo" manager="mbaldwin" />

<tags ms.service="remoteapp" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="12/12/2014" ms.author="elizapo" ms.manager="mbaldwin" />

#Azure RemoteApp: вопросы и ответы
Ниже приведены вопросы, которые нам задавали о Azure RemoteApp. У вас есть другие вопросы? Посетите [форумы RemoteApp](https://social.msdn.microsoft.com/Forums/azure/ru-ru/home?forum=AzureRemoteApp) и сообщите нам, что вы хотели бы узнать.

##Что такое Azure RemoteApp?##


- **Что такое Azure RemoteApp?** RemoteApp - это служба Azure, которая предоставляет функции локальной службы Microsoft RemoteApp на основе служб удаленных рабочих столов из Azure. RemoteApp помогает предоставлять безопасный удаленный доступ к приложениям со многих пользовательских устройств. Подробнее об [Azure RemoteApp](http://azure.microsoft.com/ru-ru/documentation/articles/remoteapp-whatis/).
- **Какие два варианта развертывания доступны?** Существует два типа развертываний (или коллекций) RemoteApp: облачные и гибридные. Узнайте, какой [вариант развертывания](http://azure.microsoft.com/ru-ru/documentation/articles/remoteapp-whatis/) лучше подходит для вашей организации.

##Поддерживаемые конфигурации##


- **Поддерживаются ли настраиваемые бизнес-приложения?** Да. Чтобы использовать настраиваемое приложение в Azure RemoteApp, создайте [настраиваемый образ шаблона](http://azure.microsoft.com/ru-ru/documentation/articles/remoteapp-create-custom-image/) и отправьте его в коллекцию RemoteApp.
- **Будут ли настраиваемые бизнес-приложения работать в Azure RemoteApp?** Лучший способ узнать это - протестировать приложение. Изучите [требования к совместимости приложений](http://www.microsoft.com/ru-ru/download/details.aspx?id=18704) и посетите [Центр совместимости удаленных рабочих столов](http://www.rdcompatibility.com/compatibility/default.aspx).
- **Какой метод развертывания (облачное или гибридное) лучше подходит для моей организации?** Гибридные коллекции обеспечивают наиболее полные возможности, если вам необходима полная интеграция с единым входом (SSO) и защищенным подключением к локальной сети. Облачные коллекции предоставляют гибкий и простой способ изолировать развертывание с использованием нескольких методов проверки подлинности. Подробнее о [вариантах развертывания](http://azure.microsoft.com/ru-ru/documentation/articles/remoteapp-whatis/).
- **Для гибридной коллекции необходима виртуальная сеть. Можно ли использовать имеющуюся сеть?** В настоящий момент это невозможно, но мы знаем, что многих интересует эта возможность. Пока мы работаем над ее внедрением, вы можете подключить имеющуюся виртуальную сеть к виртуальной сети Azure RemoteApp, следуя [этим указаниям](http://blogs.msdn.com/b/rds/archive/2014/07/21/how-to-link-azure-remoteapp-to-an-existing-vnet.aspx).
- **Можно ли использовать облако или имеющуюся виртуальную машину в качестве шаблона коллекции RemoteApp?** В настоящий момент это невозможно, но это популярный запрос на нашем [сайте обратной связи](http://feedback.azure.com/forums/247748-azure-remoteapp/suggestions/5923679-allow-creation-of-custom-hybrid-image-completely-w).
- **У нас есть база данных SQL или другая база данных в локальной среде или Azure. Какой тип развертывания следует использовать?** Это зависит от расположения базы данных SQL или тыловой базы данных. Если база данных находится в частной сети, используйте гибридную коллекцию. Если база данных доступна из Интернета и к ней могут подключаться клиенты, вы можете использовать облачную коллекцию.
- **Поддерживаются ли сопоставление дисков, USB и последовательный порт, совместное использование буфера обмена и перенаправление принтеров?** Все эти функции поддерживаются в Azure RemoteApp. Совместное использование буфера обмена и перенаправление принтеров включены по умолчанию. [Свяжитесь с нами](mailto:remoteappforum@microsoft.com?subject=Azure%20RemoteApp%20enable%20Redirection%20request), чтобы включить сопоставление дисков либо перенаправление USB или последовательного порта (мы работаем над добавлением этих функций на портал, но еще не все готово).
- **Как насчет проверки подлинности? Какие методы поддерживаются?** Облачная коллекция поддерживает учетные записи Майкрософт и Azure Active Directory, которые также являются учетными записями Office 365. Гибридная коллекция поддерживает только те учетные записи Azure Active Directory, которые были синхронизированы (с помощью такого средства, как DirSync) из развертывания Windows Server Active Directory. В частности, они могут быть синхронизированы с помощью параметра "Синхронизация паролей" или служб федерации Active Directory (AD FS). Вы также можете настроить [многофакторную проверку подлинности (MFA)](http://azure.microsoft.com/ru-ru/documentation/services/multi-factor-authentication/).

	**Примечание.** Пользователи Azure Active Directory должны относиться к клиенту, связанному с вашей подпиской. Вы можете просматривать и изменять свою подписку на вкладке **Параметры** на портале. Дополнительные сведения см. в статье [Изменение клиента Azure Active Directory, используемого службой RemoteApp](http://msdn.microsoft.com/ru-ru/3d6c4fd1-c981-4c57-9402-59fe31b11883).

- **Почему не удается предоставить доступ учетной записи Azure Active Directory?** Пользователи Azure Active Directory должны относиться к каталогу, связанному с вашей подпиской. Вы можете просматривать и изменять этот каталог на вкладке "Параметры" на портале. Дополнительные сведения см. в статье [Изменение клиента Azure Active Directory, используемого службой RemoteApp](http://msdn.microsoft.com/ru-ru/3d6c4fd1-c981-4c57-9402-59fe31b11883).
- **Какие устройства и операционные системы поддерживают клиентские приложения?** Клиентские приложения доступны для Windows 8.1, Windows 8, Windows 7 с пакетом обновления 1, iOS, Mac OS X, Windows RT, устройств Android и Windows Phone. Кроме того, поддерживается предварительная версия Windows 10.
 
	[Скачайте](https://www.remoteapp.windowsazure.com/ClientDownload/AllClients.aspx) клиент RemoteApp.
- **Поддерживает ли Azure RemoteApp тонкие клиенты?** Да, поддерживаются следующие тонкие клиенты Windows Embedded:
	- Windows Embedded Standard 7 с пакетом обновления 1
	- Windows Embedded POSReady7 
	- Windows Embedded Thin PC 
	- Windows Embedded 8.1 Industry

- **Какая версия Windows Server поддерживается для узла сеансов удаленных рабочих столов (RDSH)?** Windows Server 2012 R2.

##Поддержка и обратная связь

- **Можно ли попробовать эту службу бесплатно?** Да. Бесплатная пробная версия доступна в течение 30 дней. По окончании пробного периода вы можете переключиться на платную учетную запись (которую можно использовать в рабочей среде) или перестать использовать службу. Начните свой пробный период, посетив сайт [manage.windowsazure.com](http://manage.windowsazure.com), - создайте новый экземпляр RemoteApp. Бесплатная пробная версия позволяет создать 2 экземпляра RemoteApp с 10 пользователями на экземпляр. Помните, что пробный период составляет 30 дней.
- **Какой план поддержки предусматривается для RemoteApp?** Поддержка по выставлению счетов и управлению подпиской предоставляется бесплатно. Техническая поддержка доступна по [планам обслуживания Azure](http://azure.microsoft.com/support/plans/). Вы также можете получить поддержку от сообщества на [форуме Azure](http://social.msdn.microsoft.com/Forums/windowsazure/ru-ru/home?forum=AzureRemoteApp). 
- **Сколько стоит RemoteApp?** См. [Подробные сведения о ценах на Azure RemoteApp ](http://azure.microsoft.com/ru-ru/pricing/details/remoteapp/).
- **Как оставить отзыв?** Посетите [форум обратной связи](http://feedback.azure.com/forums/247748-azure-remoteapp).
- **От кого можно узнать больше об Azure RemoteApp?** Помимо [форума](http://social.msdn.microsoft.com/Forums/windowsazure/ru-ru/home?forum=AzureRemoteApp), который отлично подходит, чтобы задавать вопросы, вы можете присоединиться к еженедельному [веб-семинару "Спросите экспертов"](https://azureinfo.microsoft.com/US-Azure-WBNR-FY15-11Nov-AzureRemoteAppAskTheExperts-Registration-Page.html), где мы обсуждаем любые темы, связанные с RemoteApp.
- **Существует ли документация по RemoteApp?** Хорошо, что вы спросили. Помимо справочных материалов в соответствующем разделе портала (достаточно щелкнуть значок **?** на любой странице портала), доступны следующие статьи, посвященные RemoteApp:
	- **Начало работы:**
		- [Что такое RemoteApp?](http://azure.microsoft.com/ru-ru/documentation/articles/remoteapp-whatis/)
		- [Что входит в образы шаблонов RemoteApp?](http://azure.microsoft.com/ru-ru/documentation/articles/remoteapp-images/)
		- [Как работает лицензирование?](http://azure.microsoft.com/ru-ru/documentation/articles/remoteapp-licensing/)
		- [Как RemoteApp и Office работают вместе?](http://azure.microsoft.com/ru-ru/documentation/articles/remoteapp-o365/)
	- **Развертывание:**
		- [Создание настраиваемого образа шаблона](http://azure.microsoft.com/ru-ru/documentation/articles/remoteapp-create-custom-image/)
		- [Создание гибридной коллекции](http://azure.microsoft.com/ru-ru/documentation/articles/remoteapp-create-hybrid-deployment/)
		- [Создание облачной коллекции](http://azure.microsoft.com/ru-ru/documentation/articles/remoteapp-create-cloud-deployment/)
		- [Настройка Azure Active Directory для RemoteApp](http://azure.microsoft.com/ru-ru/documentation/articles/remoteapp-ad/)
		- [Публикация приложения в RemoteApp](http://azure.microsoft.com/ru-ru/documentation/articles/remoteapp-publish/)
	- **Управление:**
		- [Добавление пользователей](http://azure.microsoft.com/ru-ru/documentation/articles/remoteapp-user/)
		- [Рекомендации по настройке и использованию RemoteApp](http://azure.microsoft.com/ru-ru/documentation/articles/remoteapp-bestpractices/)	

	Видео! У нас также есть несколько видеороликов, посвященных RemoteApp. Некоторые из них содержат вводные сведения ([Введение в Azure RemoteApp](http://azure.microsoft.com/ru-ru/documentation/videos/cloud-cover-ep-150-azure-remote-app-with-thomas-willingham-and-nihar-namjoshi/)), а другие посвящены развертыванию ([Облачное развертывание](https://www.youtube.com/watch?v=3NAv2iwZtGc&feature=youtu.be) и [Гибридное развертывание](https://www.youtube.com/watch?v=GCIMxPUvg0c&feature=youtu.be)). Смотрите сами!


<!--HONumber=35.2-->
