---
title: Автоматизация установки Mobility Service для Azure Site Recovery с помощью System Center Configuration Manager | Документация Майкрософт
description: Эта статья поможет вам автоматизировать установку Mobility Service с помощью System Center Configuration Manager.
services: site-recovery
author: AnoopVasudavan
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 03/05/2018
ms.author: anoopkv
ms.openlocfilehash: 50328fc3b594a9162ffb9f82f699f43f9106640f
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/10/2018
---
# <a name="automate-mobility-service-installation-with-system-center-configuration-manager"></a>Автоматизация установки Mobility Service с помощью System Center Configuration Manager

Служба Mobility Service установлена на всех виртуальных машинах VMware и физических серверах, которые требуется реплицировать в Azure с помощью [Azure Site Recovery](site-recovery-overview.md).

В этой статье приведен пример того, как можно применить System Center Configuration Manager для развертывания Azure Site Recovery Mobility Service на виртуальной машине VMware. Такие средства развертывания программного обеспечения, как Configuration Manager, предоставляют следующие преимущества:

* планирование свежих обновлений на выделенный период обслуживания для обновления программного обеспечения;
* масштабирование развертывания вплоть до нескольких сотен серверов.

В этой статье демонстрируется развертывание на примере System Center Configuration Manager 2012 R2. В этой статье предполагается, что вы используете Mobility Service **9.9.4510.1** или более поздней версии.

Кроме того, установку службы Mobility Service можно автоматизировать с помощью [Azure Automation DSC](vmware-azure-mobility-deploy-automation-dsc.md).

## <a name="prerequisites"></a>предварительным требованиям

1. Установленное в вашей среде средство развертывания программного обеспечения, например Configuration Manager.
2. Необходимо создать две [коллекции устройств](https://technet.microsoft.com/library/gg682169.aspx) — одну для всех **серверов Windows** и другую для всех **серверов Linux**, для защиты которых вы будете применять Site Recovery.
3. Сервер конфигурации, зарегистрированный в хранилище служб восстановления.
4. Защищенная сетевая общая папка (общая папка SMB), к которому есть доступ с компьютера Configuration Manager.

## <a name="deploy-on-windows-machines"></a>Развертывание на виртуальных машинах Windows
> [!NOTE]
> В этой статье предполагается, что сервер конфигурации использует IP-адрес 192.168.3.121, а защищенная общая сетевая папка имеет адрес \\\ContosoSecureFS\MobilityServiceInstallers.

### <a name="prepare-for-deployment"></a>Подготовка к развертыванию
1. Создайте в общей сетевой папке каталог с именем **MobSvcWindows**.
2. Войдите на сервер конфигурации и откройте административную командную строку.
3. Выполните следующие команды, чтобы создать файл парольной фразы.

    `cd %ProgramData%\ASR\home\svsystems\bin`

    `genpassphrase.exe -v > MobSvc.passphrase`
4. Скопируйте файл **MobSvc.passphrase** в каталог **MobSvcWindows**, расположенный в общей сетевой папке.
5. Перейдите к репозиторию установщика на сервере конфигурации, выполнив такую команду:

   `cd %ProgramData%\ASR\home\svsystems\pushinstallsvc\repository`

6. Скопируйте файл **Microsoft-ASR\_UA\_*version*\_Windows\_GA\_*date*\_Release.exe** в папку **MobSvcWindows**, расположенную в сетевой папке.
7. Скопируйте приведенный ниже код в файл и сохраните этот файл с именем **install.bat** в каталоге **MobSvcWindows**.

   > [!NOTE]
   > Замените в этом скрипте заполнители [CSIP] реальными значениями IP-адреса сервера конфигурации.

```DOS
Time /t >> C:\Temp\logfile.log
REM ==================================================
REM ==== Clean up the folders ========================
RMDIR /S /q %temp%\MobSvc
MKDIR %Temp%\MobSvc
MKDIR C:\Temp
REM ==================================================

REM ==== Copy new files ==============================
COPY M*.* %Temp%\MobSvc
CD %Temp%\MobSvc
REN Micro*.exe MobSvcInstaller.exe
REM ==================================================

REM ==== Extract the installer =======================
MobSvcInstaller.exe /q /x:%Temp%\MobSvc\Extracted
REM ==== Wait 10s for extraction to complete =========
TIMEOUT /t 10
REM =================================================

REM ==== Perform installation =======================
REM =================================================

CD %Temp%\MobSvc\Extracted
whoami >> C:\Temp\logfile.log
SET PRODKEY=HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Uninstall
REG QUERY %PRODKEY%\{275197FC-14FD-4560-A5EB-38217F80CBD1}
IF NOT %ERRORLEVEL% EQU 0 (
    echo "Product is not installed. Goto INSTALL." >> C:\Temp\logfile.log
    GOTO :INSTALL
) ELSE (
    echo "Product is installed." >> C:\Temp\logfile.log

    echo "Checking for Post-install action status." >> C:\Temp\logfile.log
    GOTO :POSTINSTALLCHECK
)

:POSTINSTALLCHECK
    REG QUERY "HKLM\SOFTWARE\Wow6432Node\InMage Systems\Installed Products\5" /v "PostInstallActions" | Find "Succeeded"
    If %ERRORLEVEL% EQU 0 (
        echo "Post-install actions succeeded. Checking for Configuration status." >> C:\Temp\logfile.log
        GOTO :CONFIGURATIONCHECK
    ) ELSE (
        echo "Post-install actions didn't succeed. Goto INSTALL." >> C:\Temp\logfile.log
        GOTO :INSTALL
    )

:CONFIGURATIONCHECK
    REG QUERY "HKLM\SOFTWARE\Wow6432Node\InMage Systems\Installed Products\5" /v "AgentConfigurationStatus" | Find "Succeeded"
    If %ERRORLEVEL% EQU 0 (
        echo "Configuration has succeeded. Goto UPGRADE." >> C:\Temp\logfile.log
        GOTO :UPGRADE
    ) ELSE (
        echo "Configuration didn't succeed. Goto CONFIGURE." >> C:\Temp\logfile.log
        GOTO :CONFIGURE
    )


:INSTALL
    echo "Perform installation." >> C:\Temp\logfile.log
    UnifiedAgent.exe /Role MS /InstallLocation "C:\Program Files (x86)\Microsoft Azure Site Recovery" /Platform "VmWare" /Silent
    IF %ERRORLEVEL% EQU 0 (
        echo "Installation has succeeded." >> C:\Temp\logfile.log
        (GOTO :CONFIGURE)
    ) ELSE (
        echo "Installation has failed." >> C:\Temp\logfile.log
        GOTO :ENDSCRIPT
    )

:CONFIGURE
    echo "Perform configuration." >> C:\Temp\logfile.log
    cd "C:\Program Files (x86)\Microsoft Azure Site Recovery\agent"
    UnifiedAgentConfigurator.exe  /CSEndPoint "[CSIP]" /PassphraseFilePath %Temp%\MobSvc\MobSvc.passphrase
    IF %ERRORLEVEL% EQU 0 (
        echo "Configuration has succeeded." >> C:\Temp\logfile.log
    ) ELSE (
        echo "Configuration has failed." >> C:\Temp\logfile.log
    )
    GOTO :ENDSCRIPT

:UPGRADE
    echo "Perform upgrade." >> C:\Temp\logfile.log
    UnifiedAgent.exe /Platform "VmWare" /Silent
    IF %ERRORLEVEL% EQU 0 (
        echo "Upgrade has succeeded." >> C:\Temp\logfile.log
    ) ELSE (
        echo "Upgrade has failed." >> C:\Temp\logfile.log
    )
    GOTO :ENDSCRIPT

:ENDSCRIPT
    echo "End of script." >> C:\Temp\logfile.log


```

### <a name="create-a-package"></a>Создание пакета

1. Войдите в консоль Configuration Manager.
2. Перейдите к разделу **Библиотека программного обеспечения** > **Управление приложениями** > **Пакеты**.
3. Щелкните **Пакеты** правой кнопкой мыши и выберите **Создать пакет**.
4. Введите значения для параметров Имя, Описание, Изготовитель, Язык и Версия.
5. Установите флажок **Этот пакет содержит исходные файлы**.
6. Щелкните **Обзор** и выберите общую сетевую папку, в которой хранится установщик (\\\ContosoSecureFS\MobilityServiceInstaller\MobSvcWindows).

  ![Снимок экрана с мастером создания пакета и программы](./media/vmware-azure-mobility-install-configuration-mgr/create_sccm_package.png)

7. На странице **Выберите тип создаваемой программы** выберите **Стандартная программа** и щелкните **Далее**.

  ![Снимок экрана с мастером создания пакета и программы](./media/vmware-azure-mobility-install-configuration-mgr/sccm-standard-program.png)

8. На странице **Укажите сведения об этой стандартной программе** предоставьте следующие данные и щелкните **Далее**. (Для остальных параметров можно оставить значения по умолчанию.)

  | **Имя параметра** | **Значение** |
  |--|--|
  | ИМЯ | Установка Microsoft Azure Mobility Service (Windows) |
  | Команда | install.bat |
  | Программа может запускаться | Независимо от входа пользователя в систему |

  ![Снимок экрана с мастером создания пакета и программы](./media/vmware-azure-mobility-install-configuration-mgr/sccm-program-properties.png)

9. На следующей странице выберите целевые операционные системы. Mobility Service можно устанавливать только на Windows Server 2012 R2, Windows Server 2012 и Windows Server 2008 R2.

  ![Снимок экрана с мастером создания пакета и программы](./media/vmware-azure-mobility-install-configuration-mgr/sccm-program-properties-page2.png)

10. Дважды щелкните **Далее**, чтобы завершить работу мастера.


> [!NOTE]
> Скрипт поддерживает как новые установки агентов Mobility Service, так и обновление уже установленных агентов.

### <a name="deploy-the-package"></a>Развертывание пакета
1. В консоли Configuration Manager щелкните пакет правой кнопкой мыши и выберите команду **Распространить содержимое**.
  ![Снимок экрана с консолью Configuration Manager](./media/vmware-azure-mobility-install-configuration-mgr/sccm_distribute.png)
2. Выберите **[Точки распространения](https://technet.microsoft.com/library/gg712321.aspx#BKMK_PlanForDistributionPoints)**, на которые следует скопировать эти пакеты.
3. Завершите работу мастера. Теперь пакет будет реплицироваться на выбранные точки распространения.
4. Когда распространение пакета завершится, щелкните пакет правой кнопкой мыши и выберите **Развернуть**.
  ![Снимок экрана с консолью Configuration Manager](./media/vmware-azure-mobility-install-configuration-mgr/sccm_deploy.png)
5. Выберите коллекцию устройств Windows Server, созданную на этапе подготовки предварительных требований, в качестве целевой коллекции для развертывания.

  ![Снимок экрана с мастером развертывания программного обеспечения](./media/vmware-azure-mobility-install-configuration-mgr/sccm-select-target-collection.png)

6. На странице **Укажите места распространения содержимого** выберите нужные **Точки распространения**.
7. На странице **Укажите параметры управления процессом развертывания этого программного обеспечения** убедитесь, что выбрана цель **Обязательно**.

  ![Снимок экрана с мастером развертывания программного обеспечения](./media/vmware-azure-mobility-install-configuration-mgr/sccm-deploy-select-purpose.png)

8. Настройте расписание в разделе **Укажите расписание этого развертывания**. Дополнительные сведения см. в статье [Развертывание пакетов и программ в Configuration Manager](https://technet.microsoft.com/library/gg682178.aspx).
9. Настройте свойства на странице **Точки распространения** в соответствии с потребностями вашего центра обработки данных. Теперь завершите работу мастера.

> [!TIP]
> Чтобы избежать лишних перезагрузок, запланируйте установку пакета на период ежемесячного обслуживания или обновления программного обеспечения.

Ход развертывания можно отслеживать с помощью консоли Configuration Manager. Последовательно выберите пункты **Мониторинг** > **Развертывания** > *[имя пакета]*.

  ![Снимок экрана с настройкой отслеживания расписания в Configuration Manager](./media/vmware-azure-mobility-install-configuration-mgr/report.PNG)

## <a name="deploy-on-linux-machines"></a>Развертывание на виртуальных машинах Linux
> [!NOTE]
> В этой статье предполагается, что сервер конфигурации использует IP-адрес 192.168.3.121, а защищенная общая сетевая папка имеет адрес \\\ContosoSecureFS\MobilityServiceInstallers.

### <a name="prepare-for-deployment"></a>Подготовка к развертыванию
1. Создайте в общей сетевой папке каталог с именем **MobSvcLinux**.
2. Войдите на сервер конфигурации и откройте административную командную строку.
3. Выполните следующие команды, чтобы создать файл парольной фразы.

    `cd %ProgramData%\ASR\home\svsystems\bin`

    `genpassphrase.exe -v > MobSvc.passphrase`
4. Скопируйте файл **MobSvc.passphrase** в каталог **MobSvcLinux**, расположенный в общей сетевой папке.
5. Перейдите к репозиторию установщика на сервере конфигурации, выполнив такую команду.

   `cd %ProgramData%\ASR\home\svsystems\pushinstallsvc\repository`

6. Скопируйте следующие файлы в каталог **MobSvcLinux**, расположенный в общей сетевой папке.
   * Microsoft-ASR\_UA\*RHEL6-64*release.tar.gz
   * Microsoft-ASR\_UA\*RHEL7-64\*release.tar.gz
   * Microsoft-ASR\_UA\*SLES11-SP3-64\*release.tar.gz
   * Microsoft-ASR\_UA\*SLES11-SP4-64\*release.tar.gz
   * Microsoft-ASR\_UA\*OL6-64\*release.tar.gz
   * Microsoft-ASR\_UA\*UBUNTU-14.04-64\*release.tar.gz


7. Скопируйте приведенный ниже код в файл и сохраните этот файл с именем **install_linux.sh** в каталоге **MobSvcLinux**.
   > [!NOTE]
   > Замените в этом скрипте заполнители [CSIP] реальными значениями IP-адреса сервера конфигурации.

```Bash
#!/usr/bin/env bash

rm -rf /tmp/MobSvc
mkdir -p /tmp/MobSvc
INSTALL_DIR='/usr/local/ASR'
VX_VERSION_FILE='/usr/local/.vx_version'

echo "=============================" >> /tmp/MobSvc/sccm.log
echo `date` >> /tmp/MobSvc/sccm.log
echo "=============================" >> /tmp/MobSvc/sccm.log

if [ -f /etc/oracle-release ] && [ -f /etc/redhat-release ]; then
    if grep -q 'Oracle Linux Server release 6.*' /etc/oracle-release; then
        if uname -a | grep -q x86_64; then
            OS="OL6-64"
            echo $OS >> /tmp/MobSvc/sccm.log
            cp *OL6*.tar.gz /tmp/MobSvc
        fi
    fi
elif [ -f /etc/redhat-release ]; then
    if grep -q 'Red Hat Enterprise Linux Server release 6.* (Santiago)' /etc/redhat-release || \
        grep -q 'CentOS Linux release 6.* (Final)' /etc/redhat-release || \
        grep -q 'CentOS release 6.* (Final)' /etc/redhat-release; then
        if uname -a | grep -q x86_64; then
            OS="RHEL6-64"
            echo $OS >> /tmp/MobSvc/sccm.log
            cp *RHEL6*.tar.gz /tmp/MobSvc
        fi
    elif grep -q 'Red Hat Enterprise Linux Server release 7.* (Maipo)' /etc/redhat-release || \
        grep -q 'CentOS Linux release 7.* (Core)' /etc/redhat-release; then
        if uname -a | grep -q x86_64; then
            OS="RHEL7-64"
            echo $OS >> /tmp/MobSvc/sccm.log
            cp *RHEL7*.tar.gz /tmp/MobSvc
                fi
    fi
elif [ -f /etc/SuSE-release ] && grep -q 'VERSION = 11' /etc/SuSE-release; then
    if grep -q "SUSE Linux Enterprise Server 11" /etc/SuSE-release && grep -q 'PATCHLEVEL = 3' /etc/SuSE-release; then
        if uname -a | grep -q x86_64; then
            OS="SLES11-SP3-64"
            echo $OS >> /tmp/MobSvc/sccm.log
            cp *SLES11-SP3*.tar.gz /tmp/MobSvc
        fi
    elif grep -q "SUSE Linux Enterprise Server 11" /etc/SuSE-release && grep -q 'PATCHLEVEL = 4' /etc/SuSE-release; then
        if uname -a | grep -q x86_64; then
            OS="SLES11-SP4-64"
            echo $OS >> /tmp/MobSvc/sccm.log
            cp *SLES11-SP4*.tar.gz /tmp/MobSvc
        fi
    fi
elif [ -f /etc/lsb-release ] ; then
    if grep -q 'DISTRIB_RELEASE=14.04' /etc/lsb-release ; then
       if uname -a | grep -q x86_64; then
           OS="UBUNTU-14.04-64"
           echo $OS >> /tmp/MobSvc/sccm.log
           cp *UBUNTU-14*.tar.gz /tmp/MobSvc
       fi
    fi
else
    exit 1
fi

if [ -z "$OS" ]; then
    exit 1
fi

Install()
{
    echo "Perform Installation." >> /tmp/MobSvc/sccm.log
    ./install -q -d ${INSTALL_DIR} -r MS -v VmWare
    RET_VAL=$?
    echo "Installation Returncode: $RET_VAL" >> /tmp/MobSvc/sccm.log
    if [ $RET_VAL -eq 0 ]; then
        echo "Installation has succeeded. Proceed to configuration." >> /tmp/MobSvc/sccm.log
        Configure
    else
        echo "Installation has failed." >> /tmp/MobSvc/sccm.log
        exit $RET_VAL
    fi
}

Configure()
{
    echo "Perform configuration." >> /tmp/MobSvc/sccm.log
    ${INSTALL_DIR}/Vx/bin/UnifiedAgentConfigurator.sh -i [CSIP] -P MobSvc.passphrase
    RET_VAL=$?
    echo "Configuration Returncode: $RET_VAL" >> /tmp/MobSvc/sccm.log
    if [ $RET_VAL -eq 0 ]; then
        echo "Configuration has succeeded." >> /tmp/MobSvc/sccm.log
    else
        echo "Configuration has failed." >> /tmp/MobSvc/sccm.log
        exit $RET_VAL
    fi
}

Upgrade()
{
    echo "Perform Upgrade." >> /tmp/MobSvc/sccm.log
    ./install -q -v VmWare
    RET_VAL=$?
    echo "Upgrade Returncode: $RET_VAL" >> /tmp/MobSvc/sccm.log
    if [ $RET_VAL -eq 0 ]; then
        echo "Upgrade has succeeded." >> /tmp/MobSvc/sccm.log
    else
        echo "Upgrade has failed." >> /tmp/MobSvc/sccm.log
        exit $RET_VAL
    fi
}

cp MobSvc.passphrase /tmp/MobSvc
cd /tmp/MobSvc

tar -zxvf *.tar.gz

if [ -e ${VX_VERSION_FILE} ]; then
    echo "${VX_VERSION_FILE} exists. Checking for configuration status." >> /tmp/MobSvc/sccm.log
    agent_configuration=$(grep ^AGENT_CONFIGURATION_STATUS "${VX_VERSION_FILE}" | cut -d"=" -f2 | tr -d " ")
    echo "agent_configuration=$agent_configuration" >> /tmp/MobSvc/sccm.log
     if [ "$agent_configuration" == "Succeeded" ]; then
        echo "Agent is already configured. Proceed to Upgrade." >> /tmp/MobSvc/sccm.log
        Upgrade
    else
        echo "Agent is not configured. Proceed to Configure." >> /tmp/MobSvc/sccm.log
        Configure
    fi
else
    Install
fi

cd /tmp

```

### <a name="create-a-package"></a>Создание пакета

1. Войдите в консоль Configuration Manager.
2. Перейдите к разделу **Библиотека программного обеспечения** > **Управление приложениями** > **Пакеты**.
3. Щелкните **Пакеты** правой кнопкой мыши и выберите **Создать пакет**.
4. Введите значения для параметров Имя, Описание, Изготовитель, Язык и Версия.
5. Установите флажок **Этот пакет содержит исходные файлы**.
6. Щелкните **Обзор** и выберите общую сетевую папку, в которой хранится установщик (\\\ContosoSecureFS\MobilityServiceInstaller\MobSvcLinux).

  ![Снимок экрана с мастером создания пакета и программы](./media/vmware-azure-mobility-install-configuration-mgr/create_sccm_package-linux.png)

7. На странице **Выберите тип создаваемой программы** выберите **Стандартная программа** и щелкните **Далее**.

  ![Снимок экрана с мастером создания пакета и программы](./media/vmware-azure-mobility-install-configuration-mgr/sccm-standard-program.png)

8. На странице **Укажите сведения об этой стандартной программе** предоставьте следующие данные и щелкните **Далее**. (Для остальных параметров можно оставить значения по умолчанию.)

    | **Имя параметра** | **Значение** |
  |--|--|
  | ИМЯ | Установка Microsoft Azure Mobility Service (Linux) |
  | Команда | ./install_linux.sh |
  | Программа может запускаться | Независимо от входа пользователя в систему |

  ![Снимок экрана с мастером создания пакета и программы](./media/vmware-azure-mobility-install-configuration-mgr/sccm-program-properties-linux.png)

9. На следующей странице выберите **Эта программа может запускаться на любой платформе**.
  ![Снимок экрана с мастером создания пакета и программы](./media/vmware-azure-mobility-install-configuration-mgr/sccm-program-properties-page2-linux.png)

10. Дважды щелкните **Далее**, чтобы завершить работу мастера.

> [!NOTE]
> Скрипт поддерживает как новые установки агентов Mobility Service, так и обновление уже установленных агентов.

### <a name="deploy-the-package"></a>Развертывание пакета
1. В консоли Configuration Manager щелкните пакет правой кнопкой мыши и выберите команду **Распространить содержимое**.
  ![Снимок экрана с консолью Configuration Manager](./media/vmware-azure-mobility-install-configuration-mgr/sccm_distribute.png)
2. Выберите **[Точки распространения](https://technet.microsoft.com/library/gg712321.aspx#BKMK_PlanForDistributionPoints)**, на которые следует скопировать эти пакеты.
3. Завершите работу мастера. Теперь пакет будет реплицироваться на выбранные точки распространения.
4. Когда распространение пакета завершится, щелкните пакет правой кнопкой мыши и выберите **Развернуть**.
  ![Снимок экрана с консолью Configuration Manager](./media/vmware-azure-mobility-install-configuration-mgr/sccm_deploy.png)
5. Выберите коллекцию устройств Linux Server, созданную на этапе подготовки предварительных требований, в качестве целевой коллекции для развертывания.

  ![Снимок экрана с мастером развертывания программного обеспечения](./media/vmware-azure-mobility-install-configuration-mgr/sccm-select-target-collection-linux.png)

6. На странице **Укажите места распространения содержимого** выберите нужные **Точки распространения**.
7. На странице **Укажите параметры управления процессом развертывания этого программного обеспечения** убедитесь, что выбрана цель **Обязательно**.

  ![Снимок экрана с мастером развертывания программного обеспечения](./media/vmware-azure-mobility-install-configuration-mgr/sccm-deploy-select-purpose.png)

8. Настройте расписание в разделе **Укажите расписание этого развертывания**. Дополнительные сведения см. в статье [Развертывание пакетов и программ в Configuration Manager](https://technet.microsoft.com/library/gg682178.aspx).
9. Настройте свойства на странице **Точки распространения** в соответствии с потребностями вашего центра обработки данных. Теперь завершите работу мастера.

Mobility Service устанавливается в коллекцию устройств Linux Server согласно настроенному расписанию.


## <a name="uninstall-the-mobility-service"></a>Удаление службы Mobility Service
Вы можете создать пакеты Configuration Manager для удаления службы Mobility Service. Для этого выполните следующий скрипт.

```
Time /t >> C:\logfile.log
REM ==================================================
REM ==== Check if Mob Svc is already installed =======
REM ==== If not installed no operation required ========
REM ==== Else run uninstall command =====================
REM ==== {275197FC-14FD-4560-A5EB-38217F80CBD1} is ====
REM ==== guid for Mob Svc Installer ====================
whoami >> C:\logfile.log
NET START | FIND "InMage Scout Application Service"
IF  %ERRORLEVEL% EQU 1 (GOTO :INSTALL) ELSE GOTO :UNINSTALL
:NOOPERATION
                echo "No Operation Required." >> c:\logfile.log
                GOTO :ENDSCRIPT
:UNINSTALL
                echo "Uninstall" >> C:\logfile.log
                MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
:ENDSCRIPT

```

## <a name="next-steps"></a>Дополнительная информация
Теперь вы можете [включить защиту](vmware-azure-enable-replication.md) для виртуальных машин.
