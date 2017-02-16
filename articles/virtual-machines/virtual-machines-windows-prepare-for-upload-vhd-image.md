---
title: "Подготовка виртуального жесткого диска Windows к передаче в Azure | Документация Майкрософт"
description: "Подготовка диска VHD или VHDX для Windows перед отправкой в Azure."
services: virtual-machines-windows
documentationcenter: 
author: genlin
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 7802489d-33ec-4302-82a4-91463d03887a
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 1/11/2017
ms.author: glimoli;genli
translationtype: Human Translation
ms.sourcegitcommit: 5d8274f61c3de178c9d418adb9be1efe0fe62bc1
ms.openlocfilehash: 6fbfc74cb1cce744b51345c0732b40b95be21c94


---
# <a name="prepare-a-windows-vhd-or-vhdx-to-upload-to-azure"></a>Подготовка диска VHD или VHDX для Windows к отправке в Azure
Чтобы передать виртуальную машину Windows из локальной среды в Azure, следует правильно подготовить виртуальный жесткий диск (VHD или VHDX). В Azure поддерживаются только виртуальные машины первого поколения, использующие формат файла VHD и фиксированный размер диска. Максимально допустимый размер виртуального жесткого диска составляет 1023 ГБ. Виртуальную машину первого поколения можно преобразовать из формата VHDX в формат VHD, а также переключить с динамически расширяемого диска на диск фиксированного размера. Но вы не можете изменить поколение виртуальной машины. Дополнительные сведения см. в статье о том, [как выбрать поколение для виртуальной машины в Hyper-V](https://technet.microsoft.com/en-us/windows-server-docs/compute/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v).

## <a name="convert-the-virtual-disk-to-vhd-and-fixed-size-disk"></a>Преобразование виртуального жесткого диска в формат VHD с фиксированным размером 
Если вы хотите преобразовать виртуальный диск в формат, поддерживаемый Azure, используйте один из методов, указанных в этом разделе. Прежде чем запускать процесс преобразования виртуального диска, создайте резервную копию виртуальной машины и убедитесь, что виртуальный жесткий диск Windows правильно работает на локальном сервере. Устраните все ошибки на виртуальной машине, прежде чем преобразовывать ее или отправлять в Azure.

После преобразования диска создайте виртуальную машину, которая использует этот преобразованный диск. Запустите виртуальную машину и войдите в нее для завершения подготовки виртуальной машины к отправке.

### <a name="convert-disk-using-hyper-v-manager"></a>Преобразование диска с помощью диспетчера Hyper-V
1. Откройте диспетчер Hyper-V и выберите свой локальный компьютер в левой части окна. В меню над ним выберите **Действие** > **Изменить диск**.
2. На экране **Поиск виртуального жесткого диска** найдите и выберите свой виртуальный диск.
3. На экране **Выбрать действие** щелкните **Преобразовать** и **Далее**.
4. Если необходимо преобразовать диск в формате VHDX, выберите **VHD** и нажмите кнопку **Далее**.
5. Если необходимо преобразовать динамически расширяемый диск, выберите **Фиксированный размер** и нажмите кнопку **Далее**.
6. Укажите путь к папке, в которой нужно сохранить новый VHD-файл.
7. Нажмите кнопку **Готово** , чтобы закрыть окно.

### <a name="convert-disk-using-powershell"></a>Преобразование диска с помощью PowerShell
Виртуальный диск можно преобразовать с помощью командлета Windows PowerShell [Convert-VHD](http://technet.microsoft.com/library/hh848454.aspx). При запуске PowerShell выберите **Запуск от имени администратора**. В следующем примере показано, как преобразовать диск VHDX в VHD, а динамически расширяемый диск — в диск фиксированного размера.

```powershell
Convert-VHD –Path c:\test\MY-VM.vhdx –DestinationPath c:\test\MY-NEW-VM.vhd -VHDType Fixed
```
Замените значения, указав вместо -Path путь к виртуальному жесткому диску, который нужно преобразовать, а вместо -DestinationPath — путь и имя для нового, преобразованного диска.

### <a name="convert-from-vmware-vmdk-disk-format"></a>Преобразование диска VMware в формате VMDK
Если у вас есть образ виртуальной машины Windows [в формате VMDK](https://en.wikipedia.org/wiki/VMDK), преобразуйте его в VHD с помощью [Microsoft Virtual Machine Converter](https://www.microsoft.com/download/details.aspx?id=42497). Дополнительные сведения см. в записи блога [How to Convert a VMware VMDK to Hyper-V VHD](http://blogs.msdn.com/b/timomta/archive/2015/06/11/how-to-convert-a-vmware-vmdk-to-hyper-v-vhd.aspx) (Преобразование диска VMDK VMWare в VHD Hyper-V).

## <a name="set-windows-configurations-for-azure"></a>Настройка конфигурации Windows для Azure

На виртуальной машине, которую вы намерены отправить в Azure, выполните из командной строки следующие команды с [правами администратора](https://technet.microsoft.com/library/cc947813.aspx).

1. Удалите все постоянные статические маршруты из таблицы маршрутизации.
   
   * Чтобы просмотреть таблицу маршрутов, запустите `route print` из окна командной строки.
   * Проверьте разделы **Persistence Routes** (Сохраняемые маршруты). При наличии постоянного маршрута удалите его с помощью команды [route delete](https://technet.microsoft.com/library/cc739598.apx) .
2. Удалите прокси-сервер WinHTTP.
   
    ```CMD
    netsh winhttp reset proxy
    ```
3. Установите для политики SAN дисков значение [Onlineall](https://technet.microsoft.com/library/gg252636.aspx). 
   
    ```CMD
    diskpart 
    san policy=onlineall
    exit
    ```
    

4. Установите время в формате UTC для Windows, а для типа запуска службы времени Windows (w32time) — значение **Автоматически**.
   
    ```CMD
    REG ADD HKLM\SYSTEM\CurrentControlSet\Control\TimeZoneInformation /v RealTimeIsUniversal /t REG_DWORD /d 1
    ```
    ```CMD
    sc config w32time start= auto
    ```

## <a name="set-services-startup-to-windows-default-values"></a>Установка для запуска службы стандартных значений Windows
Убедитесь, что для каждой из приведенных ниже служб Windows заданы **значения Windows по умолчанию**. Чтобы сбросить параметры загрузки, можно использовать приведенные ниже команды.
   
```CMD
sc config bfe start= auto
   
sc config dcomlaunch start= auto
   
sc config dhcp start= auto
   
sc config dnscache start= auto
   
sc config IKEEXT start= auto
   
sc config iphlpsvc start= auto
   
sc config PolicyAgent start= demand
   
sc config LSM start= auto
   
sc config netlogon start= demand
   
sc config netman start= demand
   
sc config NcaSvc start= demand
   
sc config netprofm start= demand
   
sc config NlaSvc start= auto
   
sc config nsi start= auto
   
sc config RpcSs start= auto
   
sc config RpcEptMapper start= auto
   
sc config termService start= demand
   
sc config MpsSvc start= auto
   
sc config WinHttpAutoProxySvc start= demand
   
sc config LanmanWorkstation start= auto
   
sc config RemoteRegistry start= auto
```

## <a name="update-remote-desktop-registry-settings"></a>Обновление параметров реестра для удаленного рабочего стола
1. Если к прослушивателю протокола удаленного рабочего стола (RDP) привязаны какие-либо самозаверяющие сертификаты, удалите их.
   
    ```CMD
    REG DELETE "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp\SSLCertificateSHA1Hash”
    ```
   
    Дополнительные сведения о настройке сертификатов для прослушивателя RDP см. в разделе [Настройка сертификатов прослушивателя в Windows Server](https://blogs.technet.microsoft.com/askperf/2014/05/28/listener-certificate-configurations-in-windows-server-2012-2012-r2/).
2. Настройте значения [KeepAlive](https://technet.microsoft.com/library/cc957549.aspx) для службы RDP.
   
    ```CMD
    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v KeepAliveEnable /t REG_DWORD  /d 1 /f
    ```
    ```CMD
    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v KeepAliveInterval /t REG_DWORD  /d 1 /f
    ```
    ```CMD
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp" /v KeepAliveTimeout /t REG_DWORD /d 1 /f
    ```
3. Настройте режим аутентификации для службы RDP.
   
    ```CMD
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v UserAuthentication /t REG_DWORD  /d 1 /f
   ```
    ```CMD
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SecurityLayer /t REG_DWORD  /d 1 /f
   ```
    ```CMD
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v fAllowSecProtocolNegotiation /t REG_DWORD  /d 1 /f
    ```
4. Включите службу RDP, добавив следующие подразделы в реестр.
   
    ```CMD
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections /t REG_DWORD  /d 0 /f
    ```

## <a name="configure-windows-firewall-rules"></a>Настройка правил брандмауэра Windows
1. Выполните следующие команды PowerShell, чтобы разрешить трафик WinRM в трех профилях брандмауэра ("Домен", "Частный" и "Общий") и включить удаленное управление PowerShell.
   
   ```powershell
   Enable-PSRemoting -force
   ```
2. Убедитесь, что нужные правила брандмауэра для операционной системы на виртуальной машине настроены, выполнив следующие команды:
   
   * Входящий трафик
   
   ```CMD
   netsh advfirewall firewall set rule dir=in name="File and Printer Sharing (Echo Request - ICMPv4-In)" new enable=yes
   
   netsh advfirewall firewall set rule dir=in name="Network Discovery (LLMNR-UDP-In)" new enable=yes
   
   netsh advfirewall firewall set rule dir=in name="Network Discovery (NB-Datagram-In)" new enable=yes
   
   netsh advfirewall firewall set rule dir=in name="Network Discovery (NB-Name-In)" new enable=yes
   
   netsh advfirewall firewall set rule dir=in name="Network Discovery (Pub-WSD-In)" new enable=yes
   
   netsh advfirewall firewall set rule dir=in name="Network Discovery (SSDP-In)" new enable=yes
   
   netsh advfirewall firewall set rule dir=in name="Network Discovery (UPnP-In)" new enable=yes
   
   netsh advfirewall firewall set rule dir=in name="Network Discovery (WSD EventsSecure-In)" new enable=yes
   
   netsh advfirewall firewall set rule dir=in name="Windows Remote Management (HTTP-In)" new enable=yes
   
   netsh advfirewall firewall set rule dir=in name="Windows Remote Management (HTTP-In)" new enable=yes
   ```
   
   * Исходящий и входящий
   
   ```CMD
   netsh advfirewall firewall set rule group="Remote Desktop" new enable=yes
   
   netsh advfirewall firewall set rule group="Core Networking" new enable=yes
   ```
   
   * Исходящие
   
   ```CMD
   netsh advfirewall firewall set rule dir=out name="Network Discovery (LLMNR-UDP-Out)" new enable=yes
   
   netsh advfirewall firewall set rule dir=out name="Network Discovery (NB-Datagram-Out)" new enable=yes
   
   netsh advfirewall firewall set rule dir=out name="Network Discovery (NB-Name-Out)" new enable=yes
   
   netsh advfirewall firewall set rule dir=out name="Network Discovery (Pub-WSD-Out)" new enable=yes
   
   netsh advfirewall firewall set rule dir=out name="Network Discovery (SSDP-Out)" new enable=yes
   
   netsh advfirewall firewall set rule dir=out name="Network Discovery (UPnPHost-Out)" new enable=yes
   
   netsh advfirewall firewall set rule dir=out name="Network Discovery (UPnP-Out)" new enable=yes
   
   netsh advfirewall firewall set rule dir=out name="Network Discovery (WSD Events-Out)" new enable=yes
   
   netsh advfirewall firewall set rule dir=out name="Network Discovery (WSD EventsSecure-Out)" new enable=yes
   
   netsh advfirewall firewall set rule dir=out name="Network Discovery (WSD-Out)" new enable=yes
   ```

## <a name="verify-vm-is-healthy-secure-and-accessible-with-rdp"></a>Проверка виртуальной машины: работоспособность, защищенность и доступ по протоколу RDP 
1. В окне командной строки выполните команду `winmgmt /verifyrepository`, чтобы подтвердить согласованность репозитория инструментария управления Windows (WMI). Если репозиторий поврежден, см. запись блога [WMI: Repository Corruption, or Not?](https://blogs.technet.microsoft.com/askperf/2014/08/08/wmi-repository-corruption-or-not) (WMI: поврежден ли репозиторий?)
2. Задайте параметры данных конфигурации загрузки (BCD):
   
   ```CMD
   bcdedit /set {bootmgr} integrityservices enable
   
   bcdedit /set {default} device partition=C:
   
   bcdedit /set {default} integrityservices enable
   
   bcdedit /set {default} recoveryenabled Off
   
   bcdedit /set {default} osdevice partition=C:
   
   bcdedit /set {default} bootstatuspolicy IgnoreAllFailures
   ```
3. Удалите все дополнительные фильтры TDI, например программное обеспечение, которое анализирует пакеты TCP.
4. Чтобы убедиться, что диск работоспособен и согласован, выполните в окне командной строки команду `CHKDSK /f`. Введите "Y", чтобы запланировать проверку, и перезапустите виртуальную машину.
5. Удалите все стороннее программное обеспечение и драйверы, связанные с физическими компонентами или какой-либо другой технологией виртуализации.
6. Убедитесь в том, что никакое стороннее приложение не использует порт 3389. Этот порт используется для службы RDP в Azure. Запустив `netstat -anob` в окне командной строки, вы сможете увидеть порты, используемые приложениями.
7. Если требуется передать виртуальный жесткий диск Windows, который является контроллером домена, выполните [эти дополнительные шаги](https://support.microsoft.com/kb/2904015) по подготовке диска.
8. Перезагрузите виртуальную машину, чтобы убедиться в том, что ОС Windows по-прежнему работоспособна и доступна с помощью подключения к удаленному рабочему столу.
9. Сбросьте текущий пароль локального администратора и убедитесь, что эту учетную запись можно использовать для входа в Windows через подключение к удаленному рабочему столу. Это разрешение на доступ контролируется объектом групповой политики "Разрешить вход в систему через службу удаленных рабочих столов". Этот объект находится в папке редактора локальных групповых политик "Computer Configuration\Windows Settings\Security Settings\Local Policies\User Rights Assignment".

## <a name="install-windows-updates"></a>Установка обновлений Windows
Установите последние обновления для Windows. Если это невозможно, установите по меньшей мере следующие обновления:
   
   * [KB3137061](https://support.microsoft.com/kb/3137061) : виртуальные машины Microsoft Azure не восстанавливаются после сбоя сети и возникают проблемы из-за повреждения данных.
   * [KB3115224](https://support.microsoft.com/kb/3115224) : улучшения надежности виртуальных машин, работающих на узле Windows Server 2012 R2 или Windows Server 2012.
   * [KB3140410](https://support.microsoft.com/kb/3140410) MS16-031: обновление безопасности для Microsoft Windows, устраняющее проблему с повышением привилегий (8 марта 2016 г.).
   * [KB3063075](https://support.microsoft.com/kb/3063075) : при запуске виртуальной машины Windows Server 2012 R2 в Microsoft Azure регистрируется множество событий с идентификатором 129.
   * [KB3137061](https://support.microsoft.com/kb/3137061) : виртуальные машины Microsoft Azure не восстанавливаются после сбоя сети и возникают проблемы из-за повреждения данных.
   * [KB3114025](https://support.microsoft.com/kb/3114025) : низкая производительность при доступе к хранилищу файлов Azure из Windows 8.1 или Server 2012 R2.
   * [KB3033930](https://support.microsoft.com/kb/3033930) : исправление увеличивает предельное количество буферов RIO на процесс (64 000) для службы Azure в Windows.
   * [KB3004545](https://support.microsoft.com/kb/3004545) : невозможно получить доступ к виртуальным машинам, размещенным в службах размещения Azure, через VPN-подключение в Windows.
   * [KB3082343](https://support.microsoft.com/kb/3082343) : распределенные VPN-подключения утрачиваются, когда туннели VPN типа "сеть — сеть" Azure используют RRAS в Windows Server 2012 R2.
   * [KB3140410](https://support.microsoft.com/kb/3140410) MS16-031: обновление безопасности для Microsoft Windows, устраняющее проблему с повышением привилегий (8 марта 2016 г.).
   * [KB3146723](https://support.microsoft.com/kb/3146723) MS16-048: описание обновления для системы безопасности CSRSS от 12 апреля 2016 г.
   * [KB2904100](https://support.microsoft.com/kb/2904100): система перестает отвечать на запросы при дисковых операциях ввода-вывода в Windows.
     
## <a name="run-sysprep--a-idstep23a"></a>Запуск Sysprep <a id="step23"></a>    
Если вы хотите создать образ для развертывания нескольких виртуальных машин, то перед отправкой VHD в Azure образ необходимо [подготовить к использованию, выполнив команду Sysprep](virtual-machines-windows-generalize-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Для подготовки специализированного виртуального жесткого диска запускать Sysprep не требуется. Дополнительные сведения см. в следующих статьях:
   
   * [Generalize a Windows virtual machine using Sysprep](virtual-machines-windows-generalize-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Подготовка виртуальной машины Windows к использованию с помощью Sysprep)
   * [Sysprep Support for Server Roles (Поддержка ролей сервера в Sysprep)](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)

## <a name="complete-recommended-configurations"></a>Рекомендуемые настройки
Приведенные ниже параметры не влияют на передачу VHD. Тем не менее настоятельно рекомендуется их настроить.

* Установите [агент виртуальной машины Azure](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). После установки агента можно включить расширения виртуальной машины. Расширения виртуальной машины реализуют большую часть важных функций, которые могут вам понадобиться при работе с виртуальными машинами, в том числе сброс паролей, настройку протокола RDP и множество других функций.
* Журнал дампа может быть полезен при устранении критических неполадок Windows. Включите сбор журнала дампа, как показано ниже.
  
    ```CMD
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 2 /f`
  
    REG ADD "HKLM\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps" /v DumpFolder /t REG_EXPAND_SZ /d "c:\CrashDumps" /f
  
    REG ADD "HKLM\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps" /v DumpCount /t REG_DWORD /d 10 /f
  
    REG ADD "HKLM\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps" /v DumpType /t REG_DWORD /d 2 /f
  
    sc config wer start= auto
    ```
* После создания виртуальной машины в Azure настройте определенный системой размер файла подкачки на диске D.
  
    ```CMD
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\Session Manager\Memory Management" /t REG_MULTI_SZ /v PagingFiles /d "D:\pagefile.sys 0 0" /f
    ```

## <a name="next-steps"></a>Дальнейшие действия
* [Отправка образа виртуальной машины Windows в Azure для развертываний Resource Manager](virtual-machines-windows-upload-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)




<!--HONumber=Jan17_HO2-->


