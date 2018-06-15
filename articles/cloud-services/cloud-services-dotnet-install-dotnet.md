---
title: Установка .NET для ролей облачных служб Azure | Документация Майкрософт
description: В этой статье описывается, как вручную установить платформу .NET Framework для веб-роли и рабочей роли облачной службы.
services: cloud-services
documentationcenter: .net
author: thraka
manager: timlt
editor: ''
ms.assetid: 8d1243dc-879c-4d1f-9ed0-eecd1f6a6653
ms.service: cloud-services
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/10/2018
ms.author: adegeo
ms.openlocfilehash: 02e778154662c28b78b284fccb3bd9b9420a8bec
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34608446"
---
# <a name="install-net-on-azure-cloud-services-roles"></a>Установка .NET для ролей облачных служб Azure
В этой статье описывается установка версий платформы .NET Framework, которые не входят в состав гостевой ОС Azure. .NET в гостевой ОС можно использовать для настройки веб-ролей и рабочих ролей облачной службы.

Например, можно установить .NET 4.6.1 в семействе версий 4 гостевых ОС, которые не входят в состав какого-либо из выпусков .NET 4.6. (Семейство версий 5 гостевых ОС поставляется с NET 4.6.) Самые актуальные сведения о выпусках гостевой ОС Azure см. в статье [Таблица совместимости выпусков гостевых ОС Azure и пакетов SDK](cloud-services-guestos-update-matrix.md). 

>[!IMPORTANT]
>Пакет SDK 2.9 для Azure содержит ограничение на развертывание .NET 4.6 в семействе версий 4 гостевых ОС. Исправление ограничения доступно на сайте [документации Майкрософт](https://github.com/MicrosoftDocs/azure-cloud-services-files/tree/master/Azure%20Targets%20SDK%202.9).

Чтобы установить .NET для веб-ролей и рабочих ролей, включите веб-установщик .NET в качестве части проекта облачной службы. Запустите установщик в рамках задач запуска роли. 

## <a name="add-the-net-installer-to-your-project"></a>Добавление установщика .NET в проект
Чтобы скачать веб-установщик для платформы .NET Framework, выберите версию, которую требуется установить:

* [Веб-установщик .NET 4.7.2](http://go.microsoft.com/fwlink/?LinkId=863262).
* [Веб-установщик .NET 4.6.1](http://go.microsoft.com/fwlink/?LinkId=671729).

Чтобы добавить установщик для *веб*-роли:
  1. В **обозревателе решений** в разделе **Роли** проекта облачной службы щелкните правой кнопкой мыши *веб-роль* и выберите **Добавить** > **Новая папка**. Создайте папку с именем **bin**.
  2. Щелкните правой кнопкой мыши папку bin и выберите **Добавить** > **Существующий элемент**. Выберите установщик .NET и добавьте его в папку bin.
  
Чтобы добавить установщик для *рабочей* роли:
* Щелкните правой кнопкой мыши *рабочую* роль и выберите **Добавить** > **Существующий элемент**. Выберите установщик .NET и добавьте его в роль. 

При добавлении файлов таким образом в папку содержимого роли они автоматически добавляются в пакет облачной службы. Файлы затем развертываются в согласованное расположение на виртуальной машине. Повторите эту процедуру для каждой веб-роли и рабочей роли в облачной службе, чтобы у всех ролей была копия установщика.

> [!NOTE]
> Установите .NET 4.6.1 в роль облачной службы, даже если приложению необходим .NET 4.6. Гостевая ОС включает [обновление 3098779](https://support.microsoft.com/kb/3098779) и [обновление 3097997](https://support.microsoft.com/kb/3097997) базы знаний. Проблемы могут возникнуть при запуске приложений .NET, если .NET 4.6 установлена поверх обновлений базы знаний Майкрософт. Чтобы избежать этих проблем, установите .NET 4.6.1 вместо версии 4.6. Дополнительные сведения см. в [статье базы знаний 3118750](https://support.microsoft.com/kb/3118750).
> 
> 

![Роль с файлами установщика][1]

## <a name="define-startup-tasks-for-your-roles"></a>Определение начальных задач для ролей
С помощью задач запуска вы можете выполнять различные операции перед запуском роли. Установка платформы .NET Framework как части начальной задачи позволяет установить платформу до того, как будет запущено выполнение программного кода. Дополнительные сведения о начальных задачах см. в статье [Как настроить и выполнить задачи запуска для облачной службы](cloud-services-startup-tasks.md). 

1. В файл ServiceDefinition.csdef в узле **WebRole** или **WorkerRole** добавьте для всех ролей следующее содержимое:
   
    ```xml
    <LocalResources>
      <LocalStorage name="NETFXInstall" sizeInMB="1024" cleanOnRoleRecycle="false" />
    </LocalResources>    
    <Startup>
      <Task commandLine="install.cmd" executionContext="elevated" taskType="simple">
        <Environment>
          <Variable name="PathToNETFXInstall">
            <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='NETFXInstall']/@path" />
          </Variable>
          <Variable name="ComputeEmulatorRunning">
            <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
          </Variable>
        </Environment>
      </Task>
    </Startup>
    ```
   
    Предыдущая конфигурация выполняет консольную команду `install.cmd` с правами администратора и устанавливает платформу .NET Framework. Конфигурация также создает элемент **LocalStorage** с именем **NETFXInstall**. Сценарий запуска задает временную папку для использования этого локального ресурса хранилища. 
    
    > [!IMPORTANT]
    > Для обеспечения правильной установки платформы размер этого ресурса должен быть не менее 1024 МБ.
    
    Дополнительные сведения о задачах запуска см. в статье [Стандартные задачи запуска в облачной службе](cloud-services-startup-tasks-common.md).

2. Создайте файл с именем **install.cmd** и добавьте в него следующий скрипт установки.

    Скрипт путем поиска по реестру проверяет, установлена ли на компьютере выбранная версия .NET Framework. Если требуемая версия .NET не установлена, открывается веб-установщик .NET. Для помощи в устранении возникших проблем скрипт регистрирует все действия в файл startuptasklog-(текущая дата и время).txt, который хранится в локальном хранилище **InstallLogs**.
  
    > [!IMPORTANT]
    > Для создания файла install.cmd используйте простой текстовый редактор, например Блокнот. Если для создания текстового файла и изменения расширения на .cmd используется Visual Studio, файл по-прежнему может содержать метку порядка байтов UTF-8. Эта метка может вызвать ошибку при выполнении первой строки скрипта. Чтобы избежать этой ошибки, сделайте первую строку скрипта оператором REM, который может быть пропущен при обработке порядка байтов. 
    > 
    >
   
    ```cmd
    REM Set the value of netfx to install appropriate .NET Framework. 
    REM ***** To install .NET 4.5.2 set the variable netfx to "NDP452" *****
    REM ***** To install .NET 4.6 set the variable netfx to "NDP46" *****
    REM ***** To install .NET 4.6.1 set the variable netfx to "NDP461" ***** http://go.microsoft.com/fwlink/?LinkId=671729
    REM ***** To install .NET 4.6.2 set the variable netfx to "NDP462" *****
    REM ***** To install .NET 4.7 set the variable netfx to "NDP47" ***** 
    REM ***** To install .NET 4.7.1 set the variable netfx to "NDP471" ***** http://go.microsoft.com/fwlink/?LinkId=852095
    REM ***** To install .NET 4.7.2 set the variable netfx to "NDP472" ***** http://go.microsoft.com/fwlink/?LinkId=863262
    set netfx="NDP472"
    
    REM ***** Set script start timestamp *****
    set timehour=%time:~0,2%
    set timestamp=%date:~-4,4%%date:~-10,2%%date:~-7,2%-%timehour: =0%%time:~3,2%
    set "log=install.cmd started %timestamp%."
    
    REM ***** Exit script if running in Emulator *****
    if %ComputeEmulatorRunning%=="true" goto exit
    
    REM ***** Needed to correctly install .NET 4.6.1, otherwise you may see an out of disk space error *****
    set TMP=%PathToNETFXInstall%
    set TEMP=%PathToNETFXInstall%
    
    REM ***** Setup .NET filenames and registry keys *****
    if %netfx%=="NDP472" goto NDP472
    if %netfx%=="NDP471" goto NDP471
    if %netfx%=="NDP47" goto NDP47
    if %netfx%=="NDP462" goto NDP462
    if %netfx%=="NDP461" goto NDP461
    if %netfx%=="NDP46" goto NDP46
    
    set "netfxinstallfile=NDP452-KB2901954-Web.exe"
    set netfxregkey="0x5cbf5"
    goto logtimestamp
    
    :NDP46
    set "netfxinstallfile=NDP46-KB3045560-Web.exe"
    set netfxregkey="0x6004f"
    goto logtimestamp
    
    :NDP461
    set "netfxinstallfile=NDP461-KB3102438-Web.exe"
    set netfxregkey="0x6040e"
    goto logtimestamp
    
    :NDP462
    set "netfxinstallfile=NDP462-KB3151802-Web.exe"
    set netfxregkey="0x60632"
    goto logtimestamp
    
    :NDP47
    set "netfxinstallfile=NDP47-KB3186500-Web.exe"
    set netfxregkey="0x707FE"
    goto logtimestamp
    
    :NDP471
    set "netfxinstallfile=NDP471-KB4033344-Web.exe"
    set netfxregkey="0x709fc"
    goto logtimestamp
    
    :NDP472
    set "netfxinstallfile=NDP472-KB4054531-Web.exe"
    set netfxregkey="0x70BF6"
    goto logtimestamp
    
    
    :logtimestamp
    REM ***** Setup LogFile with timestamp *****
    md "%PathToNETFXInstall%\log"
    set startuptasklog="%PathToNETFXInstall%log\startuptasklog-%timestamp%.txt"
    set netfxinstallerlog="%PathToNETFXInstall%log\NetFXInstallerLog-%timestamp%"
    echo %log% >> %startuptasklog%
    echo Logfile generated at: %startuptasklog% >> %startuptasklog%
    echo TMP set to: %TMP% >> %startuptasklog%
    echo TEMP set to: %TEMP% >> %startuptasklog%
    
    REM ***** Check if .NET is installed *****
    echo Checking if .NET (%netfx%) is installed >> %startuptasklog%
    set /A netfxregkeydecimal=%netfxregkey%
    set foundkey=0
    FOR /F "usebackq skip=2 tokens=1,2*" %%A in (`reg query "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\NET Framework Setup\NDP\v4\Full" /v Release 2^>nul`) do @set /A foundkey=%%C
    echo Minimum required key: %netfxregkeydecimal% -- found key: %foundkey% >> %startuptasklog%
    if %foundkey% GEQ %netfxregkeydecimal% goto installed
    
    REM ***** Installing .NET *****
    echo Installing .NET with commandline: start /wait %~dp0%netfxinstallfile% /q /serialdownload /log %netfxinstallerlog%  /chainingpackage "CloudService Startup Task" >> %startuptasklog%
    start /wait %~dp0%netfxinstallfile% /q /serialdownload /log %netfxinstallerlog% /chainingpackage "CloudService Startup Task" >> %startuptasklog% 2>>&1
    if %ERRORLEVEL%== 0 goto installed
        echo .NET installer exited with code %ERRORLEVEL% >> %startuptasklog%    
        if %ERRORLEVEL%== 3010 goto restart
        if %ERRORLEVEL%== 1641 goto restart
        echo .NET (%netfx%) install failed with Error Code %ERRORLEVEL%. Further logs can be found in %netfxinstallerlog% >> %startuptasklog%
    
    :restart
    echo Restarting to complete .NET (%netfx%) installation >> %startuptasklog%
    EXIT /B %ERRORLEVEL%
    
    :installed
    echo .NET (%netfx%) is installed >> %startuptasklog%
    
    :end
    echo install.cmd completed: %date:~-4,4%%date:~-10,2%%date:~-7,2%-%timehour: =0%%time:~3,2% >> %startuptasklog%
    
    :exit
    EXIT /B 0
    ```

3. Добавьте файл install.cmd для каждой роли, используя команды **Добавить** > **Существующий элемент** в **обозревателе решений**, как описано ранее в этой статье. 

    После завершения этого шага все роли должны иметь файл установщика .NET и файл install.cmd.

   ![Роль со всеми файлами][2]

## <a name="configure-diagnostics-to-transfer-startup-logs-to-blob-storage"></a>Настройка диагностики для передачи журналов задач запуска в хранилище BLOB-объектов
Чтобы упростить устранение неполадок при установке, можно настроить в системе диагностики Microsoft Azure передачу всех файлов журналов, созданных скриптом запуска или установщиком .NET, в хранилище BLOB-объектов Azure. Это позволит просматривать журналы, скачивая их из хранилища BLOB-объектов, а не на удаленном рабочем столе, где хранится роль.

Чтобы настроить систему диагностики, откройте файл diagnostics.wadcfgx и добавьте в узел **Directories** следующее содержимое: 

```xml 
<DataSources>
 <DirectoryConfiguration containerName="netfx-install">
  <LocalResource name="NETFXInstall" relativePath="log"/>
 </DirectoryConfiguration>
</DataSources>
```

Этот XML-код позволяет системе диагностики передавать файлы из каталога log в ресурсе **NETFXInstall** в учетную запись хранения диагностических данных в контейнере больших двоичных объектов **netfx-install**.

## <a name="deploy-your-cloud-service"></a>Развертывание облачной службы
При развертывании облачной службы задачи запуска устанавливают платформу .NET Framework, если она еще не установлена. Роли облачной службы находятся в состоянии *занятости* при установке платформы. Если при установке платформы необходима перезагрузка, роли службы также могут перезапуститься. 

## <a name="additional-resources"></a>Дополнительные ресурсы
* [Установка платформы .NET Framework][Installing the .NET Framework]
* [Практическое руководство. Определение установленных версий платформы .NET Framework][How to: Determine Which .NET Framework Versions Are Installed]
* [Устранение неполадок заблокированных установок и удалений .NET Framework][Troubleshooting .NET Framework Installations]

[How to: Determine Which .NET Framework Versions Are Installed]: /dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed
[Installing the .NET Framework]: /dotnet/framework/install/guide-for-developers
[Troubleshooting .NET Framework Installations]: /dotnet/framework/install/troubleshoot-blocked-installations-and-uninstallations

<!--Image references-->
[1]: ./media/cloud-services-dotnet-install-dotnet/rolecontentwithinstallerfiles.png
[2]: ./media/cloud-services-dotnet-install-dotnet/rolecontentwithallfiles.png
