<properties 
	pageTitle="Как обнаруживать несанкционированные облачные приложения, которые используются в моей организации" 
	description="В этой статье описан агент Cloud App Discovery и его назначение." 
	services="active-directory" 
	documentationCenter="" 
	authors="markusvi" 
	manager="swadhwa" 
	editor="lisatoft"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/23/2015" 
	ms.author="markusvi"/>

# Как обнаруживать несанкционированные облачные приложения, которые используются в моей организации

На современных предприятиях ИТ-специалисты часто не знают обо всех облачных приложениях, при помощи которых пользователи выполняют свои задачи. В результате администраторы часто сталкиваются с проблемами несанкционированного доступа к корпоративным данным, возможной утечки данных и другими угрозами безопасности, характерными для приложений. Так как они не знают, сколько и каких именно приложений используется, даже планирование работы с этими рисками кажется сложной задачей.

Эти проблемы можно решить с помощью Cloud App Discovery. Cloud App Discovery — это расширенная функция службы Azure Active Directory, позволяющая обнаруживать облачные приложения, которые используют сотрудники организации.


**С помощью Cloud App Discovery вы сможете:**

* Обнаруживать используемые приложения и оценивать их использование по числу пользователей, объему трафика или числу веб-запросов к приложению. 
* Идентифицировать пользователей, использующих приложение. 
* Экспортировать данные для дополнительного автономного анализа. 
* Определять приоритетные приложения, которые необходимо взять под контроль ИТ-подразделения, и легко интегрировать приложения, что даст возможность использовать единый вход и управлять пользователями. 

При использовании Cloud App Discovery извлечение данных выполняют агенты, запущенные на компьютерах в вашей среде. Информация об использовании приложения, записанная агентом, отправляется через безопасный зашифрованный канал в службу Cloud App Discovery. Служба Cloud App Discovery оценивает данные и создает отчеты, которые можно использовать для анализа среды.


<center>![How Cloud App Discovery Works](./media/active-directory-cloudappdiscovery/cad01.png)</center>

##Дальнейшие действия


* Дополнительную информацию о работе Cloud App Discovery см. в статье [Начало работы с Cloud App Discovery](http://social.technet.microsoft.com/wiki/contents/articles/30962.getting-started-with-cloud-app-discovery.aspx) 
* Вопросы конфиденциальности и безопасности см. в статье [Вопросы безопасности и конфиденциальности Cloud App Discovery](active-directory-cloudappdiscovery-security-and-privacy-considerations.md) 
* Дополнительные сведения о развертывании агента Cloud App Discovery в корпоративной среде со следующими компонентами: 
 * управление групповой политикой Active Directory — см. статью [Руководство по развертыванию групповой политики Cloud App Discovery](http://social.technet.microsoft.com/wiki/contents/articles/30965.cloud-app-discovery-group-policy-deployment-guide.aspx); 
 * Microsoft System Center Configuration Manager — см. статью [Руководство по развертыванию Cloud App Discovery с помощью System Center](http://social.technet.microsoft.com/wiki/contents/articles/30968.cloud-app-discovery-system-center-deployment-guide.aspx); 
 * прокси-серверы с пользовательскими портами — см. статью [Параметры реестра Cloud App Discovery для прокси-серверов с пользовательскими портами](active-directory-cloudappdiscovery-registry-settings-for-proxy-services.md). 





**Дополнительные ресурсы**


* [Cloud App Discovery: журнал изменений агента](http://social.technet.microsoft.com/wiki/contents/articles/24616.cloud-app-discovery-agent-changelog.aspx)
* [Cloud App Discovery: вопросы и ответы](http://social.technet.microsoft.com/wiki/contents/articles/24037.cloud-app-discovery-frequently-asked-questions.aspx)

<!---HONumber=Oct15_HO3-->