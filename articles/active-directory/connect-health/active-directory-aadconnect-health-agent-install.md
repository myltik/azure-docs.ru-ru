---
title: Установка агента Azure AD Connect Health | Документация Майкрософт
description: Это страница Azure AD Connect Health, на которой описана процедура установки агента для AD FS и синхронизации.
services: active-directory
documentationcenter: ''
author: karavar
manager: mtillman
editor: curtand
ms.assetid: 1cc8ae90-607d-4925-9c30-6770a4bd1b4e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/18/2017
ms.author: billmath
ms.openlocfilehash: b68c03803e36c282640f82f9b1cc009fb723bab2
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2018
---
# <a name="azure-ad-connect-health-agent-installation"></a>Установка агента Azure AD Connect Health
В этом документе описываются этапы установки и настройки агентов Azure AD Connect Health. Загрузить агенты можно [отсюда](active-directory-aadconnect-health.md#download-and-install-azure-ad-connect-health-agent):

## <a name="requirements"></a>Требования
В таблице ниже приведен список предварительных требований для использования Azure AD Connect Health.

| Требование | ОПИСАНИЕ |
| --- | --- |
| Azure AD Premium |Служба Azure AD Connect Health относится к Azure AD Premium и требует наличия выпуска Azure AD Premium. </br></br>Дополнительные сведения см. в статье [Приступая к работе с Azure Active Directory Premium](../active-directory-get-started-premium.md). </br>Чтобы получить бесплатную 30-дневную пробную версию, перейдите [по этой ссылке](https://azure.microsoft.com/trial/get-started-active-directory/). |
| Для начала работы с Azure AD Connect Health требуются права глобального администратора Azure AD  |По умолчанию только глобальный администратор может установить и настроить агенты работоспособности, чтобы приступить к работе, получить доступ к порталу и начать выполнять любые операции в Azure AD Connect Health. Дополнительные сведения см. в статье [Администрирование каталога Azure AD](../active-directory-administer.md). <br><br> С помощью управления доступом на основе ролей можно разрешить доступ к Azure AD Connect Health другим пользователям в организации. Дополнительные сведения см. в разделе [Управление доступом с помощью контроля доступа на основе ролей](active-directory-aadconnect-health-operations.md#manage-access-with-role-based-access-control). </br></br>**Важно!** Учетная запись, которая используется во время установки агентов, должна быть рабочей или учебной. Учетную запись Майкрософт для этого использовать нельзя. Дополнительные сведения см. в статье [Подпишитесь на Azure как организация](../sign-up-organization.md). |
| Агент Azure AD Connect Health следует установить на всех целевых серверах | Для Azure AD Connect Health на целевых серверах необходимо установить и настроить агенты работоспособности, чтобы получать данные и предоставлять возможности мониторинга и аналитики. </br></br>Например, для получения данных об инфраструктуре AD FS необходимо установить агент на серверы AD FS и прокси-серверы веб-приложений. Аналогично, для получения данных о локальной инфраструктуре AD DS агент необходимо установить на контроллеры домена. </br></br> |
| Исходящие подключения к конечным точкам службы Azure | Во время установки и выполнения агенту требуется подключение к конечным точкам службы Azure AD Connect Health. При блокировке исходящего подключения с помощью брандмауэров убедитесь, что в список разрешенных добавлены следующие конечные точки: </br></br><li>&#42;.blob.core.windows.net </li><li>&#42;.servicebus.windows.net, порт 5671; </li><li>&#42;.adhybridhealth.azure.com/;</li><li>https://management.azure.com </li><li>https://policykeyservice.dc.ad.msft.net/</li><li>https://login.windows.net</li><li>https://login.microsoftonline.com</li><li>https://secure.aadcdn.microsoftonline-p.com </li><li>https://www.office.com *Эта конечная точка используется только для обнаружения во время регистрации.</li><br> Для среды **Azure — Германия** добавьте альтернативные конечные точки в список разрешенных:</br><li>*.blob.core.cloudapi.de; </li><li>*.queue.core.cloudapi.de; </li><li>*.servicebus.cloudapi.de; </li><li>*.table.core.cloudapi.de; </li><li>*.aadconnecthealth.microsoftazure.de; </li><li>https://management.microsoftazure.de </li><li>https://policykeyservice.aadcdi.microsoftazure.de </li><li>https://login.microsoftonline.de </li><li>https://secure.aadcdn.microsoftonline-p.de </li><li>https://www.office.com *Эта конечная точка используется только для обнаружения во время регистрации.</li> Ознакомьтесь с дополнительными сведениями о [проверке исходящего подключения](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections).| 
|Исходящие подключения на основе IP-адресов | Дополнительные сведения о фильтрации на основе IP-адресов в брандмауэрах см. в статье о [диапазонах IP-адресов Azure](https://www.microsoft.com/en-us/download/details.aspx?id=41653).|
| Проверка SSL для исходящего трафика отфильтрована или отключена | Операции отправки данных или регистрация агента могут завершиться ошибкой в случае проверки SSL или завершения исходящего трафика на уровне сети. Ознакомьтесь с дополнительными сведениями о том, [как настроить проверку SSL] (https://technet.microsoft.com/library/ee796230.aspx). |
| Порты брандмауэра на сервере с агентом |Агент требует открытия следующих портов брандмауэра для обмена данными с конечными точками службы Azure AD Health.</br></br><li>TCP-порт 443</li><li>TCP-порт 5671</li> </br>Ознакомьтесь с дополнительными сведениями о [включении портов брандмауэра](https://technet.microsoft.com/library/ms345310(v=sql.100).aspx). |
| Внесите следующие веб-сайты в список разрешенных, если включена политика усиленной безопасности IE |Если на сервере, на котором будет установлен агент, включена конфигурация усиленной безопасности, потребуется открыть доступ для следующих веб-сайтов:</br></br><li>https://login.microsoftonline.com</li><li>https://secure.aadcdn.microsoftonline-p.com</li><li>https://login.windows.net</li><li>Сервер федерации вашей организации должен быть доверенным для Azure Active Directory. Например: https://sts.contoso.com</li> Ознакомьтесь с дополнительными сведениями о том, [как настроить IE](https://support.microsoft.com/help/815141/internet-explorer-enhanced-security-configuration-changes-the-browsing). |
| Установлена служба PowerShell 4.0 или более поздней версии | <li>Windows Server 2008 R2 поставляется вместе с PowerShell версии 2.0, что недостаточно для агента.  Обновите PowerShell, как описано ниже в разделе [Установка агента на серверах Windows Server 2008 R2](#agent-installation-on-windows-server-2008-r2-servers).</li><li>Windows Server 2012 поставляется вместе с PowerShell версии 3.0, что недостаточно для агента.  [Обновите](http://www.microsoft.com/en-us/download/details.aspx?id=40855) Windows Management Framework.</li><li>Windows Server 2012 R2 и более поздней версии поставляется с последней версией PowerShell.</li>|
|Отключение FIPS|FIPS не поддерживается агентами Azure Active Directory Connect Health.|

## <a name="download-and-install-the-azure-ad-connect-health-agent"></a>Скачивание и установка агента Azure AD Connect Health
* Обязательно [выполните требования](active-directory-aadconnect-health-agent-install.md#requirements) для Azure AD Connect Health.
* Приступая к работе с Azure AD Connect Health для AD FS
    * [Скачайте агент Azure AD Connect Health для AD FS.](http://go.microsoft.com/fwlink/?LinkID=518973)
    * [Ознакомьтесь с инструкциями по установке.](#installing-the-azure-ad-connect-health-agent-for-ad-fs)
* Приступая к работе с Azure AD Connect Health для синхронизации
    * [Скачайте и установите последнюю версию Azure AD Connect.](http://go.microsoft.com/fwlink/?linkid=615771) Агент Azure AD Connect Health для синхронизации будет установлен вместе с Azure AD Connect (версии 1.0.9125.0 или более поздней).
* Приступая к работе с Azure AD Connect Health для AD DS
    * [Скачайте агент Azure AD Connect Health для AD FS.](http://go.microsoft.com/fwlink/?LinkID=820540)
    * [Ознакомьтесь с инструкциями по установке.](#installing-the-azure-ad-connect-health-agent-for-ad-ds)

## <a name="installing-the-azure-ad-connect-health-agent-for-ad-fs"></a>Установка агента Azure AD Connect Health для AD FS
Дважды щелкните скачанный EXE-файл, чтобы начать установку агента. На первом экране щелкните «Установить».

![Проверка Azure AD Connect Health](./media/active-directory-aadconnect-health-requirements/install1.png)

После завершения установки нажмите кнопку «Настроить сейчас».

![Проверка Azure AD Connect Health](./media/active-directory-aadconnect-health-requirements/install2.png)

Откроется окно PowerShell, и начнется процесс регистрации агента. При появлении запроса войдите с помощью учетной записи Azure AD, которая имеет доступ на выполнение регистрации агента. По умолчанию учетная запись глобального администратора имеет такой доступ.

![Проверка Azure AD Connect Health](./media/active-directory-aadconnect-health-requirements/install3.png)

После входа PowerShell продолжит выполнять команду. После завершения можно закрыть PowerShell. Настройка завершена.

На этом этапе службы агента должны запуститься автоматически, благодаря чему агент сможет безопасно загрузить необходимые данные в облачную службу.

Если вы не выполнили все необходимые предварительные требования, которые были описаны в предыдущих разделах, в окне PowerShell будут отображаться предупреждения. Обязательно выполните эти [требования](active-directory-aadconnect-health-agent-install.md#requirements), прежде чем устанавливать агент. В качестве примера этих ошибок приведен следующий снимок экрана.

![Проверка Azure AD Connect Health](./media/active-directory-aadconnect-health-requirements/install4.png)

Чтобы проверить, установлен ли агент, найдите на сервере приведенные ниже службы. Если вы выполнили настройку, эти службы должны работать. Они не запустятся, пока не будет выполнена настройка.

* Служба диагностики AD FS Azure AD Connect Health
* Служба AD FS получения ценной информации из данных о работоспособности Azure AD Connect
* Служба наблюдения AD FS Azure AD Connect Health

![Проверка Azure AD Connect Health](./media/active-directory-aadconnect-health-requirements/install5.png)

### <a name="agent-installation-on-windows-server-2008-r2-servers"></a>Установка агента на серверах Windows Server 2008 R2
Для серверов Windows Server 2008 R2 сделайте следующее:

1. Убедитесь, что сервер работает с пакетом обновления 1 или более поздней версии.
2. Отключите IE ESC для установки агента.
3. Установите Windows PowerShell 4.0 на каждом из серверов перед установкой агента AD Health. Чтобы установить Windows PowerShell 4.0, выполните следующие действия.
   * Загрузите автономный установщик по ссылке и установите [Microsoft .NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=40779) .
   * Установите интегрированную среду сценариев PowerShell (из компонентов Windows).
   * Установите [Windows Management Framework 4.0](https://www.microsoft.com/download/details.aspx?id=40855)
   * Установите на сервере Internet Explorer версии 10 или более поздней версии. (Это необходимо службе работоспособности для аутентификации пользователя с учетными данными администратора Azure.)
4. Дополнительные сведения об установке Windows PowerShell 4.0 на Windows Server 2008 R2 см. в вики-статье [здесь](http://social.technet.microsoft.com/wiki/contents/articles/20623.step-by-step-upgrading-the-powershell-version-4-on-2008-r2.aspx).

### <a name="enable-auditing-for-ad-fs"></a>Включение аудита для AD FS
> [!NOTE]
> Этот раздел относится только к серверам AD FS. Эти действия не нужно выполнять на прокси-серверах веб-приложений.
>

Чтобы функция аналитики по использованию могла осуществлять сбор и анализ данных, агенту Azure AD Connect Health нужна информация из журналов аудита AD FS. По умолчанию эти журналы не включены. Используйте следующие процедуры для включения аудита AD FS и поиска журналов аудита AD FS на серверах AD FS.

#### <a name="to-enable-auditing-for-ad-fs-on-windows-server-2008-r2"></a>Включение аудита для AD FS на Windows Server 2008 R2
1. Нажиме кнопку **Пуск**, наведите указатель мыши на пункт **Программы**, а затем — на **Администрирование** и выберите **Локальная политика безопасности**.
2. Перейдите к папке **Параметры безопасности\Локальные политики\Предоставление прав пользователям**, а затем дважды щелкните пункт **Создание аудитов безопасности**.
3. Убедитесь, что на вкладке **Параметры локальной безопасности** в списке указана учетная запись службы AD FS 2.0. Если она отсутствует, щелкните **Добавить пользователя или группу**, добавьте учетную запись в список и нажмите кнопку **ОК**.
4. Чтобы включить аудит, откройте командную строку с более высоким уровнем привилегий и выполните следующую команду: <code>auditpol.exe /set /subcategory:"Application Generated" /failure:enable /success:enable</code>.
5. Закройте вкладку **Параметры локальной безопасности**.
<br>   -- **Следующие шаги требуются только для основных серверов AD FS.** -- </br>
6. Откройте оснастку **Управление AD FS**. Чтобы открыть оснастку управления AD FS, нажмите кнопку **Пуск**, наведите указатель мыши на пункт **Программы**, а затем — на **Администрирование** и выберите пункт **AD FS 2.0 Management** (Управление AD FS 2.0).
7. На панели **действий** выберите действие **Изменить свойства службы федерации**.
8. В диалоговом окне **Свойства службы федерации** откройте вкладку **События**.
9. Установите флажки **Success audits** (Успешные события аудита) и **Failure audits** (Неудачные события аудита).
10. Последовательно выберите **ОК**.

#### <a name="to-enable-auditing-for-ad-fs-on-windows-server-2012-r2"></a>Включение аудита для AD FS на Windows Server 2012 R2
1. Из раздела **Диспетчер сервера** на начальном экране или на панели задач откройте диалоговое окно **Локальная политика безопасности**. Затем выберите **Сервис/Локальная политика безопасности**.
2. Перейдите к папке **Параметры безопасности\Локальные политики\Предоставление прав пользователям**, а затем дважды щелкните пункт **Создание аудитов безопасности**.
3. Убедитесь, что на вкладке **Параметры локальной безопасности** в списке указана учетная запись службы AD FS. Если она отсутствует, щелкните **Добавить пользователя или группу**, добавьте учетную запись в список и нажмите кнопку **ОК**.
4. Чтобы включить аудит, откройте командную строку с повышенным уровнем привилегий и выполните следующую команду: ```auditpol.exe /set /subcategory:"Application Generated" /failure:enable /success:enable```.
5. Закройте вкладку **Параметры локальной безопасности**.
<br>   -- **Следующие шаги требуются только для основных серверов AD FS.** -- </br>
6. Откройте оснастку **Управление AD FS** (в разделе "Диспетчер сервера" выберите меню "Сервис" и щелкните "Управление AD FS").
7. На панели **действий** выберите действие **Изменить свойства службы федерации**.
8. В диалоговом окне **Свойства службы федерации** откройте вкладку **События**.
9. Установите флажки для **успешных и неудачных событий аудита** и нажмите кнопку **ОК**.

#### <a name="to-enable-auditing-for-ad-fs-on-windows-server-2016"></a>Включение аудита для AD FS на Windows Server 2016
1. Из раздела **Диспетчер сервера** на начальном экране или на панели задач откройте диалоговое окно **Локальная политика безопасности**. Затем выберите **Сервис/Локальная политика безопасности**.
2. Перейдите к папке **Параметры безопасности\Локальные политики\Предоставление прав пользователям**, а затем дважды щелкните пункт **Создание аудитов безопасности**.
3. Убедитесь, что на вкладке **Параметры локальной безопасности** в списке указана учетная запись службы AD FS. Если она отсутствует, щелкните **Добавить пользователя или группу**, добавьте учетную запись службы AD FS в список и нажмите кнопку **ОК**.
4. Чтобы включить аудит, откройте командную строку с повышенным уровнем привилегий и выполните следующую команду: <code>auditpol.exe /set /subcategory:"Application Generated" /failure:enable /success:enable.</code>.
5. Закройте вкладку **Параметры локальной безопасности**.
<br>   -- **Следующие шаги требуются только для основных серверов AD FS.** -- </br>
6. Откройте оснастку **Управление AD FS** (в разделе "Диспетчер сервера" выберите меню "Сервис" и щелкните "Управление AD FS").
7. На панели **действий** выберите действие **Изменить свойства службы федерации**.
8. В диалоговом окне **Свойства службы федерации** откройте вкладку **События**.
9. Установите флажки для **успешных и неудачных событий аудита** и нажмите кнопку **ОК**. Этот параметр должен быть включен по умолчанию.
10. Откройте окно PowerShell и выполните следующую команду: ```Set-AdfsProperties -AuditLevel Verbose```.

Обратите внимание, что по умолчанию включен "базовый" уровень аудита. См. дополнительные сведения об [усовершенствовании аудита AD FS в Windows Server 2016](https://docs.microsoft.com/windows-server/identity/ad-fs/technical-reference/auditing-enhancements-to-ad-fs-in-windows-server).


#### <a name="to-locate-the-ad-fs-audit-logs"></a>Поиск журналов аудита AD FS
1. Откройте **Средство просмотра событий**.
2. Перейдите к разделу "Журналы Windows" и выберите пункт **Безопасность**.
3. Справа щелкните **Фильтровать текущие журналы**.
4. В разделе «Источник события» выберите **Аудит AD FS**.

    Краткое [примечание из раздела часто задаваемых вопросов](active-directory-aadconnect-health-faq.md#operations-questions) о журналах аудита.

![Журналы аудита AD FS](./media/active-directory-aadconnect-health-requirements/adfsaudit.png)

> [!WARNING]
> Групповая политика может отключить аудит AD FS. Если аудит AD FS отключен, аналитика по использованию в отношении действий входа недоступна. Убедитесь, что групповая политика, которая может отключить аудит AD FS, отсутствует.>
>


## <a name="installing-the-azure-ad-connect-health-agent-for-sync"></a>Установка агента Azure AD Connect Health для синхронизации
В последней сборке Azure AD Connect агент Azure AD Connect Health для синхронизации устанавливается автоматически. Чтобы использовать Azure AD Connect для синхронизации, скачайте и установите последнюю версию Azure AD Connect. Последнюю версию можно загрузить [здесь](http://www.microsoft.com/download/details.aspx?id=47594).

Чтобы проверить, установлен ли агент, найдите на сервере приведенные ниже службы. Если вы выполнили настройку, эти службы должны работать. Они не запустятся, пока не будет выполнена настройка.

* Служба Sync Insights Azure AD Connect Health
* Служба Sync Monitoring Azure AD Connect Health

![Проверка Azure AD Connect Health для синхронизации](./media/active-directory-aadconnect-health-sync/services.png)

> [!NOTE]
> Помните, что для использования службы Azure AD Connect Health требуется Azure AD Premium. Если Azure AD Premium отсутствует, вы не сможете завершить конфигурацию на портале Azure. Дополнительные сведения о требованиях см. [здесь](active-directory-aadconnect-health-agent-install.md#requirements).
>
>

## <a name="manual-azure-ad-connect-health-for-sync-registration"></a>Регистрация агента Azure AD Connect Health для синхронизации вручную
Если после успешной установки Azure AD Connect произойдет сбой регистрации агента Azure AD Connect Health для синхронизации, можно использовать следующую команду PowerShell, чтобы зарегистрировать агент вручную.

> [!IMPORTANT]
> Использование этой команды PowerShell необходимо только в том случае, если после установки Azure AD Connect произошел сбой регистрации агента.
>
>

Следующая команда PowerShell требуется, ТОЛЬКО если регистрация агента работоспособности завершается сбоем даже после успешной установки и настройки Azure AD Connect. Службы Azure AD Connect Health запустятся после того, как агент будет успешно зарегистрирован.

Агент Azure AD Connect Health для синхронизации можно зарегистрировать вручную с помощью следующей команды PowerShell:

`Register-AzureADConnectHealthSyncAgent -AttributeFiltering $false -StagingMode $false`

Команда принимает следующие параметры:

* AttributeFiltering. Значение $true (по умолчанию) используется, если служба Azure AD Connect не синхронизирует набор атрибутов по умолчанию и была настроена для использования отфильтрованного набора атрибутов. В противном случае используется значение $false.
* StagingMode. Значение $false (по умолчанию) используется, если сервер Azure AD Connect НЕ находится в промежуточном режиме, $true — если сервер настроен на пребывание в промежуточном режиме.

При появлении запроса на аутентификацию следует использовать учетную запись глобального администратора (например, admin@domain.onmicrosoft.com) которая использовалась для настройки Azure AD Connect.

## <a name="installing-the-azure-ad-connect-health-agent-for-ad-ds"></a>Установка агента Azure AD Connect Health для AD DS
Дважды щелкните скачанный EXE-файл, чтобы начать установку агента. На первом экране щелкните «Установить».

![Проверка Azure AD Connect Health](./media/active-directory-aadconnect-health/aadconnect-health-adds-agent-install1.png)

После завершения установки нажмите кнопку «Настроить сейчас».

![Проверка Azure AD Connect Health](./media/active-directory-aadconnect-health/aadconnect-health-adds-agent-install2.png)

Это приведет к запуску командной строки и оболочки PowerShell, которая выполнит команду Register-AzureADConnectHealthADDSAgent. При появлении запроса на вход войдите в Azure.

![Проверка Azure AD Connect Health](./media/active-directory-aadconnect-health/aadconnect-health-adds-agent-install3.png)

После входа PowerShell продолжит выполнять команду. После завершения можно закрыть PowerShell. Настройка завершена.

На этом этапе службы должны запускаться автоматически, а агент будет выполнять мониторинг и сбор данных. Если вы не выполнили все необходимые предварительные требования, которые были описаны в предыдущих разделах, в окне PowerShell будут отображаться предупреждения. Обязательно выполните эти [требования](active-directory-aadconnect-health-agent-install.md#requirements), прежде чем устанавливать агент. В качестве примера этих ошибок приведен следующий снимок экрана.

![Проверка Azure AD Connect Health для AD DS](./media/active-directory-aadconnect-health/aadconnect-health-adds-agent-install4.png)

Чтобы проверить, установлен ли агент, найдите приведенные ниже службы в контроллере домена.

* служба AD DS для Azure AD Connect Health;
* служба наблюдения AD DS Azure AD Connect Health.

Если вы выполнили настройку, эти службы должны работать. Они не запустятся, пока не будет выполнена настройка.

![Проверка Azure AD Connect Health](./media/active-directory-aadconnect-health/aadconnect-health-adds-agent-install5.png)


## <a name="agent-registration-using-powershell"></a>Регистрация агента с помощью PowerShell
После установки установочного файла для соответствующего агента в зависимости от роли вы можете зарегистрировать агент с помощью следующих команд PowerShell. Откройте окно PowerShell и выполните соответствующую команду.

```
    Register-AzureADConnectHealthADFSAgent
    Register-AzureADConnectHealthADDSAgent
    Register-AzureADConnectHealthSyncAgent

```

Эти команды принимают учетные данные в качестве параметра, чтобы завершить регистрацию в неинтерактивном режиме или на основном серверном компьютере.
* Учетные данные можно указать в переменной PowerShell, которая передается в качестве параметра.
* Можно указать любое удостоверение Azure AD, которое имеет доступ на регистрацию агентов и в котором отключена Многофакторная идентификация.
* По умолчанию глобальные администраторы имеют доступ на выполнение регистрации агента. Можно также разрешить выполнять этот шаг другим, менее привилегированным пользователям. Дополнительные сведения см. в статье [Контроль доступа на основе ролей в Azure](active-directory-aadconnect-health-operations.md#manage-access-with-role-based-access-control).

```
    $cred = Get-Credential
    Register-AzureADConnectHealthADFSAgent -Credential $cred

```

## <a name="configure-azure-ad-connect-health-agents-to-use-http-proxy"></a>Настройка агентов Azure AD Connect Health для использования HTTP-прокси
Вы можете настроить агенты Azure AD Connect Health на работу с HTTP-прокси.

> [!NOTE]
> * Команда Netsh WinHttp set ProxyServerAddress не поддерживается, потому что для создания веб-запросов агент применяет System.Net, а не службы Microsoft Windows HTTP.
> * Настроенный адрес прокси-сервера HTTP будет использоваться для передачи зашифрованных сообщений HTTPS.
> * Прокси-серверы с проверкой подлинности (с помощью HTTPBasic) не поддерживаются.
>
>

### <a name="change-health-agent-proxy-configuration"></a>Изменение конфигурации прокси-сервера агента работоспособности
Доступны следующие варианты настройки агента Azure AD Connect Health для использования HTTP-прокси.

> [!NOTE]
> Необходимо перезапустить все службы агента Azure AD Connect Health, чтобы обновить параметры прокси-сервера. Выполните следующую команду:<br>
> Restart-Service AdHealth*
>
>

#### <a name="import-existing-proxy-settings"></a>Импорт имеющихся параметров прокси-сервера
##### <a name="import-from-internet-explorer"></a>Импорт из Internet Explorer
Можно импортировать параметры прокси-сервера HTTP из Internet Explorer для использования агентами Azure AD Connect Health. Для этого на каждом сервере, где запущен агент работоспособности, выполните следующую команду PowerShell:

    Set-AzureAdConnectHealthProxySettings -ImportFromInternetSettings

##### <a name="import-from-winhttp"></a>Импорт из WinHTTP
Можно импортировать параметры WinHTTP для использования агентами Azure AD Connect Health. Для этого на каждом сервере, где запущен агент работоспособности, выполните следующую команду PowerShell:

    Set-AzureAdConnectHealthProxySettings -ImportFromWinHttp

#### <a name="specify-proxy-addresses-manually"></a>Задание адресов прокси-сервера вручную
Вы можете вручную задать адрес прокси-сервера, выполнив следующую команду PowerShell на каждом сервере, где запущен агент работоспособности.

    Set-AzureAdConnectHealthProxySettings -HttpsProxyAddress address:port

Пример: *Set-AzureAdConnectHealthProxySettings -HttpsProxyAddress myproxyserver:443*.

* Значение "address" может быть именем сервера, разрешимым в DNS, или адресом IPv4.
* Значение "port" можно опустить. Если это значение не указано, в качестве порта по умолчанию используется 443.

#### <a name="clear-existing-proxy-configuration"></a>Очистка существующей конфигурации прокси-сервера
Чтобы очистить текущую конфигурацию прокси-сервера, выполните следующую команду:

    Set-AzureAdConnectHealthProxySettings -NoProxy


### <a name="read-current-proxy-settings"></a>Считывание текущих параметров прокси-сервера
Вы можете считать текущие параметры прокси-сервера с помощью следующей команды:

    Get-AzureAdConnectHealthProxySettings


## <a name="test-connectivity-to-azure-ad-connect-health-service"></a>Проверка подключения к службе Azure AD Connect Health
Существуют проблемы, при которых агент Azure AD Connect Health может потерять соединение со службой Azure AD Connect Health. К ним относятся проблемы с сетью, с правами доступа и другие причины.

Если агент не может отправить данные службе Azure AD Connect Health более 2 часов, на портале появится следующее оповещение: "Данные службы работоспособности неактуальны". Чтобы проверить, может ли затронутый агент Azure AD Connect Health передавать данные в службу Azure AD Connect Health, выполните следующую команду PowerShell:

    Test-AzureADConnectHealthConnectivity -Role ADFS

Параметр role в настоящее время принимает следующие значения:

* ADFS
* Sync
* ADDS

> [!NOTE]
> Чтобы использовать средство подключения, необходимо сначала завершить регистрацию агента. Если зарегистрировать агент не получается, убедитесь, что соблюдены все [требования](active-directory-aadconnect-health-agent-install.md#requirements) для Azure AD Connect Health. Эта проверка подключения выполняется по умолчанию во время регистрации агента.
>
>

## <a name="related-links"></a>Связанные ссылки
* [Azure AD Connect Health](active-directory-aadconnect-health.md)
* [Операции Azure AD Connect Health](active-directory-aadconnect-health-operations.md)
* [Использование Azure AD Connect Health с AD FS](active-directory-aadconnect-health-adfs.md)
* [Использование Azure AD Connect Health для синхронизации](active-directory-aadconnect-health-sync.md)
* [Using Azure AD Connect Health with AD DS (Использование Azure AD Connect Health с AD DS)](active-directory-aadconnect-health-adds.md)
* [Часто задаваемые вопросы об Azure AD Connect Health](active-directory-aadconnect-health-faq.md)
* [Azure AD Connect Health: история версий](active-directory-aadconnect-health-version-history.md)
