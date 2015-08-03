<properties 
	pageTitle="Azure AD Connect: советы по удаленному управлению Windows" 
	description="Azure AD Connect: советы по удаленному управлению Windows для работы со службами AD FS." 
	services="active-directory" 
	documentationCenter="" 
	authors="billmath" 
	manager="swadhwa" 
	editor="curtand"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/28/2015" 
	ms.author="billmath"/>

# Azure AD Connect: советы по удаленному управлению Windows


При использовании средства Azure AD Connect для развертывания служб федерации Active Directory или прокси-службы веб-приложения ознакомьтесь с приведенными ниже требованиями. Их выполнение гарантирует успешное подключение и настройку.

- Если целевой сервер входит в домен, включите удаленное управление Windows. 
	* В командном окне PSH с повышенными привилегиями выполните команду Enable-PSRemoting -force. 

- Если целевой сервер — это не входящий в домен WAP-компьютер, существует несколько дополнительных требований.
	- На целевом компьютере (WAP): 

- с помощью оснастки «Службы» убедитесь, что служба WinRM (Windows Remote Management/WS-Management) запущена;

- в командном окне PSH с повышенными привилегиями выполните команду Enable-PSRemoting -force.
	- На компьютере, где запущен мастер (если целевой компьютер не присоединен к домену или присоединен к ненадежному домену), выполните следующие действия. 

- В командном окне PSH с повышенными привилегиями выполните команду Set-Item WSMan:\localhost\Client\TrustedHosts –Value <DMZServerFQDN> -Force -Concatenate.
	- В диспетчере серверов:
		- Добавьте узел DMZ WAP в пул компьютеров (Диспетчер серверов -> Управление -> Добавление серверов -> DNS (вкладка)). 
		- В диспетчере серверов на вкладке «Все серверы» щелкните правой кнопкой мыши WAP-сервер и выберите параметр «Управлять как», введите локальные учетные данные (не домена) для WAP-компьютера. 
		- Чтобы проверить возможность удаленного подключения из PSH, в диспетчере серверов на вкладке «Все сервера» щелкните правой кнопкой мыши WAP-сервер и выберите Windows PowerShell. Должен открыться удаленный сеанс PSH для проверки возможности создания удаленных сеансов PowerShell. 

**Дополнительные ресурсы**


* [Дополнительные сведения об учетных записях и разрешениях Azure AD Connect](active-directory-aadconnect-account-summary.md)
* [Выборочная установка Azure AD Connect](active-directory-aadconnect-get-started-custom.md)
* [Azure AD Connect на портале MSDN](https://msdn.microsoft.com/library/azure/dn832695.aspx) 

<!---HONumber=July15_HO4-->