---
title: "Автоматизация установки Mobility Service для Azure Site Recovery с использованием средств развертывания программного обеспечения | Документация Майкрософт."
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
ms.sourcegitcommit: 1e6ae31b3ef2d9baf578b199233e61936aa3528e
ms.openlocfilehash: 310f2a2fe793601d22952bf516a812bf4867bbec
ms.lasthandoff: 03/03/2017

---
# <a name="automate-mobility-service-installation-using-software-deployment-tools"></a>Автоматизация установки Mobility Service с использованием средств развертывания программного обеспечения

В этой статье приведен пример того, как можно применить System Center Configuration Manager для развертывания Azure Site Recovery Mobility Service в центре обработки данных. Такие средства развертывания программного обеспечения, как System Center Configuration Manager, предоставляют следующие преимущества:
* планирование развертывания — свежие обновления выполняются в течение запланированного периода обслуживания для обновления программного обеспечения;
* одновременное масштабное развертывание вплоть до нескольких сотен серверов.


> [!NOTE]
> В этой статье демонстрируется развертывание на примере System Center Configuration Manager 2012 R2. Автоматизировать установку Mobility Service можно также с помощью [службы автоматизации Azure и настройки требуемого состояния](site-recovery-automate-mobility-service-install.md).

## <a name="prerequisites"></a>Предварительные требования
1. Заранее развернутые в вашей среде средства развертывания программного обеспечения, например System Center Configuration Manager(SCCM).
  * Создайте две [коллекции устройств](https://technet.microsoft.com/library/gg682169.aspx) — одну для всех **серверов Windows** и еще одну для всех **серверов Linux**, для защиты которых вы будете применять Azure Site Recovery.
3. Сервер конфигурации, зарегистрированный в Azure Site Recovery.
4. Защищенная общая сетевая папка, к которой сможет обратиться сервер System Center Configuration Manager.

## <a name="deploy-mobility-service-on-computers-running-microsoft-windows-operating-systems"></a>Развертывание Mobility Service на компьютерах под управлением операционных систем Microsoft Windows
> [!NOTE]
> В данной статье предполагается, что выполняются следующие условия.
> 1. Сервер конфигурации имеет IP-адрес 192.168.3.121.
> 2. Защищенная общая сетевая папка имеет адрес \\\ContosoSecureFS\MobilityServiceInstallers.

### <a name="step-1-prepare-for-deployment"></a>Шаг 1. Подготовка к развертыванию
1. Создайте в общей сетевой папке каталог с именем **MobSvcWindows**.
2. Войдите на сервер конфигурации и откройте командную строку администратора.
3. Выполните следующие команды для создания файла парольной фразы.

    `cd %ProgramData%\ASR\home\svsystems\bin`

    `genpassphrase.exe -v > MobSvc.passphrase`
6. Скопируйте файл MobSvc.passphrase в каталог MobSvcWindows, расположенный в общей сетевой папке.
5. Теперь перейдите к репозиторию установщика на сервере конфигурации, выполнив такую команду.

  `cd %ProgramData%\ASR\home\svsystems\puhsinstallsvc\repository`
6. Скопируйте файл **Microsoft-ASR\_UA\_*version*\_Windows\_GA\_*date*\_Release.exe** в каталог **MobSvcWindows**, расположенный в общей сетевой папке.
7. Скопируйте приведенный ниже в файл и сохраните файл под именем **install.bat**, поместив его в каталог **MobSvcWindows**.
> [!NOTE]
> Не забудьте заменить в этом скрипте заполнители [CSIP] реальными значениями IP-адреса сервера конфигурации.

  [!INCLUDE [site-recovery-sccm-windows-script](../../includes/site-recovery-sccm-windows-script.md)]

### <a name="step-2-create-a-package"></a>Шаг 2. Создание пакета

1. Войдите в консоль управления System Center Configuration Manager
2. Перейдите к разделу **Библиотека программного обеспечения** > **Управление приложениями** > **Пакеты**
3. Щелкните **Пакеты** правой кнопкой мыши и выберите **Создать пакет**
4. Введите значения для параметров Имя, Описание, Изготовитель, Язык, Версия.
5. Установите флажок **Этот пакет содержит исходные файлы**.
6. Нажмите кнопку **Обзор** и выберите общую сетевую папку, в которой хранится установщик (\\\ContosoSecureFS\MobilityServiceInstaller\MobSvcWindows).

  ![create-sccm-package](./media/site-recovery-install-mobility-service-using-sccm/create_sccm_package.png)

7. На странице **Выберите тип создаваемой программы** выберите **Стандартная программа** и нажмите кнопку **Далее**.

  ![create-sccm-package](./media/site-recovery-install-mobility-service-using-sccm/sccm-standard-program.png)
8. На странице **Укажите сведения об этой стандартной программе** предоставьте следующие данные и нажмите кнопку **Далее**. (Для остальных параметров можно оставить значения по умолчанию).

  ![sccm-package-properties](./media/site-recovery-install-mobility-service-using-sccm/sccm-program-properties.png)   
| **Имя параметра** | **Значение** |
|--|--|
| Имя | Установка Microsoft Azure Mobility Service (Windows) |
| Команда | install.bat |
| Программа может запускаться | Независимо от входа пользователя в систему |
9. На следующей странице выберите целевые операционные системы. Mobility Service можно устанавливать только на Windows Server 2012 R2, Windows Server 2012 и Windows Server 2008 R2.

  ![sccm-package-properties-page2](./media/site-recovery-install-mobility-service-using-sccm/sccm-program-properties-page2.png)   
10. Дважды нажмите кнопку "Далее", чтобы завершить работу мастера.

> [!NOTE]
> Скрипт поддерживает как новые установки агентов Mobility Service, так и обновление уже установленных агентов.

### <a name="step-3-deploy-the-package"></a>Шаг 3. Развертывание пакета
1. В консоли System Center Configuration Manager щелкните пакет правой кнопкой мыши и выберите команду **Распространить содержимое**
  ![distribute-sccm-package](./media/site-recovery-install-mobility-service-using-sccm/sccm_distribute.png)
2. Выберите **[Точки распространения](https://technet.microsoft.com/library/gg712321.aspx#BKMK_PlanForDistributionPoints)**, на которые следует скопировать эти пакеты.
3. Когда работа мастера завершится, пакет начинает реплицироваться на выбранные точки распространения.
4. После завершения распространения пакета щелкните пакет правой кнопкой мыши и выберите **Развернуть**
  ![deploy-sccm-package](./media/site-recovery-install-mobility-service-using-sccm/sccm_deploy.png)
5. Выберите коллекцию устройств Widows Server, созданную на этапе подготовки предварительных требований, в качестве целевой коллекции для развертывания.

  ![sccm-select-target-collection](./media/site-recovery-install-mobility-service-using-sccm/sccm-select-target-collection.png)
6. На странице **Укажите места распространения содержимого** выберите нужные **Точки распространения**
7. На странице **Укажите параметры управления процессом развертывания этого программного обеспечения** проверьте, выбрана ли правильная цель.

  ![sccm-deploy-select-purpose](./media/site-recovery-install-mobility-service-using-sccm/sccm-deploy-select-purpose.png)
8. Выберите расписание в разделе **Укажите расписание этого развертывания**. Дополнительные сведения о [составлении расписания для пакетов](https://technet.microsoft.com/library/gg682178.aspx).
9. Настройте свойства на странице **Точки распространения** в соответствии с потребностями вашего центра обработки данных и завершите работу мастера.

> [!TIP]
> Чтобы избежать ненужных перезагрузок, запланируйте установку пакета на период ежемесячного обслуживания или обновления программного обеспечения.

Ход развертывания вы можете отслеживать с помощью консоли System Center Configuration Manager, перейдя в раздел **Мониторинг** > **Развертывания** > *[имя_пакета]*
  ![monitor-sccm](./media/site-recovery-install-mobility-service-using-sccm/report.PNG).

## <a name="deploy-mobility-service-on-computers-running-linux-operating-systems"></a>Развертывание Mobility Service на компьютерах под управлением операционных систем Linux
> [!NOTE]
> В данной статье предполагается, что выполняются следующие условия.
> 1. Сервер конфигурации имеет IP-адрес 192.168.3.121.
> 2. Защищенная общая сетевая папка имеет адрес \\\ContosoSecureFS\MobilityServiceInstallers.

### <a name="step-1-prepare-for-deployment"></a>Шаг 1. Подготовка к развертыванию
1. Создайте в общей сетевой папке каталог с именем **MobSvcLinux**.
2. Войдите на сервер конфигурации и откройте командную строку администратора.
3. Выполните следующие команды для создания файла парольной фразы.

    `cd %ProgramData%\ASR\home\svsystems\bin`

    `genpassphrase.exe -v > MobSvc.passphrase`
6. Скопируйте файл MobSvc.passphrase в каталог MobSvcWindows, расположенный в общей сетевой папке.
5. Теперь перейдите к репозиторию установщика на сервере конфигурации, выполнив такую команду.

  `cd %ProgramData%\ASR\home\svsystems\puhsinstallsvc\repository`
6. Скопируйте следующие файлы в каталог **MobSvcLinux**, расположенный в общей сетевой папке.
  * Microsoft-ASR\_UA\_*version*\_OEL-64\_GA\_*date*\_Release.tar.gz
  * Microsoft-ASR\_UA\_*version*\_RHEL6-64\_GA\_*date*\_Release.tar.gz
  * Microsoft-ASR\_UA\_*version*\_RHEL7-64\_GA\_*date*\_Release.tar.gz
  * Microsoft-ASR\_UA\_*version*\_SLES11-SP3-64\_GA\_*date*\_Release.tar.gz

7. Скопируйте приведенный ниже в файл и сохраните файл под именем **install_linux.sh**, поместив его в каталог **MobSvcLinux**.
> [!NOTE]
> Не забудьте заменить в этом скрипте заполнители [CSIP] реальными значениями IP-адреса сервера конфигурации.

  [!INCLUDE [site-recovery-sccm-linux-script](../../includes/site-recovery-sccm-linux-script.md)]

### <a name="step-2-create-a-package"></a>Шаг 2. Создание пакета

1. Войдите в консоль управления System Center Configuration Manager
2. Перейдите к разделу **Библиотека программного обеспечения** > **Управление приложениями** > **Пакеты**
3. Щелкните **Пакеты** правой кнопкой мыши и выберите **Создать пакет**
4. Введите значения для параметров Имя, Описание, Изготовитель, Язык, Версия.
5. Установите флажок **Этот пакет содержит исходные файлы**.
6. Нажмите кнопку **Обзор** и выберите общую сетевую папку, в которой хранится установщик (\\\ContosoSecureFS\MobilityServiceInstaller\MobSvcLinux).

  ![create-sccm-package](./media/site-recovery-install-mobility-service-using-sccm/create_sccm_package-linux.png)

7. На странице **Выберите тип создаваемой программы** выберите **Стандартная программа** и нажмите кнопку **Далее**.

  ![create-sccm-package](./media/site-recovery-install-mobility-service-using-sccm/sccm-standard-program.png)
8. На странице **Укажите сведения об этой стандартной программе** предоставьте следующие данные и нажмите кнопку **Далее**. (Для остальных параметров можно оставить значения по умолчанию).

  ![sccm-package-properties](./media/site-recovery-install-mobility-service-using-sccm/sccm-program-properties-linux.png)   
| **Имя параметра** | **Значение** |
|--|--|
| Имя | Установка Microsoft Azure Mobility Service (Linux) |
| Команда | ./install_linux.sh |
| Программа может запускаться | Независимо от входа пользователя в систему |

9. На следующей странице выберите **Эта программа может запускаться на любой платформе**
  ![sccm-package-properties-page2](./media/site-recovery-install-mobility-service-using-sccm/sccm-program-properties-page2-linux.png)   

10. Дважды нажмите кнопку **Далее**, чтобы завершить работу мастера.
> [!NOTE]
> Скрипт поддерживает как новые установки агентов Mobility Service, так и обновление уже установленных агентов.

### <a name="step-3-deploy-the-package"></a>Шаг 3. Развертывание пакета
1. В консоли System Center Configuration Manager щелкните пакет правой кнопкой мыши и выберите команду **Распространить содержимое**
  ![distribute-sccm-package](./media/site-recovery-install-mobility-service-using-sccm/sccm_distribute.png)
2. Выберите **[Точки распространения](https://technet.microsoft.com/library/gg712321.aspx#BKMK_PlanForDistributionPoints)**, на которые следует скопировать эти пакеты.
3. Когда работа мастера завершится, пакет начинает реплицироваться на выбранные точки распространения.
4. После завершения распространения пакета щелкните пакет правой кнопкой мыши и выберите **Развернуть**
  ![deploy-sccm-package](./media/site-recovery-install-mobility-service-using-sccm/sccm_deploy.png)
5. Выберите коллекцию устройств Linux Server, созданную на этапе подготовки предварительных требований, в качестве целевой коллекции для развертывания.

  ![sccm-select-target-collection](./media/site-recovery-install-mobility-service-using-sccm/sccm-select-target-collection-linux.png)
6. На странице **Укажите места распространения содержимого** выберите нужные **Точки распространения**
7. На странице **Укажите параметры управления процессом развертывания этого программного обеспечения** проверьте, выбрана ли правильная цель.

  ![sccm-deploy-select-purpose](./media/site-recovery-install-mobility-service-using-sccm/sccm-deploy-select-purpose.png)
8. Выберите расписание в разделе **Укажите расписание этого развертывания**. Дополнительные сведения о [составлении расписания для пакетов](https://technet.microsoft.com/library/gg682178.aspx).
9. Настройте свойства на странице **Точки распространения** в соответствии с потребностями вашего центра обработки данных и завершите работу мастера.

Mobility Service устанавливается в коллекцию устройств Linux Server согласно настроенному расписанию.

## <a name="other-methods-to-install-mobility-services"></a>Другие методы установки службы Mobility Service
Дополнительные сведения о других способах установки службы Mobility Service.
* [Ручная установка с использованием графического интерфейса](http://aka.ms/mobsvcmanualinstall)
* [Ручная установка с использованием командной строки](http://aka.ms/mobsvcmanualinstallcli)
* [Принудительная установка с использованием сервера конфигурации](http://aka.ms/pushinstall)
* [Автоматическая установка с использованием службы автоматизации Azure и настройки требуемого состояния](http://aka.ms/mobsvcdscinstall)

## <a name="uninstall-mobility-service"></a>Удаление службы Mobility Service
Вы можете создать пакеты SCCM для удаления службы Mobility Service, как и для установки. Чтобы удалить службу Mobility Service, используйте следующий сценарий:

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

