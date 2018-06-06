---
title: Установка службы Mobility Service (из виртуальных машин VMware или физических серверов в Azure) | Документация Майкрософт
description: Узнайте, как установить агент службы Mobility Service для защиты виртуальных машин VMware и физических серверов с помощью Azure Site Recovery.
services: site-recovery
author: AnoopVasudavan
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 03/05/2018
ms.author: anoopkv
ms.openlocfilehash: 3279dbc2eeecd3a3f0f36a47d8dd51ef4f3f503f
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/05/2018
ms.locfileid: "33942141"
---
# <a name="install-the-mobility-service"></a>Установка службы Mobility 

Служба Azure Site Recovery Mobility Service установлена на всех виртуальных машинах VMware и физических серверах, которые требуется реплицировать в Azure. Служба фиксирует операции записи данных, выполняемые на компьютере, и передает их на сервер обработки. Разверните службу Mobility Service на каждом компьютере (виртуальная машина или физический сервер VMware), который требуется реплицировать в Azure. Службу Mobility Service можно развернуть на виртуальных машинах VMware, которые необходимо защитить, следующими способами:


* [Установка с помощью инструментов развертывания программного обеспечения, таких как System Center Configuration Manager](vmware-azure-mobility-install-configuration-mgr.md)
* [Установка с помощью службы автоматизации Azure и настройки требуемого состояния (Automation DSC)](vmware-azure-mobility-deploy-automation-dsc.md)
* [Установка вручную из пользовательского интерфейса](vmware-azure-install-mobility-service.md#install-mobility-service-manually-by-using-the-gui)
* [Установка вручную из командной строки](vmware-azure-install-mobility-service.md#install-mobility-service-manually-at-a-command-prompt)
* [Установка с помощью принудительной установки Site Recovery](vmware-azure-install-mobility-service.md#install-mobility-service-by-push-installation-from-azure-site-recovery)


>[!IMPORTANT]
> Начиная с версии 9.7.0.0 установщик службы Mobility Service также устанавливает последнюю доступную версию [агента виртуальных машин Azure](../virtual-machines/extensions/features-windows.md#azure-vm-agent) на виртуальных машинах Windows. Если при отработке отказа компьютер переходит в Azure, выполняется предварительное требование агента, связанное с использованием любого расширения виртуальной машины.

## <a name="prerequisites"></a>предварительным требованиям
Перед установкой службы Mobility Service вручную на сервере выполните следующие обязательные действия:
1. Войдите на сервер конфигурации и откройте окно командной строки с правами администратора.
2. Откройте каталог bin и создайте файл с парольной фразой.

    ```
    cd %ProgramData%\ASR\home\svsystems\bin
    genpassphrase.exe -v > MobSvc.passphrase
    ```
3. Сохраните файл парольной фразы в безопасном месте. Используйте файл во время установки службы Mobility Service.
4. Установщики службы Mobility Service для всех поддерживаемых операционных систем находятся в папке %ProgramData%\ASR\home\svsystems\pushinstallsvc\repository.

### <a name="mobility-service-installer-to-operating-system-mapping"></a>Сопоставление установщика Mobility Service с операционной системой

| Имя шаблона файла установщика| Операционная система |
|---|--|
|Microsoft-ASR\_UA\*Windows\*release.exe | Windows Server 2008 R2 с пакетом обновления 1 (64-разрядная версия) </br> Windows Server 2012 (64-разрядная версия) </br> Windows Server 2012 R2 (64-разрядная версия) </br> Windows Server 2016 (64-разрядная версия) |
|Microsoft-ASR\_UA\*RHEL6-64*release.tar.gz| Red Hat Enterprise Linux (RHEL) 6.4, 6.5, 6.6, 6.7, 6.8, 6.9 (только 64-разрядная версия) </br> CentOS 6.4, 6.5, 6.6, 6.7, 6.8, 6.9 (только 64-разрядная версия) |
|Microsoft-ASR\_UA\*RHEL7-64\*release.tar.gz | Red Hat Enterprise Linux (RHEL) 7.1, 7.2, 7.3 (только 64-разрядная версия) </br> CentOS 7.0, 7.1, 7.2, 7.3 (только 64-разрядная версия) |
|Microsoft-ASR\_UA\*SLES11-SP3-64\*release.tar.gz| SUSE Linux Enterprise Server 11 SP3 (только 64-разрядная версия)|
|Microsoft-ASR\_UA\*SLES11-SP4-64\*release.tar.gz| SUSE Linux Enterprise Server 11 SP4 (только 64-разрядная версия)|
|Microsoft-ASR\_UA\*OL6-64\*release.tar.gz | Oracle Enterprise Linux 6.4, 6.5 (только 64-разрядная версия)|
|Microsoft-ASR\_UA\*UBUNTU-14.04-64\*release.tar.gz | Ubuntu Linux 14.04 (только 64-разрядная версия)|
|Microsoft-ASR\_UA\*UBUNTU-16.04-64\*release.tar.gz | Сервер Ubuntu Linux 16.04 LTS (только 64-разрядная версия)|
|Microsoft-ASR_UA\*DEBIAN7-64\*release.tar.gz | Debian 7 (только 64-разрядная версия)|
|Microsoft-ASR_UA\*DEBIAN8-64\*release.tar.gz | Debian 8 (только 64-разрядная версия)|


## <a name="install-mobility-service-manually-by-using-the-gui"></a>Установка службы Mobility Service вручную с помощью графического пользовательского интерфейса

>[!IMPORTANT]
> Если вы используете сервер конфигурации для репликации виртуальных машин Azure IaaS из одной подписки (региона) Azure в другую, примените метод установки с помощью командной строки.

[!INCLUDE [site-recovery-install-mob-svc-gui](../../includes/site-recovery-install-mob-svc-gui.md)]

## <a name="install-mobility-service-manually-at-a-command-prompt"></a>Установка Mobility Service в командной строке вручную

### <a name="command-line-installation-on-a-windows-computer"></a>Установка из командной строки на компьютере с Windows
[!INCLUDE [site-recovery-install-mob-svc-win-cmd](../../includes/site-recovery-install-mob-svc-win-cmd.md)]

### <a name="command-line-installation-on-a-linux-computer"></a>Установка из командной строки на компьютере Linux
[!INCLUDE [site-recovery-install-mob-svc-lin-cmd](../../includes/site-recovery-install-mob-svc-lin-cmd.md)]


## <a name="install-mobility-service-by-push-installation-from-azure-site-recovery"></a>Принудительная установка Mobility Service из Azure Site Recovery
Принудительную установку службы Mobility Service можно выполнить с помощью Site Recovery. Все целевые компьютеры должны соответствовать следующим требованиям.

[!INCLUDE [site-recovery-prepare-push-install-mob-svc-win](../../includes/site-recovery-prepare-push-install-mob-svc-win.md)]

[!INCLUDE [site-recovery-prepare-push-install-mob-svc-lin](../../includes/site-recovery-prepare-push-install-mob-svc-lin.md)]


> [!NOTE]
После установки службы Mobility Service на портале Azure выберите **+ Репликация**, чтобы приступить к защите виртуальных машин.

## <a name="update-mobility-service"></a>Обновляет службу Mobility Service.

> [!WARNING]
> Прежде чем начать обновление службы Mobility Service на защищенных серверах, убедитесь, что сервер конфигурации, серверы обработки масштабирования и все главные целевые серверы, которые являются частью развертывания, обновлены.

1. На портале Azure перейдите к представлению *имя вашего хранилища* > **Реплицированные элементы**.
2. Если сервер конфигурации уже обновлен до последней версии, отобразится уведомление: "Доступно обновление для агента репликации Site Recovery. Щелкните, чтобы установить его."

     ![Окно "Реплицированные элементы"](.\media\vmware-azure-install-mobility-service\replicated-item-notif.png)
3. Выберите уведомление, чтобы открыть страницу выбора виртуальной машины.
4. Выберите виртуальные машины, на которых нужно обновить службу Mobility Service, и нажмите кнопку **ОК**.

     ![Список виртуальных машин "Реплицированные элементы"](.\media\vmware-azure-install-mobility-service\update-okpng.png)

Для каждой выбранной виртуальной машины будет запущено задание обновления службы Mobility Service.

> [!NOTE]
> [Узнайте больше](vmware-azure-manage-configuration-server.md) о том, как обновить пароль учетной записи, используемой для установки службы Mobility Service.

## <a name="uninstall-mobility-service-on-a-windows-server-computer"></a>Удаление Mobility Service с компьютера Windows Server
Воспользуйтесь одним из следующих методов для удаления службы Mobility Service на компьютере Windows Server.

### <a name="uninstall-by-using-the-gui"></a>Удаление с помощью графического пользовательского интерфейса
1. На панели управления выберите **Программы**.
2. Выберите **Microsoft Azure Site Recovery Mobility Service / главный целевой сервер** и нажмите кнопку **Удалить**.

### <a name="uninstall-at-a-command-prompt"></a>Удаление из командной строки
1. Откройте окно командной строки с правами администратора.
2. Выполните следующую команду, чтобы удалить службу Mobility Service.

    ```
    MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
    ```

## <a name="uninstall-mobility-service-on-a-linux-computer"></a>Удаление службы Mobility на компьютере Linux
1. На сервере Linux войдите как **привилегированный** пользователь.
2. В разделе "Терминал" перейдите к /user/local/ASR.
3. Выполните следующую команду, чтобы удалить службу Mobility Service.

    ```
    uninstall.sh -Y
    ```
