<properties 
	pageTitle="Установка агента Azure AD Connect Health для AD FS | Microsoft Azure"
	description="В этой статье по Azure AD Connect Health описывается установка агента служб федерации Active Directory (AD FS)."
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
	ms.date="08/14/2015"
	ms.author="billmath"/>






# Установка агента Azure AD Connect Health для AD FS

В этой статье описываются этапы установки и настройки агента Azure AD Connect Health для AD FS на ваших серверах.

>[AZURE.NOTE]Помните, что прежде чем вы увидите какие-либо данные в экземпляре Azure AD Connect Health, потребуется установить агент Azure AD Connect Health на целевых серверах. Обязательно выполните [эти](active-directory-aadconnect-health.md#requirements) требования, прежде чем устанавливать агент. Агент можно загрузить [здесь](http://go.microsoft.com/fwlink/?LinkID=518973).


## Установка агента
Дважды щелкните загруженный EXE-файл, чтобы начать установку агента. На первом экране щелкните «Установить».

![Проверка Azure AD Connect Health](./media/active-directory-aadconnect-health-requirements/install1.png)

После завершения установки нажмите кнопку «Настроить сейчас».

![Проверка Azure AD Connect Health](./media/active-directory-aadconnect-health-requirements/install2.png)

Это приведет к запуску командной строки и оболочки PowerShell, которая выполнит команду Register-AzureADConnectHealthADFSAgent. Вам будет предложено войти в Azure. Выполните вход.

![Проверка Azure AD Connect Health](./media/active-directory-aadconnect-health-requirements/install3.png)


После входа PowerShell продолжит выполнять команду. После завершения можно закрыть PowerShell. Настройка завершена.

На этом этапе службы должны запускаться автоматически, а агент будет выполнять мониторинг и сбор данных. Учтите, что если вы не выполнили всех необходимых предварительных требований, которые были описаны в предыдущих разделах, в окне PowerShell будут отображаться предупреждения. Обязательно выполните [эти](active-directory-aadconnect-health.md#requirements) требования, прежде чем устанавливать агент. В качестве примера этих ошибок приведен следующий снимок экрана.

![Проверка Azure AD Connect Health](./media/active-directory-aadconnect-health-requirements/install4.png)

Чтобы проверить, установлен ли агент, откройте службы и обратите внимание на следующее. Если вы выполнили настройку, эти службы должны работать. В противном случае они не запустятся, пока не будет выполнена настройка.

- Служба диагностики AD FS Azure AD Connect Health
- Служба AD FS получения ценной информации из данных о работоспособности Azure AD Connect
- Служба наблюдения AD FS Azure AD Connect Health
 
![Проверка Azure AD Connect Health](./media/active-directory-aadconnect-health-requirements/install5.png)


## Установка агента на серверах Windows Server 2008 R2

Для серверов Windows Server 2008 R2 сделайте следующее:

1. Убедитесь, что сервер работает с пакетом обновления 1 или более поздней версии.
1. Отключите IE ESC для установки агента.
1. Установите Windows PowerShell 4.0 на каждом из серверов перед установкой агента AD Health. Чтобы установить Windows PowerShell 4.0, выполните следующие действия.
 - Загрузите автономный установщик по ссылке и установите [Microsoft .NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=40779).
 - Установите интегрированную среду сценариев PowerShell (из компонентов Windows).
 - Установите [Windows Management Framework 4.0](https://www.microsoft.com/download/details.aspx?id=40855).
 - Установите на сервере Internet Explorer версии 10 или более поздней версии. Это необходимо службе работоспособности для аутентификации пользователя с учетными данными администратора Azure.
1. Дополнительную информацию об установке Windows PowerShell 4.0 на Windows Server 2008 R2 см. в вики-статье [здесь](http://social.technet.microsoft.com/wiki/contents/articles/20623.step-by-step-upgrading-the-powershell-version-4-on-2008-r2.aspx).

## Связанные ссылки

* [Azure AD Connect Health](active-directory-aadconnect-health.md)
* [Операции Azure AD Connect Health](active-directory-aadconnect-health-operations.md)
* [Использование Azure AD Connect Health с AD FS](active-directory-aadconnect-health-adfs.md)
* [Часто задаваемые вопросы об Azure AD Connect Health](active-directory-aadconnect-health-faq.md)

<!---HONumber=August15_HO9-->