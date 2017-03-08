---
title: 
description: 
services: site-recovery
documentationcenter: 
author: 
manager: 
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: 
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 
ms.author: 
translationtype: Human Translation
ms.sourcegitcommit: 4871e5959483058aa957e3373ddc5121b398e1d9
ms.openlocfilehash: 1d6d595bf788fc3fdba0bbc98887614e17c791dc
ms.lasthandoff: 02/17/2017


---



### <a name="install-the-mobility-service"></a>Установка службы Mobility
При включении защиты для виртуальных машин и физических серверов нужно прежде всего установить службу Mobility Service. Это можно сделать несколькими способами.

* **Принудительная установка с сервера обработки**. Если вы включаете репликацию для компьютера, выполните принудительную установку компонента службы Mobility Service с сервера обработки. 
*Обратите внимание*, что принудительная установка не выполняется, если на компьютере уже работает актуальная версия компонента.
* **Принудительная установка корпоративными средствами**. Автоматическая установка компонента с помощью корпоративных средств принудительной установки, таких как WSUS, System Center Configuration Manager или [служба автоматизации Azure и служба настройки требуемого состояния](site-recovery-automate-mobility-service-install.md). Перед этим настройте сервер конфигурации.
* **Установка вручную**. Установите компонент вручную на каждом компьютере, который нужно реплицировать. Перед этим настройте сервер конфигурации.

#### <a name="prepare-for-automatic-push-on-windows-machines"></a>Подготовка к принудительной установке на компьютерах Windows
Ниже описан процесс подготовки компьютеров Windows для автоматической установки службы Mobility Service сервером обработки.

1. Создайте учетную запись, используемую сервером обработки для доступа к компьютеру. Учетная запись должна иметь права администратора (локального или администратора домена) и использоваться только для принудительной установки.

   > [!NOTE]
   > Если учетная запись домена не используется, на локальном компьютере потребуется отключить контроль удаленного доступа пользователей. Для этого в разделе реестра HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System добавьте запись LocalAccountTokenFilterPolicy DWORD и задайте для нее значение 1. Чтобы добавить в реестр запись из интерфейса командной строки, введите **`REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`**.
   >
   >
2. В брандмауэре Windows на компьютере, который нужно защитить, выберите **Разрешить запуск программы или компонента через брандмауэр Windows**. Активируйте **общий доступ к файлам и принтерам** и **инструментарий управления Windows**. Для компьютеров, принадлежащих домену, можно настроить параметры брандмауэра с помощью объекта групповой политики.

   ![Параметры брандмауэра](./media/site-recovery-vmware-to-azure/mobility1.png)
3. Добавьте созданную учетную запись:

   * Откройте **cspsconfigtool**. Он доступен через ярлык на рабочем столе и расположен в папке [РАСПОЛОЖЕНИЕ УСТАНОВКИ]\home\svsystems\bin.
   * На вкладке **Управление учетными записями** щелкните **Добавить учетную запись**.
   * Добавьте созданную учетную запись. После этого понадобится указать учетные данные при добавлении репликации для компьютера.

#### <a name="prepare-for-automatic-push-on-linux-servers"></a>Подготовка к принудительной установке на серверах Linux
1. Убедитесь, что компьютер Linux, который необходимо защитить, соответствует всем [предварительным требованиям для защищаемого компьютера](#protected-machine-prerequisites). Убедитесь, что между компьютером Linux и сервером обработки установлено сетевое подключение.
2. Создайте учетную запись, используемую сервером обработки для доступа к компьютеру. Учетная запись должна принадлежать привилегированному пользователю на исходном сервере Linux и использоваться только для принудительной установки.

   * Откройте **cspsconfigtool**. Он доступен через ярлык на рабочем столе и расположен в папке [РАСПОЛОЖЕНИЕ УСТАНОВКИ]\home\svsystems\bin.
   * На вкладке **Управление учетными записями** щелкните **Добавить учетную запись**.
   * Добавьте созданную учетную запись. После этого понадобится указать учетные данные при добавлении репликации для компьютера.
3. Убедитесь, что файл /etc/hosts на исходном сервере Linux содержит записи, которые связывают имя локального узла с IP-адресами всех сетевых адаптеров.
4. Установите последние пакеты openssh, openssh-server, openssl на компьютере, который нужно реплицировать.
5. Убедитесь, что служба SSH включена и работает через порт 22.
6. Включите подсистему SFTP и проверку подлинности с помощью пароля в файле sshd_config следующим образом:

   * Выполните вход в качестве привилегированного пользователя.
   * В файле /etc/ssh/sshd_config найдите строку, которая начинается с **PasswordAuthentication**.
   * Раскомментируйте ее и измените значение с **no** на **yes**.
   * Найдите строку, которая начинается с **Subsystem** , и раскомментируйте ее.

     ![Linux](./media/site-recovery-vmware-to-azure/mobility2.png)

### <a name="install-the-mobility-service-manually"></a>Установка службы Mobility Service вручную
Установщики находятся на сервере конфигурации в каталоге **C:\Program Files (x86)\Microsoft Azure Site Recovery\home\svsystems\pushinstallsvc\repository**.

| Исходная операционная система | Файл установки службы Mobility Service |
| --- | --- |
| Windows Server (только&64;-разрядная версия) |Microsoft-ASR_UA_9.*.0.0_Windows_* release.exe |
| CentOS 6.4, 6.5, 6.6 (только 64-разрядная версия) |Microsoft-ASR_UA_9.*.0.0_RHEL6-64_*release.tar.gz |
| SUSE Linux Enterprise Server 11 SP3 (только 64-разрядная версия) |Microsoft-ASR_UA_9.*.0.0_SLES11-SP3-64_*release.tar.gz |
| Oracle Enterprise Linux 6.4, 6.5 (только 64-разрядная версия) |Microsoft-ASR_UA_9.*.0.0_OL6-64_*release.tar.gz |

#### <a name="install-mobility-service-on-a-windows-server"></a>Установка Mobility Service на сервере Windows Server
1. Скачайте и запустите соответствующий установщик.
2. На странице **Перед началом работы** выберите **Служба мобильности**.

    ![Служба мобильности](./media/site-recovery-vmware-to-azure/mobility3.png)
3. На странице **Configuration Server Details** (Сведения о сервере конфигурации) укажите IP-адрес сервера конфигурации и парольную фразу, созданную при выполнении единой установки. Ее можно получить, выполнив на сервере конфигурации команду **<SiteRecoveryInstallationFolder>\home\sysystems\bin\genpassphrase.exe –n**.

    ![Служба мобильности](./media/site-recovery-vmware-to-azure/mobility6.png)
4. Для параметра **Расположение установки** оставьте значение по умолчанию и нажмите кнопку **Далее**, чтобы начать установку.
5. На странице **Ход установки** следите за ходом установки и при появлении соответствующего запроса перезапустите компьютер. После установки службы ее состояние обновится на портале с задержкой до 15 минут.

#### <a name="install-mobility-service-on-a-windows-server-using-the-command-prompt"></a>Установка Mobility Service на сервере Windows Server с помощью командной строки
1. Скопируйте установщик в локальную папку (например, C:\Temp) на сервере, который необходимо защитить. Установщик можно найти на сервере конфигурации в каталоге **[расположение_установки]\home\svsystems\pushinstallsvc\repository**. Пакет для операционных систем Windows будет иметь похожее имя: Microsoft ASR_UA_9.3.0.0_Windows_GA_17thAug2016_release.exe.
2. Переименуйте этот файл в MobilitySvcInstaller.exe.
3. Извлеките MSI-файл установщика, используя следующую команду.

    ``C:\> cd C:\Tempww
    ``C:\Temp> MobilitySvcInstaller.exe /q /xC:\Temp\Extracted``
    ``C:\Temp> cd Extracted``
    ``C:\Temp\Extracted> UnifiedAgent.exe /Role "Agent" /CSEndpoint "IP Address of Configuration Server" /PassphraseFilePath <Full path to the passphrase file>``

##### <a name="full-command-line-syntax"></a>Полный синтаксис для командной строки
    UnifiedAgent.exe [/Role <Agent/MasterTarget>] [/InstallLocation <Installation Directory>] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>] [/LogFilePath <Log File Path>]<br/>

**Параметры**

* **/Role**: обязательный параметр. (Обязательный параметр.) Указывает, следует ли устанавливать службу Mobility Service. Входные значения: Agent|MasterTarget
* **/InstallLocation:** обязательный параметр. (Обязательный параметр.) Указывает место установки службы.
* **/PassphraseFilePath:** обязательный параметр. (Обязательный параметр.) Парольная фраза сервера конфигурации.
* **/LogFilePath:** обязательный параметр. Расположение, в котором следует создать файлы журнала установки.

#### <a name="uninstall-the-mobility-service-manually"></a>Установка Mobility Service вручную
Службу Mobility Service можно установить с помощью элемента "Установка и удаление программ" в "Панели управления" или с помощью операции командной строки: MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1}

#### <a name="install-the-mobility-service-on-a-linux-server"></a>Установка службы Mobility Service на сервере Linux
1. Скопируйте соответствующий TAR-архив согласно приведенной выше таблице на компьютер Linux, который нужно реплицировать.
2. Откройте программу оболочки и извлеките содержимое сжатого TAR-файла архива в локальный каталог, выполнив команду `tar -xvzf Microsoft-ASR_UA_8.5.0.0*`.
3. Создайте файл passphrase.txt в локальном каталоге, в который извлечено содержимое TAR-архива. Для этого скопируйте парольную фразу из файла C:\ProgramData\Microsoft Azure Site Recovery\private\connection.passphrase на сервере конфигурации и сохраните ее в файле passphrase.txt, выполнив в оболочке команду *`echo <passphrase> >passphrase.txt`*.
4. Установите службу Mobility Service, выполнив команду *`sudo ./install -t both -a host -R Agent -d /usr/local/ASR -i <IP address> -p <port> -s y -c https -P passphrase.txt`*.
5. Укажите внутренний IP-адрес сервера конфигурации и убедитесь, что выбран порт 443. После установки службы ее состояние обновится на портале в течение 15 минут.

**Кроме того, установку можно выполнить из командной строки**.

Скопируйте парольную фразу из файла C:\Program Files (x86)\InMage Systems\private\connection на сервере конфигурации и сохраните ее там же как файл passphrase.txt. Затем выполните следующие команды. В нашем примере используется IP-адрес сервера конфигурации 104.40.75.37 и порт HTTPS 443:


Установка на рабочем сервере

    ./install -t both -a host -R Agent -d /usr/local/ASR -i 104.40.75.37 -p 443 -s y -c https -P passphrase.txt

Установка на главном целевом сервере:

    ./install -t both -a host -R MasterTarget -d /usr/local/ASR -i 104.40.75.37 -p 443 -s y -c https -P passphrase.txt




