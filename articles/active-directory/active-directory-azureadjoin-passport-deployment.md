---
title: "Включение Microsoft Windows Hello для бизнеса в организации | Документация Майкрософт"
description: "Инструкции по развертыванию для включения в организации службы Microsoft Passport."
services: active-directory
documentationcenter: 
keywords: "настройка Microsoft Passport, развертывание Microsoft Windows Hello для бизнеса"
author: MarkusVi
manager: mtillman
tags: azure-classic-portal
ms.assetid: 7dbbe3c6-1cd7-429c-a9b2-115fcbc02416
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: markvi
ms.openlocfilehash: 0aa16e3466b36b6d1d83308cf37623aa15d61fcb
ms.sourcegitcommit: 384d2ec82214e8af0fc4891f9f840fb7cf89ef59
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/16/2018
---
# <a name="enable-microsoft-windows-hello-for-business-in-your-organization"></a>Включение Microsoft Windows Hello для бизнеса в организации
После [подключения присоединенных к домену устройств Windows 10 к Azure Active Directory](active-directory-azureadjoin-devices-group-policy.md) выполните следующую процедуру, чтобы включить Microsoft Windows Hello для бизнеса в организации:

1. Развертывание System Center Configuration Manager  
2. Настройка параметров политики
3. Настройка профиля сертификата  

## <a name="deploy-system-center-configuration-manager"></a>Развертывание System Center Configuration Manager
Для развертывания сертификатов пользователей на основе ключей Windows Hello для бизнеса необходимо следующее:

* **System Center Configuration Manager (текущая версия)**. Необходимо установить версию 1606 или более позднюю. Дополнительные сведения см. в [документации по System Center Configuration Manager](https://technet.microsoft.com/library/mt346023.aspx) и [блоге рабочей группы по System Center Configuration Manager](http://blogs.technet.com/b/configmgrteam/archive/2015/09/23/now-available-update-for-system-center-config-manager-tp3.aspx).
* **Инфраструктура открытых ключей (PKI)**. Для поддержки использования сертификатов пользователей службой Microsoft Windows Hello для бизнеса необходима инфраструктура PKI. Если она отсутствует или вы не хотите использовать ее для сертификатов пользователей, то вы можете развернуть новый контроллер домена с установленной сборкой 10551 (или более поздней версии) Windows Server 2016. Выполните процедуру по [установке реплики контроллера домена в существующем домене](https://technet.microsoft.com/library/jj574134.aspx) или [установке нового леса Active Directory при создании новой среды](https://technet.microsoft.com/library/jj574166). (Образы ISO доступны для скачивания на странице [Signiant Media Exchange](https://datatransfer.microsoft.com/signiant_media_exchange/spring/main?sdkAccessible=true).)

## <a name="configure-policy-settings"></a>Настройка параметров политики
Существует два варианта настройки параметров политики Microsoft Windows Hello для бизнеса:

* групповая политика в Active Directory; 
* System Center Configuration Manager. 

Для настройки параметров политики Microsoft Windows Hello для бизнеса рекомендуется использовать групповую политику в Active Directory. 

Использование System Center Configuration Manager также является предпочтительным методом при развертывании сертификатов. Данный сценарий:

* обеспечивает совместимость с более новыми сценариями развертывания;
* требует на стороне клиента наличие Windows 10 версии 1607 или более новой.

### <a name="configure-microsoft-windows-hello-for-business-via-group-policy-in-active-directory"></a>Настройка Microsoft Windows Hello для бизнеса с помощью групповой политики в Active Directory
**Шаги**:

1. Откройте диспетчер серверов и выберите **Инструменты** > **Управление групповой политикой**.
2. В разделе "Управление групповой политикой" перейдите к узлу, соответствующему домену, для которого необходимо включить присоединение к Azure AD.
3. Щелкните правой кнопкой мыши **Объекты групповой политики** и выберите **Создать**. Укажите имя объекта групповой политики, например "Включение Windows Hello для бизнеса". Последовательно выберите **ОК**.
4. Щелкните правой кнопкой мыши новый объект групповой политики и выберите пункт **Изменить**.
5. Последовательно выберите **Конфигурация компьютера** > **Политики** > **Административные шаблоны** > **Компоненты Windows** > **Windows Hello для бизнеса**.
6. Щелкните правой кнопкой мыши **Enable Windows Hello for Business** (Включить Windows Hello для бизнеса), а затем выберите пункт **Изменить**.
7. Установите переключатель в положение **Включено**, а затем щелкните **Применить**. Последовательно выберите **ОК**.
8. Теперь вы можете связать объект групповой политики с выбранным расположением. Чтобы включить эту политику для всех присоединенных к домену устройств Windows 10 в организации, свяжите групповую политику с доменом. Например: 
   * с определенным подразделением в Active Directory, в котором будут размещаться компьютеры Windows 10, присоединенные к домену;
   * с определенной группой безопасности, содержащей присоединенные к домену компьютеры Windows 10, для которых будет выполняться автоматическая регистрация в Azure AD.

### <a name="configure-windows-hello-for-business-using-system-center-configuration-manager"></a>Настройка Windows Hello для бизнеса с помощью System Center Configuration Manager
**Шаги**:

1. Откройте **System Center Configuration Manager** и перейдите к разделу **Assets & Compliance > Параметры соответствия > Доступ к ресурсам компании > Профили Windows Hello для бизнеса** (Ресурсы и соответствие).
   
    ![Настройка Windows Hello для бизнеса](./media/active-directory-azureadjoin-passport-deployment/01.png)
2. На панели инструментов в верхней части щелкните **Создать профиль Windows Hello для бизнеса**.
   
    ![Настройка Windows Hello для бизнеса](./media/active-directory-azureadjoin-passport-deployment/02.png)
3. В диалоговом окне **Общие** выполните следующее:
   
    ![Настройка Windows Hello для бизнеса](./media/active-directory-azureadjoin-passport-deployment/03.png)
   
    a. В текстовом поле **Имя** введите имя профиля, например **Мой профиль WHfB**.
   
    Б. Нажмите кнопку **Далее**.
4. В диалоговом окне **Поддерживаемые платформы** выберите платформы, которые будут подготовлены с помощью этого профиля Windows Hello для бизнеса, и нажмите кнопку **Далее**.
   
    ![Настройка Windows Hello для бизнеса](./media/active-directory-azureadjoin-passport-deployment/04.png)
5. В диалоговом окне **Параметры** выполните следующее:
   
    ![Настройка Windows Hello для бизнеса](./media/active-directory-azureadjoin-passport-deployment/05.png)
   
    a. Для параметра **Настройка Windows Hello для бизнеса** выберите значение **Включено**.
   
    Б. Для параметра **Использовать доверенный платформенный модуль (TPM)** выберите **Требуется**. 
   
    c. Для параметра **Способ проверки подлинности** выберите значение **На основе сертификата**.
   
    d. Нажмите кнопку **Далее**.
6. В диалоговом окне **Сводка** нажмите кнопку **Далее**.
7. В диалоговом окне **Завершение** нажмите кнопку **Закрыть**.
8. На панели инструментов в верхней части экрана щелкните **Развернуть**.
   
    ![Настройка Windows Hello для бизнеса](./media/active-directory-azureadjoin-passport-deployment/06.png)

## <a name="configure-the-certificate-profile"></a>Настройка профиля сертификата
При использовании аутентификации на основе сертификатов для локальной аутентификации необходимо настроить и развернуть профиль сертификата. Для этого нужно настроить сервер NDES и роль сайта "Точка регистрации сертификатов" в System Center Configuration Manager. Дополнительные сведения см. в статье [Необходимые условия для профилей сертификатов в Configuration Manager](https://technet.microsoft.com/library/dn261205.aspx).

1. Откройте **System Center Configuration Manager** и перейдите к разделу **Assets & Compliance > Параметры соответствия > Доступ к ресурсам компании > Профили сертификатов** (Ресурсы и соответствие).
2. Выберите шаблон с EKU входа со смарт-картой.

На странице **Регистрация SCEP** профиля сертификата выберите для параметра **Поставщик хранилища ключей** значение **Установить в Passport for Work, в ином случае — выдать отказ**.

## <a name="next-steps"></a>Дополнительная информация
* [Windows 10 для предприятия: использование устройств для работы](active-directory-azureadjoin-windows10-devices-overview.md)
* [Использование возможностей облачных служб на устройствах с Windows 10 с помощью присоединения к Azure Active Directory](active-directory-azureadjoin-user-upgrade.md)
* [Проверка подлинности без использования паролей](active-directory-azureadjoin-passport.md)
* [Сценарии использования для присоединения к Azure AD](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Подключение присоединенных к домену устройств к Azure AD для работы в Windows 10](active-directory-azureadjoin-devices-group-policy.md)
* [Настройка присоединения к Azure AD](active-directory-azureadjoin-setup.md)

