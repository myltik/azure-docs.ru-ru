<properties 
	pageTitle="Подключение каталогов с помощью Azure AD Connect"
	description="Описание пользовательских параметров каталогов, подключенных с помощью Azure AD Connect."
	services="active-directory"
	documentationCenter=""
	authors="billmath"
	manager="stevenpo"
	editor="curtand"/>

<tags 
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/24/2015"
	ms.author="billmath"/>



# Подключение каталогов с помощью Azure AD Connect

Если выбраны пользовательские настройки, для подключения к вашим лесам Active Directory не нужна учетная запись администратора предприятия. В этом случае в мастере можно указать учетную запись локального пользователя или домена. Учетная запись используется в качестве локальной учетной записи соединителя AD, которая считывает и записывает данные каталога для синхронизации.

Это означает, что для нее необходимо назначить дополнительные разрешения, которые позволят реализовать следующие сценарии.

Сценарий |Разрешение
------------- | ------------- |
Синхронизация паролей| <li>Реплицирование изменений каталога.</li> <li>Реплицирование всех изменений каталога.</li>
Гибридное развертывание Exchange|См. раздел [Атрибуты и разрешения обратной записи AAD Sync для гибридного развертывания в Office 365 и Exchange](https://msdn.microsoft.com/library/azure/dn757602.aspx#exchange).
Обратная запись паролей | <li>Изменение паролей.</li><li>Сброс паролей.</li>
Обратная запись пользователей, групп и устройств|Разрешения на запись в объекты и атрибуты каталога, которые вы хотите «записать обратно».
Единый вход и AD FS| Разрешения администратора домена в том домене, где расположены ваши федеративные серверы.





**Дополнительные ресурсы**

* [Дополнительные сведения об учетных записях и разрешениях Azure AD Connect](active-directory-aadconnect-account-summary.md)
* [Разрешения для синхронизации паролей](https://msdn.microsoft.com/library/azure/dn757602.aspx#psynch)
* [Разрешения для гибридного развертывания Exchange](https://msdn.microsoft.com/library/azure/dn757602.aspx#exchange)
* [Разрешения для обратной записи паролей](https://msdn.microsoft.com/library/azure/dn757602.aspx#pwriteback)
* [Выборочная установка Azure AD Connect](active-directory-aadconnect-get-started-custom.md)
* [Azure AD Connect на портале MSDN](active-directory-aadconnect.md)
 

<!---HONumber=August15_HO9-->