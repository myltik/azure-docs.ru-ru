---
title: Управление устройствами StorSimple с помощью Runbook службы автоматизации Azure | Документация Майкрософт
description: Узнайте, как использовать Runbook службы автоматизации Azure для автоматизации заданий StorSimple.
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 10/23/2017
ms.author: alkohli
ms.openlocfilehash: cfd0e4dbb6a4f24df5ba42cd45f9c16fbe5b493c
ms.sourcegitcommit: e6029b2994fa5ba82d0ac72b264879c3484e3dd0
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/24/2017
ms.locfileid: "23493134"
---
# <a name="use-azure-automation-runbooks-to-manage-storsimple-devices"></a>Управление устройствами StorSimple с помощью Runbook службы автоматизации Azure

В этой статье описано управления устройством StorSimple серии 8000 на портале Azure c помощью модулей Runbook службы автоматизации Azure. Пример модуля Runbook включен в пошаговое руководство по настройке среды для выполнения этого модуля Runbook.


## <a name="configure-add-and-run-azure-runbook"></a>Настройка, добавление и запуск Runbook Azure

В этом разделе приведен пример сценария Windows PowerShell для StorSimple и подробные сведения о различных шагах, необходимых для импорта сценария в Runbook, а также публикации и выполнения Runbook.

### <a name="prerequisites"></a>предварительным требованиям

Перед началом работы убедитесь, что у вас есть следующие компоненты:

* активная подписка Azure, связанная с зарегистрированной службой диспетчера устройств StorSimple с устройством StorSimple серии 8000.


* среда Windows PowerShell 5.0, установленная на компьютере (или узел Windows Server для StorSimple, если он используется).


### <a name="create-automation-runbook-module-in-windows-powershell"></a>Создание модуля Runbook службы автоматизации в Windows PowerShell

Чтобы создать модуль службы автоматизации для управления устройствами серии StorSimple 8000, сделайте следующее:

1. Запустите Windows PowerShell. Создайте папку и замените каталог на новую папку.

    ```
        mkdir C:\scripts\StorSimpleSDKTools
        cd C:\scripts\StorSimpleSDKTools
    ```    
2. [Скачайте интерфейс командной строки NuGet](http://www.nuget.org/downloads) из папки, созданной на предыдущем шаге. Существуют различные версии _nuget.exe_. Выберите версию, соответствующую пакету SDK. Каждая ссылка для скачивания указывает непосредственно на файл _EXE_. Щелкните правой кнопкой мыши, сохраните файл на компьютере и не запускайте его из браузера.

    Следующую команду также можно выполнить, чтобы загрузить и сохранить сценарий в той же папке (созданной ранее).
    
    ```
        wget https://dist.nuget.org/win-x86-commandline/latest/nuget.exe -Out C:\scripts\StorSimpleSDKTools\nuget.exe
    ```
3. Загрузите зависимый пакет SDK.

    ```
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Azure.Management.Storsimple8000series
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.28.3
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Rest.ClientRuntime.Azure.Authentication -Version 2.2.9-preview
    ```    
4. Загрузите сценарий из образца проекта GitHub.

    ```
        wget https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Monitor-Backups.ps1 -Out Monitor-Backups.ps1

    ```
5. Создайте модуль Runbook службы автоматизации Azure для управления устройствами StorSimple серии 8000. В окне Windows Powershell введите следующие команды:

    ```
            # set path variables
            $downloadDir = "C:\scripts\StorSimpleSDKTools"
            $moduleDir = "$downloadDir\AutomationModule\Microsoft.Azure.Management.StorSimple8000Series"

            #don't change the folder name "Microsoft.Azure.Management.StorSimple8000Series"
            mkdir "$moduleDir"
            copy "$downloadDir\Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3\lib\net45\Microsoft.IdentityModel.Clients.ActiveDirectory*.dll" $moduleDir
            copy "$downloadDir\Microsoft.Rest.ClientRuntime.Azure.3.3.7\lib\net452\Microsoft.Rest.ClientRuntime.Azure*.dll" $moduleDir
            copy "$downloadDir\Microsoft.Rest.ClientRuntime.2.3.8\lib\net452\Microsoft.Rest.ClientRuntime*.dll" $moduleDir
            copy "$downloadDir\Newtonsoft.Json.6.0.8\lib\net45\Newtonsoft.Json*.dll" $moduleDir
            copy "$downloadDir\Microsoft.Rest.ClientRuntime.Azure.Authentication.2.2.9-preview\lib\net45\Microsoft.Rest.ClientRuntime.Azure.Authentication*.dll" $moduleDir
            copy "$downloadDir\Microsoft.Azure.Management.Storsimple8000series.1.0.0\lib\net452\Microsoft.Azure.Management.Storsimple8000series*.dll" $moduleDir

            #Don't change the name of the Archive
            compress-Archive -Path "$moduleDir" -DestinationPath Microsoft.Azure.Management.StorSimple8000Series.zip

    ```

6. Убедитесь, что в `C:\scripts\StorSimpleSDKTools` создан ZIP-файл модуля службы автоматизации.

    ![verify-automation-module](./media/storsimple-8000-automation-azurerm-runbook/verify-automation-module.png)

7. При создании модуля службы автоматизации через Windows PowerShell предоставляются следующие данные. 

    ```
    -----------------------------------------
    Windows PowerShell
    Copyright (C) 2016 Microsoft Corporation. All rights reserved.

    PS C:\WINDOWS\system32> mkdir C:\scripts\StorSimpleSDKTools

        Directory: C:\scripts

    Mode                LastWriteTime         Length Name
    ----                -------------         ------ ----
    d-----       10/18/2017   8:43 AM                StorSimpleSDKTools


    PS C:\WINDOWS\system32> cd c:\scripts\StorSimpleSDKTools
    PS C:\scripts\StorSimpleSDKTools> wget https://dist.nuget.org/win-x86-commandline/latest/nuget.exe -Out C:\scripts\StorS
    impleSDKTools\nuget.exe
    PS C:\scripts\StorSimpleSDKTools> C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Azure.Management.Storsimple8
    000series

    -------------------------------------------
    CUT              CUT  
    -------------------------------------------
    Successfully installed 'Microsoft.Azure.Management.Storsimple8000series 1.0.0' to C:\scripts\StorSimpleSDKTools
    Executing nuget actions took 1.77 sec
    PS C:\scripts\StorSimpleSDKTools> C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.IdentityModel.Clients.Active
    Directory -Version 2.28.3

    -------------------------------------------
    CUT              CUT  
    -------------------------------------------
    Successfully installed 'Microsoft.IdentityModel.Clients.ActiveDirectory 2.28.3' to C:\scripts\StorSimpleSDKTools
    Executing nuget actions took 927.64 ms
    PS C:\scripts\StorSimpleSDKTools> C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Rest.ClientRuntime.Azure.Aut
    hentication -Version 2.2.9-preview

    -------------------------------------------
    CUT              CUT  
    -------------------------------------------
    Successfully installed 'Microsoft.Rest.ClientRuntime.Azure.Authentication 2.2.9-preview' to C:\scripts\StorSimpleSDKTool
    s
    Executing nuget actions took 717.48 ms
    PS C:\scripts\StorSimpleSDKTools> wget https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Mo
    nitor-Backups.ps1 -Out Monitor-Backups.ps1
    PS C:\scripts\StorSimpleSDKTools> # set path variables
    PS C:\scripts\StorSimpleSDKTools>             $downloadDir = "C:\scripts\StorSimpleSDKTools"
    PS C:\scripts\StorSimpleSDKTools>             $moduleDir = "$downloadDir\AutomationModule\Microsoft.Azure.Management.Sto
    rSimple8000Series"
    PS C:\scripts\StorSimpleSDKTools>
    PS C:\scripts\StorSimpleSDKTools>             #don't change the folder name "Microsoft.Azure.Management.StorSimple8000Se
    ries"
    PS C:\scripts\StorSimpleSDKTools>             mkdir "$moduleDir"

        Directory: C:\scripts\StorSimpleSDKTools\AutomationModule


    Mode                LastWriteTime         Length Name
    ----                -------------         ------ ----
    d-----       10/18/2017   8:48 AM                Microsoft.Azure.Management.StorSimple8000Series

    PS C:\scripts\StorSimpleSDKTools>             copy "$downloadDir\Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3\
    lib\net45\Microsoft.IdentityModel.Clients.ActiveDirectory*.dll" $moduleDir
    PS C:\scripts\StorSimpleSDKTools>             copy "$downloadDir\Microsoft.Rest.ClientRuntime.Azure.3.3.7\lib\net452\Mic
    rosoft.Rest.ClientRuntime.Azure*.dll" $moduleDir
    PS C:\scripts\StorSimpleSDKTools>             copy "$downloadDir\Microsoft.Rest.ClientRuntime.2.3.8\lib\net452\Microsoft
    .Rest.ClientRuntime*.dll" $moduleDir
    PS C:\scripts\StorSimpleSDKTools>             copy "$downloadDir\Newtonsoft.Json.6.0.8\lib\net45\Newtonsoft.Json*.dll" $
    moduleDir
    PS C:\scripts\StorSimpleSDKTools>             copy "$downloadDir\Microsoft.Rest.ClientRuntime.Azure.Authentication.2.2.9
    -preview\lib\net45\Microsoft.Rest.ClientRuntime.Azure.Authentication*.dll" $moduleDir
    PS C:\scripts\StorSimpleSDKTools>             copy "$downloadDir\Microsoft.Azure.Management.Storsimple8000series.1.0.0\l
    ib\net452\Microsoft.Azure.Management.Storsimple8000series*.dll" $moduleDir
    PS C:\scripts\StorSimpleSDKTools>
    PS C:\scripts\StorSimpleSDKTools>             #Don't change the name of the Archive
    PS C:\scripts\StorSimpleSDKTools>             compress-Archive -Path "$moduleDir" -DestinationPath Microsoft.Azure.Manag
    ement.StorSimple8000Series.zip
    PS C:\scripts\StorSimpleSDKTools>

    -------------------------------------------

    ```

### <a name="import-publish-and-run-automation-runbook"></a>Импорт, публикация и запуск модуля Runbook службы автоматизации

1. Создайте учетную запись запуска от имени Azure на портале Azure. Для этого перейдите к **Azure Marketplace > Все** и выполните поиск **службы автоматизации**. Выберите элемент **Учетные записи автоматизации**.

    ![search-automation](./media/storsimple-8000-automation-azurerm-runbook/automation1.png)

2. В колонке **Добавление учетной записи службы автоматизации**:

    1. Укажите **имя** учетной записи автоматизации.
    2. Выберите **подписку**, связанную со службой диспетчера устройств StorSimple.
    3. Создайте группу ресурсов или выберите имеющуюся.
    4. Выберите **Расположение** (если возможно, то же самое, что и для вашей службы).
    5. Оставьте выбранный по умолчанию вариант **Создать учетную запись запуска от имени**.
    5. При необходимости установите флажок **Закрепить на панели мониторинга**. Нажмите кнопку **Создать**.

        ![create-automation-account](./media/storsimple-8000-automation-azurerm-runbook/create-automation-account.png)

    После успешного создания учетной записи автоматизации вы получите уведомление. Дополнительные сведения о создании учетной записи автоматизации см. в статье [Обновление проверки подлинности учетных записей службы автоматизации с использованием учетных записей запуска от имени](https://docs.microsoft.com/azure/automation/automation-create-runas-account).

3. Чтобы убедится, что созданная учетная запись службы автоматизации имеем доступ к службе диспетчера устройств StorSimple, необходимо присвоить этой учетной записи соответствующие разрешения. Перейдите в раздел **Управление доступом** в службе диспетчера устройств StorSimple. Щелкните **+ Добавить** и укажите имя учетной записи службы автоматизации Azure. **Сохраните** параметры.

    ![add-permissions-automation-account](./media/storsimple-8000-automation-azurerm-runbook/goto-add-roles.png)

4. В созданной учетной записи перейдите к разделу **Общие ресурсы > Модули** и выберите **+ Add module** (+ Добавить модуль).

5. В колонке **Добавить модуль** перейдите к нужному сжатому модулю, выберите и откройте его. Последовательно выберите **ОК**.

    ![add-module](./media/storsimple-8000-automation-azurerm-runbook/add-module.png)

6. Последовательно выберите пункты **"Автоматизация процессов > Модули Runbook" и щелкните + Add a runbook (+ Добавить модуль Runbook)**. В колонке **Добавить модуль Runbook** щелкните **Импорт существующего модуля Runbook**. Укажите файл сценария Windows PowerShell для **файла Runbook**. Тип модуля Runbook выбирается автоматически. Укажите имя и необязательное описание модуля Runbook. Нажмите кнопку **Создать**.

    ![add-module](./media/storsimple-8000-automation-azurerm-runbook/import-runbook.png)

7. Runbook будет добавлен в список модулей Runbook. Выберите и щелкните этот модуль Runbook.

    ![click-new-runbook](./media/storsimple-8000-automation-azurerm-runbook/verify-runbook-created.png)

8. Измените модуль Runbook и щелкните **Тестовая область**. Укажите параметры, такие как имя службы диспетчера устройства StorSimple, а также имя устройства StorSimple и подписку. **Запустите** тест. После завершения выполнения создается отчет. Дополнительные сведения см. в разделе [Шаг 3. Тестирование модуля Runbook](../automation/automation-first-runbook-textual-powershell.md#step-3---test-the-runbook).

    ![test-runbook](./media/storsimple-8000-automation-azurerm-runbook/test-runbook.png)

9. Проверьте выходные данные модуля Runbook в области тестирования. Если условие выполнено, закройте область. Щелкните **Публикация** и при появлении запроса на подтверждение подтвердите и опубликуйте модуль Runbook.

    ![publish-runbook](./media/storsimple-8000-automation-azurerm-runbook/publish-runbook.png)


## <a name="next-steps"></a>Дополнительная информация

[Использование службы диспетчера устройств StorSimple для администрирования устройства StorSimple](storsimple-8000-manager-service-administration.md).