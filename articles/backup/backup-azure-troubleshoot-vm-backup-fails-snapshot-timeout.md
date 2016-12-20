---
title: "Ошибка архивации виртуальной машины Azure: &quot;Не удалось запросить состояние моментального снимка в агенте ВМ. Истекло время ожидания для подзадачи моментального снимка ВМ&quot; | Документация Майкрософт"
description: "Симптомы, причины и способы устранения проблем при резервном копировании виртуальной машины Azure, связанных с невозможностью запросить состояние моментальных снимков в агенте виртуальной машины. Ошибка &quot;Истекло время ожидания для подзадачи моментального снимка ВМ&quot;"
services: backup
documentationcenter: 
author: genlin
manager: cfreeman
editor: 
ms.assetid: 4b02ffa4-c48e-45f6-8363-73d536be4639
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2016
ms.author: jimpark; markgal;genli
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 87387e4f182214fa0c34a6a1358c6cc2648be906


---
# <a name="azure-vm-backup-fails-could-not-communicate-with-the-vm-agent-for-snapshot-status---snapshot-vm-sub-task-timed-out"></a>Ошибка резервного копирования виртуальной машины Azure: "Не удалось запросить состояние моментального снимка в агенте ВМ. Истекло время ожидания для подзадачи моментального снимка ВМ"
## <a name="summary"></a>Сводка
Когда виртуальная машина будет зарегистрирована в службе архивации Azure и для нее будет составлено расписание, служба архивации Azure в установленное время запустит задание резервного копирования. Она свяжется с модулем резервного копирования на виртуальной машине, который создаст снимок текущего состояния. При некоторых условиях создание моментального снимка невозможно, и это приводит к сбою резервного копирования. В этой статье описано, как устранять неполадки, связанные с ошибками резервного копирования виртуальных машин Azure при превышении времени ожидания моментального снимка.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="symptom"></a>Симптом
Резервное копирование виртуальной машины IaaS в Microsoft Azure завершается сбоем, и в сведениях об ошибке задания на [портале Azure](https://portal.azure.com/) отображается следующее сообщение об ошибке:

**Не удалось запросить состояние моментального снимка в агенте ВМ. Истекло время ожидания для подзадачи моментального снимка ВМ.**

## <a name="cause"></a>Причина:
Существует четыре основные причины этой ошибки:

* Виртуальная машина не подключена к Интернету.
* Устарел агент виртуальной машины Microsoft Azure, установленный на виртуальной машине (для виртуальных машин Linux).
* Не удалось обновить или загрузить расширение резервного копирования.
* Не удалось получить состояние моментальных снимков или создать моментальные снимки.

## <a name="cause-1-the-vm-does-not-have-internet-access"></a>Причина 1. Виртуальная машина не подключена к Интернету.
Согласно требованиям к развертыванию виртуальная машина не имеет доступа к Интернету или существуют ограничения на доступ к инфраструктуре Azure.

Для правильной работы расширения резервного копирования требуется возможность подключения к общедоступным IP-адресам Azure. Это связано с тем, что для управления моментальными снимками виртуальной машины это расширение отправляет команды к конечной точке хранилища Azure (URL-адрес HTTP). Если расширение не имеет доступа к общедоступному Интернету, резервное копирование завершится сбоем.

### <a name="solution"></a>Решение
В этом случае для устранения проблемы можно применить один из следующих методов:

* Добавьте диапазоны IP-адресов центра обработки данных Azure в список разрешений.
* Создание пути для прохождения трафика HTTP

### <a name="to-whitelist-the-azure-datacenter-ip-ranges"></a>Добавление диапазонов IP-адресов центра обработки данных Azure в список разрешений
1. Получите [список IP-адресов центра обработки данных Azure](https://www.microsoft.com/download/details.aspx?id=41653) для добавления в список разрешений.
2. Разблокируйте IP-адреса с помощью командлета New-NetRoute. Запустите этот командлет на виртуальной машине Azure в окне PowerShell с повышенными привилегиями (от имени администратора).
3. Добавьте правила в группу безопасности сети (если она настроена) для доступа к IP-адресам.

### <a name="to-create-a-path-for-http-traffic-to-flow"></a>Создание пути для прохождения трафика HTTP
1. При наличии каких-либо ограничений сети (например, группы безопасности сети) разверните прокси-сервер HTTP для перенаправления трафика.
2. Если вы используете группу безопасности сети, добавьте правила, чтобы разрешить доступ к Интернету с прокси-сервера HTTP.

Узнайте, как [настроить прокси-сервер HTTP для резервного копирования виртуальной машины](backup-azure-vms-prepare.md#using-an-http-proxy-for-vm-backups).

## <a name="cause-2-the-microsoft-azure-vm-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>Причина 2. Устарел агент виртуальной машины Microsoft Azure, установленный на виртуальной машине (для виртуальных машин Linux)
### <a name="solution"></a>Решение
Большая часть неполадок, связанных с агентом или расширением на виртуальных машинах Linux, вызваны проблемами с устаревшим агентом виртуальной машины. В общем случае для устранения таких проблем прежде всего сделайте следующее.

1. [Установите последнюю версию агента виртуальной машины Azure](https://github.com/Azure/WALinuxAgent).
2. Убедитесь, что на виртуальной машине запущен агент Azure. Для этого выполните команду ```ps -e```.
   
    Если нужный процесс не запущен, используйте следующие команды для его перезапуска.
   
    Для Ubuntu: ```service walinuxagent start```.
   
    Для других дистрибутивов: ```service waagent start.
   ```
3. [Configure the auto restart agent](https://github.com/Azure/WALinuxAgent/wiki/Known-Issues#mitigate_agent_crash).
4. Run a new test backup. If the failures persist, please collect logs from the following folders for further debugging.
   
    We require the following logs from the customer’s VM:
   
   * /var/lib/waagent/*.xml
   * /var/log/waagent.log
   * /var/log/azure/*

If we require verbose logging for waagent, follow these steps to enable this:

1. In the /etc/waagent.conf file, locate the following line:
   
    Enable verbose logging (y|n)
2. Change the **Logs.Verbose** value from n to y.
3. Save the change, and then restart waagent by following the previous steps in this section.

## Cause 3: The backup extension fails to update or load
If extensions cannot be loaded, then Backup fails because a snapshot cannot be taken.

### Solution
For Windows guests:

1. Verify that the iaasvmprovider service is enabled and has a startup type of automatic.
2. If this is not the configuration, enable the service to determine whether the next backup succeeds.

For Linux guests:

The latest version of VMSnapshot Linux (extension used by backup) is 1.0.91.0

If the backup extension still fails to update or load, you can force the VMSnapshot extension to be reloaded by uninstalling the extension. The next backup attempt will reload the extension.

### To uninstall the extension
1. Go to the [Azure portal](https://portal.azure.com/).
2. Locate the particular VM that has backup problems.
3. Click **Settings**.
4. Click **Extensions**.
5. Click **Vmsnapshot Extension**.
6. Click **uninstall**.

This will cause the extension to be reinstalled during the next backup.

## Cause 4: The snapshots status cannot be retrieved or the snapshots cannot be taken
VM backup relies on issuing snapshot command to underlying storage. The backup can fail because it has no access to storage or because of a delay in snapshot task execution.

### Solution
The following conditions can cause snapshot task failure:

| Cause | Solution |
| --- | --- |
| VMs that have Microsoft SQL Server Backup configured. By default, VM Backup runs a VSS Full backup on Windows VMs. On VMs that are running SQL Server-based servers and on which SQL Server Backup is configured, snapshot execution delays may occur. |Set following registry key if you are experiencing backup failures because of snapshot issues.<br><br>[HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\BCDRAGENT] "USEVSSCOPYBACKUP"="TRUE" |
| VM status is reported incorrectly because the VM is shut down in RDP. If you shut down the virtual machine in RDP, check the portal to determine whether that VM status is reflected correctly. |If it’s not, shut down the VM in the portal by using the ”Shutdown” option in the VM dashboard. |
| Many VMs from the same cloud service are configured to back up at the same time. |It’s a best practice to spread out the VMs from the same cloud service to have different backup schedules. |
| The VM is running at high CPU or memory usage. |If the VM is running at high CPU usage (more than 90 percent) or high memory usage, the snapshot task is queued and delayed and eventually times out. In this situation, try on-demand backup. |
| The VM cannot get host/fabric address from DHCP. |DHCP must be enabled inside the guest for IaaS VM Backup to work.  If the VM cannot get host/fabric address from DHCP response 245, then it cannot download ir run any extensions. If you need a static private IP, you should configure it through the platform. The DHCP option inside the VM should be left enabled. View more information about [Setting a Static Internal Private IP](../virtual-network/virtual-networks-reserved-private-ip.md). |




<!--HONumber=Nov16_HO3-->


