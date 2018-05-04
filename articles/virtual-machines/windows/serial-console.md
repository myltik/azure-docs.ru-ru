---
title: Последовательная консоль виртуальной машины Azure | Документация Майкрософт
description: Двунаправленная последовательная консоль виртуальных машин Azure.
services: virtual-machines-windows
documentationcenter: ''
author: harijay
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/05/2018
ms.author: harijay
ms.openlocfilehash: e891e9c9fd87f370f0c98639ff0c6fc5b8cc81af
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
---
# <a name="virtual-machine-serial-console-preview"></a>Последовательная консоль виртуальной машины (предварительная версия) 


Последовательная консоль виртуальных машин в Azure предоставляет доступ к текстовой консоли для виртуальных машин Linux и Windows. Это последовательное подключение к виртуальной машине происходит через последовательный порт COM1 и открывает доступ к виртуальной машине вне зависимости от состояния, в котором находится сеть или операционная система виртуальной машины. Получить доступ к последовательной консоли для виртуальной машины сейчас можно только на портале Azure. Он предоставляется только тем пользователям, у которых есть права участника виртуальной машины или выше на доступ к виртуальной машине. 

> [!Note] 
> Предварительные версии предоставляются при условии, что вы соглашаетесь с условиями использования. Дополнительные сведения см. в статье [Дополнительные условия использования Предварительных выпусков Microsoft Azure.] (https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Сейчас эта служба находится на этапе **общедоступной предварительной версии** и доступ к последовательной консоли для виртуальных машин предоставляется в глобальных регионах Azure. На этом этапе в последовательной консоли недоступно облако Azure для государственных организаций, Azure для Германии или Azure для Китая.

 

## <a name="prerequisites"></a>предварительным требованиям 

* На виртуальной машине ДОЛЖНА быть включена [диагностика загрузки](boot-diagnostics.md). 
* Учетной записи, использующей последовательную консоль, должна быть присвоена [роль участника](../../role-based-access-control/built-in-roles.md) для виртуальной машины и учетная запись хранения [диагностики загрузки](boot-diagnostics.md). 

## <a name="open-the-serial-console"></a>Открытие последовательной консоли
Получить доступ к последовательной консоли для виртуальной машины можно только на [портале Azure](https://portal.azure.com). Ниже приведены шаги для получения доступа к последовательной консоли виртуальных машин через портал. 

  1. Откройте портал Azure.
  2. В меню слева выберите виртуальные машины.
  3. Щелкните виртуальную машину в списке. Откроется страница обзора для виртуальной машины.
  4. Прокрутите вниз до раздела Support + Troubleshooting (Поддержка + Устранение неполадок) и щелкните параметр последовательной консоли (предварительная версия). Откроется новая область с последовательной консолью, и запустится подключение.

![](../media/virtual-machines-serial-console/virtual-machine-windows-serial-console-connect.gif)

### <a name="disable-feature"></a>Функция отключения
Функции последовательной консоли можно отключить для конкретных виртуальных машин, отключив параметр диагностики загрузки этой виртуальной машины.

## <a name="serial-console-security"></a>Безопасность последовательной консоли 

### <a name="access-security"></a>Безопасность доступа 
Доступ к последовательной консоли ограничен кругом пользователей, обладающих на виртуальной машине правами [Участник](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) или более широкими полномочиями доступа к ней. Если клиентам AAD нужна многофакторная идентификация, тогда она потребуется и для доступа к последовательной консоли, так как доступ осуществляется через [портал Azure](https://portal.azure.com).

### <a name="channel-security"></a>Безопасность канала
Все данные, исходящие и поступающие, при пересылке по сети зашифрованы.

### <a name="audit-logs"></a>Журналы аудита
Все сведения о доступе к последовательной консоли записываются в журналы [диагностики загрузки](https://docs.microsoft.com/azure/virtual-machines/linux/boot-diagnostics) на виртуальной машине. Права доступа к этим журналам и на работу с ними принадлежат администратору виртуальной машины Azure.  

>[!CAUTION] 
Так как пароли доступа для консоли не записываются в журнал, если команды, выполняемые в консоли, содержат или выводят пароли, секреты, имена пользователей и любые другие формы персональных данных, они должны записываться в журналы диагностики загрузки виртуальной машины вместе с остальными элементами с видимым текстом. Это является частью реализации функциональности последовательной консоли. Эти журналы являются циклическими, и только пользователи с разрешениями на чтение для учетной записи хранения диагностики имеют к ним доступ. Но мы рекомендуем следовать рекомендации использования удаленного рабочего стола для всего, что может содержать секретные или персональные данные. 

### <a name="concurrent-usage"></a>Одновременное использование
Когда один пользователь подключен к последовательной консоли, а другой пользователь успешно запрашивает доступ к этой же виртуальной машине, первый пользователь будет отключен, а второй пользователь будет подключаться таким способом, как будто первый пользователь вышел из физической консоли, а новый пользователь остался.

>[!CAUTION] 
Это означает, что отключенный пользователь не выходит из системы! Возможность принудительного выхода из системы после отключения (с помощью SIGHUP или похожего механизма) по-прежнему планируется. Для Windows включено автоматическое истечение времени ожидания в SAC, а для Linux можно настроить параметр времени ожидания терминала. 


## <a name="accessing-serial-console-for-windows"></a>Доступ к последовательной консоли в Windows 
Новые образы Windows Server в Azure по умолчанию будут оснащены [специальной административной консолью](https://technet.microsoft.com/library/cc787940(v=ws.10).aspx) (SAC). SAC поддерживается в серверных версиях Windows, но не поставляется в клиентских версиях (например, в Windows 10, Windows 8 или Windows 7). Включение последовательной консоли в виртуальных машинах Windows, созданных с помощью февральских образов 2018 года или более поздних, происходит следующим образом. 

1. Подключитесь к виртуальной машине через удаленный рабочий стол Windows.
2. Запустите следующие команды из командной строки с правами администратора. 
* `bcdedit /ems {current} on`
* `bcdedit /emssettings EMSPORT:1 EMSBAUDRATE:115200`
3. Для получения доступа к консоли SAC перезагрузите систему.

![](../media/virtual-machines-serial-console/virtual-machine-windows-serial-console-connect.gif)

В случае необходимости SAC можно также включить в автономном режиме. 

1. Подключите диск Windows, который необходимо настроить с помощью консоли SAC в качестве диска данных, к имеющейся виртуальной машине. 
2. Запустите следующие команды из командной строки с правами администратора. 
* `bcdedit /store <mountedvolume>\boot\bcd /ems {default} on`
* `bcdedit /store <mountedvolume>\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

### <a name="how-do-i-know-if-sac-is-enabled-or-not"></a>Как узнать, доступна ли SAC 

Если [SAC] (https://technet.microsoft.com/library/cc787940(v=ws.10).aspx)) не включена, последовательная консоль не выведет запрос SAC. В некоторых случаях она может показывать сведения о работоспособности виртуальной машины или же она может быть пустой.  

### <a name="enabling-boot-menu-to-show-in-the-serial-console"></a>Включение меню загрузки для отображения в последовательной консоли 

Если необходимо включить отображение сообщений загрузчика Windows в последовательной консоли, добавьте дополнительные параметры в загрузчик Windows.

1. Подключитесь к виртуальной машине через удаленный рабочий стол Windows.
2. Запустите следующие команды из командной строки с правами администратора. 
* `bcdedit /set {bootmgr} displaybootmenu yes`
* `bcdedit /set {bootmgr} timeout 5`
* `bcdedit /set {bootmgr} bootems yes`
3. Перезагрузите систему, чтобы включить меню загрузки.

> [!NOTE] 
> В этот момент поддержка функциональных клавиш не включена, и если требуются расширенные варианты загрузки, используйте bcdedit /set {current} onetimeadvancedoptions. Дополнительные сведения см. в разделе о [bcdedit](https://docs.microsoft.com/windows-hardware/drivers/devtest/bcdedit--set).

## <a name="windows-commands---cmd"></a>Команды Windows (CMD) 

В этом разделе приведены примеры команд для выполнения общих задач в сценариях, где вам может потребоваться использовать SAC для доступа к виртуальной машине, например, когда вам необходимо устранить ошибки RDP-соединения.

SAC включена во все версии Windows, начиная с Windows Server 2003, но по умолчанию отключена. SAC использует драйвер ядра `sacdrv.sys`, службу `Special Administration Console Helper` (`sacsvr`) и процесс `sacsess.exe`. Дополнительные сведения см. в статье [Emergency Management Services Tools and Settings](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc787940(v%3dws.10)) (Средства и параметры служб аварийного управления).

SAC позволяет подключаться к вашей операционной системе через последовательный порт. Когда вы запускаете CMD из SAC, `sacsess.exe` запускает `cmd.exe` в вашей операционной системе. Если вы подключаетесь к виртуальной машине по RDP, в диспетчере задач можно увидеть, что вы одновременно подключаетесь к SAC через функцию последовательной консоли. CMD, к которой вы обращаетесь через SAC, аналогична `cmd.exe`, которую вы используете при подключении через RDP. Доступны все те же команды и средства, в том числе возможность запуска PowerShell из этого экземпляра CMD. Основное различие между SAC и средой восстановления Windows (Windows RE) заключается в том, что SAC позволяет вам управлять вашей операционной системой, в то время как WinRE загружается в другую минимальную сборку ОС. Хотя виртуальные машины Azure не поддерживают возможность доступа к WinRE с помощью функции последовательной консоли, управление виртуальными машинами Azure можно осуществлять с помощью SAC.

Так как SAC ограничена буфером экрана размером 80 x 24 и не имеет обратной прокрутки, добавьте `| more` к командам для отображения выходных данных по одной странице за раз. Используйте `<spacebar>` для просмотра следующей страницы или `<enter>` для просмотра следующей строки.  

`SHIFT+INSERT` — сочетание клавиш для вставки в окно последовательной консоли.

Из-за ограниченного буфера экрана SAC более длинные команды проще вводить в локальном текстовом редакторе, а затем вставлять в SAC.

### <a name="view-and-edit-windows-registry-settings"></a>Просмотр и изменение параметров реестра Windows
#### <a name="verify-rdp-is-enabled"></a>Проверка включения RDP
`reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections`

`reg query "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fDenyTSConnections`

Второй ключ (в \Policies) будет существовать только в том случае, если настроен соответствующий параметр групповой политики.

#### <a name="enable-rdp"></a>Включите RDP:
`reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 0`

`reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fDenyTSConnections /t REG_DWORD /d 0` 

Второй ключ (в \Policies) потребуется только в том случае, если настроен соответствующий параметр групповой политики. Значение будет перезаписано при следующем обновлении групповой политики, если оно настроено в групповой политике.

### <a name="manage-windows-services"></a>Управление службами Windows

#### <a name="view-service-state"></a>Отображение состояния службы
`sc query termservice`
####  <a name="view-service-logon-account"></a>Отображение учетной записи для входа в службу
`sc qc termservice`
#### <a name="set-service-logon-account"></a>Настройка учетной записи для входа в службу 
`sc config termservice obj= "NT Authority\NetworkService"`

После знака равенства требуется пробел.
#### <a name="set-service-start-type"></a>Настройка типа запуска службы
`sc config termservice start= demand` 

После знака равенства требуется пробел. Возможные значения запуска: `boot`, `system`, `auto`, `demand`, `disabled` и `delayed-auto`.
#### <a name="set-service-dependencies"></a>Настройка зависимостей служб
`sc config termservice depend= RPCSS`

После знака равенства требуется пробел.
#### <a name="start-service"></a>Запуск службы
`net start termservice`

или

`sc start termservice`
#### <a name="stop-service"></a>Остановка службы
`net stop termservice`

или

`sc stop termservice`
### <a name="manage-networking-features"></a>Управление компонентами сети
#### <a name="show-nic-properties"></a>Отображение свойств сетевого адаптера
`netsh interface show interface` 
#### <a name="show-ip-properties"></a>Отображение свойств IP
`netsh interface ip show config`
#### <a name="show-ipsec-configuration"></a>Отображение конфигурации IPSec
`netsh nap client show configuration`  
#### <a name="enable-nic"></a>Включение сетевого адаптера
`netsh interface set interface name="<interface name>" admin=enabled`
#### <a name="set-nic-to-use-dhcp"></a>Настройка сетевого адаптера для использования DHCP
`netsh interface ip set address name="<interface name>" source=dhcp`

Виртуальные машины Azure всегда должны использовать DHCP в гостевой ОС, чтобы получить IP-адрес. Параметр статического IP-адреса Azure по-прежнему использует DHCP, чтобы предоставить статический IP-адрес виртуальной машине.
#### <a name="ping"></a>Проверка связи
`ping 8.8.8.8` 
#### <a name="port-ping"></a>Проверка связи с портом  
Установка клиента telnet

`dism /online /Enable-Feature /FeatureName:TelnetClient`

Проверка подключения

`telnet bing.com 80`

Удаление клиента telnet

`dism /online /Disable-Feature /FeatureName:TelnetClient`

Если можно использовать только методы, доступные в Windows по умолчанию, PowerShell может быть лучшим способом для тестирования подключения к порталу. Примеры приведены в разделе PowerShell ниже.
#### <a name="test-dns-name-resolution"></a>Тестирование разрешений DNS-имен
`nslookup bing.com`
#### <a name="show-windows-firewall-rule"></a>Отображение правила брандмауэра Windows
`netsh advfirewall firewall show rule name="Remote Desktop - User Mode (TCP-In)"`
#### <a name="disable-windows-firewall"></a>Отключение брандмауэра Windows
`netsh advfirewall set allprofiles state off`

Вы можете использовать эту команду при устранении неполадок, чтобы временно исключить брандмауэр Windows. Он будет включен при следующем перезапуске или когда вы включите его, используя приведенную ниже команду. Не останавливайте службу брандмауэра Windows (MPSSVC) или службу "Базовый модуль фильтрации" (BFE), чтобы исключить брандмауэр Windows. Остановка служб MPSSVC или BFE приведет к блокировке всех подключений.
#### <a name="enable-windows-firewall"></a>Включение брандмауэра Windows
`netsh advfirewall set allprofiles state on`
### <a name="manage-users-and-groups"></a>Управление пользователями и группами
#### <a name="create-local-user-account"></a>Создание локальной учетной записи пользователя
`net user /add <username> <password>`
#### <a name="add-local-user-to-local-group"></a>Добавление локального пользователя в локальную группу
`net localgroup Administrators <username> /add`
#### <a name="verify-user-account-is-enabled"></a>Проверка включения учетной записи пользователя
`net user <username> | find /i "active"`

Виртуальные машины Azure, созданные из обобщенного образа, будут иметь учетную запись локального администратора, переименованную на имя, указанное во время подготовки виртуальной машины. Таким образом, обычно оно не может быть `Administrator`.
#### <a name="enable-user-account"></a>Включение учетной записи пользователя
`net user <username> /active:yes`  
#### <a name="view-user-account-properties"></a>Просмотр свойств учетной записи пользователя
`net user <username>`

Пример необходимых строк из учетной записи локального администратора:

`Account active Yes`

`Account expires Never`

`Password expires Never`

`Workstations allowed All`

`Logon hours allowed All`

`Local Group Memberships *Administrators`

#### <a name="view-local-groups"></a>Отображение локальных групп
`net localgroup`
### <a name="manage-the-windows-event-log"></a>Управление журналом событий Windows
#### <a name="query-event-log-errors"></a>Запрос ошибок в журнале событий
`wevtutil qe system /c:10 /f:text /q:"Event[System[Level=2]]" | more`

Измените `/c:10` на желаемое количество возвращаемых событий или удалите его, чтобы получить все события, которые соответствуют фильтру.
#### <a name="query-event-log-by-event-id"></a>Запрос журнала событий по идентификатору события
`wevtutil qe system /c:1 /f:text /q:"Event[System[EventID=11]]" | more`
#### <a name="query-event-log-by-event-id-and-provider"></a>Запрос журнала событий по идентификатору события и поставщику
`wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11]]" | more`
#### <a name="query-event-log-by-event-id-and-provider-for-the-last-24-hours"></a>Запрос журнала событий по идентификатору события и поставщику за последние сутки
`wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11 and TimeCreated[timediff(@SystemTime) <= 86400000]]]"`

Используйте `604800000` для поиска за последнюю неделю вместо суток.
#### <a name="query-event-log-by-event-id-provider-and-eventdata-in-the-last-7-days"></a>Запрос журнала событий по идентификатору события, поставщику и данным событий за последнюю неделю
`wevtutil qe security /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-Security-Auditing'] and EventID=4624 and TimeCreated[timediff(@SystemTime) <= 604800000]] and EventData[Data[@Name='TargetUserName']='<username>']]" | more`
### <a name="view-or-remove-installed-applications"></a>Просмотр и удаление установленных приложений
#### <a name="list-installed-applications"></a>Вывод списка установленных приложений
`wmic product get Name,InstallDate | sort /r | more`

`sort /r` выполняет сортировку по убыванию даты установки, что позволяет легко увидеть недавно установленные приложения. Используйте `<spacebar>`, чтобы перейти на следующую страницу выходных данных, или `<enter>`, чтобы перейти на следующую строку.
#### <a name="uninstall-an-application"></a>Удаление приложения
`wmic path win32_product where name="<name>" call uninstall`

Замените `<name>` именем, возвращенным в приведенной выше команде, для приложения, которое вы хотите удалить.

### <a name="file-system-management"></a>Управление файловой системой
#### <a name="get-file-version"></a>Получение версии файла
`wmic datafile where "drive='C:' and path='\\windows\\system32\\drivers\\' and filename like 'netvsc%'" get version /format:list`

В этом примере возвращается версия файла виртуального драйвера сетевого адаптера (netvsc.sys, netvsc63.sys или netvsc60.sys в зависимости от версии Windows).
#### <a name="scan-for-system-file-corruption"></a>Поиск повреждений системного файла
`sfc /scannow`

См. также сведения в статье [Repair a Windows Image](https://docs.microsoft.com/windows-hardware/manufacture/desktop/repair-a-windows-image) (Исправление образа Windows).
#### <a name="scan-for-system-file-corruption"></a>Поиск повреждений системного файла
`dism /online /cleanup-image /scanhealth`

См. также сведения в статье [Repair a Windows Image](https://docs.microsoft.com/windows-hardware/manufacture/desktop/repair-a-windows-image) (Исправление образа Windows).
#### <a name="export-file-permissions-to-text-file"></a>Экспорт разрешений для файла в текстовый файл
`icacls %programdata%\Microsoft\Crypto\RSA\MachineKeys /t /c > %temp%\MachineKeys_permissions_before.txt`
#### <a name="save-file-permissions-to-acl-file"></a>Сохранение разрешений для файла в файл ACL
`icacls %programdata%\Microsoft\Crypto\RSA\MachineKeys /save %temp%\MachineKeys_permissions_before.aclfile /t`  
#### <a name="restore-file-permissions-from-acl-file"></a>Восстановление разрешений для файла из файла ACL
`icacls %programdata%\Microsoft\Crypto\RSA /save %temp%\MachineKeys_permissions_before.aclfile /t`

При использовании `/restore` путь должен быть родительской папкой папки, указанной вами при использовании `/save`. В этом примере `\RSA` является родительской папкой папки `\MachineKeys`, указанной в примере `/save` выше.
#### <a name="take-ntfs-ownership-of-a-folder"></a>Получение прав владельца NTFS для папки
`takeown /f %programdata%\Microsoft\Crypto\RSA\MachineKeys /a /r`  
#### <a name="grant-ntfs-permissions-to-a-folder-recursively"></a>Рекурсивное предоставление разрешений NTFS для папки
`icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "BUILTIN\Administrators:(F)"`  
### <a name="manage-devices"></a>Управление устройствами
#### <a name="remove-non-present-pnp-devices"></a>Удаление отсутствующих устройств PNP
`%windir%\System32\RUNDLL32.exe %windir%\System32\pnpclean.dll,RunDLL_PnpClean /Devices /Maxclean`
### <a name="manage-group-policy"></a>Управление групповой политикой
#### <a name="force-group-policy-update"></a>Принудительное обновление групповой политики
`gpupdate /force /wait:-1`
### <a name="miscellaneous-tasks"></a>Прочие задачи
#### <a name="show-os-version"></a>Отображение версии ОС
`ver`

или 

`wmic os get caption,version,buildnumber /format:list`

или 

`systeminfo  find /i "os name"`

`systeminfo | findstr /i /r "os.*version.*build"`
#### <a name="view-os-install-date"></a>Отображение даты установки ОС
`systeminfo | find /i "original"`

или 

`wmic os get installdate`
#### <a name="view-last-boot-time"></a>Отображение времени последней загрузки
`systeminfo | find /i "system boot time"`
#### <a name="view-time-zone"></a>Отображение часового пояса
`systeminfo | find /i "time zone"`

или

`wmic timezone get caption,standardname /format:list`
#### <a name="restart-windows"></a>Перезапуск Windows
`shutdown /r /t 0`

Если добавить `/f`, запускаемые приложения будут закрываться без предупреждения.
#### <a name="detect-safe-mode-boot"></a>Определение загрузки в безопасном режиме
`bcdedit /enum | find /i "safeboot"` 

## <a name="windows-commands---powershell"></a>Команды Windows — PowerShell

Чтобы запустить PowerShell в SAC, в командной строке введите:

`powershell <enter>`

>[!CAUTION]
Удалите модуль PSReadLine из сеанса PowerShell перед запуском каких-либо других команд PowerShell. Существует известная проблема, когда дополнительные символы могут быть введены в текст, вставленный из буфера обмена, если в сеансе PowerShell в SAC выполняется PSReadLine.

Сначала проверьте, загружен ли PSReadLine. Он загружается по умолчанию в Windows Server 2016, Windows 10 и более поздних версиях Windows. В более ранних версиях Windows его потребуется установить вручную. 

Если эта команда не вернет выходные данные в строке, значить модуль не загружен и можно продолжать использовать сеанс PowerShell в SAC в обычном режиме.

`get-module psreadline`

Если приведенная выше команда возвращает версию модуля PSReadLine, выполните следующую команду, чтобы выгрузить ее. Эта команда не удаляет модуль, а только выгружает его из текущего сеанса PowerShell.

`remove-module psreadline`

### <a name="view-and-edit-windows-registry-settings"></a>Просмотр и изменение параметров реестра Windows
#### <a name="verify-rdp-is-enabled"></a>Проверка включения RDP
`get-itemproperty -path 'hklm:\system\curRentcontrolset\control\terminal server' -name 'fdenytsconNections'`

`get-itemproperty -path 'hklm:\software\policies\microsoft\windows nt\terminal services' -name 'fdenytsconNections'`

Второй ключ (в \Policies) будет существовать только в том случае, если настроен соответствующий параметр групповой политики.
#### <a name="enable-rdp"></a>Включите RDP:
`set-itemproperty -path 'hklm:\system\curRentcontrolset\control\terminal server' -name 'fdenytsconNections' 0 -type dword`

`set-itemproperty -path 'hklm:\software\policies\microsoft\windows nt\terminal services' -name 'fdenytsconNections' 0 -type dword`

Второй ключ (в \Policies) потребуется только в том случае, если настроен соответствующий параметр групповой политики. Значение будет перезаписано при следующем обновлении групповой политики, если оно настроено в групповой политике.
### <a name="manage-windows-services"></a>Управление службами Windows
#### <a name="view-service-details"></a>Отображение сведений о службе
`get-wmiobject win32_service -filter "name='termservice'" |  format-list Name,DisplayName,State,StartMode,StartName,PathName,ServiceType,Status,ExitCode,ServiceSpecificExitCode,ProcessId`

Можно использовать команду `Get-Service`, но она не содержит учетную запись входа в систему. Учетную запись содержит `Get-WmiObject win32-service`.
#### <a name="set-service-logon-account"></a>Настройка учетной записи для входа в службу
`(get-wmiobject win32_service -filter "name='termservice'").Change($null,$null,$null,$null,$null,$false,'NT Authority\NetworkService')`

При использовании учетной записи службы, отличной от `NT AUTHORITY\LocalService`, `NT AUTHORITY\NetworkService` или `LocalSystem`, укажите пароль учетной записи как последний (восьмой) аргумент после имени учетной записи.
#### <a name="set-service-startup-type"></a>Настройка типа запуска службы
`set-service termservice -startuptype Manual`

В качестве типа запуска `Set-service` принимает `Automatic`, `Manual` или `Disabled`.
#### <a name="set-service-dependencies"></a>Настройка зависимостей служб
`Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Services\TermService' -Name DependOnService -Value @('RPCSS','TermDD')`
#### <a name="start-service"></a>Запуск службы
`start-service termservice`
#### <a name="stop-service"></a>Остановка службы
`stop-service termservice`
### <a name="manage-networking-features"></a>Управление компонентами сети
#### <a name="show-nic-properties"></a>Отображение свойств сетевого адаптера
`get-netadapter | where {$_.ifdesc.startswith('Microsoft Hyper-V Network Adapter')} |  format-list status,name,ifdesc,macadDresS,driverversion,MediaConNectState,MediaDuplexState`

или 

`get-wmiobject win32_networkadapter -filter "servicename='netvsc'" |  format-list netenabled,name,macaddress`

Команда `Get-NetAdapter` доступна в версии 2012 и выше. В версии 2008R2 используйте `Get-WmiObject`.
#### <a name="show-ip-properties"></a>Отображение свойств IP
`get-wmiobject Win32_NetworkAdapterConfiguration -filter "ServiceName='netvsc'" |  format-list DNSHostName,IPAddress,DHCPEnabled,IPSubnet,DefaultIPGateway,MACAddress,DHCPServer,DNSServerSearchOrder`
#### <a name="enable-nic"></a>Включение сетевого адаптера
`get-netadapter | where {$_.ifdesc.startswith('Microsoft Hyper-V Network Adapter')} | enable-netadapter`

или

`(get-wmiobject win32_networkadapter -filter "servicename='netvsc'").enable()`

Команда `Get-NetAdapter` доступна в версии 2012 и выше. В версии 2008R2 используйте `Get-WmiObject`.
#### <a name="set-nic-to-use-dhcp"></a>Настройка сетевого адаптера для использования DHCP
`get-netadapter | where {$_.ifdesc.startswith('Microsoft Hyper-V Network Adapter')} | Set-NetIPInterface -DHCP Enabled`

`(get-wmiobject Win32_NetworkAdapterConfiguration -filter "ServiceName='netvsc'").EnableDHCP()`

Команда `Get-NetAdapter` доступна в версии 2012 и выше. В версии 2008R2 используйте `Get-WmiObject`. Виртуальные машины Azure всегда должны использовать DHCP в гостевой ОС, чтобы получить IP-адрес. Параметр статического IP-адреса Azure по-прежнему использует DHCP, чтобы предоставить IP-адрес виртуальной машине.
#### <a name="ping"></a>Проверка связи
`test-netconnection`

или

`get-wmiobject Win32_PingStatus -Filter 'Address="8.8.8.8"' | format-table -autosize IPV4Address,ReplySize,ResponseTime`

Без параметров команда `Test-Netconnection` будет проверять связь с `internetbeacon.msedge.net`. Она доступна в версии 2012 и выше. В версии 2008R2 используйте `Get-WmiObject`, как во втором примере.
#### <a name="port-ping"></a>Проверка связи с портом
`test-netconnection -ComputerName bing.com -Port 80`

или

`(new-object Net.Sockets.TcpClient).BeginConnect('bing.com','80',$null,$null).AsyncWaitHandle.WaitOne(300)`

Команда `Test-NetConnection` доступна в версии 2012 и выше. В версии 2008R2 используйте `Net.Sockets.TcpClient`.
#### <a name="test-dns-name-resolution"></a>Тестирование разрешений DNS-имен
`resolve-dnsname bing.com` 

или 

`[System.Net.Dns]::GetHostAddresses('bing.com')`

Команда `Resolve-DnsName` доступна в версии 2012 и выше. В версии 2008R2 используйте `System.Net.DNS`.
#### <a name="show-windows-firewall-rule-by-name"></a>Отображение правила брандмауэра Windows по имени
`get-netfirewallrule -name RemoteDesktop-UserMode-In-TCP` 
#### <a name="show-windows-firewall-rule-by-port"></a>Отображение правила брандмауэра Windows по номеру порта
`get-netfirewallportfilter | where {$_.localport -eq 3389} | foreach {Get-NetFirewallRule -Name $_.InstanceId} | format-list Name,Enabled,Profile,Direction,Action`

или

`(new-object -ComObject hnetcfg.fwpolicy2).rules | where {$_.localports -eq 3389 -and $_.direction -eq 1} | format-table Name,Enabled`

Команда `Get-NetFirewallPortFilter` доступна в версии 2012 и выше. В версии 2008R2 используйте COM-объект `hnetcfg.fwpolicy2`. 
#### <a name="disable-windows-firewall"></a>Отключение брандмауэра Windows
`Set-NetFirewallProfile -Profile Domain,Public,Private -Enabled False`

Команда `Set-NetFirewallProfile` доступна в версии 2012 и выше. В версии 2008R2 используйте `netsh advfirewall`, как указано в разделе CMD выше.
### <a name="manage-users-and-groups"></a>Управление пользователями и группами
#### <a name="create-local-user-account"></a>Создание локальной учетной записи пользователя
`new-localuser <name>`
#### <a name="verify-user-account-is-enabled"></a>Проверка включения учетной записи пользователя
`(get-localuser | where {$_.SID -like "S-1-5-21-*-500"}).Enabled`

или 

`(get-wmiobject Win32_UserAccount -Namespace "root\cimv2" -Filter "SID like 'S-1-5-%-500'").Disabled`

Команда `Get-LocalUser` доступна в версии 2012 и выше. В версии 2008R2 используйте `Get-WmiObject`. В этом примере показана встроенная учетная запись локального администратора, которая всегда имеет идентификатор SID `S-1-5-21-*-500`. Виртуальные машины Azure, созданные из обобщенного образа, будут иметь учетную запись локального администратора, переименованную на имя, указанное во время подготовки виртуальной машины. Таким образом, обычно оно не может быть `Administrator`.
#### <a name="add-local-user-to-local-group"></a>Добавление локального пользователя в локальную группу
`add-localgroupmember -group Administrators -member <username>`
#### <a name="enable-local-user-account"></a>Включение локальной учетной записи пользователя
`get-localuser | where {$_.SID -like "S-1-5-21-*-500"} | enable-localuser` 

В этом примере включается встроенная учетная запись локального администратора, которая всегда имеет идентификатор SID `S-1-5-21-*-500`. Виртуальные машины Azure, созданные из обобщенного образа, будут иметь учетную запись локального администратора, переименованную на имя, указанное во время подготовки виртуальной машины. Таким образом, обычно оно не может быть `Administrator`.
#### <a name="view-user-account-properties"></a>Просмотр свойств учетной записи пользователя
`get-localuser | where {$_.SID -like "S-1-5-21-*-500"} | format-list *`

или 

`get-wmiobject Win32_UserAccount -Namespace "root\cimv2" -Filter "SID like 'S-1-5-%-500'" |  format-list Name,Disabled,Status,Lockout,Description,SID`

Команда `Get-LocalUser` доступна в версии 2012 и выше. В версии 2008R2 используйте `Get-WmiObject`. В этом примере показана встроенная учетная запись локального администратора, которая всегда имеет идентификатор SID `S-1-5-21-*-500`.
#### <a name="view-local-groups"></a>Отображение локальных групп
`(get-localgroup).name | sort` `(get-wmiobject win32_group).Name | sort`

Команда `Get-LocalUser` доступна в версии 2012 и выше. В версии 2008R2 используйте `Get-WmiObject`.
### <a name="manage-the-windows-event-log"></a>Управление журналом событий Windows
#### <a name="query-event-log-errors"></a>Запрос ошибок в журнале событий
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Level=2]]" | more`

Измените `/c:10` на желаемое количество возвращаемых событий или удалите его, чтобы получить все события, которые соответствуют фильтру.
#### <a name="query-event-log-by-event-id"></a>Запрос журнала событий по идентификатору события
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[EventID=11]]" | more`
#### <a name="query-event-log-by-event-id-and-provider"></a>Запрос журнала событий по идентификатору события и поставщику
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11]]" | more`
#### <a name="query-event-log-by-event-id-and-provider-for-the-last-24-hours"></a>Запрос журнала событий по идентификатору события и поставщику за последние сутки
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11 and TimeCreated[timediff(@SystemTime) <= 86400000]]]"`

Используйте `604800000` для поиска за последнюю неделю вместо суток. |
#### <a name="query-event-log-by-event-id-provider-and-eventdata-in-the-last-7-days"></a>Запрос журнала событий по идентификатору события, поставщику и данным событий за последнюю неделю
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Provider[@Name='Microsoft-Windows-Security-Auditing'] and EventID=4624 and TimeCreated[timediff(@SystemTime) <= 604800000]] and EventData[Data[@Name='TargetUserName']='<username>']]" | more`
### <a name="view-or-remove-installed-applications"></a>Просмотр и удаление установленных приложений
#### <a name="list-installed-software"></a>Вывод списка установленного программного обеспечения
`get-wmiobject win32_product | select installdate,name | sort installdate -descending | more`
#### <a name="uninstall-software"></a>Удаление программного обеспечения
`(get-wmiobject win32_product -filter "Name='<name>'").Uninstall()`
### <a name="file-system-management"></a>Управление файловой системой
#### <a name="get-file-version"></a>Получение версии файла
`(get-childitem $env:windir\system32\drivers\netvsc*.sys).VersionInfo.FileVersion`

В этом примере возвращается версия файла драйвера виртуального сетевого адаптера (netvsc.sys, netvsc63.sys или netvsc60.sys в зависимости от версии Windows).
#### <a name="download-and-extract-file"></a>Скачивание и извлечение файла
`$path='c:\bin';md $path;cd $path;(new-object net.webclient).downloadfile( ('htTp:/'+'/download.sysinternals.com/files/SysinternalsSuite.zip'),"$path\SysinternalsSuite.zip");(new-object -com shelL.apPlication).namespace($path).CopyHere( (new-object -com shelL.apPlication).namespace("$path\SysinternalsSuite.zip").Items(),16)`

В этом примере создается папка `c:\bin`, затем загружается и извлекается набор инструментов Sysinternals в `c:\bin`.
### <a name="miscellaneous-tasks"></a>Прочие задачи
#### <a name="show-os-version"></a>Отображение версии ОС
`get-wmiobject win32_operatingsystem | format-list caption,version,buildnumber` 
#### <a name="view-os-install-date"></a>Отображение даты установки ОС
`(get-wmiobject win32_operatingsystem).converttodatetime((get-wmiobject win32_operatingsystem).installdate)`
#### <a name="view-last-boot-time"></a>Отображение времени последней загрузки
`(get-wmiobject win32_operatingsystem).lastbootuptime`
#### <a name="view-windows-uptime"></a>Отображение времени непрерывной работы
`"{0:dd}:{0:hh}:{0:mm}:{0:ss}.{0:ff}" -f ((get-date)-(get-wmiobject win32_operatingsystem).converttodatetime((get-wmiobject win32_operatingsystem).lastbootuptime))`

Возвращает время непрерывной работы в виде `<days>:<hours>:<minutes>:<seconds>:<milliseconds>`, например `49:16:48:00.00`. 
#### <a name="restart-windows"></a>Перезапуск Windows
`restart-computer`

Если добавить `-force`, запускаемые приложения будут закрываться без предупреждения.
### <a name="instance-metadata"></a>Метаданные экземпляра

Вы можете запросить метаданные экземпляра Azure из виртуальной машины Azure, чтобы просмотреть такие данные, как osType, Location, vmSize, vmId, name, resourceGroupName, subscriptionId, privateIpAddress и publicIpAddress.

Чтобы запросить метаданные экземпляра, требуется подключение к гостевой сети, так как выполняется вызов REST через узел Azure к службе метаданных экземпляра. Поэтому, если вы можете запросить метаданные экземпляра, это говорит о том, что гость может общаться по сети со службой, размещенной в Azure.

Дополнительные сведения см. в статье [Служба метаданных экземпляров Azure](https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service).

#### <a name="instance-metadata"></a>Метаданные экземпляра
`$im = invoke-restmethod -headers @{"metadata"="true"} -uri http://169.254.169.254/metadata/instance?api-version=2017-08-01 -method get`

`$im | convertto-json`
#### <a name="os-type-instance-metadata"></a>Тип ОС (метаданные экземпляра)
`$im.Compute.osType`
#### <a name="location-instance-metadata"></a>Расположение (метаданные экземпляра)
`$im.Compute.Location`
#### <a name="size-instance-metadata"></a>Размер (метаданные экземпляра)
`$im.Compute.vmSize`
#### <a name="vm-id-instance-metadata"></a>Идентификатор виртуальной машины (метаданные экземпляра)
`$im.Compute.vmId`
#### <a name="vm-name-instance-metadata"></a>Имя виртуальной машины (метаданные экземпляра)
`$im.Compute.name`
#### <a name="resource-group-name-instance-metadata"></a>Имя группы ресурсов (метаданные экземпляра)
`$im.Compute.resourceGroupName`
#### <a name="subscription-id-instance-metadata"></a>Идентификатор подписки (метаданные экземпляра)
`$im.Compute.subscriptionId`
#### <a name="tags-instance-metadata"></a>Теги (метаданные экземпляра)
`$im.Compute.tags`
#### <a name="placement-group-id-instance-metadata"></a>Идентификатор группы размещения (метаданные экземпляра)
`$im.Compute.placementGroupId`
#### <a name="platform-fault-domain-instance-metadata"></a>Домен сбоя платформы (метаданные экземпляра)
`$im.Compute.platformFaultDomain`
#### <a name="platform-update-domain-instance-metadata"></a>Домен обновления платформы (метаданные экземпляра)
`$im.Compute.platformUpdateDomain`
#### <a name="ipv4-private-ip-address-instance-metadata"></a>Частный IP-адрес IPv4 (метаданные экземпляра)
`$im.network.interface.ipv4.ipAddress.privateIpAddress`
#### <a name="ipv4-public-ip-address-instance-metadata"></a>Общедоступный IP-адрес IPv4 (метаданные экземпляра)
`$im.network.interface.ipv4.ipAddress.publicIpAddress`
#### <a name="ipv4-subnet-address--prefix-instance-metadata"></a>Адрес подсети или префикс IPv4 (метаданные экземпляра)
`$im.network.interface.ipv4.subnet.address`

`$im.network.interface.ipv4.subnet.prefix`
#### <a name="ipv6-ip-address-instance-metadata"></a>IP-адрес IPv6 (метаданные экземпляра)
`$im.network.interface.ipv6.ipAddress`
#### <a name="mac-address-instance-metadata"></a>MAC-адрес (метаданные экземпляра)
`$im.network.interface.macAddress`

## <a name="errors"></a>Errors
Большинство ошибок носят временный характер и устраняются после повторной попытки подключения. В таблице ниже приведен список ошибок и способы устранения неисправностей. 

Ошибка                            |   Устранение 
:---------------------------------|:--------------------------------------------|
Не удалось получить параметры диагностики загрузки для "<VMNAME>". Чтобы использовать последовательную консоль, включите диагностику загрузки для этой виртуальной машины. | Включите [диагностику загрузки](boot-diagnostics.md) для виртуальной машины. 
Виртуальная машина находится в состоянии "Остановлено (освобождено)". Запустите виртуальную машину и повторите попытку подключения к последовательной консоли. | Для доступа к последовательной консоли виртуальная машина должна быть в состоянии "Запущено".
У вас нет нужных разрешений для использования последовательной консоли этой виртуальной машины. Убедитесь в наличии по крайней мере разрешений роли участника виртуальной машины.| Для доступа к последовательной консоли требуются определенные разрешения. Подробные сведения см. в разделе [требований доступа](#prerequisites).
Не удалось определить группу ресурсов для учетной записи хранения диагностики загрузки "<STORAGEACCOUNTNAME>". Убедитесь, что для этой виртуальной машины включена диагностика загрузки и у вас есть доступ к этой учетной записи хранения. | Для доступа к последовательной консоли требуются определенные разрешения. Подробные сведения см. в разделе [требований доступа](#prerequisites).

## <a name="known-issues"></a>Известные проблемы 
Так как доступ к последовательной консоли по-прежнему находится на этапе предварительной версии, мы работаем над некоторыми известными проблемами. Ниже приведен список этих возможных решений. 

Проблема                           |   Устранение 
:---------------------------------|:--------------------------------------------|
Невозможно получить доступ к последовательной консоли экземпляра масштабируемого набора виртуальных машин | На этапе предварительной версии доступ к последовательной консоли для экземпляров масштабируемого набора виртуальных машин не поддерживается.
Нажатие клавиши ВВОД после заголовка соединения не отображает запрос на вход | [Нажатие клавиши ВВОД не приводит ни к каким результатам](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md)
Отображаются только сведения о работоспособности при подключении к виртуальной машине Windows| [Сигналы работоспособности Windows](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Windows_Health_Info.md)
Не удается выполнить ввод в командной строке SAC, если включена отладка ядра | Подключитесь к виртуальной машине по протоколу RDP и выполните `bcdedit /debug {current} off` из командной строки с повышенными привилегиями. Если вы не можете использовать RDP, вместо этого подключите диск ОС к другой виртуальной машине Azure и при подключении сделайте его диском данных с помощью `bcdedit /store <drive letter of data disk>:\boot\bcd /debug <identifier> off`, а затем переключите диск обратно.

## <a name="frequently-asked-questions"></a>Часто задаваемые вопросы 
**В. Как отправить отзыв?**

О. Чтобы отправить отзыв как ошибку, перейдите на страницу https://aka.ms/serialconsolefeedback. Кроме того, (менее предпочтительно) отправьте отзыв по адресу azserialhelp@microsoft.com или в категории виртуальной машины на сайте http://feedback.azure.com. 

**В. Появляется сообщение об ошибке Existing console has conflicting OS type "Windows" with the requested OS type of Linux (В имеющейся консоли есть тип ОС Windows, конфликтующий с запрошенным типом ОС Linux).**

О. Это известная проблема. Чтобы ее исправить, просто откройте [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) в режиме bash и повторите попытку.

**В. Я не могу получить доступ к последовательной консоли. Где можно отправить запрос в службу поддержки?**

О. Эта функция предварительной версии рассматривается в условиях предварительной версии Azure. Соответствующая поддержка эффективнее всего предоставляется через вышеупомянутые каналы. 

## <a name="next-steps"></a>Дополнительная информация
* Последовательная консоль есть также в виртуальных машинах [Linux](../linux/serial-console.md).
* Узнайте больше о [диагностике загрузки](boot-diagnostics.md).
