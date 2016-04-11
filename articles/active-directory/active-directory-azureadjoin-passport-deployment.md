<properties
	pageTitle="Включение Microsoft Passport for Work в организации | Microsoft Azure"
	description="Инструкции по развертыванию для включения в организации службы Microsoft Passport."
	services="active-directory"
	documentationCenter=""
	keywords="настройка Microsoft Passport, развертывание Microsoft Passport for Work"
	authors="femila"
	manager="stevenpo"
	editor=""
	tags="azure-classic-portal"/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/23/2016"
	ms.author="femila"/>


# Включение Microsoft Passport for Work в организации

После подключения присоединенных к домену устройств Windows 10 к Azure Active Directory (Azure AD) выполните следующую процедуру, чтобы включить Microsoft Passport for Work в организации.

## Развертывание System Center Configuration Manager (ознакомительная техническая версия 1509)
Для развертывания сертификатов пользователей на основе ключей Microsoft Passport необходимо следующее.

- **System Center Configuration Manager (ознакомительная техническая версия 1509)**. Дополнительные сведения см. в разделе [Microsoft System Center Configuration Manager Technical Preview](https://technet.microsoft.com/library/dn965439.aspx#BKMK_TP3Update) и [блоге рабочей группы по System Center Configuration Manager](http://blogs.technet.com/b/configmgrteam/archive/2015/09/23/now-available-update-for-system-center-config-manager-tp3.aspx).
- **Инфраструктура открытых ключей (PKI)**. Для поддержки использования сертификатов пользователей службой Microsoft Passport for Work необходима инфраструктура PKI. Если у вас отсутствует инфраструктура PKI или вы не хотите использовать ее для сертификатов пользователей, выполните указанные ниже действия.
 - **Развертывание контроллера домена**. Выполните развертывание нового контроллера домена с установленной сборкой 10551 (или более поздней версии) Windows Server 2016 и выполните процедуру по [установке реплики контроллера домена в существующем домене](https://technet.microsoft.com/library/jj574134.aspx) или [установке нового леса Active Directory при создании новой среды](https://technet.microsoft.com/library/jj574166). (Образы ISO доступны для скачивания на странице [Signiant Media Exchange](https://datatransfer.microsoft.com/signiant_media_exchange/spring/main?sdkAccessible=true).)

## Настройка Microsoft Passport for Work с помощью групповой политики в Active Directory

 Групповую политику в Active Directory Windows Server можно использовать для настройки присоединенных к домену устройств Windows 10 для подготовки учетных данных Microsoft Passport при входе пользователя в систему Windows.

1. 	Откройте диспетчер серверов и выберите **Инструменты** > **Управление групповой политикой**.
2.	В разделе "Управление групповой политикой" перейдите к узлу, соответствующему домену, для которого необходимо включить присоединение к Azure AD.
3.	Щелкните правой кнопкой мыши элемент **Объекты групповой политики** и выберите пункт **Создать**. Укажите имя объекта групповой политики, например "Включение Microsoft Passport". Нажмите кнопку **ОК**.
4.	Щелкните правой кнопкой мыши новый объект групповой политики и выберите пункт **Изменить**.
5.	Последовательно выберите **Конфигурация компьютера** > **Политики** > **Административные шаблоны** > **Компоненты Windows** > **Passport for Work**.
6.	Щелкните правой кнопкой мыши элемент **Включить Passport for Work** и выберите пункт **Изменить**.
7.	Установите переключатель в положение **Включено**, а затем нажмите кнопку **Применить**. Нажмите кнопку **ОК**.
8.	Теперь вы можете связать объект групповой политики с выбранным расположением. Чтобы включить эту политику для всех присоединенных к домену устройств Windows 10 в организации, свяжите групповую политику с доменом. Например:
 - с определенным подразделением в Active Directory, в котором будут размещаться компьютеры Windows 10, присоединенные к домену;
 - с определенной группой безопасности, содержащей присоединенные к домену компьютеры Windows 10, для которых будет выполняться автоматическая регистрация в Azure AD.

## Настройка Microsoft Passport for Work путем использования PowerShell с помощью Configuration Manager

Выполните следующую команду PowerShell:

    powershell.exe -ExecutionPolicy Bypass -NoLogo -NoProfile -Command "& {New-ItemProperty "HKLM:\Software\Policies\Microsoft\PassportForWork" -Name "Enabled" -Value 1 -PropertyType "DWord" -Force}"

## Настройка профиля сертификата для использования сертификата Passport for Work в Configuration Manager
Для использования входа на основе сертификата Passport for Work или Microsoft Hello настройте профиль сертификата (**Активы и соответствие** > **Параметры соответствия** > **Доступ к ресурсам компании** > **Профили сертификатов**). Выберите шаблон с EKU входа со смарт-картой.

## Настройка запланированной задачи для запроса оценки сертификата
Эта запланированная задача является краткосрочным исправлением. Администраторы должны создать запланированную задачу, ожидающую создание контейнера Passport for Work и запрашивающую оценку сертификата. Запланированная задача запускается при включении контейнера Passport for Work. Задача сокращает задержки при настройке контейнера и ПИН-кода и их доступности для использования при следующем входе в систему.

**Чтобы создать запланированное задание, можно использовать пользовательский интерфейс или следующую команду:**

    schtasks /create /xml %0\..<EnrollCertificate.xml> /tn <Task Name>

Ниже приведен пример XML-кода.

    <?xml version="1.0" encoding="UTF-16"?>
    <Task version="1.2" xmlns="http://schemas.microsoft.com/windows/2004/02/mit/task">
       <RegistrationInfo>
         <Date>2015-09-04T17:48:45.9973761</Date>
          <Author><DomainName/UserName></Author>
        <URI>\Enroll Certificates</URI>
      </RegistrationInfo>
      <Triggers>
        <EventTrigger>
          <Enabled>true</Enabled>
          <Subscription>&lt;QueryList&gt;&lt;Query Id="0" Path="Microsoft-Windows-User Device Registration/Admin"&gt;&lt;Select Path="Microsoft-Windows-User Device Registration/Admin"&gt;*[System[Provider[@Name='Microsoft-Windows-User Device Registration'] and EventID=300]]&lt;/Select&gt;&lt;/Query&gt;&lt;/QueryList&gt;</Subscription>
        </EventTrigger>
      </Triggers>
      <Principals>
      </Principals>
      <Settings>
        <MultipleInstancesPolicy>IgnoreNew</MultipleInstancesPolicy>
        <DisallowStartIfOnBatteries>false</DisallowStartIfOnBatteries>
        <StopIfGoingOnBatteries>true</StopIfGoingOnBatteries>
        <AllowHardTerminate>true</AllowHardTerminate>
        <StartWhenAvailable>true</StartWhenAvailable>
        <RunOnlyIfNetworkAvailable>false</RunOnlyIfNetworkAvailable>
        <IdleSettings>
          <StopOnIdleEnd>true</StopOnIdleEnd>
          <RestartOnIdle>false</RestartOnIdle>
        </IdleSettings>
        <AllowStartOnDemand>true</AllowStartOnDemand>
        <Enabled>true</Enabled>
        <Hidden>false</Hidden>
        <RunOnlyIfIdle>false</RunOnlyIfIdle>
        <WakeToRun>false</WakeToRun>
        <ExecutionTimeLimit>PT1H</ExecutionTimeLimit>
        <Priority>7</Priority>
        <RestartOnFailure>
          <Interval>PT1M</Interval>
          <Count>3</Count>
        </RestartOnFailure>
      </Settings>
      <Actions Context="Author">
        <Exec>
          <Command>wmic</Command>
          <Arguments>/namespace:\\root\ccm\dcm path SMS_DesiredConfiguration call TriggerEvaluation 1,0,"ScopeId_73F3BB5E-5EDC-4928-87BD-4E75EB4BBC34/ConfigurationPolicy_db89c51e-1d1b-4a17-8e42-a5459b742ccd",8</Arguments>
        </Exec>
      </Actions>
    </Task>

 Где ScopeId\_73F3BB5E-5EDC-4928-87BD-4E75EB4BBC34/ConfigurationPolicy\_db89c51e-1d1b-4a17-8e42-a5459b742ccd — это идентификатор для профиля сертификата, созданного в процедуре "Настройка профиля сертификата для использования сертификата регистрации Passport for Work в Configuration Manager". После ИД области идет номер версии — 8.

## Дополнительная информация
* [Windows 10 для предприятия: использование устройств для работы](active-directory-azureadjoin-windows10-devices-overview.md)
* [Использование возможностей облачных служб на устройствах с Windows 10 с помощью присоединения к Azure Active Directory](active-directory-azureadjoin-user-upgrade.md)
* [Проверка подлинности удостоверений без использования паролей с помощью службы Microsoft Passport](active-directory-azureadjoin-passport.md)
* [Сценарии использования для присоединения к Azure AD](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Подключение присоединенных к домену устройств к Azure AD для работы в Windows 10](active-directory-azureadjoin-devices-group-policy.md)
* [Настройка присоединения к Azure AD](active-directory-azureadjoin-setup.md)

<!---HONumber=AcomDC_0330_2016-->