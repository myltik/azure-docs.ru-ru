<properties
	pageTitle="Azure AD Connect: вопросы и ответы | Microsoft Azure"
	description="На этой странице изложены часто задаваемые вопросы об Azure AD Connect."
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
	ms.date="10/13/2015"
	ms.author="billmath"/>

# Azure Active Directory Connect: вопросы и ответы

## Экспресс-установка

## Выборочная установка

## Сеть
**Вопрос. У меня есть брандмауэр, сетевое устройство или иное средство, ограничивающее максимальное время, в течение которого подключения могут оставаться открытыми в моей сети. Каким должно быть пороговое значение времени ожидания на стороне клиента при использовании Azure AD Connect?**

Всему сетевому программному обеспечению, физическим устройствам или прочим средствам, ограничивающим максимальное время, в течение которого подключение может оставаться открытым, следует использовать пороговое значение в 5 минут (300 секунд) для подключений между сервером, на который установлен клиент Azure AD Connect, и Azure Active Directory. Это также относится ко всем ранее выпущенным инструментам синхронизации Microsoft Identity.


**Вопрос. Что делать, если мне на электронную почту пришло сообщение с требованием продлить срок действия сертификата Office 365?**

Продлите срок действия сертификата, используя указаниям из [соответствующей статьи](active-directory-aadconnect-o365-certs.md).

## Устранение неполадок

**Вопрос. Как получить справку по Azure AD Connect?**

[Поиск в базе знаний Майкрософт](https://www.microsoft.com/ru-RU/Search/result.aspx?q=azure%20active%20directory%20connect&form=mssupport)

- В базе знаний Майкрософт можно найти технические решения распространенных проблем, связанные с поддержкой Azure AD Connect.

[Форумы по Microsoft Azure Active Directory](https://social.msdn.microsoft.com/Forums/azure/ru-RU/home?forum=WindowsAzureAD)

- [Здесь](https://social.msdn.microsoft.com/Forums/azure/ru-RU/newthread?category=windowsazureplatform&forum=WindowsAzureAD&prof=required) вы можете искать и просматривать ответы членов сообщества на технические вопросы, а также задавать собственные.


[Служба поддержки клиентов Azure AD Connect](https://manage.windowsazure.com/?getsupport=true)

- Перейдите по этой ссылке, чтобы получить поддержку на портале Azure.

<!---HONumber=Oct15_HO3-->