---
title: "Azure AD Connect: включение обратной записи устройств | Документация Майкрософт"
description: "В этом документе объясняется, как включить функцию обратной записи устройств с помощью службы Azure AD Connect"
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: curtand
ms.assetid: c0ff679c-7ed5-4d6e-ac6c-b2b6392e7892
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2017
ms.author: billmath
ms.openlocfilehash: ac5c39eac34221992803f3b27db241263f828b36
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/14/2017
---
# <a name="azure-ad-connect-enabling-device-writeback"></a>Azure AD Connect: включение обратной записи устройств
> [!NOTE]
> Для обратной записи устройств требуется подписка Azure AD Premium.
> 
> 

Следующая документация содержит сведения о том, как включить функцию обратной записи устройств в службе Azure AD Connect. Обратная запись устройств используется в ситуациях,

* когда необходимо включить условный доступ на основе устройств служб ADFS (2012 R2 или более поздней версии) к защищенным приложениям (отношения доверия с проверяющей стороной).

Это обеспечивает дополнительную защиту и гарантию того, что доступ к приложениям предоставляется только доверенным устройствам. Дополнительные сведения о настройке условного доступа см. в статьях [Управление рисками с помощью условного доступа](../active-directory-conditional-access-azure-portal.md) и [Настройка локального условного доступа с помощью регистрации устройств в Azure Active Directory](../active-directory-conditional-access-automatic-device-registration-setup.md).

> [!IMPORTANT]
> <li>Устройства должны находиться в том же лесу, что и пользователи. Поскольку обратная запись устройств должна производиться в один лес, эта функция в настоящий момент не поддерживает развертывание с несколькими лесами пользователей.</li>
> <li>В локальный лес Active Directory можно добавить только один объект конфигурации регистрации. Эта функция несовместима с топологией, в которой локальный каталог Active Directory синхронизируется в несколько каталогов Azure AD.</li>> 

## <a name="part-1-install-azure-ad-connect"></a>Часть 1. Установка Azure AD Connect
1. Установите Azure AD Connect с использованием стандартных или пользовательских параметров. Корпорация Майкрософт рекомендует перед включением обратной записи устройства выполнить синхронизацию всех пользователей и групп.

## <a name="part-2-prepare-active-directory"></a>Часть 2. Подготовка Active Directory
Выполните следующие действия для подготовки к использованию функции обратной записи устройств.

1. На компьютере, где устанавливается служба Azure AD Connect, запустите PowerShell с повышенными правами.
2. Если модуль PowerShell для Active Directory НЕ установлен, установите средства удаленного администрирования сервера, содержащие модуль PowerShell для AD и dsacls.exe, которые необходимы для выполнения сценария.  Выполните следующую команду:
  
   ``` powershell
   Add-WindowsFeature RSAT-AD-Tools
   ```

3. Если модуль Azure Active Directory PowerShell НЕ установлен, скачайте и установите его из [модуля Azure Active Directory для Windows PowerShell (64-разрядная версия)](http://go.microsoft.com/fwlink/p/?linkid=236297). Этот компонент имеет зависимость от помощника по входу, который устанавливается вместе с Azure AD Connect.  
4. Используя учетные данные администратора предприятия, выполните следующие команды и выйдите из PowerShell.
   
   ``` powershell
   Import-Module 'C:\Program Files\Microsoft Azure Active Directory Connect\AdPrep\AdSyncPrep.psm1'
   ```

   ``` powershell
   Initialize-ADSyncDeviceWriteback {Optional:–DomainName [name] Optional:-AdConnectorAccount [account]}
   ```

Необходимо указать именно учетные данные администратора предприятия, так как изменения вносятся в пространство имен конфигурации. Администратор домена не имеет достаточных разрешений.

![PowerShell для включения обратной записи устройств](./media/active-directory-aadconnect-feature-device-writeback/powershell.png)


Описание:

* Если они больше не существуют, функция создает и настраивает их в разделах CN=Device Registration Configuration,CN=Services,CN=Configuration,[forest-dn].
* Если они больше не существуют, функция создает и настраивает их в разделе CN=RegisteredDevices,[domain-dn]. Объекты устройства будут созданы в этом контейнере.
* Задает необходимые разрешения для учетной записи Azure AD Connector для управления устройствами в Active Directory.
* Предполагает выполнение только в одном лесу, даже если служба Azure AD Connect устанавливается в нескольких лесах.

Параметры

* DomainName: домен Active Directory, в котором будут созданы объекты устройства. Примечание: все устройства для отдельно взятого леса Active Directory будут созданы в одном домене.
* AdConnectorAccount: учетная запись Active Directory, которая будет использоваться службой Azure AD Connect для управления объектами в каталоге. Это учетная запись, используемая Azure AD Connect Sync для подключения к AD. При установке с помощью стандартных параметров это учетная запись с префиксом MSOL_.

## <a name="part-3-enable-device-writeback-in-azure-ad-connect"></a>Часть 3. Включение обратной записи устройств в Azure AD Connect
Используйте следующую процедуру, чтобы включить функцию обратной записи устройств в службе Azure AD Connect.

1. Снова запустите мастер установки. Выберите **Настроить параметры синхронизации** на странице «Дополнительные задачи» и нажмите кнопку **Далее**.
   ![Выборочная установка: настройка параметров синхронизации](./media/active-directory-aadconnect-feature-device-writeback/devicewriteback2.png)
2. На странице дополнительных функций параметр «Обратная запись устройств» станет активным. Обратите внимание: если подготовительные шаги по настройке службы Connect Azure AD не завершены, параметр «Обратная запись устройств» на странице дополнительных функций будет оставаться неактивным. Установите флажок для обратной записи устройства и нажмите кнопку **Далее**. Если флажок по-прежнему отключен, см. раздел [Устранение неполадок](#the-writeback-checkbox-is-still-disabled).
   ![Выборочная установка: дополнительные компоненты обратной записи устройства](./media/active-directory-aadconnect-feature-device-writeback/devicewriteback3.png)
3. На странице обратной записи вы увидите указанный домен в качестве леса обратной записи устройств по умолчанию.
   ![Выборочная установка: целевой лес обратной записи устройства](./media/active-directory-aadconnect-feature-device-writeback/devicewriteback4.png)
4. Завершите установку в мастере, не изменяя другие параметры. При необходимости см. статью [Выборочная установка Azure AD Connect](active-directory-aadconnect-get-started-custom.md).

## <a name="enable-conditional-access"></a>Включение условного доступа
Подробные инструкции о включении этого сценария см. в статье [Настройка локального условного доступа с помощью регистрации устройств в Azure Active Directory](../active-directory-conditional-access-automatic-device-registration-setup.md).

## <a name="verify-devices-are-synchronized-to-active-directory"></a>Проверка синхронизации устройств с Active Directory
Функция обратной записи устройств должна теперь работать правильно. Имейте в виду, что обратная запись объектов устройств в AD может выполняться до 3 часов.  Проверить, правильно ли синхронизированы устройства после выполнения правила синхронизации, можно так.

1. Запустите центр администрирования Active Directory.
2. Разверните раздел RegisteredDevices в домене, который включается в федерацию.
   ![Центр администрирования Active Directory: зарегистрированные устройства](./media/active-directory-aadconnect-feature-device-writeback/devicewriteback5.png)
3. Там будут перечислены зарегистрированные в настоящий момент устройства.
   ![Центр администрирования Active Directory: список зарегистрированных устройств](./media/active-directory-aadconnect-feature-device-writeback/devicewriteback6.png)

## <a name="troubleshooting"></a>Устранение неполадок
### <a name="the-writeback-checkbox-is-still-disabled"></a>Флажок обратной записи по-прежнему отключен
Если флажок для обратной записи устройства не включен, несмотря на то, что были выполнены шаги, описанные выше, с помощью описанных ниже действий вы сможете проверить то, что проверяет мастер установки перед включением флажка.

Начнем сначала:

* Убедитесь, что по крайней мере в одном лесу есть 2012R2 Windows Server. Тип объекта устройства должен быть указан.
* Если мастер установки уже запущен, то изменения не будут обнаружены. В этом случае завершите работу мастера установки и запустите его снова.
* Убедитесь, что учетная запись, указанная в сценарии инициализации, соответствует пользователю, используемому соединителем Active Directory. Чтобы это проверить, выполните следующие действия.
  * В меню "Пуск" щелкните **Служба синхронизации**.
  * Откройте вкладку **Соединители** .
  * Найдите соединитель с типом "Доменные службы Active Directory" и выберите его.
  * В разделе **Действия** выберите **Свойства**.
  * Выберите **Подключиться к лесу Active Directory**. Убедитесь, что домен и имя пользователя, указанные в этом окне, совпадают с учетной записью, указанной в сценарии.
    ![Учетная запись соединителя в диспетчере службы синхронизации](./media/active-directory-aadconnect-feature-device-writeback/connectoraccount.png)

Проверьте конфигурацию Active Directory.

* Убедитесь, что служба регистрации устройств расположена в контексте именования конфигурации ниже (CN=DeviceRegistrationService,CN=Device Registration Services,CN=Device Registration Configuration,CN=Services,CN=Configuration).

![Устранение неполадок, служба регистрации устройств в пространстве имен конфигурации](./media/active-directory-aadconnect-feature-device-writeback/troubleshoot1.png)

* Убедитесь, что имеется только один объект конфигурации, выполнив поиск в пространстве имен конфигурации. Если объектов несколько, удалите дубликаты.

![Устранение неполадок, поиск дубликатов объектов](./media/active-directory-aadconnect-feature-device-writeback/troubleshoot2.png)

* В объекте службы регистрации устройств убедитесь, что атрибут msDS-DeviceLocation присутствует и для него установлено значение. Найдите указанное расположение и убедитесь, что оно существует с типом объекта msDS-DeviceContainer.

![Устранение неполадок, расположение устройств msDS](./media/active-directory-aadconnect-feature-device-writeback/troubleshoot3.png)

![Устранение неполадок, класс объекта зарегистрированных устройств](./media/active-directory-aadconnect-feature-device-writeback/troubleshoot4.png)

* Убедитесь, что учетная запись, используемая соединителем Active Directory, имеет необходимые разрешения для контейнера "Зарегистрированные устройства", обнаруженного на предыдущем шаге. Разрешения для контейнера должны быть следующими:

![Устранение неполадок, проверка разрешений в контейнере](./media/active-directory-aadconnect-feature-device-writeback/troubleshoot5.png)

* Проверьте, что у учетной записи Active Directory есть разрешения для объекта CN=Device Registration Configuration,CN=Services,CN=Configuration.

![Устранение неполадок, проверка разрешений в конфигурации регистрации устройства](./media/active-directory-aadconnect-feature-device-writeback/troubleshoot6.png)

## <a name="additional-information"></a>Дополнительная информация
* [Управление рисками с помощью условного доступа](../active-directory-conditional-access-azure-portal.md)
* [Настройка локального условного доступа с помощью регистрации устройств в Azure Active Directory](../active-directory-device-registration-on-premises-setup.md)

## <a name="next-steps"></a>Дальнейшие действия
Узнайте больше об [интеграции локальных удостоверений с Azure Active Directory](active-directory-aadconnect.md).

