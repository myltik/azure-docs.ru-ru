---
title: " Управление сервером конфигурации для аварийного восстановления физических серверов с помощью Azure Site Recovery | Документация Майкрософт"
description: Из этой статьи вы узнаете, как управлять сервером конфигурации для аварийного восстановления физических серверов в Azure с помощью службы Azure Site Recovery.
services: site-recovery
author: AnoopVasudavan
ms.service: site-recovery
ms.topic: article
ms.date: 04/11/2018
ms.author: anoopkv
ms.openlocfilehash: 580d32a51f6b38916ddccd46784b80b1179c29c4
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/19/2018
ms.locfileid: "31598869"
---
# <a name="manage-the-configuration-server-for-physical-server-disaster-recovery"></a>Управление сервером конфигурации для аварийного восстановления физических серверов

При использовании службы [Azure Site Recovery](site-recovery-overview.md) для аварийного восстановления физических серверов в Azure настраивается локальный сервер конфигурации. Сервер конфигурации координирует обмен данными между локальными компьютерами и Azure, а также управляет репликацией данных. В этой статье перечислены распространенные задачи управления сервером конфигурации после его развертывания.

## <a name="prerequisites"></a>предварительным требованиям

В приведенной ниже таблице перечислены предварительные требования для развертывания серверного компьютера в локальной конфигурации.

| **Компонент** | **Требование** |
| --- |---|
| Ядра ЦП| 8 |
| ОЗУ | 16 ГБ|
| Количество дисков | 3 (диск ОС, диск кэша сервера обработки, диск хранения (для восстановления размещения)) |
| Свободное место на диске (кэш сервера обработки) | 600 ГБ
| Свободное место на диске (диск хранения) | 600 ГБ|
| Операционная система  | Windows Server 2012 R2 <br> Windows Server 2016 |
| Язык операционной системы | Английский (США)|
| Версия VMware vSphere PowerCLI | [PowerCLI 6.0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1 "PowerCLI 6.0")|
| Роли Windows Server | Не включайте эти роли: <br> — доменные службы Active Directory; <br>— службы IIS; <br> — Hyper-V. |
| Групповые политики| Не включать эти групповые политики: <br> — запрет на использование командной строки; <br> — запрет на использование инструментов редактирования реестра; <br> — логика доверия для вложенных файлов; <br> — включение выполнения сценариев; <br> [Подробнее](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)|
| IIS | — Должен отсутствовать предварительно созданный веб-сайт по умолчанию. <br> — включите [анонимную аутентификацию](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx); <br> — включите параметр [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx);  <br> — Должен отсутствовать предварительно созданный веб-сайт или приложение, ожидающее передачи данных на порте 443.<br>|
| Тип сетевой карты | VMXNET3 (при развертывании в качестве виртуальной машины VMware) |
| Тип IP-адреса | Статическое |
| Доступ к Интернету | Сервер должен иметь доступ к этим URL-адресам: <br> - \*.accesscontrol.windows.net<br> - \*.backup.windowsazure.com <br>- \*.store.core.windows.net<br> - \*.blob.core.windows.net<br> - \*.hypervrecoverymanager.windowsazure.com <br> - dc.services.visualstudio.com <br> — https://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi (не требуется для сервера обработки масштабирования) <br> time.nist.gov <br> time.windows.com |
| порты; | 443 (оркестрация канала управления)<br>9443 (передача данных)|

## <a name="download-the-latest-installation-file"></a>Скачивание последней версии файла установки

Последняя версия файла установки для сервера конфигурации доступна на портале Site Recovery. Также ее можно скачать непосредственно из [Центра загрузки Майкрософт](http://aka.ms/unifiedsetup).

1. Войдите на портал Azure и перейдите в хранилище служб восстановления.
2. Откройте **Инфраструктура Site Recovery** > **Серверы конфигурации** (в разделе VMware и физических компьютеров).
3. Нажмите кнопку **+Серверы**.
4. На странице **добавления сервера** нажмите кнопку "Скачать", чтобы скачать регистрационный ключ. Этот ключ требуется при установке сервера конфигурации для его регистрации в службе Azure Site Recovery.
5. Щелкните ссылку **Download the Microsoft Azure Site Recovery Unified Setup** (Скачать единый файл установки Microsoft Azure Site Recovery), чтобы скачать последнюю версию сервера конфигурации.

  ![Страница загрузки](./media/physical-manage-configuration-server/downloadcs.png)


## <a name="install-and-register-the-server"></a>Установка и регистрация сервера

1. Запустите файл единой установки.
2. На странице **Перед началом работы** выберите **Install the configuration server and process server** (Установить сервер конфигурации и сервер обработки).

    ![Перед началом работы](./media/physical-manage-configuration-server/combined-wiz1.png)

3. В окне **Third-Party Software License** (Лицензия на программное обеспечение стороннего поставщика) щелкните **I Accept** (Принимаю), чтобы скачать и установить MySQL.
4. В окне **Internet Settings** (Параметры Интернета) укажите параметры для подключения поставщика, который будет выполняться на сервере конфигурации, к Azure Site Recovery через Интернет. Убедитесь, что вам предоставлены необходимые URL-адреса.

    - Чтобы подключаться с помощью прокси-сервера, который уже настроен на компьютере, выберите **Подключиться к Azure Site Recovery с использованием прокси-сервера**.
    - Чтобы поставщик подключался напрямую, выберите **Подключиться к Azure Site Recovery напрямую без прокси-сервера**.
    - Если для имеющегося прокси-сервера требуется аутентификация или для подключения поставщика нужно использовать пользовательский прокси-сервер, установите переключатель **Connect with custom proxy settings** (Подключение с параметрами пользовательского прокси-сервера), а затем укажите адрес, порт и учетные данные.
     ![Брандмауэр](./media/physical-manage-configuration-server/combined-wiz4.png)
6. В окне **Проверка необходимых компонентов** программа установки проверяет возможность установки. Если появится предупреждение о **проверке глобальной синхронизации времени**, убедитесь, что время системных часов (параметры **даты и времени**) соответствует часовому поясу.

    ![предварительным требованиям](./media/physical-manage-configuration-server/combined-wiz5.png)
7. На странице **Конфигурация MySQL** создайте учетные данные для входа в экземпляр сервера MySQL, который будет установлен.

    ![MySQL](./media/physical-manage-configuration-server/combined-wiz6.png)
8. На странице **Сведения о среде** укажите, нужно ли выполнять репликацию виртуальных машин VMware. Если нужно, программа установки проверяет, установлен ли компонент PowerCLI 6.0.
9. На странице **Расположение установки** выберите место для установки двоичных файлов и хранения кэша. Выбранный вами диск кэша должен иметь не менее 5 ГБ доступной памяти. Однако рекомендуемый объем — не менее 600 ГБ.

    ![Расположение установки](./media/physical-manage-configuration-server/combined-wiz8.png)
10. На странице **Выбор сети** укажите прослушиватель (сетевой адаптер и порт SSL), через который сервер конфигурации будет отправлять и получать данные репликации. Порт 9443 — это стандартный порт для отправки и приема трафика репликации, но вы можете изменить номер порта в соответствии с требованиями среды. Наряду с портом 9443 мы также открываем порт 443, который используется веб-сервером для оркестрации операций репликации. Не следует использовать порт 443 для отправки и приема трафика репликации.

    ![Выбор сети](./media/physical-manage-configuration-server/combined-wiz9.png)


11. Просмотрите информацию на странице **Сводка** и нажмите кнопку **Установить**. Когда установка завершится, будет создана парольная фраза. Они потребуются при включении репликации, поэтому ее необходимо скопировать и сохранить в безопасном месте.


После ее завершения сервер появится в колонке **Параметры** > **Серверы** в хранилище.


## <a name="install-from-the-command-line"></a>Установка из командной строки

Запустите файл установки следующим образом:

  ```
  UnifiedSetup.exe [/ServerMode <CS/PS>] [/InstallDrive <DriveLetter>] [/MySQLCredsFilePath <MySQL credentials file path>] [/VaultCredsFilePath <Vault credentials file path>] [/EnvType <VMWare/NonVMWare>] [/PSIP <IP address to be used for data transfer] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>]
  ```

### <a name="sample-usage"></a>Пример использования
  ```
  MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /xC:\Temp\Extracted
  cd C:\Temp\Extracted
  UNIFIEDSETUP.EXE /AcceptThirdpartyEULA /servermode "CS" /InstallLocation "D:\" /MySQLCredsFilePath "C:\Temp\MySQLCredentialsfile.txt" /VaultCredsFilePath "C:\Temp\MyVault.vaultcredentials" /EnvType "VMWare"
  ```


### <a name="parameters"></a>Параметры

|Имя параметра| type | ОПИСАНИЕ| Значения|
|-|-|-|-|
| /ServerMode|Обязательно|Указывает, нужно ли установить и сервер конфигурации, и сервер обработки или только север обработки.|CS<br>PS|
|/InstallLocation|Обязательно|Папка для установки компонентов.| Любая папка на компьютере.|
|/MySQLCredsFilePath|Обязательно|Путь к файлу, в котором хранятся учетные данные сервера MySQL.|Этот файл должен быть в формате, указанном ниже.|
|/VaultCredsFilePath|Обязательно|Путь к файлу с учетными данными хранилища.|Допустимый путь к файлу.|
|/EnvType|Обязательно|Тип среды, которую необходимо защитить. |VMware<br>NonVMware|
|/PSIP|Обязательно|IP-адрес сетевой карты для передачи данных репликации.| Любой допустимый IP-адрес|
|/CSIP|Обязательно|IP-адрес сетевой карты, передачу данных через который ожидает сервер конфигурации.| Любой допустимый IP-адрес|
|/PassphraseFilePath|Обязательно|Полный путь к расположению файла с парольной фразой.|Допустимый путь к файлу.|
|/BypassProxy|Необязательно|Указывает, что сервер конфигурации подключается к Azure без использования прокси-сервера.|Следует получить это значение от Venu.|
|/ProxySettingsFilePath|Необязательно|Параметры прокси-сервера (прокси-сервер по умолчанию с обязательной аутентификацией или пользовательский прокси-сервер).|Этот файл должен быть в формате, указанном ниже.|
|DataTransferSecurePort|Необязательно|Номер порта PSIP для данных репликации.| Допустимый номер порта (значение по умолчанию — 9433).|
|/SkipSpaceCheck|Необязательно|Пропуск проверки места на диске кэша.| |
|/AcceptThirdpartyEULA|Обязательно|Флажок означает принятие лицензионного соглашения между пользователем и сторонним разработчиком.| |
|/ShowThirdpartyEULA|Необязательно|Отображает лицензионное соглашение со сторонним разработчиком. Если оно задано как источник данных, то все остальные параметры игнорируются.| |



### <a name="create-file-input-for-mysqlcredsfilepath"></a>Создание файла входных данных для MYSQLCredsFilePath

Параметр MySQLCredsFilePath принимает в качестве входных данных файл. Создайте файл, используя следующий формат, и передайте его в качестве входных данных для параметра MySQLCredsFilePath.
```
[MySQLCredentials]
MySQLRootPassword = "Password>"
MySQLUserPassword = "Password"
```
### <a name="create-file-input-for-proxysettingsfilepath"></a>Создание файла входных данных для ProxySettingsFilePath
Параметр ProxySettingsFilePath принимает в качестве входных данных файл. Создайте файл, используя следующий формат, и передайте его в качестве входных данных для параметра ProxySettingsFilePath.

```
[ProxySettings]
ProxyAuthentication = "Yes/No"
Proxy IP = "IP Address"
ProxyPort = "Port"
ProxyUserName="UserName"
ProxyPassword="Password"
```
## <a name="modify-proxy-settings"></a>Изменение параметров прокси-сервера

Параметры прокси-сервера для компьютера, выступающего сервером конфигурации, можно изменить следующим образом:

1. Войдите на сервер конфигурации.
2. Запустите файл cspsconfigtool.exe с помощью ярлыка.
3. Откройте вкладку **Vault Registration** (Регистрация хранилища).
4. Скачайте новый файл регистрации хранилища на портале и укажите его в качестве входных данных для средства.

  ![register-configuration-server](./media/physical-manage-configuration-server/register-csconfiguration-server.png)
5. Укажите сведения о новом прокси-сервере и нажмите кнопку **Зарегистрировать**.
6. Откройте командную строку PowerShell с правами администратора.
7. Выполните следующую команду:
  ```
  $pwd = ConvertTo-SecureString -String MyProxyUserPassword
  Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber – ProxyUserName domain\username -ProxyPassword $pwd
  net stop obengine
  net start obengine
  ```

  >[!WARNING]
  Если вы используете дополнительные серверы обработки, подключенные к серверу конфигурации, необходимо [исправить параметры прокси-сервера на всех серверах обработки масштабирования](vmware-azure-manage-process-server.md#modify-proxy-settings-for-an-on-premises-process-server) в развернутой службе.

## <a name="reregister-a-configuration-server-with-the-same-vault"></a>Повторная регистрация сервера конфигурации с тем же хранилищем
  1. Войдите на сервер конфигурации.
  2. Запустите файл cspsconfigtool.exe с помощью ярлыка на рабочем столе.
  3. Откройте вкладку **Vault Registration** (Регистрация хранилища).
  4. Скачайте новый файл регистрации на портале и укажите его в качестве входных данных для средства.
        ![register-configuration-server](./media/physical-manage-configuration-server/register-csconfiguration-server.png)
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
  При наличии нескольких серверов обработки их необходимо [повторно зарегистрировать](vmware-azure-manage-process-server.md#reregister-a-process-server).

## <a name="register-a-configuration-server-with-a-different-vault"></a>Регистрация сервера конфигурации с другим хранилищем

> [!WARNING]
> Если выполнить описанное ниже действие, связь сервера конфигурации с текущим хранилищем будет удалена и репликация защищенных виртуальных машин на сервере конфигурации будет остановлена.

1. Выполните вход на сервер конфигурации.
2. В командной строке от имени администратора выполните следующую команду:

    ```
    reg delete HKLM\Software\Microsoft\Azure Site Recovery\Registration
    net stop dra
    ```
3. Запустите файл cspsconfigtool.exe с помощью ярлыка на рабочем столе.
4. Откройте вкладку **Vault Registration** (Регистрация хранилища).
5. Скачайте новый файл регистрации на портале и укажите его в качестве входных данных для средства.
6. Укажите сведения о прокси-сервере и нажмите кнопку **Register** (Зарегистрировать).  
7. Откройте командную строку PowerShell с правами администратора.
8. Выполните следующую команду
    ```
    $pwd = ConvertTo-SecureString -String MyProxyUserPassword
    Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber – ProxyUserName domain\username -ProxyPassword $pwd
    net stop obengine
    net start obengine
    ```

## <a name="upgrade-a-configuration-server"></a>Обновление сервера конфигурации

Для обновления сервера конфигурации запускаются накопительные пакеты обновления. Обновления применимы к версиям вплоть до N-4. Например: 

- Если вы используете версии 9.7, 9.8, 9.9 или 9.10, можно обновить их непосредственно до версии 9.11.
- Если вы используете версию 9.6 или ниже и хотите обновить ее до версии 9.11, сначала выполните обновление до версии 9.7, а затем — до версии 9.11.

Ссылки на накопительные пакеты обновления для всех версий сервера конфигурации доступны на [вики-странице обновлений](https://social.technet.microsoft.com/wiki/contents/articles/38544.azure-site-recovery-service-updates.aspx).

Инструкции по обновлению сервера:

1. Скачайте файл установщика обновлений на сервер конфигурации.
2. Дважды щелкните, чтобы запустить установщик.
3. Установщик определит текущую версию на компьютере.
4. Нажмите кнопку **ОК** для подтверждения и запустите обновление. 


## <a name="delete-or-unregister-a-configuration-server"></a>Отмена регистрации или удаление сервера конфигурации

> [!WARNING]
> Прежде чем списывать сервер конфигурации, выполните следующие условия.
> 1. [Отключите защиту](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) для всех виртуальных машин на сервере конфигурации.
> 2. [Отмените связь](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) всех политик репликации с сервером конфигурации и [удалите](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) их.
> 3. [Удалите](vmware-azure-manage-vcenter.md#delete-a-vcenter-server) все узлы vSphere и серверы vCenters, связанные с сервером конфигурации.


### <a name="delete-the-configuration-server-from-azure-portal"></a>Удаление сервера конфигурации на портале Azure
1. На портале Azure в меню хранилища откройте последовательно **Инфраструктура Site Recovery** > **Серверы конфигурации**.
2. Выберите сервер конфигурации, который должен быть списан.
3. На странице сведений о сервере конфигурации нажмите кнопку **Удалить**.
4. Нажмите кнопку **Да**, чтобы подтвердить удаление сервера.

### <a name="uninstall-the-configuration-server-and-its-dependencies"></a>Удаление сервера конфигурации и его зависимостей
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

## <a name="delete-or-unregister-a-configuration-server-powershell"></a>Отмена регистрации или удаление сервера конфигурации (PowerShell)

1. [Установите](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-4.4.0) модуль Azure PowerShell.
2. Войдите в свою учетную запись Azure с помощью следующей команды.
    
    `Connect-AzureRmAccount`
3. Выберите подписку, в которой находится хранилище.

     `Get-AzureRmSubscription –SubscriptionName <your subscription name> | Select-AzureRmSubscription`
3.  Теперь настройте контекст хранилища.
    
    ```
    $vault = Get-AzureRmRecoveryServicesVault -Name <name of your vault>
    Set-AzureRmSiteRecoveryVaultSettings -ARSVault $vault
    ```
4. Выберите сервер конфигурации.

    `$fabric = Get-AzureRmSiteRecoveryFabric -FriendlyName <name of your configuration server>`
6. Удалите сервер конфигурации.

    `Remove-AzureRmSiteRecoveryFabric -Fabric $fabric [-Force] `

> [!NOTE]
> Параметр **-Force** в Remove-AzureRmSiteRecoveryFabric можно использовать для принудительного удаления сервера конфигурации.

## <a name="renew-ssl-certificates"></a>Обновление SSL-сертификатов
На сервере конфигурации есть встроенный веб-сервер, который координирует действия службы Mobility Service, серверов обработки и главных целевых серверов, подключенных к этому серверу конфигурации. Веб-сервер использует SSL-сертификат для аутентификации клиентов. Срок действия этого сертификата составляет три года, его можно обновить в любое время.

### <a name="check-expiry"></a>Проверка срока действия

Для серверов конфигурации, развернутых до мая 2016 года, срока действия сертификата составлял один год. При наличии сертификата, срок действия которого истекает, происходит следующее.

- Когда до даты истечения срока действия остается не больше двух месяцев, служба начинает отправлять уведомления на портале и по электронной почте (если вы подписаны на уведомления Azure Site Recovery).
- Баннер уведомления отображается на странице ресурсов хранилища. Щелкните этот баннер для получения дополнительных сведений.
- Если вы видите кнопку **Обновить**, это означает, что некоторые компоненты в вашей среде не обновлены до версии 9.4.xxxx.x или более поздней версии. Обновите компоненты, прежде чем обновлять сертификат. При наличии более ранних версий обновление сертификата будет невозможно.

### <a name="renew-the-certificate"></a>Обновление сертификата

1. В хранилище выберите **Site Recovery Infrastructure (Инфраструктура Site Recovery)** > **Сервер конфигурации** и выберите требуемый сервер конфигурации.
2. Дата окончания срока действия отображается в разделе **Работоспособность сервера конфигурации**.
3. Щелкните **Обновить сертификаты**. 




## <a name="common-issues"></a>Распространенные проблемы
[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]

## <a name="next-steps"></a>Дополнительная информация

Ознакомьтесь с руководствами по настройке аварийного восстановления [физических серверов](tutorial-physical-to-azure.md) в Azure.

