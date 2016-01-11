<properties
	pageTitle="Azure AD Connect: включение обратной записи устройств | Microsoft Azure"
	description="В этом документе объясняется, как включить функцию обратной записи устройств с помощью службы Azure AD Connect"
	services="active-directory"
	documentationCenter=""
	authors="billmath"
	manager="StevenPo"
	editor="curtand"/>

<tags
	ms.service="active-directory"  
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/18/2015"
	ms.author="billmath;andkjell"/>

# Включение обратной записи устройств в службе Azure AD Connect

Следующая документация содержит сведения о том, как включить функцию обратной записи устройств в службе Azure AD Connect. Обратная запись устройств используется в ситуациях,

когда необходимо включить условный доступ на основе устройств служб ADFS (2012 R2 или более поздней версии) к защищенным приложениям (отношения доверия с проверяющей стороной).

Это обеспечивает дополнительную защиту и гарантию того, что доступ к приложениям предоставляется только доверенным устройствам. Дополнительные сведения о настройке условного доступа см. в статьях [Управление рисками с помощью условного доступа](active-directory-conditional-access.md) и [Настройка локального условного доступа с помощью регистрации устройств в Azure Active Directory](https://msdn.microsoft.com/library/azure/dn788908.aspx).

>[AZURE.NOTE]Для обратной записи устройств требуется подписка Azure AD Premium.

.

>[AZURE.IMPORTANT]Устройства должны находиться в том же лесу, что и пользователи. Поскольку обратная запись устройств должна производиться в один лес, эта функция в настоящий момент не поддерживает развертывание с несколькими лесами пользователя.

## Часть 1. Установка Azure AD Connect
1. Установите Azure AD Connect с использованием стандартных или пользовательских параметров. Перед включением обратной записи устройства рекомендуется выполнить синхронизацию всех пользователей и групп.

## Часть 2. Подготовка Active Directory
Выполните следующие действия для подготовки к использованию функции обратной записи устройств.

1.	На компьютере, где устанавливается служба Azure AD Connect, запустите PowerShell с повышенными правами.

2.	Если модуль Active Directory PowerShell НЕ установлен, установите его с помощью следующей команды.

	`Install-WindowsFeature –Name AD-DOMAIN-Services –IncludeManagementTools`

3.	Используя учетные данные администратора предприятия, выполните следующие команды и выйдите из PowerShell.

	`Import-Module 'C:\Program Files\Microsoft Azure Active Directory Connect\AdPrep\AdSyncPrep.psm1'`

	`Initialize-ADSyncDeviceWriteback {Optional:–DomainName [name] Optional:-AdConnectorAccount [account]}`


![PowerShell](./media/active-directory-aadconnect-get-started-custom-device-writeback/powershell.png)

Описание:

- Если контейнеры и объекты отсутствуют, функция создает и настраивает их в разделах CN=Device Registration Configuration,CN=Services,CN=Configuration,[forest-dn].
- Если контейнеры и объекты отсутствуют, функция создает и настраивает их в разделе CN=RegisteredDevices,[domain-dn]. Объекты устройства будут созданы в этом контейнере.
- Задает необходимые разрешения для учетной записи Azure AD Connector для управления устройствами в Active Directory.
- Предполагает выполнение только в одном лесу, даже если служба Azure AD Connect устанавливается в нескольких лесах.

Параметры

- DomainName: домен Active Directory, в котором будут созданы объекты устройства. Примечание: все устройства для отдельно взятого леса Active Directory будут созданы в одном домене.
- AdConnectorAccount: учетная запись Active Directory, которая будет использоваться службой Azure AD Connect для управления объектами в каталоге. Это учетная запись, используемая Azure AD Connect Sync для подключения к AD. При установке с помощью стандартных параметров это учетная запись с префиксом MSOL\_.

## Часть 3. Включение обратной записи устройств в Azure AD Connect
Используйте следующую процедуру, чтобы включить функцию обратной записи устройств в службе Azure AD Connect.

1.	Снова запустите мастер установки. Выберите **Настроить параметры синхронизации** на странице "Дополнительные задачи" и нажмите кнопку **Далее**. ![Выборочная установка](./media/active-directory-aadconnect-get-started-custom-device-writeback/devicewriteback2.png)
2.	На странице дополнительных функций параметр «Обратная запись устройств» станет активным. Обратите внимание: если подготовительные шаги по настройке службы Connect Azure AD не завершены, параметр «Обратная запись устройств» на странице дополнительных функций будет оставаться неактивным. Установите флажок для обратной записи устройства и нажмите кнопку **Далее**. Если флажок по-прежнему отключен, обратитесь к разделу [Устранение неполадок](#the-writeback-checkbox-is-still-disabled). ![Обратная запись устройств](./media/active-directory-aadconnect-get-started-custom-device-writeback/devicewriteback3.png)
3.	На странице обратной записи вы увидите указанный домен в качестве леса обратной записи устройств по умолчанию. ![Выборочная установка](./media/active-directory-aadconnect-get-started-custom-device-writeback/devicewriteback4.png)
4.	Завершите установку в мастере, не изменяя другие параметры. При необходимости см. статью [Выборочная установка Azure AD Connect](active-directory-aadconnect-get-started-custom.md).


## Включение условного доступа
Подробные инструкции о включении этого сценария см. в статье [Настройка локальной регистрации устройств Azure Active Directory с помощью условного доступа](https://msdn.microsoft.com/library/azure/dn788908.aspx).

## Проверка синхронизации устройств с Active Directory
Функция обратной записи устройств должна теперь работать правильно. Имейте в виду, что обратная запись объектов устройств в AD может выполняться до 3 часов. Проверить, правильно ли синхронизированы устройства после выполнения правила синхронизации, можно так.

1.	Запустите центр администрирования Active Directory.
2.	Разверните раздел RegisteredDevices в домене, который включается в федерацию. ![Выборочная установка](./media/active-directory-aadconnect-get-started-custom-device-writeback/devicewriteback5.png)
3.	Там будут перечислены зарегистрированные в настоящий момент устройства.

![Выборочная установка](./media/active-directory-aadconnect-get-started-custom-device-writeback/devicewriteback6.png)

## Устранение неполадок

### Флажок обратной записи по-прежнему отключен
Если флажок для обратной записи устройства не включен, несмотря на то, что были выполнены шаги, описанные выше, с помощью описанных ниже действий вы сможете проверить то, что проверяет мастер установки перед включением флажка.

Начнем сначала:

- Убедитесь, что по крайней мере в одном лесу есть 2012R2 Windows Server. Тип объекта устройства должен быть указан.
- Если мастер установки уже запущен, то изменения не будут обнаружены. В этом случае завершите работу мастера установки и запустите его снова.
- Убедитесь, что учетная запись, указанная в сценарии инициализации, соответствует пользователю, используемому соединителем Active Directory. Чтобы это проверить, выполните следующие действия.
	- В меню "Пуск" щелкните **Служба синхронизации**.
	- Откройте вкладку **Соединители**.
	- Найдите соединитель с типом «Доменные службы Active Directory» и выберите его.
	- В разделе **Действия** выберите **Свойства**.
	- Выберите **Подключиться к лесу Active Directory**. Убедитесь, что домен и имя пользователя, указанные в этом окне, совпадают с учетной записью, указанной в сценарии. ![Учетная запись соединителя](./media/active-directory-aadconnect-get-started-custom-device-writeback/connectoraccount.png)

Проверьте конфигурацию Active Directory: –Убедитесь, что служба регистрации устройств расположена в контексте именования конфигурации ниже (CN=DeviceRegistrationService,CN=Device Registration Services,CN=Device Registration Configuration,CN=Services,CN=Configuration).

![Устранение неполадок 1](./media/active-directory-aadconnect-get-started-custom-device-writeback/troubleshoot1.png)

- Убедитесь, что имеется только один объект конфигурации, выполнив поиск в пространстве имен конфигурации. Если объектов несколько, удалите дубликаты.

![Устранение неполадок 2](./media/active-directory-aadconnect-get-started-custom-device-writeback/troubleshoot2.png)

- В объекте службы регистрации устройств убедитесь, что атрибут msDS-DeviceLocation присутствует и для него установлено значение. Найдите указанное расположение и убедитесь, что оно существует с типом объекта msDS-DeviceContainer.

![Устранение неполадок 3](./media/active-directory-aadconnect-get-started-custom-device-writeback/troubleshoot3.png)

![Устранение неполадок 4](./media/active-directory-aadconnect-get-started-custom-device-writeback/troubleshoot4.png)

- Убедитесь, что учетная запись, используемая соединителем Active Directory, имеет необходимые разрешения для контейнера "Зарегистрированные устройства", обнаруженного на предыдущем шаге. Разрешения для контейнера должны быть следующими:

![Устранение неполадок 5](./media/active-directory-aadconnect-get-started-custom-device-writeback/troubleshoot5.png)

- Проверьте, что у учетной записи Active Directory есть разрешения для объекта CN=Device Registration Configuration,CN=Services,CN=Configuration.

![Устранение неполадок 6](./media/active-directory-aadconnect-get-started-custom-device-writeback/troubleshoot6.png)

## Дополнительная информация
- [Управление рисками с помощью условного доступа](active-directory-conditional-access.md)
- [Настройка локального условного доступа с помощью регистрации устройств в Azure Active Directory](https://msdn.microsoft.com/library/azure/dn788908.aspx)

## Дальнейшие действия
Узнайте больше об [интеграции локальных удостоверений с Azure Active Directory](active-directory-aadconnect.md).

<!---HONumber=AcomDC_1223_2015-->