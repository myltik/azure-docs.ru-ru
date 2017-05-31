---
title: "Подключение компьютеров Linux к Operations Management Suite (OMS) | Документация Майкрософт"
description: "В этой статье объясняется, как с помощью Microsoft Monitoring Agent (MMA) подключить расположенные в локальной инфраструктуре компьютеры Linux к рабочим областям OMS."
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
ms.date: 05/04/2017
ms.author: magoedte
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: 3c556f3d9e81caae574ec093b6f2ce15651c4485
ms.contentlocale: ru-ru
ms.lasthandoff: 05/11/2017

---

# <a name="connect-your-linux-computers-to-operations-management-suite-oms"></a>Подключение компьютеров Linux к Operations Management Suite (OMS)

С помощью OMS можно собирать и использовать данные, которые создаются на компьютерах под управлением Linux и в контейнерах (например, Docker), расположенных в локальном центре обработки данных как физические серверы или виртуальные машины либо виртуальные машины в облачной службе (например, Amazon Web Services (AWS) или Microsoft Azure). Также можно использовать доступные в OMS решения для управления: например функцию отслеживания изменений конфигурации и функцию управления обновлениями программного обеспечения. Эти решения позволяют выполнять упреждающее управление жизненным циклом виртуальных машин Linux.

Агент OMS для Linux обменивается исходящими данными со службой OMS через TCP-порт 443. Если компьютер подключен к брандмауэру или прокси-серверу для обмена данными через Интернет, узнайте, как [настроить параметры прокси-сервера и брандмауэра](log-analytics-proxy-firewall.md), чтобы применить необходимые изменения конфигурации.  Компьютер, отслеживаемый решением System Center 2016 Operations Manager или Operations Manager 2012 R2, может использоваться как многосетевой: с помощью службы OMS будет выполняться сбор данных и их пересылка в службу, а компьютер по-прежнему будет отслеживаться решением Operations Manager.  Компьютеры Linux, отслеживаемые группой управления Operations Manager, интегрированной с OMS, не получают конфигурацию для источников данных. Они пересылают собранные данные через группы управления.  

Если политики ИТ-безопасности запрещают подключение компьютеров в сети к Интернету, агент можно настроить для подключения к шлюзу OMS. Так он сможет получать сведения о конфигурации и отправлять собранные данные в зависимости от включенного решения. Дополнительные сведения и инструкции по настройке агента OMS Linux для обмена данными со службой OMS через шлюз OMS см. в статье [Подключения компьютеров к OMS с помощью шлюза OMS без доступа к Интернету](log-analytics-oms-gateway.md).  

Ниже представлена схема подключения между компьютерами Linux, управляемыми агентом, и OMS, включая направление и порты.

![Схема взаимодействия прямого агента с OMS](./media/log-analytics-agent-linux/log-analytics-agent-linux-communication.png)

## <a name="system-requirements"></a>Требования к системе
Сначала необходимо выполнить следующие требования.

### <a name="supported-linux-operating-systems"></a>Поддерживаемые операционные системы Linux
Официально поддерживаются следующие дистрибутивы Linux.  Однако агент OMS для Linux может выполняться и на других дистрибутивах, помимо следующих:

* Amazon Linux 2012.09 --> 2015.09 (x86/x64)
* CentOS Linux 5,6 и 7 (x86/x64)
* Oracle Linux 5,6 и 7 (x86/x64)
* Red Hat Enterprise Linux Server 5,6 и 7 (x86/x64)
* Debian GNU/Linux 6, 7 и 8 (x86/x64)
* Ubuntu 12.04 LTS, 14.04 LTS, 15.04, 15.10, 16.04 LTS (x86/x64)
* SUSE Linux Enterprise Server 11 и 12 (x86/x64)

### <a name="package-requirements"></a>Требования к пакетам

 **Требуемый пакет**     | **Описание**     | **Минимальная версия**
--------------------- | --------------------- | -------------------
Glibc |    Библиотека C GNU    | 2.5-12
Openssl    | Библиотеки OpenSSL | 0.9.8e или 1.0
Curl | Веб-клиент cURL | 7.15.5
Python-ctypes | |
PAM | Подключаемые модули аутентификации     |

> [!NOTE]
>  Для сбора сообщений системного журнала требуется rsyslog или syslog-ng. Управляющая программа syslog по умолчанию не поддерживается для сбора событий системного журнала в Red Hat Enterprise Linux версии 5, CentOS и Oracle Linux (sysklog). Чтобы собирать данные системного журнала из дистрибутивов этих версий, требуется установить и настроить управляющую программу rsyslog, которая заменит sysklog.

Агент включает несколько пакетов. Файл выпуска содержит указанные ниже пакеты, доступ к которым можно получить, запустив пакет оболочки с помощью команды `--extract`.

**Package** | **Версия** | **Описание**
----------- | ----------- | --------------
omsagent | 1.3.4 | Агент Operations Management Suite для Linux
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
* В каталоге /etc/sudoers.d/omsagent создается файл sudoers с директивами include. Так пользователь omsagent получает право на перезапуск управляющих программ syslog и omsagent. Если директивы include sudo не поддерживаются в установленной версии sudo, эти записи будут записываться в каталог /etc/sudoers.
* Конфигурация системного журнала настраивается для перенаправления подмножества событий агенту. Дополнительные сведения см. в разделе о **настройке сбора данных** ниже.

### <a name="upgrade-from-a-previous-release"></a>Обновление предыдущей версии
В этом выпуске поддерживаются обновления версий ниже 1.0.0-47. При установке с помощью команды `--upgrade` все компоненты агента будут обновлены до последней версии.

## <a name="install-the-oms-agent-for-linux"></a>Установка агента OMS для Linux
Агент OMS для Linux предоставляется в самораспаковывающемся и устанавливаемом пакете скриптов оболочки. Этот пакет содержит пакеты Debian и RPM для каждого из компонентов агента. Его можно установить непосредственно или извлечь отдельные пакеты. Один пакет предназначен для архитектуры x64 и один — для архитектуры x86.

### <a name="installing-the-agent"></a>Установка агента

1. Перенесите соответствующий пакет (x86 или x64) на компьютер Linux с помощью scp или sftp.
2. Установите пакет, используя аргумент `--install` или `--upgrade`.

    > [!NOTE]
    > Используйте аргумент `--upgrade`, если все существующие пакеты (например, агент System Center Operations Manager для Linux) уже установлены. Чтобы подключиться к Operations Management Suite во время установки, используйте параметры `-w <WorkspaceID>` и `-s <Shared Key>`.

### <a name="bundle-command-line-arguments"></a>Аргументы командной строки пакета
```
Options:
  --extract              Extract contents and exit.
  --force                Force upgrade (override version checks).
  --install              Install the package from the system.
  --purge                Uninstall the package and remove all related data.
  --remove               Uninstall the package from the system.
  --restart-deps         Reconfigure and restart dependent service
  --source-references    Show source code reference hashes.
  --upgrade              Upgrade the package in the system.
  --version              Version of this shell bundle.
  --version-check        Check versions already installed to see if upgradable.
  --debug                use shell debug mode.

  -w id, --id id         Use workspace ID <id> for automatic onboarding.
  -s key, --shared key   Use <key> as the shared key for automatic onboarding.
  -d dmn, --domain dmn   Use <dmn> as the OMS domain for onboarding. Optional.
                         default: opinsights.azure.com
                         ex: opinsights.azure.us (for FairFax)
  -p conf, --proxy conf  Use <conf> as the proxy configuration.
                         ex: -p [protocol://][user:password@]proxyhost[:port]
  -a id, --azure-resource id Use Azure Resource ID <id>.
  -m marker, --multi-homing-marker marker
                         Onboard as a multi-homing(Non-Primary) workspace.

  -? | --help            shows this usage text.
```

#### <a name="to-install-and-onboard-directly"></a>Для установки и прямого подключения
```
sudo sh ./omsagent-1.3.0-1.universal.x64.sh --upgrade -w <workspace id> -s <shared key>
```

#### <a name="to-install-and-onboard-to-a-workspace-in-us-government-cloud"></a>Для установки и подключения к рабочей области в облаке правительства США
```
sudo sh ./omsagent-1.3.0-1.universal.x64.sh --upgrade -w <workspace id> -s <shared key> -d opinsights.azure.us
```

#### <a name="to-install-the-agent-packages-and-onboard-at-a-later-time"></a>Для установки пакетов агента и подключения в будущем
```
sudo sh ./omsagent-1.3.0-1.universal.x64.sh --upgrade
```

#### <a name="to-extract-the-agent-packages-from-the-bundle-without-installing"></a>Для извлечения пакетов агента из пакета без установки
```
sudo sh ./omsagent-1.3.0-1.universal.x64.sh --extract
```

## <a name="configuring-the-agent-for-use-with-an-http-proxy-server-or-oms-gateway"></a>Настройка агента для использования со шлюзом OMS или прокси-сервером HTTP
Агент OMS для Linux поддерживает обмен данными со службой OMS через прокси-сервер HTTP или HTTPS либо шлюз OMS.  Поддерживается анонимная и базовая аутентификация (с именем пользователя и паролем).  

### <a name="proxy-configuration"></a>Конфигурация прокси-сервера
Значение конфигурации прокси-сервера имеет следующий синтаксис:

`[protocol://][user:password@]proxyhost[:port]`

Свойство|Описание
-|-
Протокол|HTTP или HTTPS
user|Необязательное имя пользователя для аутентификации прокси-сервера
пароль|Необязательный пароль для аутентификации прокси-сервера
proxyhost|Адрес или полное доменное имя прокси-сервера или шлюза OMS
порт|Номер дополнительного порта для прокси сервера или OMS шлюза

Например: `http://user01:password@proxy01.contoso.com:8080`

Прокси-сервер можно указать во время установки или при изменении файла конфигурации proxy.conf после установки.   

### <a name="specify-proxy-configuration-during-installation"></a>Определение конфигурации прокси-сервера во время установки
Аргумент `-p` или `--proxy` для установки пакета omsagent определяет используемую конфигурацию прокси-сервера.

```
sudo sh ./omsagent-1.3.0-1.universal.x64.sh --upgrade -p http://<proxy user>:<proxy password>@<proxy address>:<proxy port> -w <workspace id> -s <shared key>
```

### <a name="define-the-proxy-configuration-in-a-file"></a>Определение конфигурации прокси-сервера в файле
Конфигурацию прокси-сервера можно определить в файле: `/etc/opt/microsoft/omsagent/proxy.conf`. Этот файл можно создать непосредственно или изменить. В любом случае он должен быть доступным для чтения для пользователя omsagent. Например:
```
proxyconf="https://proxyuser:proxypassword@proxyserver01:8080"
sudo echo $proxyconf >>/etc/opt/microsoft/omsagent/proxy.conf
sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/proxy.conf
sudo chmod 600 /etc/opt/microsoft/omsagent/proxy.conf
sudo /opt/microsoft/omsagent/bin/service_control restart [<workspace id>]
```

### <a name="removing-the-proxy-configuration"></a>Удаление конфигурации прокси-сервера.
Чтобы удалить ранее заданную конфигурацию прокси-сервера и вернуться к прямому подключению, удалите файл proxy.conf:
```
sudo rm /etc/opt/microsoft/omsagent/proxy.conf
sudo /opt/microsoft/omsagent/bin/service_control restart [<workspace id>]
```
## <a name="enable-the-oms-agent-for-linux-to-report-to-system-center-operations-manager"></a>Включение агента OMS для Linux для передачи отчета в System Center Operations Manager
Чтобы настроить агент OMS для Linux и передать отчет в группу управления System Center Operations Manager, сделайте следующее.  

1. Измените файл `/etc/opt/omi/conf/omiserver.conf`
2. Укажите для **httpsport=** номер порта 1270. Например: `httpsport=1270`.
3. Перезапустите сервер OMI: `sudo /opt/omi/bin/service_control restart`.

## <a name="onboarding-with-operations-management-suite"></a>Подключение к Operations Management Suite
Если идентификатор и ключ рабочей области не предоставлены во время установки пакета, агент нужно впоследствии зарегистрировать с использованием Operations Management Suite.

### <a name="onboarding-using-the-command-line"></a>Подключение с помощью командной строки
Выполните команду omsadmin.sh, указав идентификатор и ключ для рабочей области. Эту команду должен выполнять привилегированный пользователь (sudo):
```
cd /opt/microsoft/omsagent/bin
sudo ./omsadmin.sh -w <WorkspaceID> -s <Shared Key>
```

### <a name="onboarding-using-a-file"></a>Подключение с помощью файла
1.    Создайте файл `/etc/omsagent-onboard.conf`. Файл должен быть доступным для чтения и записи для привилегированного пользователя.
`sudo vi /etc/omsagent-onboard.conf`
2.    Вставьте следующие строки в файл, используя идентификатор рабочей области и общий ключ:

        WORKSPACE_ID=<WorkspaceID>  
        SHARED_KEY=<Shared Key>  

3.    Чтобы подключиться к OMS, выполните следующую команду: `sudo /opt/microsoft/omsagent/bin/omsadmin.sh`.
4.    Файл будет удален после подключения.

## <a name="manage-omsagent-daemon"></a>Администрирование управляющей программы omsagent
Начиная с версии 1.3.0-1 мы регистрируем управляющую программу omsagent для каждой подключенной рабочей области. Имя управляющей программы имеет формат *omsagent-\<идентификатор рабочей области>*.  Для администрирования управляющей программы можно использовать команду `/opt/microsoft/omsagent/bin/service_control`.

```
sudo sh /opt/microsoft/omsagent/bin/service_control start|stop|restart|enable|disable [<workspace id>]
```

Идентификатор рабочей области — это необязательный параметр. Если его указать, он будет использоваться только с управляющей программой определенной рабочей области.  В противном случае он будет использоваться со всеми управляющими программами.


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
Пакеты агента можно удалить с помощью dpkg или rpm. Также можно запустить SH-файл пакета с аргументом `--remove`.  Если же требуется полностью удалить все элементы OMS агента для Linux, можно запустить SH-файл пакета с аргументом `--purge`.

### <a name="debian--ubuntu"></a>Debian и Ubuntu
```
> sudo dpkg -P omsconfig
> sudo dpkg -P omsagent
> sudo /opt/microsoft/scx/bin/uninstall
```

### <a name="centos-oracle-linux-rhel-and-sles"></a>CentOS, Oracle Linux, RHEL и SLES
```
> sudo rpm -e omsconfig
> sudo rpm -e omsagent
> sudo /opt/microsoft/scx/bin/uninstall
```
## <a name="troubleshooting"></a>Устранение неполадок

### <a name="issue-unable-to-connect-through-proxy-to-oms"></a>Проблема. Не удается подключиться к OMS через прокси-сервер

#### <a name="probable-causes"></a>Возможные причины
* При подключении указан недопустимый прокси-сервер.
* Конечные точки службы OMS не включены в список разрешенных в вашем центре обработки данных.

#### <a name="resolutions"></a>Способы устранения
1. Повторно подключитесь к службе OMS, используя службу OMS для Linux и следующую команду с включенным параметром `-v`. За счет этого подробные выходные данные агента могут подключиться к службе OMS через прокси-сервер.
`/opt/microsoft/omsagent/bin/omsadmin.sh -w <OMS Workspace ID> -s <OMS Workspace Key> -p <Proxy Conf> -v`

2. Просмотрите раздел [Настройка агента для использования со шлюзом OMS или прокси-сервером HTTP(#configuring the-agent-for-use-with-a-http-proxy-server), чтобы убедиться в правильности настройки агента для обмена данными через прокси-сервер.    
* Также проверьте включение в список разрешенных следующих конечных точек службы OMS:

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
Новые функции! Последняя версия агента OMS для Linux теперь сообщает вам, если разница во времени вызывает сбой подключения. Повторно подключитесь, используя допустимый идентификатор и ключ рабочей области.

### <a name="issue-you-see-a-500-and-404-error-in-the-log-file-right-after-onboarding"></a>Проблема. Сразу после подключения в файле журнала появляется ошибка 500 и 404.
Это известная проблема, которая возникает при первой отправке данных Linux в рабочую область OMS. Это не влияет на отправляемые данные и не мешает работе службы.

### <a name="issue--you-are-not-seeing-any-data-in-the-oms-portal"></a>Проблема. На портале OMS не отображаются данные.

#### <a name="probable-causes"></a>Возможные причины

- Сбой при подключении к службе OMS.
- Подключение к службе OMS заблокировано.
- Создана резервная копия данных агента OMS для Linux.

#### <a name="resolutions"></a>Способы устранения
1. Проверьте состояние подключения для службы OMS, убедившись в наличии следующего файла: `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsadmin.conf`.
2. Повторно подключитесь, используя инструкции командной строки `omsadmin.sh`.
3. Если используется прокси-сервер, см. описанные выше шаги по разрешению прокси-сервера.
4. В некоторых случаях сбой подключения между агентом OMS для Linux и службой OMS связан с тем, что в буфере достигнут максимальный размер запросов к данным агента (50 МБ). Следует перезапустить агент OMS для Linux, выполнив следующую команду: `/opt/microsoft/omsagent/bin/service_control restart [<workspace id>]`.
> [!NOTE]
> Эта проблема исправлена в агенте версии 1.1.0-28 и выше.

