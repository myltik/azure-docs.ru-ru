---
title: Установка агента виртуальной машины Azure в автономном режиме | Документация Майкрософт
description: Узнайте, как установить агент виртуальной машины Azure в автономном режиме.
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: genli
ms.openlocfilehash: 77ce133232b63a590c6c7f9361d1ca456f484093
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/10/2018
---
# <a name="install-the-azure-virtual-machine-agent-in-offline-mode"></a>Установка агента виртуальной машины Azure в автономном режиме 

Агент виртуальной машины Azure обеспечивает полезные функции, такие как сброс пароля локального администратора и принудительная отправка сценария. В этой статье описывается, как установить агент виртуальной машины для автономной виртуальной машины Windows. 

## <a name="when-to-use-the-vm-agent-in-offline-mode"></a>Когда нужно использовать агент виртуальной машины в автономном режиме

Установите агент виртуальной машины в автономном режиме в таких случаях:

- На развернутой виртуальной машине Azure агент не установлен или он не работает.
- Вы забыли пароль администратора для виртуальной машины или не можете получить к ней доступ.

## <a name="how-to-install-the-vm-agent-in-offline-mode"></a>Как установить агент виртуальной машины в автономном режиме

Выполните шаги ниже, чтобы установить агент виртуальной машины в автономном режиме.

### <a name="step-1-attach-the-os-disk-of-the-vm-to-another-vm-as-a-data-disk"></a>Шаг 1. Подключение диска операционной системы виртуальной машины к другой виртуальной машине в качестве диска данных

1.  Удалите виртуальную машину. При удалении установите флажок для **сохранения дисков**.

2.  Подключите диск операционной системы как диск данных к другой виртуальной машине (известной как виртуальная машина для _устранения неполадок_). Дополнительные сведения см. в статье [Как подключить управляемый диск данных к виртуальной машине Windows на портале Azure](attach-managed-disk-portal.md).

3.  Подключитесь к виртуальной машине для устранения неполадок. Откройте **Computer management** (Управление компьютерами)  > **Disk management** (Управление дисками). Убедитесь, что диск операционной системы подключен и что разделам диска назначены буквы диска.

### <a name="step-2-modify-the-os-disk-to-install-the-azure-vm-agent"></a>Шаг 2. Изменение диска операционной системы для установки агента виртуальной машины Azure

1.  Установите подключение к удаленному рабочему столу для виртуальной машины для устранения неполадок.

2.  В подключенном диске операционной системы перейдите в папку \windows\system32\config. Скопируйте все файлы в этой папке как файл резервной копии. Это необходимо, если потребуется откат.

3.  Запустите **редактор реестра** (файл regedit.exe).

4.  Выберите раздел **HKEY_LOCAL_MACHINE**. Выберите в меню **Файл** > **Загрузка куста реестра**:

    ![Загрузка куста реестра](./media/install-vm-agent-offline/load-hive.png)

5.  В подключенном диске операционной системы перейдите в системную папку \windows\system32\config\SYSTEM. Для имени куста введите **BROKENSYSTEM**. В разделе **HKEY_LOCAL_MACHINE** появится новый куст реестра.

6.  В подключенном диске операционной системы перейдите в папку \windows\system32\config\SOFTWARE. Для имени программного обеспечения куста введите **BROKENSOFTWARE**.

7.  Если агент виртуальной машины не работает, создайте резервную копию текущей конфигурации.

    >[!NOTE]
    >Если на виртуальной машине не установлен агент, перейдите к шагу 8. 
      
    1. Переименуйте папку \windowsazure на \windowsazure.old.

    2. Экспортируйте следующие реестры:
        - HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet001\Services\WindowsAzureGuestAgent
        - HKEY_LOCAL_MACHINE\BROKENSYSTEM\\ControlSet001\Services\WindowsAzureTelemetryService
        - HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet001\Services\RdAgent

8.  Используйте имеющиеся файлы на виртуальной машине для устранения неполадок, как репозиторий для установки агента виртуальной машины. Затем сделайте следующее:

    1. Из виртуальной машины для устранения неполадок экспортируйте следующие подразделы в формате реестра (REG): 
        - HKEY_LOCAL_MACHINE  \SYSTEM\ControlSet001\Services\WindowsAzureGuestAgent
        - HKEY_LOCAL_MACHINE  \SYSTEM\ControlSet001\Services\WindowsAzureTelemetryService
        - HKEY_LOCAL_MACHINE  \SYSTEM\ControlSet001\Services\RdAgent

        ![Экспорт подразделов реестра](./media/install-vm-agent-offline/backup-reg.png)

    2. Измените файлы реестра. В каждом файле измените значение записи **SYSTEM** на **BROKENSYSTEM** (как показано на рисунке ниже) и сохраните файл.

        ![Изменение значений подраздела реестра](./media/install-vm-agent-offline/change-reg.png)

    3. Импортируйте файлы реестра в репозиторий, дважды щелкнув каждый файл реестра.

    4. Проверьте наличие следующих трех подразделов в кусте **BROKENSYSTEM**:
        - WindowsAzureGuestAgent
        - WindowsAzureTelemetryService
        - RdAgent

9.  Скопируйте папку агента виртуальной машины с диска C:\windowsazure\packages на &lt;подключенный диск операционной системы&gt;:\windowsazure\packages.

    ![Копирование файлов агента виртуальной машины на диск операционной системы](./media/install-vm-agent-offline/copy-package.png)
      
    >[!NOTE]
    >Не нужно копировать папку с **журналами**. Журналы будут созданы после запуска службы.

10.  Выберите **BROKENSYSTEM**. Выберите в меню **Файл** > **Выгрузить куст**.

11.  Выберите **BROKENSOFTWARE**. Выберите в меню **Файл** > **Выгрузить куст**.

12.  Отключите диск операционной системы, а затем повторно создайте виртуальную машину с помощью этого диска.

13.  Войдите на виртуальную машину. Обратите внимание, что агент RdAgent работает, а журналы создаются.

Если вы создали виртуальную машину с помощью классической модели развертывания, дополнительные шаги не требуются.


### <a name="use-the-provisionguestagent-property-for-vms-created-with-azure-resource-manager"></a>Использование свойства ProvisionGuestAgent для виртуальных машин, созданных с помощью Azure Resource Manager

При создании виртуальной машины с помощью модели развертывания Resource Manager используйте модуль Azure PowerShell для обновления свойства **ProvisionGuestAgent**. Это свойство сообщает Azure об установке агента виртуальной машины.

Чтобы задать свойство **ProvisionGuestAgent**, выполните следующие команды в Azure PowerShell:

   ```powershell
   $vm = Get-AzureVM –ServiceName <cloud service name> –Name <VM name>
   $vm.VM.ProvisionGuestAgent = $true
   Update-AzureVM –Name <VM name> –VM $vm.VM –ServiceName <cloud service name>
   ```

Затем выполните команду `Get-AzureVM` . Обратите внимание, что свойство **GuestAgentStatus** теперь заполняется такими данными:

   ```powershell
   Get-AzureVM –ServiceName <cloud service name> –Name <VM name>
   GuestAgentStatus:Microsoft.WindowsAzure.Commands.ServiceManagement.Model.PersistentVMModel.GuestAgentStatus
   ```

## <a name="next-steps"></a>Дополнительная информация

- [Обзор агента виртуальной машины Azure](../extensions/agent-windows.md)
- [Обзор расширений и компонентов виртуальной машины под управлением Windows](extensions-features.md)
