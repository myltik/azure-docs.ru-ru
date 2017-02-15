---
title: "Настройка параметров прокси-сервера и брандмауэра в службе Log Analytics | Документация Майкрософт"
description: "Настройка параметров прокси-сервера и брандмауэра требуется, если агенты или службы OMS должны использовать определенные порты."
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: b55ebd80-efd4-4220-971b-c18aea1b1ab2
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/06/2017
ms.author: banders;magoedte
translationtype: Human Translation
ms.sourcegitcommit: d5d86a0f7177b9a1e96e50a3e3e7d1f5800974bf
ms.openlocfilehash: 427d5d7ed43f19611e99705dab33a0c80a8bf9f9


---
# <a name="configure-proxy-and-firewall-settings-in-log-analytics"></a>Настройка параметров прокси-сервера и брандмауэра в службе Log Analytics
Действия, необходимые для настройки параметров прокси-сервера и брандмауэра для службы Log Analytics в OMS, отличаются при работе с Operations Manager и его агентами и при работе с агентами Microsoft Monitoring Agent, которые напрямую подключаются к серверам. Просмотрите следующие разделы для типа агента, который используете.

## <a name="configure-proxy-and-firewall-settings-with-the-microsoft-monitoring-agent"></a>Настройка параметров прокси-сервера и брандмауэра с помощью Microsoft Monitoring Agent
Чтобы Microsoft Monitoring Agent мог подключиться к службе OMS и зарегистрироваться в ней, ему нужен доступ к номеру порта ваших доменов и URL-адресов. При использовании прокси-сервера для обмена данными между агентом и службой OMS необходимо убедиться, что соответствующие ресурсы доступны. Если используется брандмауэр, чтобы ограничить доступ к Интернету, его необходимо настроить таким образом, чтобы разрешить доступ к OMS. В следующих таблицах перечислены порты, необходимые для работы OMS.

| **Ресурс агента** | **Порты** | **Обход проверки HTTPS** |
| --- | --- | --- |
| \*.ods.opinsights.azure.com |443 |Да |
| \*.oms.opinsights.azure.com |443 |Да |
| \*.blob.core.windows.net |443 |Да |
| \*.azure-automation.net |443 |Да |
| ods.systemcenteradvisor.com |443 | |

Далее описано, как настроить параметры прокси-сервера для Microsoft Monitoring Agent с помощью панели управления. Эту процедуру необходимо использовать для каждого сервера. Если вам нужно настроить несколько серверов, возможно, проще использовать скрипт для автоматизации этого процесса. Далее описано, [как настроить параметры прокси-сервера для Microsoft Monitoring Agent с помощью скрипта](#to-configure-proxy-settings-for-the-microsoft-monitoring-agent-using-a-script).

### <a name="to-configure-proxy-settings-for-the-microsoft-monitoring-agent-using-control-panel"></a>Настройка параметров прокси-сервера для Microsoft Monitoring Agent с помощью панели управления
1. Откройте **Панель управления**.
2. Откройте **Microsoft Monitoring Agent**.
3. Перейдите на вкладку **Параметры прокси-сервера**.<br>  
   ![вкладка параметров прокси-сервера](./media/log-analytics-proxy-firewall/proxy-direct-agent-proxy.png)
4. Выберите **Использовать прокси-сервер** и введите URL-адрес и номер порта (если он нужен), как показано в примере. Если для доступа к прокси-серверу требуется проверка подлинности, введите имя пользователя и пароль.

Используйте следующую процедуру для создания сценария PowerShell, который можно запустить для установки параметров прокси-сервера для каждого агента, который подключается непосредственно к серверам.

### <a name="to-configure-proxy-settings-for-the-microsoft-monitoring-agent-using-a-script"></a>как настроить параметры прокси-сервера для Microsoft Monitoring Agent с помощью скрипта
Скопируйте следующий пример, измените его, указав данные своей среды, сохраните его в файл с расширением PS1, затем выполните сценарий на каждом компьютере, который подключается непосредственно к службе OMS.

    param($ProxyDomainName="http://proxy.contoso.com:80", $cred=(Get-Credential))

    # First we get the Health Service configuration object.  We need to determine if we
    #have the right update rollup with the API we need.  If not, no need to run the rest of the script.
    $healthServiceSettings = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'

    $proxyMethod = $healthServiceSettings | Get-Member -Name 'SetProxyInfo'

    if (!$proxyMethod)
    {
         Write-Output 'Health Service proxy API not present, will not update settings.'
         return
    }

    Write-Output "Clearing proxy settings."
    $healthServiceSettings.SetProxyInfo('', '', '')

    $ProxyUserName = $cred.username

    Write-Output "Setting proxy to $ProxyDomainName with proxy username $ProxyUserName."
    $healthServiceSettings.SetProxyInfo($ProxyDomainName, $ProxyUserName, $cred.GetNetworkCredential().password)


## <a name="configure-proxy-and-firewall-settings-with-operations-manager"></a>Настройка параметров прокси-сервера и брандмауэра с помощью Operations Manager
Чтобы группа управления Operations Manager могла подключиться к службе OMS и зарегистрироваться в ней, ей нужен доступ к номерам портов ваших доменов и URL-адресам. При использовании прокси-сервера для обмена данными между сервером управления Operations Manager и службой OMS необходимо убедиться, что соответствующие ресурсы доступны. Если используется брандмауэр, чтобы ограничить доступ к Интернету, его необходимо настроить таким образом, чтобы разрешить доступ к OMS. Даже в том случае, если сервер управления Operations Manager не располагается за прокси-сервером, там могут находиться его агенты. В этом случае прокси-сервер должен быть настроен так же, как агенты, чтобы включить и разрешить отправку данных решения обеспечения безопасности и управления журналами в веб-службу OMS.

Чтобы агенты Operations Manager могли взаимодействовать со службой OMS, ваша инфраструктура Operations Manager (включая агенты) должна иметь правильные настройки и версию прокси-сервера. Настройка прокси-сервера для агентов указывается на консоли Operations Manager. У вас должна быть одна из следующих версий:

* Operations Manager 2012 SP1 с накопительным пакетом обновления 7 или более поздняя версия;
* Operations Manager 2012 R2 с накопительным пакетом обновления 3 или более поздняя версия.

В следующих таблицах перечислены порты, связанные с этими задачами.

> [!NOTE]
> В некоторых из следующих ресурсов упоминаются Advisor и Operational Insights — предыдущие версии OMS. Тем не менее, в будущем перечисленные ресурсы будут изменены.
>
>

Список ресурсов и портов агента:<br>

| **Ресурс агента** | **Порты** |
| --- | --- |
| \*.ods.opinsights.azure.com |443 |
| \*.oms.opinsights.azure.com |443 |
| \*.blob.core.windows.net/\* |443 |
| ods.systemcenteradvisor.com |443 |

<br>
Список ресурсов и портов сервера управления:<br>

| **Ресурс сервера управления** | **Порты** | **Обход проверки HTTPS** |
| --- | --- | --- |
| service.systemcenteradvisor.com |443 | |
| \*.service.opinsights.azure.com |443 | |
| \*.blob.core.windows.net |443 |Да |
| data.systemcenteradvisor.com |443 | |
| ods.systemcenteradvisor.com |443 | |
| \*.ods.opinsights.azure.com |443 |Да |
| \*.azure-automation.net |443 |Да |

<br>
Список ресурсов OMS и консоли Operations Manager:<br>

| **Ресурс OMS и консоли Operations Manager** | **Порты** |
| --- | --- |
| service.systemcenteradvisor.com |443 |
| \*.service.opinsights.azure.com |443 |
| \*.live.com |Порты 80 и 443 |
| \*microsoft.com |Порты 80 и 443 |
| \*.microsoftonline.com |Порты 80 и 443 |
| \*.mms.microsoft.com |Порты 80 и 443 |
| login.windows.net |Порты 80 и 443 |

<br>

Используйте следующие процедуры для регистрации группы управления Operations Manager в службе OMS. При возникновении проблем связи между группой управления и службой OMS используйте процедуры проверки, чтобы устранить неполадки передачи данных в службу OMS.

### <a name="to-request-exceptions-for-the-oms-service-endpoints"></a>Запрос исключений для конечных точек службы OMS
1. Используйте информацию из первой таблицы, представленной ранее, чтобы убедиться, что ресурсы, необходимые для сервера управления Operations Manager, доступны через все имеющиеся у вас брандмауэры.
2. Используйте информацию из второй таблицы, чтобы убедиться, что ресурсы, необходимые для консоли управления в Operations Manager и OMS, доступны через все имеющиеся у вас брандмауэры.
3. Если используется прокси-сервер и Internet Explorer, убедитесь, что он правильно настроен и работает. Чтобы проверить это, можно открыть безопасное интернет-подключение (HTTPS), например [https://bing.com](https://bing.com). Если безопасное интернет-подключение не работает в браузере, вероятно, оно не будет работать и в консоли управления Operations Manager с веб-службами в облаке.

### <a name="to-configure-the-proxy-server-in-the-operations-manager-console"></a>Настройка прокси-сервера в консоли Operations Manager
1. Откройте консоль Operations Manager и выберите рабочую область **Администрирование** .
2. Разверните **Operational Insights** и выберите **Operational Insights Connection** (Подключение к Operational Insights).<br>  
   ![Подключение к OMS Operations Manager](./media/log-analytics-proxy-firewall/proxy-om01.png)
3. В представлении "Подключение к OMS" щелкните **Настройка прокси-сервера**.<br>  
   ![Настройка прокси-сервера для подключения к OMS Operations Manager](./media/log-analytics-proxy-firewall/proxy-om02.png)
4. В мастере настройки прокси-сервера Operational Insights выберите **Use a proxy server to access the Operational Insights Web Service** (Использовать прокси-сервер для доступа к веб-службе Operational Insights), а затем введите URL-адрес с номером порта, например **http://myproxy:80**.<br>  
   ![Прокси-адрес OMS Operations Manager](./media/log-analytics-proxy-firewall/proxy-om03.png)

### <a name="to-specify-credentials-if-the-proxy-server-requires-authentication"></a>Указание учетных данных, если для прокси-сервера требуется аутентификация
 Учетные данные и параметры прокси-сервера необходимо распространить на управляемые компьютеры, которые будут отправлять отчеты в OMS. Эти серверы должны находиться в *группе серверов мониторинга Microsoft System Center Advisor*. Учетные данные зашифрованы в реестре каждого сервера в группе.

1. Откройте консоль Operations Manager и выберите рабочую область **Администрирование** .
2. В разделе **Конфигурация запуска от имени** выберите **Профили**.
3. Откройте профиль **Запуск от имени прокси-сервера профиля System Center Advisor** .<br>  
   ![Изображение запуска от имени прокси-сервера профиля System Center Advisor](./media/log-analytics-proxy-firewall/proxy-proxyacct1.png)
4. В мастере профиля запуска от имени нажмите кнопку **Добавить** , чтобы использовать учетную запись запуска от имени. Можно создать новую учетную запись запуска от имени или использовать существующую учетную запись. Эта учетная запись должна иметь достаточные разрешения для передачи данных через прокси-сервер.<br>   
   ![Изображение мастера запуска от имени профиля](./media/log-analytics-proxy-firewall/proxy-proxyacct2.png)
5. Чтобы настроить учетную запись для управления, выберите **Выбранный класс, группа или объект** для открытия окна поиска объектов.<br>  
   ![Изображение мастера запуска от имени профиля](./media/log-analytics-proxy-firewall/proxy-proxyacct2-1.png)
6. Найдите и выберите **группу серверов мониторинга Microsoft System Center Advisor**.<br>  
   ![Изображение поля поиска объектов](./media/log-analytics-proxy-firewall/proxy-proxyacct3.png)
7. Нажмите кнопку **ОК** , чтобы закрыть окно "Добавление учетной записи запуска от имени".<br>  
   ![Изображение мастера запуска от имени профиля](./media/log-analytics-proxy-firewall/proxy-proxyacct4.png)
8. Закройте мастер и сохраните изменения.<br>  
   ![изображение мастера запуска от имени профиля](./media/log-analytics-proxy-firewall/proxy-proxyacct5.png)

### <a name="to-validate-that-oms-management-packs-are-downloaded"></a>Вот как можно проверить, скачаны ли пакеты управления OMS
Если вы добавили решения в OMS, их можно просмотреть в консоли Operations Manager как пакеты управления, в разделе **Администрирование**. Выполните поиск *System Center Advisor* , чтобы быстро их найти.<br>  
   ![пакеты управления скачаны](./media/log-analytics-proxy-firewall/proxy-mpdownloaded.png)  <br>  
Пакеты управления OMS можно также проверить с помощью следующих команд Windows PowerShell на сервере управления Operations Manager:

   ```  
    Get-ScomManagementPack | where {$_.DisplayName -match 'Advisor'} | select Name,DisplayName,Version,KeyToken
   ```  

### <a name="to-validate-that-operations-manager-is-sending-data-to-the-oms-service"></a>Вот как можно проверить, отправляет ли Operations Manager данные в службу OMS
1. На сервере управления Operations Manager откройте системный монитор (perfmon.exe) и выберите **Системный монитор**.
2. Щелкните **Добавить** и выберите **Группы управления службы работоспособности**.
3. Добавьте все счетчики, которые начинаются с **HTTP**.<br>  
   ![Добавление счетчиков](./media/log-analytics-proxy-firewall/proxy-sendingdata1.png)
4. Если Operations Manager настроен правильно, вы увидите активность счетчиков управления службы работоспособности для событий и других данных для пакетов управления, которые вы добавили в OMS и для которых настроили политику сбора данных журналов.<br>  
   ![Отображение активности на мониторе производительности](./media/log-analytics-proxy-firewall/proxy-sendingdata2.png)

## <a name="next-steps"></a>Дальнейшие действия
* [добавьте решения Log Analytics из коллекции решений](log-analytics-add-solutions.md) .
* Подробная информация, которую собирают решения, описана в статье [о поиске по журналам](log-analytics-log-searches.md) .



<!--HONumber=Jan17_HO1-->


