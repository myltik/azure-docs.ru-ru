---
title: " Управление сервером конфигурации в службе Azure Site Recovery | Документация Майкрософтs"
description: "В этой статье описывается, как настраивать и администрировать сервер конфигурации."
services: site-recovery
documentationcenter: 
author: AnoopVasudavan
manager: gauravd
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: backup-recovery
ms.date: 2/14/2017
ms.author: anoopkv
translationtype: Human Translation
ms.sourcegitcommit: 73d5f91f31780350c68b3475c2cbbb597f9b438e
ms.openlocfilehash: 0c8f37055a6c64a54009ecafd883426824dcd901
ms.lasthandoff: 03/01/2017

---

# <a name="manage-a-configuration-server"></a>Управление сервером конфигурации

Сервер конфигурации выполняет роль координатора между службами Site Recovery и локальной инфраструктурой. В этой статье описывается, как выполнить установку, настройку и администрирование сервера конфигурации.

## <a name="prerequisites"></a>Предварительные требования
Ниже перечислены минимальные требования к оборудованию, программному обеспечению и сети для установки сервера конфигурации.

> [!NOTE]
> [Планирование мощности](site-recovery-capacity-planner.md) — это важный шаг для развертывания сервера конфигурации с настройками, соответствующими требованиям нагрузки. Дополнительные сведения см. в разделе [Требования к размерам сервера конфигурации](#sizing-requirements-for-a-configuration-server).

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

## <a name="downloading-the-configuration-server-software"></a>Скачивание программного обеспечения сервера конфигурации
1. Войдите на портал Azure и перейдите в хранилище служб восстановления.
2. Откройте последовательно **Инфраструктура Site Recovery** > **Серверы конфигурации** (в разделе "VMware или физические компьютеры").

  ![Страница "Добавление серверов"](./media/site-recovery-vmware-to-azure-manage-configuration-server/AddServers.png)
3. Нажмите кнопку **+Серверы**.
4. На странице **добавления сервера** нажмите кнопку "Скачать", чтобы скачать регистрационный ключ. Этот ключ требуется при установке сервера конфигурации для его регистрации в службе Azure Site Recovery.
5. Щелкните ссылку **Download the Microsoft Azure Site Recovery Unified Setup** (Скачать единый файл установки Microsoft Azure Site Recovery), чтобы скачать последнюю версию сервера конфигурации.

  ![Страница загрузки](./media/site-recovery-vmware-to-azure-manage-configuration-server/downloadcs.png)

  > [!TIP]
  Вы можете скачать последнюю версию сервера конфигурации непосредственно с соответствующей [страницы в центре скачивания Майкрософт](http://aka.ms/unifiedsetup).

## <a name="installing-and-registering-a-configuration-server-from-gui"></a>Установка и регистрация сервера конфигурации и графического пользовательского интерфейса
[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

## <a name="installing-and-registering-a-configuration-server-using-command-line"></a>Установка и регистрация сервера конфигурации с помощью командой строки

  ```
  UnifiedSetup.exe [/ServerMode <CS/PS>] [/InstallDrive <DriveLetter>] [/MySQLCredsFilePath <MySQL credentials file path>] [/VaultCredsFilePath <Vault credentials file path>] [/EnvType <VMWare/NonVMWare>] [/PSIP <IP address to be used for data transfer] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>]
  ```

### <a name="sample-usage"></a>Пример использования
  ```
  MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /xC:\Temp\Extracted
  cd C:\Temp\Extracted
  UNIFIEDSETUP.EXE /AcceptThirdpartyEULA /servermode "CS" /InstallLocation "D:\" /MySQLCredsFilePath "C:\Temp\MySQLCredentialsfile.txt" /VaultCredsFilePath "C:\Temp\MyVault.vaultcredentials" /EnvType "VMWare"
  ```


### <a name="configuration-server-installer-command-line-arguments"></a>Аргументы командной строки установщика сервера конфигурации
[!INCLUDE [site-recovery-unified-setup-parameters](../../includes/site-recovery-unified-installer-command-parameters.md)]


### <a name="create-a-mysql-credentials-file"></a>Создание файла учетных данных MySQL
Параметр MySQLCredsFilePath принимает в качестве входных данных файл. Создайте файл, используя следующий формат, и передайте его в качестве входных данных для параметра MySQLCredsFilePath.
```
[MySQLCredentials]
MySQLRootPassword = "Password>"
MySQLUserPassword = "Password"
```
### <a name="create-a-proxy-settings-configuration-file"></a>Создание файла конфигурации параметров прокси-сервера
Параметр ProxySettingsFilePath принимает в качестве входных данных файл. Создайте файл, используя следующий формат, и передайте его в качестве входных данных для параметра ProxySettingsFilePath.

```
[ProxySettings]
ProxyAuthentication = "Yes/No"
Proxy IP = "IP Address"
ProxyPort = "Port"
ProxyUserName="UserName"
ProxyPassword="Password"
```
## <a name="modifying-proxy-settings-for-configuration-server"></a>Изменение параметров прокси-сервера для сервера конфигурации
1. Войдите на сервер конфигурации.
2. Запустите файл cspsconfigtool.exe с помощью ярлыка.
3. Откройте вкладку **Vault Registration** (Регистрация хранилища).
4. Скачайте новый файл регистрации хранилища на портале и укажите его в качестве входных данных для средства.

  ![register-configuration-server](./media/site-recovery-vmware-to-azure-manage-configuration-server/register-csonfiguration-server.png)
5. Укажите сведения о новом прокси-сервере и нажмите кнопку **Register** (Зарегистрировать).
6. Откройте командную строку PowerShell с правами администратора.
7. Выполните следующую команду
  ```
  $pwd = ConvertTo-SecureString -String MyProxyUserPassword
  Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber – ProxyUserName domain\username -ProxyPassword $pwd
  net stop obengine
  net start obengine
  ```

  >[!WARNING]
  Если вы используете развернутые серверы обработки, подключенные к серверу конфигурации, необходимо [исправить параметры прокси-сервера на всех серверах обработки](site-recovery-vmware-to-azure-manage-scaleout-process-server.md#modifying-proxy-settings-for-scale-out-process-server) в развернутой службе.

## <a name="re-register-a-configuration-server-with-the-same-recovery-services-vault"></a>Повторная регистрация сервера конфигурации в том же хранилище служб восстановления
  1. Войдите на сервер конфигурации.
  2. Запустите файл cspsconfigtool.exe с помощью ярлыка.
  3. Откройте вкладку **Vault Registration** (Регистрация хранилища).
  4. Скачайте новый файл регистрации на портале и укажите его в качестве входных данных для средства.
        ![register-configuration-server](./media/site-recovery-vmware-to-azure-manage-configuration-server/register-csonfiguration-server.png)
  5. Укажите сведения о прокси-сервере и нажмите кнопку **Register** (Зарегистрировать).  
  6. Откройте командную строку PowerShell с правами администратора.
  7. Выполните следующую команду

      ```
      $pwd = ConvertTo-SecureString -String MyProxyUserPassword
      Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber – ProxyUserName domain\username -ProxyPassword $pwd
      net stop obengine
      net start obengine
      ```

  >[!WARNING]
  Если вы используете серверы обработки масштабирования, подключенные к серверу конфигурации, вам необходимо [повторно зарегистрировать все серверы обработки масштабирования](site-recovery-vmware-to-azure-manage-scaleout-process-server.md#re-registering-a-scale-out-process-server) в развернутой службе.

## <a name="registering-a-configuration-server-with-a-different-recovery-services-vault"></a>Регистрация сервера конфигурации в другом хранилище служб восстановления.
1. Войдите на сервер конфигурации.
2. В командной строке от имени администратора выполните команду:

```
reg delete HKLM\Software\Microsoft\Azure Site Recovery\Registration
net stop dra
```
3. Запустите файл cspsconfigtool.exe с помощью ярлыка.
4. Откройте вкладку **Vault Registration** (Регистрация хранилища).
5. Скачайте новый файл регистрации на портале и укажите его в качестве входных данных для средства.

    ![register-configuration-server](./media/site-recovery-vmware-to-azure-manage-configuration-server/register-csonfiguration-server.png)
6. Укажите сведения о прокси-сервере и нажмите кнопку **Register** (Зарегистрировать).  
7. Откройте командную строку PowerShell с правами администратора.
8. Выполните следующую команду
    ```
    $pwd = ConvertTo-SecureString -String MyProxyUserPassword
    Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber – ProxyUserName domain\username -ProxyPassword $pwd
    net stop obengine
    net start obengine
    ```

## <a name="decommissioning-a-configuration-server"></a>Списание сервера конфигурации
Прежде чем списывать сервер конфигурации, выполните следующие условия.
1. Отключите защиту для всех виртуальных машин на сервере конфигурации.
2. Отмените связь всех политик репликации с сервером конфигурации.
3. Удалите все узлы vSphere и серверы vCenters, связанные с сервером конфигурации.

### <a name="delete-the-configuration-server-from-azure-portal"></a>Удаление сервера конфигурации на портале Azure
1. На портале Azure в меню хранилища откройте последовательно **Инфраструктура Site Recovery** > **Серверы конфигурации**.
2. Выберите сервер конфигурации, который должен быть списан.
3. На странице сведений о сервере конфигурации нажмите кнопку "Удалить".

  ![delete-configuration-server](./media/site-recovery-vmware-to-azure-manage-configuration-server/delete-configuration-server.PNG)
4. Нажмите кнопку **Да**, чтобы подтвердить удаление сервера.

  >[!WARNING]
  Если с сервером конфигурации связаны виртуальные машины, политики репликации, серверы vCenter или узлы vSphere, вы не сможете удалить сервер. Удалите эти сущности, прежде чем пытаться удалить хранилище.

### <a name="uninstall-the-configuration-server-software-and-its-dependencies"></a>Удаление программного обеспечения сервера конфигурации и его зависимостей
  > [!TIP]
  Если вы планируете повторно использовать сервер конфигурации с Azure Site Recovery, перейдите сразу к шагу 4.

1. Войдите на сервер конфигурации от имени администратора.
2. Откройте последовательно "Панель управления" > "Программы" > "Удалить программы".
3. Удалите программы в следующей последовательности:
  * агент служб восстановления Microsoft Azure.
  * Microsoft Azure Site Recovery Mobility Service/Master Target Server (Microsoft Azure Site Recovery Mobility Service/главный целевой сервер)
  * Microsoft Azure Site Recovery Provider (Поставщик Microsoft Azure Site Recovery)
  * серверы конфигурации и обработки Microsoft Azure Site Recovery;
  * зависимости сервера конфигурации Microsoft Azure Site Recovery;
  * MySQL Server 5.5
4. Выполните следующую команду из командной строки с правами администратора:
  ```
  reg delete HKLM\Software\Microsoft\Azure Site Recovery\Registration
  ```

## <a name="renew-configuration-server-secure-socket-layerssl-certificates"></a>Обновление сертификатов Secure Socket Layer (SSL) конфигурации сервера
На сервере конфигурации есть встроенный веб-сервер, который координирует действия Mobility Service, серверов обработки и главных целевых серверов с сервером конфигурации. Веб-сервер сервера конфигурации использует SSL-сертификат для проверки подлинности клиентов. Срок действия этого сертификата — три года. Сертификат можно обновить в любое время, следуя инструкциям ниже.

> [!WARNING]
Сертификат можно обновить до окончания его срока действия для версии 9.4.XXXX.X и выше. Обновите все компоненты Azure Site Recovery (сервер конфигурации, сервер обработки, главный целевой сервер, Mobility Service), прежде чем начинать обновление сертификатов.

1. На портале Azure откройте "Хранилище" > "Инфраструктура Site Recovery" > "Сервер конфигурации".
2. Щелкните сервер конфигурации, для которого требуется обновить сертификат SSL.
3. В разделе сведений о работоспособности сервера конфигурации сервера вы увидите срок действия SSL-сертификата.
4. Обновите сертификат, нажав кнопку **Обновить сертификаты**, как показано на следующем рисунке:

  ![delete-configuration-server](./media/site-recovery-vmware-to-azure-manage-configuration-server/renew-cert-page.png)

### <a name="secure-socket-layer-certificate-expiry-warning"></a>Предупреждение об окончании срока сертификата Secure Socket Layer

> [!NOTE]
Сертификаты SSL для всех установок, которые выполнены до мая 2016 года, действительны в течение года. На портале Azure стали появляться уведомления об истечении срока действия сертификата.

1. Если срок действия SSL-сертификата сервера конфигурации заканчивается в течение следующих двух месяцев, служба начинает уведомлять пользователей с помощью портала Azure и электронной почты (вы должны быть подписаны на уведомления Azure Site Recovery). На странице ресурсов хранилища стал появляться баннер с уведомлением.

  ![certificate-notification](./media/site-recovery-vmware-to-azure-manage-configuration-server/ssl-cert-renew-notification.png)
2. Щелкните баннер, чтобы узнать больше об окончании срока действия сертификата.

  ![certificate-details](./media/site-recovery-vmware-to-azure-manage-configuration-server/ssl-cert-expiry-details.png)

  >[!TIP]
  Если вместо кнопки **Обновить сейчас** отображается кнопка **Обновить**, это означает, что некоторые компоненты в вашей среде не обновлены до версии 9.4.xxxx.x и выше.

## <a name="sizing-requirements-for-a-configuration-server"></a>Требования к размерам сервера конфигурации

| **ЦП** | **Память** | **Размер диска кэша** | **Частота изменения данных** | **Защищенные компьютеры** |
| --- | --- | --- | --- | --- |
| 8 виртуальных ЦП (2 сокета * 4 ядра с частотой 2,5 ГГц) |16 ГБ |300 ГБ |500 ГБ или менее |Репликация не более 100 компьютеров |
| 12 виртуальных ЦП (2 сокета * 6 ядер с частотой 2,5 ГГц) |18 ГБ |600 ГБ |От 500 ГБ до 1 ТБ |Репликация от 100 до 150 компьютеров |
| 16 виртуальных ЦП (2 сокета * 8 ядер с частотой 2,5 ГГц) |32 ГБ |1 TБ |От 1 ТБ до 2 ТБ |Репликация от 150 до 200 компьютеров |

  >[!TIP]
  Если вы ежедневно обновляете более 2 ТБ данных или планируете репликацию более 200 виртуальных машин, рекомендуется развернуть дополнительные серверы обработки для балансировки нагрузки трафика репликации. Узнайте больше о процессе развертывания серверов обработки.


## <a name="common-issues"></a>Распространенные проблемы
[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]

