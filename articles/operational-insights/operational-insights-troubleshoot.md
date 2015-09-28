<properties
   pageTitle="Устранение неполадок с оперативной аналитикой"
   description="Дополнительные сведения об устранении неполадок с оперативной аналитикой"
   services="operational-insights"
   documentationCenter=""
   authors="bandersmsft"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="operational-insights"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/10/2015"
   ms.author="banders" />

# Устранение неполадок с оперативной аналитикой

[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

Для устранения неполадок используйте сведения в следующих разделах. Если ваша проблема не описана в этой статье, попробуйте найти ее в [блоге группы Operational Insights](http://blogs.technet.com/b/momteam/archive/2014/05/29/advisor-error-3000-unable-to-register-to-the-advisor-service-amp-onboarding-troubleshooting-steps.aspx).

## Устранение неполадок разрешений с помощью оценки SQL
Набор Operations Management Suite (OMS) использует группы управления Microsoft Monitoring Agent и Operations Manager для сбора и отправки данных в службу OMS. Определенной рабочей нагрузке, например SQL Server, требуются привилегии, относящиеся к конкретным рабочим нагрузкам, для выполнения сбора данных в другом контексте безопасности, например в учетной записи домена. Если ваш Microsoft Monitoring Agent подключен через System Center Operations Manager, и вы испытываете проблемы с разрешениями при сборе данных, вам понадобится предоставить учетные данные при настройке учетной записи запуска от имени.

Если пакет управления SQL Server уже используется, следует использовать эту учетную запись Windows для запуска от имени.

### Настройка учетной записи SQL запуска от имени в консоли управления

1. В Operations Manager откройте консоль управления и щелкните **Администрирование**.

2. В разделе **Настройка запуска от имени** щелкните **Профили** и откройте **Профиль запуска от имени Microsoft System Center Advisor**.

3. На странице **Учетные записи запуска от имени** нажмите кнопку **Добавить**.

4. Выберите учетную запись Windows запуска от имени, которая содержит учетные данные, необходимые для SQL Server, или нажмите кнопку **Создать** для ее создания.

    >[AZURE.NOTE]Тип учетной записи «Запуск от имени» должен быть указан как Windows. Учетная запись запуска от имени также должна входить в группу локальных администраторов на всех серверах Windows Server, на которых размещены экземпляры SQL Server.

5. Щелкните **Сохранить**.

6. Измените, а затем выполните следующий пример T-SQL на каждом экземпляре SQL Server, чтобы предоставить минимальные разрешения, необходимые учетной записи запуска от имени для выполнения оценки SQL. Но это не требуется делать в том случае, если учетная запись запуска от имени уже является частью серверной роли sysadmin в экземплярах SQL Server.

```
---
    -- Replace <UserName> with the actual user name being used as Run As Account.
    USE master

    -- Create login for the user, comment this line if login is already created.
    CREATE LOGIN [<UserName>] FROM WINDOWS

    -- Grant permissions to user.
    GRANT VIEW SERVER STATE TO [<UserName>]
    GRANT VIEW ANY DEFINITION TO [<UserName>]
    GRANT VIEW ANY DATABASE TO [<UserName>]

    -- Add database user for all the databases on SQL Server Instance, this is required for connecting to individual databases.
    -- NOTE: This command must be run anytime new databases are added to SQL Server instances.
    EXEC sp_msforeachdb N'USE [?]; CREATE USER [<UserName>] FOR LOGIN [<UserName>];'

```

### To configure the SQL Run As account using Windows PowerShell
Alternatively, you can use the following PowerShell script to set the SQL Run As account. Open a PowerShell window and run the following script after you’ve updated it with your information:

```

    import-module OperationsManager
    New-SCOMManagementGroupConnection "<your management group name>"
     
    $profile = Get-SCOMRunAsProfile -DisplayName "Operational Insights SQL Assessment Run As Profile"
    $account = Get-SCOMrunAsAccount | Where-Object {$_.Name -eq "<your run as account name>"}
    Set-SCOMRunAsProfile -Action "Add" -Profile $Profile -Account $Account
```
After the PowerShell Script finishes executing, perform the T-SQL commands provided above.

## Diagnose connection issues for Operational Insights

Because Microsoft Azure Operational Insights relies on data that is moved to and from the cloud, connection issues can be crippling. Use the following information to understand and solve your connection issues.


**Error message:** The Internet connectivity was verified, but connection to Operational Insights service could not be established. Please try again later.

**Possible causes:**
- The Operational Insights service is under maintenance. Wait until the Operational Insights maintenance is done.
- Your network has blocked Operational Insights. Contact your network administrator and request access to Operational Insights, or use another server as your gateway.

**Error message:** Internet connection could not be established. Please check your proxy settings.

**Possible causes:**
- This server is not connected to the Internet. Check the Internet connectivity status, and connect the server to the Internet.
- The proxy setting is not correct. See [Configure proxy and firewall settings](operational-insights-proxy-firewall.md) for information about how to set or change your proxy settings.
- The proxy server requires authentication. See [Configure proxy and firewall settings](operational-insights-proxy-firewall.md) to learn about how to configure Operations Manager to use a proxy server.


## Troubleshoot SQL Server discovery

If you are running Microsoft SQL Server 2008 R2, and despite deploying the Operations Manager agent, you do not see alerts for this server, you might have a discovery issue.

To confirm if this is the source of your trouble, check for the following two issues:

- In the Operations Manager event log, you see Event ID 4001. This event indicates that there is an invalid class.

- In SQL Server Configuration Manager, when you view SQL Server Services, you see the error message, “The remote procedure call failed. [0x0800706be]”

If both issues are true, you need to install SQL Server 2008 R2 Service Pack 2. To download this service pack, see [SQL Server 2008 R2 Service Pack 2](http://go.microsoft.com/fwlink/?LinkId=271310) in the Microsoft Download Center.

After you install the service pack, you should see Operational Insights data for the server within 24 hours.

## Troubleshoot agents or Operations Manager data flow to Operational Insights

The following set of procedures is meant as a guide to help you troubleshoot your directly-connected agents or Operations Manager deployments configured to report data to Azure Operational Insights.

### Procedure 1: Validate if the right Management Packs get downloaded to your Operations Manager Environment
>[AZURE.NOTE] If you only use Direct Agent, you can skip to the next procedure.

Depending on which solutions (previously called intelligence packs) you have enabled from the OpInsights Portal will you see more or less of these MPs. Search for keyword ‘Advisor’ or ‘Intelligence’ in their name.
You can check for these MPs using OpsMgr PowerShell:

```Powershell
Get-SCOMManagementPack | where {$\_.DisplayName -match 'Advisor'} | Select Name,Sealed,Version Get-SCOMManagementPack | where {$\_.Name -match 'IntelligencePacks'} | Select Name,Sealed,Version ```

>[AZURE.NOTE]При устранении неполадок для решения управления емкостью проверьте, *сколько* у вас пакетов управления с именем, включающим слово «емкость»: в наборе пакетов управления существует два пакета управления с одним отображаемым именем (но разными внутренними идентификаторами). Если один из них не импортировать (это часто случается из-за отсутствия зависимости VMM), другой пакет управления не импортируется, и операция не выполняется повторно.

Должно отображаться три пакета управления, связанных с емкостью: пакет аналитики емкости Microsoft System Center Advisor, пакет аналитики емкости Microsoft System Center Advisor и данные хранилища емкости Microsoft System Center Advisor.

Если вы видите только один или два, а не все три пакета, подождите 5–10 минут, пока Operations Manager повторно скачает и импортирует их, а тем временем проверьте журналы событий на предмет ошибок в этот период.

### Процедура 2. Проверка правильности загруженных решений в Direct Agent
>[AZURE.NOTE]Если вы используете только Operations Manager, можно пропустить эту процедуру.

В Direct Agent политика коллекции решений должна кэшироваться в папке **C:\\Program Files\\Microsoft Monitoring Agent\\Agent\\Health Service State\\Management Packs**.


### Процедура 3. Проверка отправки данных в службу Advisor (или попытки отправки)
В зависимости от того, используете ли вы напрямую подключенные агенты или Operations Manager, можно выполнить следующую процедуру на компьютере Direct Agent или на сервере управления Operations Manager.

1. - Откройте «Системный монитор».
2. - Выберите «Группы управления службы работоспособности».
3. - Добавьте все счетчики, которые начинаются с HTTP.

Если все настроено правильно, по мере отправки событий и других элементов данных (на основе решений, выставленных на портале, и настроенной политики сбора журналов) для этих счетчиков должны появиться действия. Эти счетчики не должны быть постоянно заняты — если действия не происходят, возможно, вы выставили немного решений или у вас очень простая политика сбора.

### Процедура 4. Проверка ошибок в журналах событий сервера управления или Direct Agent
Наконец, если вы выполнили все вышеперечисленное, но служба все еще не получает данные, проверьте ошибки в инструменте **Просмотр событий**.

Откройте **Просмотр событий** > **Приложение и службы** > **Operations Manager** и примените фильтр по источникам событий: **Advisor**, **Модули службы работоспособности**, **Служба работоспособности** и **Соединитель служб** (последнее применяется только к Direct Agent).

Большинство этих событий, а также шаги по устранению неполадок будут аналогичны в Operations Manager и Direct Agent. Единственное отличие между Operations Manager и Direct Agent — это процесс регистрации (GUI в Operations Manager; комбинация идентификатора и ключа рабочей области в Direct Agent), но после первоначальной регистрации происходит обмен сертификатами и все остальное взаимодействие со службой происходит аналогично.

Следовательно, многие из этих событий применяются к обоим типам инфраструктуры отчетности. Далее представлен список наиболее частых из них.

### События из источника «Модули службы работоспособности»
##### EventID 2138
Это означает, что для прокси-сервера требуется проверка подлинности. Выполните действия по [настройке прокси-серверов](https://msdn.microsoft.com/library/azure/dn884643.aspx).

##### EventID 2137
Operations Manager не может прочитать сертификат проверки подлинности. Повторный запуск мастера регистрации Advisor исправит сертификаты и учетные записи запуска от имени

##### EventID 2132
Это означает **Не авторизовано**. Возможно, возникла проблема с сертификатом или регистрацией в службе; попробуйте перезапустить мастер регистрации, который исправит сертификаты и учетные записи запуска от имени. Кроме того, убедитесь, что на прокси-сервере разрешены исключения. Дополнительные сведения см. в разделе [Настройка прокси-серверов](https://msdn.microsoft.com/library/azure/dn884643.aspx).

##### EventID 2129
Это подключение, завершившееся сбоем из-за сбоя согласования SSL. Настройте системы для использования TLS, а не SSL. На этом сервере могут быть заданы необычные параметры SSL для шифров в меню **Дополнительно** Internet Explorer или в разделе реестра Windows.

    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL

##### EventID 2127
При отправке данных о сбое был получен код ошибки. Если это происходит редко, возможно, это простой сбой. Обратите внимание на их частоту. Если сбои происходят очень часто (каждые 10 минут в течение продолжительного периода времени), это действительно может быть ошибкой. Проверьте параметры сети и прокси-сервера, описанные выше, и перезапустите мастер регистрации. Но если это происходит только время от времени (т. е. пару раз в день), все должно быть нормально после того, как данные будут поставлены в очередь и повторно переданы. Возможно, время ожидания сети истекло (если это происходит с такой периодичностью).

Некоторые коды ошибок HTTP имеют следующие специальные значения:

- при ПЕРВОЙ отправке данных MMA Direct Agent или сервером управления в службу появится ошибка 500 с внутренним кодом ошибки 404 (404 означает «не найдено»). Это указывает на то, что область хранилища, которая будет использоваться для этой новой рабочей области, еще не готова — идет ее подготовка. Однако при следующей попытке она будет готова и поток начнет работать (в обычных условиях).
- 403 может указывать на проблему с разрешениями или учетными данными и т. д.

##### EventID 2128
Сбой разрешения DNS-имени. Ваш сервер не может разрешить адрес в Интернете, по которому ему следует отправлять данные. Возможно, неправильно настроены параметры сопоставителя DNS на компьютере, параметры прокси-сервера или возникла (временная) проблема с DNS у поставщика. Как и в предыдущем событии, в зависимости от того, постоянно это происходит или время от времени, это может указывать или не указывать на ошибку.

##### EventID 2130
Время ожидания истекло. Как и в предыдущем событии, в зависимости от того, постоянно это происходит или время от времени, это может указывать или не указывать на ошибку.

### События из источника «Служба работоспособности»
##### EventID 4511
Не удается загрузить модуль System.PublishDataToEndPoint — файл не найден. Сбой инициализации модуля типа System.PublishDataToEndPoint (CLSID {D407D659-65E4-4476-BF40-924E56841465}) с кодом ошибки «Система не может найти указанный файл». Эта ошибка означает, что библиотеки DLL на компьютере устарели и не содержат нужные модули. Чтобы это исправить, обновите серверы управления до последнего накопительного пакета обновления.

##### EventID 4502
Сбой модуля. Если это происходит для рабочих процессов с такими именами, как **CollectInstanceSpace** или **CollectTypeSpace**, возможно, на сервере возникли проблемы с отправкой данных конфигурации. В зависимости от частоты происходящего (постоянно или время от времени) это может указывать или не указывать на ошибку. Если сбой происходит чаще, чем раз в час, это определенно представляет проблему. Если сбой операции происходит один или два раза в день, она будет восстановлена. В зависимости от того, как происходит сбой модуля (в описании содержатся дополнительные сведения), это может быть локальной ошибкой, т. е. при сборе данных в базу данных, или ошибкой отправки в облако. Проверьте параметры сети и прокси-сервера и в крайнем случае попробуйте перезапустить HealthService.

##### EventID 4501
Сбой модуля System.PublishDataToEndPoint. Модуль типа System.PublishDataToEndPoint сообщил об ошибке 87L в рамках правила Microsoft.SystemCenter.CollectAlertChangeDataToCloud, выполняющегося для экземпляра «Группа управления Operations Manager» с идентификатором {6B1D1BE8-EBB4-B425-08DC-2385C5930B04} в группе управления SCOMTEST. Эта ошибка больше *не* должна появляться в этом рабочем процессе и модуле, так как [ошибка* теперь исправлена*](http://feedback.azure.com/forums/267889-azure-operational-insights/suggestions/6714689-alert-management-intelligence-pack-not-sending-ale). При возникновении сообщите о ней по любому предпочтительному каналу поддержки Майкрософт.


### События из источника «Соединитель служб»
##### EventID 4002
Служба вернула код состояния HTTP 403 в ответ на запрос. Обратитесь к администратору службы, чтобы проверить ее работоспособность. Повторная попытка запроса будет выполнена позже. Вы можете получить ошибку 403 во время первоначальной регистрации агента. Появится следующий URL-адрес: https://<YourWorkspaceID>.oms.opinsights.azure.com/AgentService.svc/AgentTopologyRequest. Код ошибки 403 означает «запрещено» — обычно он возникает из-за неправильно скопированного значения идентификатора рабочей области или ключа либо из-за того, что часы на компьютере не синхронизированы. Попробуйте выполнить синхронизацию с надежным источником времени и используйте проверку подключения в приложении панели управления для Microsoft Monitoring Agent, чтобы убедиться, что у вас есть нужный идентификатор рабочей области и ключ.


### Процедура 5. Проверка отправки агентами данных и их индексирования на портале
Откройте портал OpInsights. Со страницы «Обзор» перейдите на небольшой элемент **Серверы и использование**, чтобы посмотреть, отправляют ли группы управления (и их агенты) и агенты Direct Agent данные в систему поиска журналов. Количество агентов на плитке является производным от данных: если компьютеры не сообщают данные в течение 2 недель, радар будет отключен.

Детализация позволит перейти в систему поиска журналов и просмотреть метку времени последних индексированных данных для каждого компьютера. Здесь вы можете просмотреть, что это за данные. Расписание и скорость передачи данных могут различаться в зависимости от заданного объема собираемых данных и решений.

На этой странице также предоставлены сведения об измерении объемов данных с разбивкой по решению (при этом используется не индекс поиска журналов, а система выставления счетов, которая обновляется каждые несколько часов).

<!---HONumber=Sept15_HO3-->