<properties
   pageTitle="Настройка параметров прокси-сервера и брандмауэра для Operational Insights"
   description="Узнайте о параметрах прокси-сервера и брандмауэра, которые необходимо настроить для типа агента, используемого для Operational Insights."
   services="operational-insights"
   documentationCenter=""
   authors="bandersmsft"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="operational-insights"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/10/2015"
   ms.author="banders" />

# Настройка параметров прокси-сервера и брандмауэра для Operational Insights

[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

Действия, необходимые для настройки параметров прокси-сервера и брандмауэра для оперативной аналитики, отличаются при работе с Operations Manager и его агентами и при работе с агентами Microsoft Monitoring Agent, которые напрямую подключаются к серверам. Просмотрите следующие разделы для типа агента, который используете.

## Настройка параметров прокси-сервера и брандмауэра с помощью Microsoft Monitoring Agent

Чтобы Microsoft Monitoring Agent мог подключиться к службе оперативной аналитики и зарегистрироваться в ней, ему нужен доступ к номеру порта ваших доменов и URL-адресов. При использовании прокси-сервера для обмена данными между агентом и службой Operational Insights необходимо убедиться, что соответствующие ресурсы доступны. Если используется брандмауэр, чтобы ограничить доступ к Интернету, его необходимо настроить таким образом, чтобы разрешить доступ к Operational Insights. В следующих таблицах перечислены порты, необходимые для работы Operational Insights.

|**Ресурс агента**|**Порты**|
|--------------|-----|
|*.ods.opinsights.azure.com|Порт 443|
|*.oms.opinsights.azure.com|Порт 443|
|ods.systemcenteradvisor.com|Порт 443|
|*.blob.core.windows.net/*|Порт 443|

Далее описано, как настроить параметры прокси-сервера для Microsoft Monitoring Agent с помощью панели управления. Эту процедуру необходимо использовать для каждого сервера. Если вам нужно настроить несколько серверов, возможно, проще использовать скрипт для автоматизации этого процесса. Далее описано, *как настроить параметры прокси-сервера для Microsoft Monitoring Agent с помощью скрипта*.

### Настройка параметров прокси-сервера для Microsoft Monitoring Agent с помощью панели управления

1. Откройте **Панель управления**.

2. Откройте **Microsoft Monitoring Agent**.

3. Перейдите на вкладку **Параметры прокси-сервера**.![вкладка параметров прокси-сервера](./media/operational-insights-proxy-firewall/direct-agent-proxy.png)

4. Выберите **Использовать прокси-сервер** и введите URL-адрес и номер порта (если он нужен), как показано в примере. Если для доступа к прокси-серверу требуется проверка подлинности, введите имя пользователя и пароль.

Используйте следующую процедуру для создания сценария PowerShell, который можно запустить для установки параметров прокси-сервера для каждого агента, который подключается непосредственно к серверам.

### Настройка параметров прокси-сервера для Microsoft Monitoring Agent с помощью скрипта


- Скопируйте следующий пример, измените его, указав данные своей среды, сохраните его в файл с расширением PS1, затем выполните сценарий на каждом компьютере, который подключается непосредственно к службе Operational Insights.


```
param($ProxyDomainName="http://proxy.contoso.com:80", $cred=(Get-Credential))

# First we get the health service configuration object.  We need to determine if we
# have the right update rollup with the API we need.  If not, no need to run the rest of the script.
$healthServiceSettings = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'

$proxyMethod = $healthServiceSettings | Get-Member -Name 'SetProxyInfo'

if (!$proxyMethod)
{
    Write-Output 'Health Service proxy API not present, will not update settings.'
    return
}


Write-Output "Clearing proxy settings."
$healthServiceSettings.SetProxyInfo('', '', '')

$ProxyUserName = $cred.username;


Write-Output "Setting Proxy to ${ProxyDomainName} with proxy username of (${ProxyUserName})."
$healthServiceSettings.SetProxyInfo($ProxyDomainName, $ProxyUserName, $cred.GetNetworkCredential().password)
```

## Настройка параметров прокси-сервера и брандмауэра с помощью Operations Manager

Чтобы группа управления Operations Manager могла подключиться к службе Operational Insights и зарегистрироваться в ней, ей нужен доступ к номерам портов ваших доменов и URL-адресам. При использовании прокси-сервера для обмена данными между сервером управления Operations Manager и службой Operational Insights необходимо убедиться, что соответствующие ресурсы доступны. Если используется брандмауэр, чтобы ограничить доступ к Интернету, его необходимо настроить таким образом, чтобы разрешить доступ к Operational Insights. Даже в том случае, если сервер управления Operations Manager не находится за прокси-сервером, такую возможность могут иметь его агенты. В этом случае прокси-сервер должен быть настроен так же, как агенты, чтобы включить и разрешить передачу данных решения обеспечения безопасности и управления журналами в веб-службу Operational Insights.

Чтобы агенты Operations Manager могли взаимодействовать со службой Operational Insights, ваша инфраструктура Operations Manager (включая агенты) должна иметь правильные настройки и версию прокси-сервера. Настройка прокси-сервера для агентов указывается на консоли Operations Manager. У вас должна быть одна из следующих версий:

- Operations Manager 2012 SP1 с накопительным пакетом обновления 7 или более поздняя версия;
- Operations Manager 2012 R2 с накопительным пакетом обновления 3 или более поздняя версия.


В следующих таблицах перечислены порты, связанные с этими задачами.

>[AZURE.NOTE]В некоторых из следующих ресурсов упомянут Advisor. Тем не менее, в будущем перечисленные ресурсы будут изменены.

|**Ресурс агента**|**Порты**|
|--------------|-----|
|*.ods.opinsights.azure.com|Порт 443|
|*.oms.opinsights.azure.com|Порт 443|
|ods.systemcenteradvisor.com|Порт 443|
|*.blob.core.windows.net/*|Порт 443|

|**Ресурс сервера управления**|**Порты**|
|--------------|-----|
|*.ods.opinsights.azure.com|Порт 443|
|service.systemcenteradvisor.com|Порт 443|
|scadvisor.accesscontrol.windows.net|Порт 443|
|scadvisorservice.accesscontrol.windows.net|Порт 443|
|*.blob.core.windows.net/*|Порт 443|
|data.systemcenteradvisor.com|Порт 443|
|ods.systemcenteradvisor.com|Порт 443|
|*.systemcenteradvisor.com|Порт 443|


|**Ресурс консоли Operations Manager и Operational Insights**|**Порты**|
|---|---|
|*.systemcenteradvisor.com|Порт 80 and 443|
|*.live.com|Порт 80 and 443|
|*.microsoftonline.com|Порт 80 and 443|
|login.windows.net|Порт 80 and 443|


Используйте следующие процедуры для регистрации группы управления Operations Manager в службе Operational Insights. При возникновении проблем связи между группой управления и службой Operational Insights используйте процедуры проверки, чтобы устранить неполадки передачи данных в службу Operational Insights.

### Запрос исключений для конечных точек службы Operational Insights

1. Используйте информацию из первой таблицы, представленной ранее, чтобы убедиться, что ресурсы, необходимые для сервера управления Operations Manager, доступны через все имеющиеся у вас брандмауэры.

2. Используйте информацию из второй таблицы, чтобы убедиться, что ресурсы, необходимые для консоли управления в Operations Manager и Operational Insights, доступны через все имеющиеся у вас брандмауэры.

3. Если используется прокси-сервер и Internet Explorer, убедитесь, что он правильно настроен и работает. Чтобы проверить это, можно открыть безопасное интернет-подключение (https), например [https://bing.com](https://bing.com). Если безопасное интернет-подключение не работает в браузере, вероятно, оно не будет работать и в консоли управления Operations Manager с веб-службами в облаке.

### Настройка прокси-сервера в консоли Operations Manager

1. Откройте консоль Operations Manager и выберите рабочую область **Администрирование**.

2. Разверните **оперативную аналитику** и выберите **Подключение к оперативной аналитике**. ![Подключение оперативной аналитики Operations Manager](./media/operational-insights-proxy-firewall/proxy-om01.png)

3. В представлении «Подключение к оперативной аналитике» щелкните **Настройка прокси-сервера**. ![Настройка прокси-сервера для подключения к оперативной аналитике Operations Manager](./media/operational-insights-proxy-firewall/proxy-om02.png)

4. В мастере настройки прокси-сервера оперативной аналитики выберите **Использовать прокси-сервер для доступа к веб-службе оперативной аналитики**, а затем введите URL-адрес с номером порта, например ****http://myproxy:80**. ![Адрес прокси-сервера оперативной аналитики Operations Manager](./media/operational-insights-proxy-firewall/proxy-om03.png)


### Указание учетных данных, если для прокси-сервера требуется аутентификация
 Учетные данные и параметры прокси-сервера необходимо распространить на управляемые компьютеры, которые будут отправлять отчеты в Operational Insights. Эти серверы должны находиться в *группе серверов мониторинга Microsoft System Center Advisor*. Учетные данные зашифрованы в реестре каждого сервера в группе.

1. Откройте консоль Operations Manager и выберите рабочую область **Администрирование**.

2. В разделе **Конфигурации запуска от имени** выберите **Профили**.

3. Откройте профиль **Запуск от имени прокси-сервера профиля System Center Advisor**. ![Изображение «Запуск от имени прокси-сервера профиля System Center Advisor»](./media/operational-insights-proxy-firewall/proxyacct1.png)
4. В мастере профиля запуска от имени нажмите кнопку **Добавить**, чтобы использовать учетную запись запуска от имени. Можно создать новую учетную запись запуска от имени или использовать существующую учетную запись. Эта учетная запись должна иметь достаточные разрешения для передачи данных через прокси-сервер. ![изображение мастера запуска от имени профиля](./media/operational-insights-proxy-firewall/proxyacct2.png)

5. Чтобы настроить учетную запись для управления, выберите **Выбранный класс, группа или объект** для открытия окна поиска объектов. ![изображение мастера запуска от имени профиля](./media/operational-insights-proxy-firewall/proxyacct2-1.png)
6. Найдите и выберите **группу серверов мониторинга Microsoft System Center Advisor**. ![изображение поля поиска объектов](./media/operational-insights-proxy-firewall/proxyacct3.png)
7. Нажмите кнопку **ОК**, чтобы закрыть окно «Добавление учетной записи запуска от имени». ![изображение мастера запуска от имени профиля](./media/operational-insights-proxy-firewall/proxyacct4.png)
8. Закройте мастер и сохраните изменения. ![изображение мастера запуска от имени профиля](./media/operational-insights-proxy-firewall/proxyacct5.png)


### Вот как можно проверить, скачаны ли пакеты управления Operational Insights

- Если вы добавили решения с помощью Operational Insights, их можно просмотреть в консоли Operations Manager как пакеты управления, в разделе **Администрирование**. Выполните поиск *System Center Advisor*, чтобы быстро их найти. ![пакеты управления скачаны](./media/operational-insights-proxy-firewall/mpdownloaded.png)
- Пакеты управления оперативной аналитики можно также проверить с помощью следующих команд Windows PowerShell на сервере управления Operations Manager:

        get-scommanagementpack | where {$_.DisplayName -match 'Advisor'} | select Name,DisplayName,Version,KeyToken

        get-scommanagementpack | where {$_.DisplayName -match 'Advisor'} | select Name,DisplayName,Version | ft

### Вот как можно проверить, отправляет ли Operations Manager данные в службу Operational Insights

1. На сервере управления Operations Manager откройте системный монитор (perfmon.exe) и выберите **Системный монитор**.

2. Щелкните **Добавить** и выберите **Группы управления службы работоспособности**.

3. Добавьте все счетчики, которые начинаются с **HTTP**. ![добавление счетчиков](./media/operational-insights-proxy-firewall/sendingdata1.png)
4. Если Operations Manager настроен правильно, вы увидите активность счетчиков управления службы работоспособности для событий и других данных для пакетов управления, которые вы добавили в оперативной аналитике и для которых настроили политику сбора данных журналов. ![Отображение активности на мониторе производительности](./media/operational-insights-proxy-firewall/sendingdata2.png)

<!---HONumber=Sept15_HO3-->