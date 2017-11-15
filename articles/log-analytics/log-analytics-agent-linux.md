---
title: "Подключение компьютеров Linux к Azure Log Analytics | Документация Майкрософт"
description: "В этой статье описывается подключение компьютеров Linux, размещенных в Azure, другой облачной службе или локальной среде, к Log Analytics с помощью агента OMS для Linux."
services: log-analytics
documentationcenter: 
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: 
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/07/2017
ms.author: magoedte
ms.openlocfilehash: 56c666d1a18937df21a6aca8acde87beda1cad8e
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/07/2017
---
# <a name="connect-your-linux-computers-to-log-analytics"></a>Подключение компьютеров Linux к Log Analytics 

С помощью Azure Log Analytics можно собирать и использовать данные, которые создаются на компьютерах под управлением Linux и в контейнерах (например, Docker), расположенных в локальном центре обработки данных как физические серверы или виртуальные машины либо виртуальные машины в облачной службе (например, Amazon Web Services (AWS) или Microsoft Azure). Кроме того, можно использовать доступные в [службе автоматизации Azure](../automation/automation-intro.md) решения для управления: например функцию отслеживания изменений конфигурации и функцию управления обновлениями программного обеспечения. Эти решения позволяют выполнять упреждающее управление жизненным циклом виртуальных машин Linux. 

Агент OMS для Linux обменивается исходящими данными со службой Log Analytics и службой автоматизации Azure через TCP-порт 443. Если компьютер подключен к брандмауэру или прокси-серверу для обмена данными через Интернет, ознакомьтесь с разделом [Настройка агента для использования со шлюзом OMS или прокси-сервером](#configuring-the-agent-for-use-with-a-proxy-server-or-oms-gateway), чтобы применить необходимые изменения конфигурации.  Компьютер, отслеживаемый решением System Center 2016 Operations Manager или Operations Manager 2012 R2, может использоваться как многосетевой. С помощью службы Log Analytics будет выполняться сбор данных и их пересылка в службу, а компьютер по-прежнему будет отслеживаться решением Operations Manager.  Компьютеры Linux, отслеживаемые группой управления Operations Manager, интегрированной с Log Analytics (сейчас называется Operations Management Suite в консоли управления Operations Manager), не получают конфигурацию для источников данных. Они пересылают собранные данные через группы управления.  Агент OMS нельзя настроить для отправки отчетов в несколько рабочих областей Log Analytics.  

Если политики ИТ-безопасности запрещают подключение компьютеров в сети к Интернету, агент можно настроить для подключения к шлюзу OMS. Так он сможет получать сведения о конфигурации и отправлять собранные данные в зависимости от включенного решения. Дополнительные сведения и инструкции по настройке агента OMS Linux для обмена данными со службами через шлюз OMS см. в статье [Подключения компьютеров к OMS с помощью шлюза OMS без доступа к Интернету](log-analytics-oms-gateway.md).  

Ниже представлена схема подключения между компьютерами Linux, управляемыми агентом, и Log Analytics, включая направление и порты.

![Схема взаимодействия прямого агента со службами Azure](./media/log-analytics-agent-linux/log-analytics-agent-linux-communication.png)

## <a name="system-requirements"></a>Требования к системе
Сначала необходимо выполнить следующие требования.

### <a name="supported-linux-operating-systems"></a>Поддерживаемые операционные системы Linux
Официально поддерживаются следующие дистрибутивы Linux.  Однако агент OMS для Linux может выполняться и на других дистрибутивах, помимо следующих:

* Amazon Linux 2012.09–2015.09 (x86/x64)
* CentOS Linux 5, 6 и 7 (x86/x64)
* Oracle Linux 5, 6 и 7 (x86/x64)
* Red Hat Enterprise Linux Server 5, 6 и 7 (x86/x64)
* Debian GNU/Linux 6, 7 и 8 (x86/x64)
* Ubuntu 12.04 LTS, 14.04 LTS, 15.04, 15.10, 16.04 LTS (x86/x64)
* SUSE Linux Enterprise Server 11 и 12 (x86/x64)

### <a name="network"></a>Сеть
Ниже приводятся сведения о конфигурации прокси-сервера и брандмауэра, необходимые для взаимодействия агента Linux с Log Analytics и службой автоматизации Azure. Трафик является исходящим из локальной сети к службе. 

|Ресурс агента| порты; |  
|------|---------|  
|*.ods.opinsights.azure.com | Порт 443|   
|*.oms.opinsights.azure.com | Порт 443|   
|*.blob.core.windows.net/ | Порт 443|   
|*.azure-automation.net | Порт 443|  

### <a name="package-requirements"></a>Требования к пакетам

 **Требуемый пакет**   | **Описание**   | **Минимальная версия**
--------------------- | --------------------- | -------------------
Glibc | Библиотека C GNU   | 2.5-12 
Openssl | Библиотеки OpenSSL | 0.9.8e или 1.0
Curl | Веб-клиент cURL | 7.15.5
Python-ctypes | | 
PAM | Подключаемые модули аутентификации | 

> [!NOTE]
>  Для сбора сообщений системного журнала требуется rsyslog или syslog-ng. Управляющая программа syslog по умолчанию не поддерживается для сбора событий системного журнала в Red Hat Enterprise Linux версии 5, CentOS и Oracle Linux (sysklog). Чтобы собирать данные системного журнала из дистрибутивов этих версий, требуется установить и настроить управляющую программу rsyslog, которая заменит sysklog. 

Этот агент включает в себя несколько пакетов. Файл выпуска содержит указанные ниже пакеты, доступ к которым можно получить, запустив пакет оболочки с помощью команды `--extract`.

**Package** | **Версия** | **Описание**
----------- | ----------- | --------------
omsagent | 1.4.0 | Агент Operations Management Suite для Linux
omsconfig | 1.1.1 | Агент конфигурации для агента OMS
omi | 1.2.0 | OMI (Open Management Infrastructure) — облегченная версия сервера CIM
scx | 1.6.3 | Поставщики OMI CIM для метрик производительности операционной системы
apache-cimprov | 1.0.1 | Поставщик мониторинга производительности сервера Apache HTTP Server для OMI. Устанавливается при обнаружении сервера Apache HTTP Server.
mysql-cimprov | 1.0.1 | Поставщик мониторинга производительности сервера MySQL для OMI. Устанавливается при обнаружении сервера MySQL или MariaDB.
docker-cimprov | 1.0.0 | Поставщик Docker для OMI. Устанавливается при обнаружении Docker.

### <a name="compatibility-with-system-center-operations-manager"></a>Совместимость с System Center Operations Manager
Агент OMS для Linux совместно использует двоичные файлы агента с агентом System Center Operations Manager. При установке агента OMS для Linux в системе под управлением Operations Manager пакеты OMI и SCX на компьютере обновляются до более новой версии. В этом выпуске агенты OMS и System Center 2016 Operations Manager или Operations Manager 2012 R2 для Linux являются совместимыми. 

> [!NOTE]
> Сейчас System Center 2012 с пакетом обновления 1 (SP1) и более ранние версии не совместимы с агентом OMS для Linux и не поддерживаются им.<br>
> Если агент OMS для Linux устанавливается на компьютер, который сейчас не отслеживается решением Operations Manager, но такое отслеживание требуется в будущем, нужно изменить [конфигурацию OMI](#enable-the-oms-agent-for-linux-to-report-to-system-center-operations-manager) до обнаружения компьютера. **Этот шаг *не* нужно выполнять, если агент Operations Manager установлен ранее, чем агент OMS для Linux.**

### <a name="system-configuration-changes"></a>Изменения конфигурации системы
После установки пакетов агента OMS для Linux применяются следующие изменения конфигурации системы. Эти артефакты удаляются после удаления пакета omsagent.

* Создается непривилегированный пользователь с именем `omsagent` . Это учетная запись, от имени которой запускается управляющая программа omsagent.
* В каталоге /etc/sudoers.d/omsagent создается файл sudoers с директивами include. Так пользователь omsagent получает право на перезапуск управляющих программ syslog и omsagent. Если директивы sudo "include" не поддерживаются в установленной версии sudo, эти записи будут записываться в каталог /etc/sudoers.
* Конфигурация системного журнала настраивается для перенаправления подмножества событий агенту. Дополнительные сведения см. в разделе о **настройке сбора данных** ниже.

### <a name="upgrade-from-a-previous-release"></a>Обновление предыдущей версии
В этом выпуске поддерживаются обновления версий ниже 1.0.0-47. При установке с помощью команды `--upgrade` все компоненты агента будут обновлены до последней версии.

## <a name="installing-the-agent"></a>Установка агента

В этом разделе описывается, как установить агент OMS для Linux вручную с помощью пакета, который содержит пакеты Debian и RPM для каждого из компонентов агента.  Его можно установить полностью или можно извлечь его содержимое, чтобы получить отдельные пакеты.  Если вы планируете установить агент на виртуальной машине Azure под управлением Linux, ознакомьтесь со статьей [Сбор данных о виртуальных машинах Azure](log-analytics-quick-collect-azurevm.md), чтобы узнать, как использовать расширения виртуальной машины Log Analytics для его установки.  Следуйте указаниям в разделе *Включение расширения виртуальной машины Log Analytics*.  Для компьютеров Linux, размещенных в среде, можно упростить процесс установки, используя метод на основе сценария, описанный в статье [Сбор данных с компьютеров Linux, размещенных в вашей среде](log-analytics-quick-collect-linux-computer.md).  

> [!NOTE]
> Хотя две указанные выше статьи предназначены для новичков, работающих с Log Analytics, и тех, которые хотят быстро приступить к работе со службой, действия по настройке компьютера актуальны.  Если у вас уже есть рабочая область и вы хотите подключить компьютер Linux, для виртуальной машины Azure под управлением Linux выберите имеющуюся рабочую область, а для компьютеров, размещенных за пределами Azure, скопируйте идентификатор и ключ рабочей области для передачи в сценарий.  

Перед установкой OMS для Linux требуется получить идентификатор и ключ для рабочей области Log Analytics.  

1. Войдите на портал Azure по адресу [https://portal.azure.com](https://portal.azure.com). 
2. На портале Azure щелкните **Другие службы** в нижнем левом углу. В списке ресурсов введите **Log Analytics**. Как только вы начнете вводить символы, список отфильтруется соответствующим образом. Выберите **Log Analytics**.
3. В списке рабочих областей Log Analytics выберите ту, в которую компьютер должен отправлять отчеты.
3. Выберите **Дополнительные параметры**.<br><br> ![Дополнительные параметры Log Analytics](media/log-analytics-quick-collect-azurevm/log-analytics-advanced-settings-01.png)<br><br>  
4. Выберите **Подключенные источники**, а затем выберите **Серверы с Linux**.   
5. Необходимые значения указаны справа от полей **Идентификатор рабочей области** и **Первичный ключ**. Скопируйте их и вставьте в любой удобный для вас редактор.  
6. Скачайте последнюю версию [агента OMS для Linux (x64)](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/download/OMSAgent_GA_v1.4.0-45/omsagent-1.4.0-45.universal.x64.sh) или [агента OMS для Linux (x86)](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/download/OMSAgent_GA_v1.4.0-45/omsagent-1.4.0-45.universal.x86.sh) с сайта GitHub.  
7. Перенесите соответствующий пакет (x86 или x64) на компьютер Linux с помощью scp или sftp.
8. Установите пакет, используя аргумент `--install` или `--upgrade`. 

    > [!NOTE]
    > Используйте аргумент `--upgrade`, если все существующие пакеты (например, агент System Center Operations Manager для Linux) уже установлены. Чтобы подключиться к Operations Management Suite во время установки, используйте параметры `-w <WorkspaceID>` и `-s <Shared Key>`.


#### <a name="to-install-and-onboard-directly"></a>Для установки и прямого подключения
```
sudo sh ./omsagent-<version>.universal.x64.sh --upgrade -w <workspace id> -s <shared key>
```

#### <a name="to-upgrade-the-agent-package"></a>Для обновления пакета агента
```
sudo sh ./omsagent-<version>.universal.x64.sh --upgrade
```

#### <a name="to-install-and-onboard-to-a-workspace-in-us-government-cloud"></a>Для установки и подключения к рабочей области в облаке правительства США
```
sudo sh ./omsagent-<version>.universal.x64.sh --upgrade -w <workspace id> -s <shared key> -d opinsights.azure.us
```

## <a name="configuring-the-agent-for-use-with-a-proxy-server-or-oms-gateway"></a>Настройка агента для использования со шлюзом OMS или прокси-сервером
Агент OMS для Linux поддерживает обмен данными со службой Log Analytics через прокси-сервер или шлюз OMS по протоколу HTTPS.  Поддерживается анонимная и базовая аутентификация (с именем пользователя и паролем).  

### <a name="proxy-configuration"></a>Конфигурация прокси-сервера
Значение конфигурации прокси-сервера имеет следующий синтаксис:

`[protocol://][user:password@]proxyhost[:port]`

Свойство|Описание
-|-
Протокол|HTTPS
user|Необязательное имя пользователя для аутентификации прокси-сервера
пароль|Необязательный пароль для аутентификации прокси-сервера
proxyhost|Адрес или полное доменное имя прокси-сервера или шлюза OMS
порт|Номер дополнительного порта для прокси сервера или OMS шлюза

Например: `https://user01:password@proxy01.contoso.com:30443`

Прокси-сервер можно указать во время установки или при изменении файла конфигурации proxy.conf после установки.   

### <a name="specify-proxy-configuration-during-installation"></a>Определение конфигурации прокси-сервера во время установки
Аргумент `-p` или `--proxy` для установки пакета omsagent определяет используемую конфигурацию прокси-сервера. 

```
sudo sh ./omsagent-<version>.universal.x64.sh --upgrade -p https://<proxy user>:<proxy password>@<proxy address>:<proxy port> -w <workspace id> -s <shared key>
```

### <a name="define-the-proxy-configuration-in-a-file"></a>Определение конфигурации прокси-сервера в файле
Конфигурацию прокси-сервера можно задать в файлах `/etc/opt/microsoft/omsagent/proxy.conf` и `/etc/opt/microsoft/omsagent/conf/proxy.conf `. Файлы можно создать или изменить напрямую, но необходимо обновить их разрешения, чтобы предоставить пользователю omiuser право на чтение файлов. Например:
```
proxyconf="https://proxyuser:proxypassword@proxyserver01:30443"
sudo echo $proxyconf >>/etc/opt/microsoft/omsagent/proxy.conf
sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/proxy.conf
sudo chmod 600 /etc/opt/microsoft/omsagent/proxy.conf /etc/opt/microsoft/omsagent/conf/proxy.conf  
sudo /opt/microsoft/omsagent/bin/service_control restart [<workspace id>]
```

### <a name="removing-the-proxy-configuration"></a>Удаление конфигурации прокси-сервера.
Чтобы удалить ранее заданную конфигурацию прокси-сервера и вернуться к прямому подключению, удалите файл proxy.conf:
```
sudo rm /etc/opt/microsoft/omsagent/proxy.conf /etc/opt/microsoft/omsagent/conf/proxy.conf
sudo /opt/microsoft/omsagent/bin/service_control restart 
```

## <a name="onboarding-with-log-analytics"></a>Подключение к Log Analytics
Если идентификатор и ключ рабочей области не предоставлены во время установки пакета, агент нужно впоследствии зарегистрировать с использованием Log Analytics.

### <a name="onboarding-using-the-command-line"></a>Подключение с помощью командной строки
Выполните команду omsadmin.sh, указав идентификатор и ключ для рабочей области. Эту команду должен выполнять привилегированный пользователь (sudo):
```
cd /opt/microsoft/omsagent/bin
sudo ./omsadmin.sh -w <WorkspaceID> -s <Shared Key>
```

### <a name="register-using-a-file"></a>Регистрация с помощью файла
1.  Создайте файл `/etc/omsagent-onboard.conf`. Файл должен быть доступным для чтения и записи для привилегированного пользователя.
`sudo vi /etc/omsagent-onboard.conf`
2.  Вставьте следующие строки в файл, используя идентификатор рабочей области и общий ключ:

        WORKSPACE_ID=<WorkspaceID>  
        SHARED_KEY=<Shared Key>  
   
3.  Выполните следующую команду, чтобы выполнить регистрацию с помощью Log Analytics: `sudo /opt/microsoft/omsagent/bin/omsadmin.sh`.
4.  Файл будет удален после успешного подключения.

## <a name="enable-the-oms-agent-for-linux-to-report-to-system-center-operations-manager"></a>Включение агента OMS для Linux для передачи отчета в System Center Operations Manager
Чтобы настроить агент OMS для Linux и передать отчет в группу управления System Center Operations Manager, сделайте следующее.  

1. Измените файл `/etc/opt/omi/conf/omiserver.conf`
2. Укажите для **httpsport=** номер порта 1270. Например: `httpsport=1270`.
3. Перезапустите сервер OMI: `sudo /opt/omi/bin/service_control restart`.

## <a name="agent-logs"></a>Журналы агента
Журналы для агента OMS для Linux доступны здесь: `/var/opt/microsoft/omsagent/<workspace id>/log/`. Журналы для программы omsconfig (конфигурация агента) доступны здесь: `/var/opt/microsoft/omsconfig/log/`. Журналы для компонентов OMI и SCX (предоставляют данные метрик производительности) доступны здесь: `/var/opt/omi/log/ and /var/opt/microsoft/scx/log`.

### <a name="log-rotation-configuration"></a>Конфигурация чередования журналов##
Конфигурация чередования журналов для omsagent доступна здесь: `/etc/logrotate.d/omsagent-<workspace id>`.

Параметры по умолчанию: 
```
/var/opt/microsoft/omsagent/<workspace id>/log/omsagent.log {
    rotate 5
    missingok
    notifempty
    compress
    size 50k
    copytruncate
}
```

## <a name="uninstalling-the-oms-agent-for-linux"></a>Удаление агента OMS для Linux
Пакеты агента можно удалить, запустив SH-файл пакета с аргументом `--purge`, полностью удаляющий агент и его конфигурации с компьютера.   

```
> sudo rpm -e omsconfig
> sudo rpm -e omsagent
> sudo /opt/microsoft/scx/bin/uninstall
```

## <a name="troubleshooting"></a>Устранение неполадок

### <a name="issue-unable-to-connect-through-proxy-to-log-analytics"></a>Проблема. Не удается подключиться к Log Analytics через прокси-сервер

#### <a name="probable-causes"></a>Возможные причины
* При подключении указан недопустимый прокси-сервер.
* Конечные точки Log Analytics и службы автоматизации Azure не включены в список разрешенных в вашем центре обработки данных. 

#### <a name="resolutions"></a>Способы устранения
1. Повторно подключитесь к службе Log Analytics, используя службу OMS для Linux и следующую команду с включенным параметром `-v`. За счет этого подробные выходные данные агента могут подключиться к службе OMS через прокси-сервер. 
`/opt/microsoft/omsagent/bin/omsadmin.sh -w <OMS Workspace ID> -s <OMS Workspace Key> -p <Proxy Conf> -v`

2. Просмотрите раздел [Настройка агента для использования со шлюзом OMS или прокси-сервером](#configuring the-agent-for-use-with-a-proxy-server-or-oms-gateway), чтобы убедиться в правильности настройки агента для обмена данными через прокси-сервер.    
* Внимательно проверьте включение в список разрешенных следующих конечных точек службы Log Analytics:

    |Ресурс агента| порты; |  
    |------|---------|  
    |*.ods.opinsights.azure.com | Порт 443|   
    |*.oms.opinsights.azure.com | Порт 443|   
    |ods.systemcenteradvisor.com | Порт 443|   
    |*.blob.core.windows.net/ | Порт 443|   

### <a name="issue-you-receive-a-403-error-when-trying-to-onboard"></a>Проблема. При попытке подключения возникает ошибка 403.

#### <a name="probable-causes"></a>Возможные причины
* На сервере Linux установлены неправильные время и дата. 
* Используется недопустимый идентификатор или ключ рабочей области.

#### <a name="resolution"></a>Способы устранения:

1. Проверьте время на своем сервере Linux с помощью команды. Если время отличается от текущего на 15 минут, подключение завершится сбоем. Чтобы исправить это, обновите дату и (или) часовой пояс на сервере Linux. 
2. Убедитесь, что установлена последняя версия агента OMS для Linux.  В последней версии вы получаете уведомления, если разница во времени приводит к сбою подключения.
3. Выполните повторное подключение, используя правильный идентификатор и ключ рабочей области и следуя инструкциям по установке, приведенным ранее в этой статье.

### <a name="issue-you-see-a-500-and-404-error-in-the-log-file-right-after-onboarding"></a>Проблема. Сразу после подключения в файле журнала появляется ошибка 500 и 404.
Это известная проблема, которая возникает при первой передаче данных Linux в рабочую область Log Analytics. Это не влияет на отправляемые данные и не мешает работе службы.

### <a name="issue-you-are-not-seeing-any-data-in-the-azure-portal"></a>Проблема. На портале Azure не отображаются данные

#### <a name="probable-causes"></a>Возможные причины

- Сбой при подключении к службе Log Analytics.
- Подключение к службе Log Analytics заблокировано.
- Создана резервная копия данных агента OMS для Linux.

#### <a name="resolutions"></a>Способы устранения
1. Проверьте состояние подключения для службы Log Analytics, убедившись в наличии следующего файла: `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsadmin.conf`.
2. Повторно подключитесь, используя инструкции командной строки `omsadmin.sh`
3. Если используется прокси-сервер, см. описанные выше шаги по разрешению прокси-сервера.
4. В некоторых случаях сбой подключения между агентом OMS для Linux и службой связан с тем, что в буфере достигнут максимальный размер запросов к данным агента (50 МБ). Следует перезапустить агент OMS для Linux, выполнив следующую команду: `/opt/microsoft/omsagent/bin/service_control restart [<workspace id>]`. 

    >[!NOTE]
    >Эта проблема исправлена в агенте версии 1.1.0-28 и выше.

### <a name="issue-omsagent-creates-excessive-number-of-user-process-on-computer-and-never-terminates-them"></a>Проблема. OMSAgent создает чрезмерное количество пользовательских процессов и никогда не завершает их
При включении решений по управлению, которые поддерживают управление виртуальными машинами Linux, он инициирует определенное число процессов на агенте Linux. Однако из-за известной проблемы перед завершением процесса запускается новый процесс. 

#### <a name="resolutions"></a>Способы устранения
Чтобы изменить количество пользовательских процессов, создаваемых OMSAgent, необходимо настроить агент, выполнив команду omsadmin.sh.  По умолчанию количество создаваемых процессов равно 75. Прежде чем изменить ограничение, необходимо сначала выполнить следующую команду, чтобы увидеть, сколько процессов OMSAgent сейчас выполняются: `ps aux | grep -E '^omsagent' | wc -l`.  
Чтобы проверить заданное ограничение, выполните следующую команду: `cat /etc/security/limits.conf | grep -E '^omsagent'`.

Выполните указанные ниже команды, чтобы настроить лимит пользовательских процессов или установить значения по умолчанию для ограничений процессов.

1. Чтобы установить ограничения процессов для OMSAgent, выполните следующую команду: `sudo /opt/microsoft/omsagent/bin/omsadmin.sh -n <specific number limit>`.<br>Обратите внимание, что минимальное устанавливаемое ограничение равно 5.  

2. Чтобы установить значения по умолчанию для ограничений процессов OMSAgent, выполните следующую команду: `sudo /opt/microsoft/omsagent/bin/omsadmin.sh -N`.

Убедитесь, что новый параметр применен, выполнив следующую команду: `cat /etc/security/limits.conf | grep -E '^omsagent'`.  Если вы не видите новую примененную конфигурацию, возможно, вы установили слишком маленькое ограничение процессов.  
