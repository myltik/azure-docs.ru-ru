---
title: "Автоматизация установки Mobility Service для Azure Site Recovery с использованием средств развертывания программного обеспечения | Документация Майкрософт"
description: "Эта статья поможет вам автоматизировать установку Mobility Service с помощью инструментов развертывания программного обеспечения, таких как System Center Configuration Manager."
services: site-recovery
documentationcenter: 
author: AnoopVasudavan
manager: gauravd
editor: 
ms.assetid: 
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/10/2017
ms.author: anoopkv
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: ded45356e6dd22b485adfe7f85ddd0abb21280e5
ms.lasthandoff: 04/03/2017

---
# <a name="automate-mobility-service-installation-by-using-software-deployment-tools"></a>Автоматизация установки Mobility Service с использованием средств развертывания программного обеспечения

В этой статье приведен пример того, как можно применить System Center Configuration Manager для развертывания Azure Site Recovery Mobility Service в центре обработки данных. Такие средства развертывания программного обеспечения, как Configuration Manager, предоставляют следующие преимущества:
* планирование развертывания свежих обновлений на выделенный период обслуживания для обновления программного обеспечения;
* масштабирование развертывания вплоть до нескольких сотен серверов.


> [!NOTE]
> В этой статье демонстрируется развертывание на примере System Center Configuration Manager 2012 R2. Автоматизировать установку Mobility Service можно также с помощью [службы автоматизации Azure и настройки требуемого состояния](site-recovery-automate-mobility-service-install.md).

## <a name="prerequisites"></a>Предварительные требования
1. Установленное в вашей среде средство развертывания программного обеспечения, например Configuration Manager.
  Создайте две [коллекции устройств](https://technet.microsoft.com/library/gg682169.aspx) — одну для всех **серверов Windows** и другую для всех **серверов Linux**, для защиты которых вы будете применять Site Recovery.
3. Сервер конфигурации, зарегистрированный в Site Recovery.
4. Защищенная общая сетевая папка (общая папка SMB), к которой есть доступ с сервера Configuration Manager.

## <a name="deploy-mobility-service-on-computers-running-windows"></a>Развертывание Mobility Service на компьютерах под управлением Windows
> [!NOTE]
> В этой статье предполагается, что сервер конфигурации использует IP-адрес 192.168.3.121, а защищенная общая сетевая папка имеет адрес \\\ContosoSecureFS\MobilityServiceInstallers.

### <a name="step-1-prepare-for-deployment"></a>Шаг 1. Подготовка к развертыванию
1. Создайте в общей сетевой папке каталог с именем **MobSvcWindows**.
2. Войдите на сервер конфигурации и откройте административную командную строку.
3. Выполните следующие команды, чтобы создать файл парольной фразы.

    `cd %ProgramData%\ASR\home\svsystems\bin`

    `genpassphrase.exe -v > MobSvc.passphrase`
4. Скопируйте файл **MobSvc.passphrase** в каталог **MobSvcWindows**, расположенный в общей сетевой папке.
5. Перейдите к репозиторию установщика на сервере конфигурации, выполнив такую команду:

   `cd %ProgramData%\ASR\home\svsystems\puhsinstallsvc\repository`

6. Скопируйте файл **Microsoft-ASR\_UA\_*version*\_Windows\_GA\_*date*\_Release.exe** в каталог **MobSvcWindows**, расположенный в общей сетевой папке.
7. Скопируйте приведенный ниже код в файл и сохраните этот файл с именем **install.bat** в каталоге **MobSvcWindows**.

   > [!NOTE]
   > Замените в этом скрипте заполнители [CSIP] реальными значениями IP-адреса сервера конфигурации.

```
Time /t >> C:\Temp\logfile.log
REM ==================================================
REM ==== Clean up the folders ========================
RMDIR /S /q %temp%\MobSvc
MKDIR %Temp%\MobSvc
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
REM ==== Extract the installer ======================
CD %Temp%\MobSvc\Extracted
REM ==================================================
REM ==== Check if Mob Svc is already installed =======
REM ==== If not installed run install command ========
REM ==== Else run upgrade command =====================
REM ==== {275197FC-14FD-4560-A5EB-38217F80CBD1} is ====
REM ==== guid for Mob Svc Installer ====================
whoami >> C:\temp\logfile.log
REM SET PRODKEY=HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Uninstall
REM REG QUERY %PRODKEY%\{275197FC-14FD-4560-A5EB-38217F80CBD1} >> C:\Temp\logfile.log 2>&1
REM REG QUERY %PRODKEY%\{275197FC-14FD-4560-A5EB-38217F80CBD1}
REM IF NOT %ERRORLEVEL% EQU 0 (GOTO :INSTALL) ELSE GOTO :UPDATE
NET START | FIND "InMage Scout Application Service"
IF  %ERRORLEVEL% EQU 1 (GOTO :INSTALL) ELSE GOTO :UPDATE
:INSTALL
    echo "Install" >> c:\Temp\logfile.log
     UnifiedAgent.exe /Role "Agent" /CSEndpoint "10.10.20.168" /PassphraseFilePath %Temp%\MobSvc\MobSvc.passphrase
GOTO :ENDSCRIPT
:UPDATE
    echo "Update" >> C:\Temp\logfile.log
    UnifiedAgent.exe /upgrade
:ENDSCRIPT

```

### <a name="step-2-create-a-package"></a>Шаг 2. Создание пакета

1. Войдите в консоль Configuration Manager.
2. Перейдите к разделу **Библиотека программного обеспечения** > **Управление приложениями** > **Пакеты**.
3. Щелкните **Пакеты** правой кнопкой мыши и выберите **Создать пакет**.
4. Введите значения для параметров Имя, Описание, Изготовитель, Язык и Версия.
5. Установите флажок **Этот пакет содержит исходные файлы**.
6. Щелкните **Обзор** и выберите общую сетевую папку, в которой хранится установщик (\\\ContosoSecureFS\MobilityServiceInstaller\MobSvcWindows).

  ![Снимок экрана с мастером создания пакета и программы](./media/site-recovery-install-mobility-service-using-sccm/create_sccm_package.png)

7. На странице **Выберите тип создаваемой программы** выберите **Стандартная программа** и щелкните **Далее**.

  ![Снимок экрана с мастером создания пакета и программы](./media/site-recovery-install-mobility-service-using-sccm/sccm-standard-program.png)

8. На странице **Укажите сведения об этой стандартной программе** предоставьте следующие данные и щелкните **Далее**. (Для остальных параметров можно оставить значения по умолчанию.)
 
  | **Имя параметра** | **Значение** |
  |--|--|
  | Имя | Установка Microsoft Azure Mobility Service (Windows) |
  | Команда | install.bat |
  | Программа может запускаться | Независимо от входа пользователя в систему |

  ![Снимок экрана с мастером создания пакета и программы](./media/site-recovery-install-mobility-service-using-sccm/sccm-program-properties.png)

9. На следующей странице выберите целевые операционные системы. Mobility Service можно устанавливать только на Windows Server 2012 R2, Windows Server 2012 и Windows Server 2008 R2.

  ![Снимок экрана с мастером создания пакета и программы](./media/site-recovery-install-mobility-service-using-sccm/sccm-program-properties-page2.png) 

10. Дважды щелкните **Далее**, чтобы завершить работу мастера.


> [!NOTE]
> Скрипт поддерживает как новые установки агентов Mobility Service, так и обновление уже установленных агентов.

### <a name="step-3-deploy-the-package"></a>Шаг 3. Развертывание пакета
1. В консоли Configuration Manager щелкните пакет правой кнопкой мыши и выберите команду **Распространить содержимое**.
  ![Снимок экрана с консолью Configuration Manager](./media/site-recovery-install-mobility-service-using-sccm/sccm_distribute.png)
2. Выберите **[Точки распространения](https://technet.microsoft.com/library/gg712321.aspx#BKMK_PlanForDistributionPoints)**, на которые следует скопировать эти пакеты.
3. Завершите работу мастера. Теперь пакет будет реплицироваться на выбранные точки распространения.
4. Когда распространение пакета завершится, щелкните пакет правой кнопкой мыши и выберите **Развернуть**.
  ![Снимок экрана с консолью Configuration Manager](./media/site-recovery-install-mobility-service-using-sccm/sccm_deploy.png)
5. Выберите коллекцию устройств Windows Server, созданную на этапе подготовки предварительных требований, в качестве целевой коллекции для развертывания.

  ![Снимок экрана с мастером развертывания программного обеспечения](./media/site-recovery-install-mobility-service-using-sccm/sccm-select-target-collection.png)

6. На странице **Укажите места распространения содержимого** выберите нужные **Точки распространения**.
7. На странице **Укажите параметры управления процессом развертывания этого программного обеспечения** убедитесь, что выбрана цель **Обязательно**.

  ![Снимок экрана с мастером развертывания программного обеспечения](./media/site-recovery-install-mobility-service-using-sccm/sccm-deploy-select-purpose.png)

8. Настройте расписание в разделе **Укажите расписание этого развертывания**. Дополнительные сведения см. в статье [Развертывание пакетов и программ в Configuration Manager](https://technet.microsoft.com/library/gg682178.aspx).
9. Настройте свойства на странице **Точки распространения** в соответствии с потребностями вашего центра обработки данных. Теперь завершите работу мастера.

> [!TIP]
> Чтобы избежать лишних перезагрузок, запланируйте установку пакета на период ежемесячного обслуживания или обновления программного обеспечения.

Ход развертывания можно отслеживать с помощью консоли Configuration Manager. Последовательно выберите пункты **Мониторинг** > **Развертывания** > *[имя пакета]*.

  ![Снимок экрана с настройкой отслеживания расписания в Configuration Manager](./media/site-recovery-install-mobility-service-using-sccm/report.PNG)

## <a name="deploy-mobility-service-on-computers-running-linux"></a>Развертывание Mobility Service на компьютерах под управлением Linux
> [!NOTE]
> В этой статье предполагается, что сервер конфигурации использует IP-адрес 192.168.3.121, а защищенная общая сетевая папка имеет адрес \\\ContosoSecureFS\MobilityServiceInstallers.

### <a name="step-1-prepare-for-deployment"></a>Шаг 1. Подготовка к развертыванию
1. Создайте в общей сетевой папке каталог с именем **MobSvcLinux**.
2. Войдите на сервер конфигурации и откройте административную командную строку.
3. Выполните следующие команды, чтобы создать файл парольной фразы.

    `cd %ProgramData%\ASR\home\svsystems\bin`

    `genpassphrase.exe -v > MobSvc.passphrase`
4. Скопируйте файл **MobSvc.passphrase** в каталог **MobSvcLinux**, расположенный в общей сетевой папке.
5. Перейдите к репозиторию установщика на сервере конфигурации, выполнив такую команду.

   `cd %ProgramData%\ASR\home\svsystems\puhsinstallsvc\repository`

6. Скопируйте следующие файлы в каталог **MobSvcLinux**, расположенный в общей сетевой папке.
   * Microsoft-ASR\_UA\_*version*\_OEL-64\_GA\_*date*\_Release.tar.gz
   * Microsoft-ASR\_UA\_*version*\_RHEL6-64\_GA\_*date*\_Release.tar.gz
   * Microsoft-ASR\_UA\_*version*\_RHEL7-64\_GA\_*date*\_Release.tar.gz
   * Microsoft-ASR\_UA\_*version*\_SLES11-SP3-64\_GA\_*date*\_Release.tar.gz

7. Скопируйте приведенный ниже код в файл и сохраните этот файл с именем **install_linux.sh** в каталоге **MobSvcLinux**.
   > [!NOTE]
   > Замените в этом скрипте заполнители [CSIP] реальными значениями IP-адреса сервера конфигурации.

```
#!/bin/sh

rm -rf /tmp/MobSvc

mkdir -p /tmp/MobSvc

if [ -f /etc/oracle-release ] && [ -f /etc/redhat-release ]; then
    if grep -q 'Oracle Linux Server release 6.*' /etc/oracle-release; then
        if uname -a | grep -q x86_64; then
            OS="OL6-64"
        cp *OL6*.tar.gz /tmp/MobSvc
        fi
    fi
elif [ -f /etc/redhat-release ]; then
    if grep -q 'Red Hat Enterprise Linux Server release 6.* (Santiago)' /etc/redhat-release || \
        grep -q 'CentOS Linux release 6.* (Final)' /etc/redhat-release || \
        grep -q 'CentOS release 6.* (Final)' /etc/redhat-release; then
        if uname -a | grep -q x86_64; then
            OS="RHEL6-64"
            cp *RHEL6*.tar.gz /tmp/MobSvc
        fi
    elif grep -q 'Red Hat Enterprise Linux Server release 7.* (Maipo)' /etc/redhat-release || \
        grep -q 'CentOS Linux release 7.* (Core)' /etc/redhat-release; then
        if uname -a | grep -q x86_64; then
            OS="RHEL7-64"
            cp *RHEL7*.tar.gz /tmp/MobSvc
    fi
    fi
elif [ -f /etc/SuSE-release ] && grep -q 'VERSION = 11' /etc/SuSE-release; then
    if grep -q "SUSE Linux Enterprise Server 11" /etc/SuSE-release && grep -q 'PATCHLEVEL = 3' /etc/SuSE-release; then
        if uname -a | grep -q x86_64; then
            OS="SLES11-SP3-64"
        echo $OS >> /tmp/MobSvc/sccm.log
        cp *SLES11*.tar.gz /tmp/MobSvc
        fi
    fi
elif [ -f /etc/lsb-release ] ; then
    if grep -q 'DISTRIB_RELEASE=14.04' /etc/lsb-release ; then
       if uname -a | grep -q x86_64; then
           OS="UBUNTU-14.04-64"
       cp *UBUNTU*.tar.gz /tmp/MobSvc
       fi
    fi
else
    exit 1
fi
if [ "${OS}" ==  "" ]; then
    exit 1
fi
cp MobSvc.passphrase /tmp/MobSvc
cd /tmp/MobSvc

tar -zxvf *.tar.gz


if [ -e /usr/local/.vx_version ];
then
    ./install -A u
    echo "Errorcode:$?"
    Error=$?

else
    ./install -t both -a host -R Agent -d /usr/local/ASR -i [CS IP] -p 443 -s y -c https -P MobSvc.passphrase >> /tmp/MobSvc/sccm.log 2>&1 && echo "Install Progress"
    Error=$?
fi
cd /tmp
rm -rf /tm/MobSvc
exit ${Error}
```

### <a name="step-2-create-a-package"></a>Шаг 2. Создание пакета

1. Войдите в консоль Configuration Manager.
2. Перейдите к разделу **Библиотека программного обеспечения** > **Управление приложениями** > **Пакеты**.
3. Щелкните **Пакеты** правой кнопкой мыши и выберите **Создать пакет**.
4. Введите значения для параметров Имя, Описание, Изготовитель, Язык и Версия.
5. Установите флажок **Этот пакет содержит исходные файлы**.
6. Щелкните **Обзор** и выберите общую сетевую папку, в которой хранится установщик (\\\ContosoSecureFS\MobilityServiceInstaller\MobSvcLinux).

  ![Снимок экрана с мастером создания пакета и программы](./media/site-recovery-install-mobility-service-using-sccm/create_sccm_package-linux.png)

7. На странице **Выберите тип создаваемой программы** выберите **Стандартная программа** и щелкните **Далее**.

  ![Снимок экрана с мастером создания пакета и программы](./media/site-recovery-install-mobility-service-using-sccm/sccm-standard-program.png)

8. На странице **Укажите сведения об этой стандартной программе** предоставьте следующие данные и щелкните **Далее**. (Для остальных параметров можно оставить значения по умолчанию.)

    | **Имя параметра** | **Значение** |
  |--|--|
  | Имя | Установка Microsoft Azure Mobility Service (Linux) |
  | Команда | ./install_linux.sh |
  | Программа может запускаться | Независимо от входа пользователя в систему |

  ![Снимок экрана с мастером создания пакета и программы](./media/site-recovery-install-mobility-service-using-sccm/sccm-program-properties-linux.png)

9. На следующей странице выберите **Эта программа может запускаться на любой платформе**.
  ![Снимок экрана с мастером создания пакета и программы](./media/site-recovery-install-mobility-service-using-sccm/sccm-program-properties-page2-linux.png)

10. Дважды щелкните **Далее**, чтобы завершить работу мастера. 
 
> [!NOTE]
> Скрипт поддерживает как новые установки агентов Mobility Service, так и обновление уже установленных агентов.

### <a name="step-3-deploy-the-package"></a>Шаг 3. Развертывание пакета
1. В консоли Configuration Manager щелкните пакет правой кнопкой мыши и выберите команду **Распространить содержимое**.
  ![Снимок экрана с консолью Configuration Manager](./media/site-recovery-install-mobility-service-using-sccm/sccm_distribute.png)
2. Выберите **[Точки распространения](https://technet.microsoft.com/library/gg712321.aspx#BKMK_PlanForDistributionPoints)**, на которые следует скопировать эти пакеты.
3. Завершите работу мастера. Теперь пакет будет реплицироваться на выбранные точки распространения.
4. Когда распространение пакета завершится, щелкните пакет правой кнопкой мыши и выберите **Развернуть**.
  ![Снимок экрана с консолью Configuration Manager](./media/site-recovery-install-mobility-service-using-sccm/sccm_deploy.png)
5. Выберите коллекцию устройств Linux Server, созданную на этапе подготовки предварительных требований, в качестве целевой коллекции для развертывания.

  ![Снимок экрана с мастером развертывания программного обеспечения](./media/site-recovery-install-mobility-service-using-sccm/sccm-select-target-collection-linux.png)

6. На странице **Укажите места распространения содержимого** выберите нужные **Точки распространения**.
7. На странице **Укажите параметры управления процессом развертывания этого программного обеспечения** убедитесь, что выбрана цель **Обязательно**.

  ![Снимок экрана с мастером развертывания программного обеспечения](./media/site-recovery-install-mobility-service-using-sccm/sccm-deploy-select-purpose.png)

8. Настройте расписание в разделе **Укажите расписание этого развертывания**. Дополнительные сведения см. в статье [Развертывание пакетов и программ в Configuration Manager](https://technet.microsoft.com/library/gg682178.aspx).
9. Настройте свойства на странице **Точки распространения** в соответствии с потребностями вашего центра обработки данных. Теперь завершите работу мастера.

Mobility Service устанавливается в коллекцию устройств Linux Server согласно настроенному расписанию.

## <a name="other-methods-to-install-mobility-service"></a>Другие методы установки службы Mobility Service
Вот еще несколько вариантов действий для установки службы Mobility Service.
* [Ручная установка с использованием графического интерфейса](http://aka.ms/mobsvcmanualinstall)
* [Ручная установка с использованием командной строки](http://aka.ms/mobsvcmanualinstallcli)
* [Принудительная установка с использованием сервера конфигурации](http://aka.ms/pushinstall)
* [Автоматическая установка с использованием службы автоматизации Azure и настройки требуемого состояния](http://aka.ms/mobsvcdscinstall)

## <a name="uninstall-mobility-service"></a>Удаление службы Mobility Service
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

## <a name="next-steps"></a>Дальнейшие действия
Теперь вы можете [включить защиту](https://docs.microsoft.com/en-us/azure/site-recovery/site-recovery-vmware-to-azure#step-6-replicate-applications) для виртуальных машин.

