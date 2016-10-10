<properties
	pageTitle="Включение Microsoft Windows Hello для бизнеса в организации | Microsoft Azure"
	description="Инструкции по развертыванию для включения в организации службы Microsoft Passport."
	services="active-directory"
	documentationCenter=""
	keywords="настройка Microsoft Passport, развертывание Microsoft Windows Hello для бизнеса"
	authors="markusvi"
	manager="femila"
	editor=""
	tags="azure-classic-portal"/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/28/2016"
	ms.author="femila;markvi"/>


# Включение Microsoft Windows Hello для бизнеса в организации

После подключения присоединенных к домену устройств Windows 10 к Azure Active Directory (Azure AD) выполните следующую процедуру, чтобы включить Microsoft Windows Hello для бизнеса в организации.

## Развертывание текущей версии System Center Configuration Manager 
Для развертывания сертификатов пользователей на основе ключей Windows Hello для бизнеса необходимо следующее:

- **Microsoft System Center Configuration Manager (текущая версия)**. Необходимо установить версию 1606 или более позднюю. Дополнительные сведения см. в статье [Документация для System Center Configuration Manager](https://technet.microsoft.com/library/mt346023.aspx) и [блоге рабочей группы по System Center Configuration Manager](http://blogs.technet.com/b/configmgrteam/archive/2015/09/23/now-available-update-for-system-center-config-manager-tp3.aspx).
- **Инфраструктура открытых ключей (PKI)**. Для поддержки использования сертификатов пользователей службой Microsoft Windows Hello для бизнеса необходима инфраструктура PKI. Если у вас отсутствует инфраструктура PKI или вы не хотите использовать ее для сертификатов пользователей, выполните указанные ниже действия.
 - **Развертывание контроллера домена**. Выполните развертывание нового контроллера домена с установленной сборкой 10551 (или более поздней версии) Windows Server 2016 и выполните процедуру по [установке реплики контроллера домена в существующем домене](https://technet.microsoft.com/library/jj574134.aspx) или [установке нового леса Active Directory при создании новой среды](https://technet.microsoft.com/library/jj574166). (Образы ISO доступны для скачивания на странице [Signiant Media Exchange](https://datatransfer.microsoft.com/signiant_media_exchange/spring/main?sdkAccessible=true).)

## Настройка Microsoft Windows Hello для бизнеса с помощью групповой политики в Active Directory

 Групповую политику в Active Directory Windows Server можно использовать для настройки присоединенных к домену устройств Windows 10 для подготовки учетных данных Windows Hello для бизнеса при входе пользователя в систему Windows:

1. 	Откройте диспетчер серверов и выберите **Инструменты** > **Управление групповой политикой**.
2.	В разделе "Управление групповой политикой" перейдите к узлу, соответствующему домену, для которого необходимо включить присоединение к Azure AD.
3.	Щелкните правой кнопкой мыши элемент **Объекты групповой политики** и выберите пункт **Создать**. Укажите имя объекта групповой политики, например "Включение Windows Hello для бизнеса". Нажмите кнопку **ОК**.
4.	Щелкните правой кнопкой мыши новый объект групповой политики и выберите пункт **Изменить**.
5.	Выберите **Конфигурация компьютера** > **Политики** > **Административные шаблоны** > **Компоненты Windows** > **Windows Hello для бизнеса**.
6.	Щелкните правой кнопкой мыши **Enable Windows Hello for Business** (Включить Windows Hello для бизнеса), а затем выберите пункт **Изменить**.
7.	Установите переключатель в положение **Включено**, а затем щелкните **Применить**. Нажмите кнопку **ОК**.
8.	Теперь вы можете связать объект групповой политики с выбранным расположением. Чтобы включить эту политику для всех присоединенных к домену устройств Windows 10 в организации, свяжите групповую политику с доменом. Например:
 - с определенным подразделением в Active Directory, в котором будут размещаться компьютеры Windows 10, присоединенные к домену;
 - с определенной группой безопасности, содержащей присоединенные к домену компьютеры Windows 10, для которых будет выполняться автоматическая регистрация в Azure AD.


## Настройка Windows Hello для бизнеса с помощью System Center Configuration Manager

System Center Configuration Manager можно использовать только для развертывания политик Windows Hello для бизнеса для Windows 10 версии 1607. Запустите файл winver на компьютере с ОС Windows 10, если необходимо узнать его версию.

1. Откройте **System Center Configuration Manager** и перейдите к разделу **Assets & Compliance > Параметры соответствия > Доступ к ресурсам компании > Профили Windows Hello для бизнеса** (Ресурсы и соответствие).

	![Настройка Windows Hello для бизнеса](./media/active-directory-azureadjoin-passport-deployment/01.png)


2. На панели инструментов в верхней части щелкните **Создать профиль Windows Hello для бизнеса**.

	![Настройка Windows Hello для бизнеса](./media/active-directory-azureadjoin-passport-deployment/02.png)

2. В диалоговом окне **Общие** выполните следующее:

	![Настройка Windows Hello для бизнеса](./media/active-directory-azureadjoin-passport-deployment/03.png)

	а. В текстовом поле **Имя** введите имя профиля, например **Мой профиль WHfB**.

	b. Нажмите кнопку **Далее**.


2. В диалоговом окне **Поддерживаемые платформы** выберите платформы, которые будут подготовлены с помощью этого профиля Windows Hello для бизнеса, и нажмите кнопку **Далее**.

	![Настройка Windows Hello для бизнеса](./media/active-directory-azureadjoin-passport-deployment/04.png)


2. В диалоговом окне **Параметры** выполните следующее:

	![Настройка Windows Hello для бизнеса](./media/active-directory-azureadjoin-passport-deployment/05.png)

	а. Для параметра **Configure Windows Hello for Business** (Настройка Windows Hello для бизнеса) выберите значение **Включено**.

	b. Для параметра **Use a Trusted Platform Module (TPM)** (Использование доверенного платформенного модуля (TPM)) выберите **Требуется**.

	c. Для параметра **Способ проверки подлинности** выберите значение "На основе сертификата"**.

	г) Нажмите кнопку **Далее**.



2. В диалоговом окне **Сводка** нажмите кнопку **Далее**.

2. В диалоговом окне **Завершение** нажмите кнопку **Закрыть**.


2. На панели инструментов в верхней части экрана щелкните **Развернуть**.

	![Настройка Windows Hello для бизнеса](./media/active-directory-azureadjoin-passport-deployment/06.png)



## Настройка профиля сертификата для использования сертификата Windows Hello для бизнеса в Configuration Manager


При использовании аутентификации на основе сертификатов для локальной аутентификации необходимо настроить и развернуть профиль сертификата. Для этого нужно настроить сервер NDES и роль сайта "Точка регистрации сертификатов" в System Center Configuration Manager. Дополнительные сведения см. в статье [Необходимые условия для профилей сертификатов в Configuration Manager](https://technet.microsoft.com/library/dn261205.aspx).

1. Откройте **System Center Configuration Manager** и перейдите к разделу **Assets & Compliance > Параметры соответствия > Доступ к ресурсам компании > Профили сертификатов** (Ресурсы и соответствие).


2. Выберите шаблон с EKU входа со смарт-картой.

На странице **Регистрация SCEP** профиля сертификата выберите для параметра **Поставщик хранилища ключей** значение **Установить в Passport for Work, в ином случае — выдать отказ**.



## Дальнейшие действия
* [Windows 10 для предприятия: использование устройств для работы](active-directory-azureadjoin-windows10-devices-overview.md)
* [Использование возможностей облачных служб на устройствах с Windows 10 с помощью присоединения к Azure Active Directory](active-directory-azureadjoin-user-upgrade.md)
* [Проверка подлинности удостоверений без использования паролей с помощью службы Microsoft Passport](active-directory-azureadjoin-passport.md)
* [Сценарии использования для присоединения к Azure AD](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Подключение присоединенных к домену устройств к Azure AD для работы в Windows 10](active-directory-azureadjoin-devices-group-policy.md)
* [Настройка присоединения к Azure AD](active-directory-azureadjoin-setup.md)

<!---HONumber=AcomDC_0928_2016-->