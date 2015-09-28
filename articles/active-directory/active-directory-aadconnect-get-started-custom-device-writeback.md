<properties 
	pageTitle="Включение обратной записи устройств в службе Azure AD Connect" 
	description="В этом документе объясняется, как включить функцию обратной записи устройств с помощью службы Azure AD Connect" 
	services="active-directory" 
	documentationCenter="" 
	authors="billmath" 
	manager="msStevenPo" 
	editor="curtand"/>

<tags 
	ms.service="active-directory"  
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/15/2015"
	ms.author="billmath"/>

# Включение обратной записи устройств в службе Azure AD Connect

Следующая документация содержит сведения о том, как включить функцию обратной записи устройств в службе Azure AD Connect. Обратная запись устройств используется в ситуациях,

когда необходимо включить условный доступ на основе устройств служб ADFS (2012 R2 или более поздней версии) к защищенным приложениям (отношения доверия с проверяющей стороной).

Это обеспечивает дополнительную защиту и гарантию того, что доступ к приложениям предоставляется только доверенным устройствам. Дополнительные сведения о настройке условного доступа см. в статьях [Управление рисками с помощью условного доступа](active-directory-conditional-access.md) и [Настройка локального условного доступа с помощью регистрации устройств в Azure Active Directory](https://msdn.microsoft.com/library/azure/dn788908.aspx).

>[AZURE.Note]При использовании устройств, зарегистрированных в политиках условного доступа службы регистрации устройств Azure Active Directory, требуется подписка на Office 365 или Azure AD Premium. Сюда относятся политики, применяемые службами федерации Active Directory к локальным ресурсам.

## Часть 1. Подготовка Azure AD Connect
Выполните следующие действия для подготовки к использованию функции обратной записи устройств.

1.	На компьютере, где устанавливается служба Azure AD Connect, запустите PowerShell с повышенными правами.

2.	Если модуль Active Directory PowerShell НЕ установлен, установите его с помощью следующей команды.

	Install-WindowsFeature –Name AD-DOMAIN-Services –IncludeManagementTools

3.	Используя учетные данные администратора предприятия, выполните следующие команды и выйдите из PowerShell.

	Import-Module ‘C:\\Program Files\\Microsoft Azure Active Directory Connect\\AdPrep\\AdSyncAdPrep.psm1’

	Initialize-ADSyncDeviceWriteback –DomainName <name> -AdConnectorAccount <account>

Описание:



- Если контейнеры и объекты отсутствуют, функция создает и настраивает их в разделах CN=Device Registration Configuration,CN=Services,CN=Configuration,<forest-dn>.



- Если контейнеры и объекты отсутствуют, функция создает и настраивает их в разделе CN=RegisteredDevices,<domain-dn>. Объекты устройства будут созданы в этом контейнере.



- Задает необходимые разрешения для учетной записи Azure AD Connector для управления устройствами в Active Directory.



- Предполагает выполнение только в одном лесу, даже если служба Azure AD Connect устанавливается в нескольких лесах.

Параметры


- DomainName: домен Active Directory, в котором будут созданы объекты устройства. Примечание: все устройства для отдельно взятого леса Active Directory будут созданы в одном домене. 


- AdConnectorAccount: учетная запись Active Directory, которая будет использоваться службой Azure AD Connect для управления объектами в каталоге.

## Часть 2. Включение обратной записи устройства
Используйте следующую процедуру, чтобы включить функцию обратной записи устройств в службе Azure AD Connect.

1.	Запустите мастер AAD Connect. Если мастер используется впервые, выполните выборочную установку, нажав кнопку «Настроить» на экране стандартных параметров. ![Выборочная установка](./media/active-directory-aadconnect-get-started-custom-device-writeback/devicewriteback1.png)
2.	Если мастер используется повторно, выберите настройки параметров синхронизации на странице дополнительных задач и нажмите кнопку «Далее». ![Выборочная установка](./media/active-directory-aadconnect-get-started-custom-device-writeback/devicewriteback2.png)
3.	На странице дополнительных функций параметр «Обратная запись устройств» станет активным. Обратите внимание: если подготовительные шаги по настройке службы Connect Azure AD не завершены, параметр «Обратная запись устройств» на странице дополнительных функций будет оставаться неактивным. Установите флажок «Обратная запись устройств» и нажмите кнопку «Далее». ![Обратная запись устройств](./media/active-directory-aadconnect-get-started-custom-device-writeback/devicewriteback3.png)
4.	На странице обратной записи вы увидите указанный домен в качестве леса обратной записи устройств по умолчанию. ![Выборочная установка](./media/active-directory-aadconnect-get-started-custom-device-writeback/devicewriteback4.png)
5.	Завершите установку в мастере, не изменяя другие параметры. При необходимости см. статью [Выборочная установка Azure AD Connect](active-directory-aadconnect-get-started-custom.md).



## Включение условного доступа
Подробные инструкции о включении условного доступа см. в статье [Настройка локальной регистрации устройств Azure Active Directory с помощью условного доступа](https://msdn.microsoft.com/library/azure/dn788908.aspx).

## Проверка синхронизации устройств с Active Directory
Функция обратной записи устройств должна теперь работать правильно. Имейте в виду, что обратная запись объектов устройств в AD может выполняться до 3 часов. Проверить, правильно ли синхронизированы устройства после выполнения правила синхронизации, можно так.
 
1.	Запустите центр администрирования Active Directory. 
2.	Разверните раздел RegisteredDevices в домене, который включается в федерацию. ![Выборочная установка](./media/active-directory-aadconnect-get-started-custom-device-writeback/devicewriteback5.png)
3.	Там будут перечислены зарегистрированные в настоящий момент устройства. 

![Выборочная установка](./media/active-directory-aadconnect-get-started-custom-device-writeback/devicewriteback6.png)

## Дополнительная информация 


- [Управление рисками с помощью условного доступа](active-directory-conditional-access.md)
- [Настройка локального условного доступа с помощью регистрации устройств в Azure Active Directory](https://msdn.microsoft.com/library/azure/dn788908.aspx)

<!---HONumber=Sept15_HO3-->