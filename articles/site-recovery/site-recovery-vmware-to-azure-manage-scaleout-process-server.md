---
title: " Управление сервером обработки масштабирования в Azure Site Recovery | Документация Майкрософт"
description: В этой статье описывается настройка сервера обработки масштабирования в Azure Site Recovery и управление им.
services: site-recovery
documentationcenter: ''
author: AnoopVasudavan
manager: gauravd
editor: ''
ms.assetid: ''
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: backup-recovery
ms.date: 02/22/2018
ms.author: anoopkv
ms.openlocfilehash: b2c2f8c6a10ca5098956de2402925bd9422212f8
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/07/2018
ms.locfileid: "29767759"
---
# <a name="manage-a-scale-out-process-server"></a>Управление сервером обработки масштабирования

Сервер обработки масштабирования выполняет роль координатора передачи данных между службами Site Recovery и локальной инфраструктурой. В этой статье описывается, как выполнить установку, настройку и администрирование серверов обработки масштабирования.

## <a name="prerequisites"></a>предварительным требованиям
Ниже перечислены рекомендуемые требования к оборудованию, программному обеспечению и сети для установки сервера обработки масштабирования.

> [!NOTE]
> [Планирование мощности](site-recovery-capacity-planner.md) — это важный шаг для развертывания сервера обработки масштабирования, соответствующего требованиям нагрузки. Дополнительные сведения см. в разделе с [требованиями к масштабированию для сервера обработки масштабирования](#sizing-requirements-for-a-configuration-server).

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

## <a name="downloading-the-scale-out-process-server-software"></a>Скачивание программного обеспечения сервера обработки масштабирования
1. Войдите на портал Azure и перейдите в хранилище служб восстановления.
2. Откройте **Инфраструктура Site Recovery** > **Серверы конфигурации** (в разделе VMware и физических компьютеров).
3. Выберите сервер конфигурации, чтобы перейти на страницу подробных сведений о нем.
4. Нажмите кнопку **+ Сервер обработки**.
5. На странице **Добавление сервера обработки** в раскрывающемся списке **Укажите, где следует развернуть сервер обработки** выберите **Локальное развертывание сервера обработки масштабирования**.

  ![Страница "Добавление серверов"](./media/site-recovery-vmware-to-azure-manage-scaleout-process-server/add-process-server.png)
6. Щелкните ссылку **Download the Microsoft Azure Site Recovery Unified Setup** (Скачать единый файл установки Microsoft Azure Site Recovery), чтобы скачать последнюю версию файла установки сервера обработки масштабирования.

  > [!WARNING]
  Версия сервера обработки масштабирования должна соответствовать версии сервера конфигурации, работающего в среде, или быть меньше ее. Простой способ обеспечить совместимость версий — использовать те же средства установки, которые недавно использовались для установки и обновления сервера конфигурации.

## <a name="installing-and-registering-a-scale-out-process-server-from-gui"></a>Установка и регистрация сервера обработки масштабирования из графического пользовательского интерфейса
Если вы увеличите развертывание так, что количество компьютеров-источников превысит 200 или объем ежедневно изменяемых данных превысит 2 ТБ, для обработки такой нагрузки потребуются дополнительные серверы обработки.

Ознакомьтесь с [рекомендациями по выбору размера серверов обработки](#size-recommendations-for-the-process-server) и выполните приведенные ниже инструкции по настройке сервера обработки. После настройки сервера необходимо перевести исходные компьютеры на его использование.

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-add-process-server.md)]


## <a name="installing-and-registering-a-scale-out-process-server-using-command-line"></a>Установка и регистрация сервера обработки масштабирования с помощью командной строки

```
UnifiedSetup.exe [/ServerMode <CS/PS>] [/InstallDrive <DriveLetter>] [/MySQLCredsFilePath <MySQL credentials file path>] [/VaultCredsFilePath <Vault credentials file path>] [/EnvType <VMWare/NonVMWare>] [/PSIP <IP address to be used for data transfer] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>]
```

### <a name="sample-usage"></a>Пример использования
```
MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /xC:\Temp\Extracted
cd C:\Temp\Extracted
UNIFIEDSETUP.EXE /AcceptThirdpartyEULA /servermode "PS" /InstallLocation "D:\" /EnvType "VMWare" /CSIP "10.150.24.119" /PassphraseFilePath "C:\Users\Administrator\Desktop\Passphrase.txt" /DataTransferSecurePort 443
```

### <a name="scale-out-process-server-installer-command-line-arguments"></a>Аргументы командной строки установщика сервера обработки масштабирования
[!INCLUDE [site-recovery-unified-setup-parameters](../../includes/site-recovery-unified-installer-command-parameters.md)]


### <a name="create-a-proxy-settings-configuration-file"></a>Создание файла конфигурации параметров прокси-сервера
Параметр ProxySettingsFilePath принимает в качестве входных данных файл. Создайте файл, используя следующий формат, и передайте его в качестве входных данных для параметра ProxySettingsFilePath.
```
* [ProxySettings]
* ProxyAuthentication = "Yes/No"
* Proxy IP = "IP Address"
* ProxyPort = "Port"
* ProxyUserName="UserName"
* ProxyPassword="Password"
```
## <a name="modifying-proxy-settings-for-scale-out-process-server"></a>Изменение параметров прокси-сервера для сервера обработки масштабирования
1. Войдите на сервер обработки масштабирования.
2. Откройте командную строку PowerShell с правами администратора.
3. Выполните следующую команду
  ```
  $pwd = ConvertTo-SecureString -String MyProxyUserPassword
  Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber –ProxyUserName domain\username -ProxyPassword $pwd
  net stop obengine
  net start obengine
  ```
4. Затем перейдите в каталог **%PROGRAMDATA%\ASR\Agent** и выполните следующую команду:
  ```
  cmd
  cdpcli.exe --registermt

  net stop obengine

  net start obengine

  exit
  ```

## <a name="re-registering-a-scale-out-process-server"></a>Повторная регистрация сервера обработки масштабирования
[!INCLUDE [site-recovery-vmware-register-process-server](../../includes/site-recovery-vmware-register-process-server.md)]

* Далее откройте командную строку с правами администратора.
* Перейдите в каталог **%PROGRAMDATA%\ASR\Agent** и выполните следующую команду:

```
cdpcli.exe --registermt

net stop obengine

net start obengine
```

## <a name="upgrading-a-scale-out-process-server"></a>Обновление сервера обработки масштабирования
[!INCLUDE [site-recovery-vmware-upgrade -process-server](../../includes/site-recovery-vmware-upgrade-process-server-internal.md)]

## <a name="decommissioning-a-scale-out-process-server"></a>Прекращение использования сервера обработки масштабирования
1. Убедитесь в следующем:
  - на портале Azure показано, что подключение сервера конфигурации установлено (состояние **Подключено**);
  - сервер обработки по-прежнему может обмениваться данными с сервером конфигурации.
2. Войдите на сервер обработки с правами администратора.
3. Откройте "Панель управления > Программы > Удалить программы".
4. Удалите программы в следующей последовательности:
  * серверы конфигурации и обработки Microsoft Azure Site Recovery;
  * зависимости сервера конфигурации Microsoft Azure Site Recovery;
  * агент служб восстановления Microsoft Azure.

Чтобы удаление сервера обработки отобразилось на портале Azure, может потребоваться около 15 минут.

  > [!NOTE]
  Если серверу обработки не удалось связаться с сервером конфигурации (на портале показано состояние подключения "Отключено"), необходимо выполнить следующие действия, чтобы удалить его с сервера конфигурации.

## <a name="unregistering-a-disconnected-scale-out-process-server-from-a-configuration-server"></a>Отмена регистрации отключенного сервера обработки масштабирования на сервере конфигурации

[!INCLUDE [site-recovery-vmware-upgrade-process-server](../../includes/site-recovery-vmware-unregister-process-server.md)]

## <a name="sizing-requirements-for-a-scale-out-process-server"></a>Требования к размеру для сервера обработки масштабирования

| **Дополнительный сервер обработки** | **Размер диска кэша** | **Частота изменения данных** | **Защищенные компьютеры** |
| --- | --- | --- | --- |
|4 виртуальных ЦП (2 сокета * 2 ядра с частотой 2,5 ГГц), 8 ГБ памяти |300 ГБ |250 ГБ или менее |Репликация до 85 компьютеров |
|8 виртуальных ЦП (2 сокета * 4 ядра с частотой 2,5 ГГц), 12 ГБ памяти |600 ГБ |От 250 ГБ до 1 ТБ |Репликация от 85 до 150 компьютеров |
|12 виртуальных ЦП (2 сокета * 6 ядер с частотой 2,5 ГГц), 24 ГБ памяти |1 TБ |От 1 ТБ до 2 ТБ |Репликация от 150 до 225 компьютеров |
