---
title: "Подключение компьютеров Windows к Azure Log Analytics | Документация Майкрософт"
description: "В этой статье приведена процедура подключения компьютеров Windows в локальной инфраструктуре к службе Log Analytics с помощью настраиваемой версии Microsoft Monitoring Agent (MMA)."
services: log-analytics
documentationcenter: 
author: mgoedtel
manager: carmonm
editor: 
ms.assetid: 932f7b8c-485c-40c1-98e3-7d4c560876d2
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/03/2017
ms.author: magoedte
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: aaf3e596f8c287c60531a6911c5797b3de26e570
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/18/2017
---
# <a name="connect-windows-computers-to-the-log-analytics-service-in-azure"></a>Подключение компьютеров Windows к службе Log Analytics в Azure

В этой статье приведена процедура подключения компьютеров Windows в локальной инфраструктуре к рабочим областям OMS с помощью настраиваемой версии Microsoft Monitoring Agent (MMA). Необходимо установить и подключить агенты для всех компьютеров, которые должны быть присоединены, чтобы те могли отправлять данные в службу Log Analytics, просматривать эти данные и выполнять с ними операции. Каждый агент может передавать данные в несколько рабочих областей.

Установить агенты можно с помощью программы установки, командной строки или DSC (настройки требуемого состояния) в службе автоматизации Azure.  

>[!NOTE]
Для виртуальных машин, запущенных в Azure, установку можно упростить с помощью [расширения виртуальной машины](../virtual-machines/windows/extensions-oms.md).

На компьютерах, подключенных к Интернету, агент использует это подключение для отправки данных в OMS. Для компьютеров, не подключенных к Интернету, можно использовать прокси-сервер или [шлюз OMS](log-analytics-oms-gateway.md).

Подключение компьютеров Windows к OMS выполняется в три простых этапа.

1. Скачайте файл установки агента на портале OMS.
2. Установка агента выбранным способом
3. Настройка агента или добавление дополнительных рабочих областей (при необходимости)

На следующей схеме показана связь между компьютерами Windows и OMS после установки и настройки агентов.

![oms-direct-agent-diagram](./media/log-analytics-windows-agents/oms-direct-agent-diagram.png)

Если установленные политики безопасности ИТ не разрешают компьютерам в вашей сети подключаться к Интернету, можно настроить эти компьютеры для подключения к шлюзу OMS. Дополнительные сведения и инструкции по настройке серверов для обмена данными со службой OMS через шлюз OMS см. в статье [Подключения компьютеров к OMS с помощью шлюза OMS без доступа к Интернету](log-analytics-oms-gateway.md).

## <a name="system-requirements-and-required-configuration"></a>Системные требования и необходимая конфигурация
Перед установкой или развертыванием агентов просмотрите следующие сведения и убедитесь в том, что требования выполнены.

- MMA OMS можно устанавливать на компьютеры под управлением Windows Server 2008 SP 1 или более поздней версии либо Windows 7 SP1 или более поздней версии.
- Вам понадобится подписка Azure.  Дополнительные сведения см. в статье [Начало работы с Log Analytics](log-analytics-get-started.md).
- Каждый компьютер Windows должен иметь доступ к Интернету по протоколу HTTPS или к шлюзу OMS. Подключение может быть установлено напрямую, через прокси-сервер или через шлюз OMS.
- MMA OMS можно устанавливать на автономные компьютеры, серверы и виртуальные машины. Если вы хотите подключить к OMS виртуальные машины, размещенные в Azure, см. статью [Подключение виртуальных машин Azure к службе Log Analytics](log-analytics-azure-vm-extension.md).
- Агенту необходимо использовать TCP-порт 443 для различных ресурсов.

### <a name="network"></a>Сеть

Чтобы агенты Windows могли подключиться к службе OMS и зарегистрироваться в ней, им нужен доступ к сетевым ресурсам, включая номера портов и URL-адреса доменов.

- Для прокси-серверов необходимо убедиться, что в параметрах агента настроены соответствующие ресурсы прокси-сервера.
- Если используются брандмауэры, ограничивающие доступ к Интернету, вам или вашим сетевым инженерам следует настроить их таким образом, чтобы разрешить доступ к OMS. Настройка параметров агента не требуется.

В следующей таблице показаны ресурсы, необходимые для обмена данными.

>[!NOTE]
>В некоторых из ресурсов упоминается Operational Insights — предыдущее название Log Analytics.

| Ресурс агента | порты; | Обход проверки HTTPS |
|---|---|---|
| *.ods.opinsights.azure.com | 443 | Да |
| *.oms.opinsights.azure.com | 443 | Да |
| *.blob.core.windows.net | 443 | Да |
| *.azure-automation.net | 443 | Да |



## <a name="download-the-agent-setup-file-from-oms"></a>Загрузка файла установки агента из OMS
1. На [портале OMS](https://www.mms.microsoft.com) на странице **Обзор** щелкните плитку **Параметры**.  Откройте расположенную сверху вкладку **Подключенные источники**.  
    ![Вкладка "Подключенные источники"](./media/log-analytics-windows-agents/oms-direct-agent-connected-sources.png)
2. Чтобы скачать файл установки, щелкните **Серверы с Windows** и нажмите кнопку **Скачать агент для Windows**, соответствующую типу процессора на вашем компьютере.
3. Справа от **идентификатора рабочей области**нажмите значок копирования и вставьте идентификатор в Блокнот.
4. Справа от **первичного ключа**нажмите значок копирования и вставьте ключ в Блокнот.     

## <a name="install-the-agent-using-setup"></a>Установка агента с помощью программы установки
1. Запустите программу установки, чтобы установить агент на компьютер, которым требуется управлять.
2. На странице приветствия нажмите кнопку **Далее**.
3. На странице "Условия лицензии" прочтите лицензию и нажмите кнопку **Принимаю**.
4. На странице "Папка назначения" измените или оставьте папку установки по умолчанию и нажмите кнопку **Далее**.
5. На странице "Параметры установки агента" можно подключить агент к Azure Log Analytics (OMS) или Operations Manager либо оставить поле пустым, чтобы настроить агент позднее. Щелкните **Далее**.   
    - Если выбрано подключение к Azure Log Analytics (OMS), вставьте **идентификатор рабочей области** и **ключ рабочей области (первичный ключ)**, скопированные в Блокнот на предыдущих шагах, и нажмите кнопку **Далее**.  
        ![Вставка идентификатора рабочей области и первичного ключа](./media/log-analytics-windows-agents/connect-workspace.png)
    - Если выбрано подключение к Operations Manager, введите **имя группы управления**, имя **сервера управления** и **порт сервера управления**, затем нажмите кнопку **Далее**. На странице "Учетная запись действия агента" выберите учетную запись Local System или учетную запись локального домена и нажмите кнопку **Далее**.  
        ![конфигурация группы управления](./media/log-analytics-windows-agents/oms-mma-om-setup01.png)![учетная запись действия агента](./media/log-analytics-windows-agents/oms-mma-om-setup02.png)

6. На странице "Готовность к установке" просмотрите выбранные параметры и нажмите кнопку **Установить**.
7. На странице "Настройка успешно завершена" нажмите кнопку **Готово**.
8. После завершения установки на **панели управления** появится **Microsoft Monitoring Agent**. Здесь можно просмотреть конфигурацию и проверить, подключен ли агент к Operational Insights (OMS). При подключении к OMS агент выдает следующее сообщение: **Microsoft Monitoring Agent успешно подключен к службе Microsoft Operations Management Suite.**

## <a name="configure-proxy-settings"></a>Настройка параметров прокси

Далее описано, как настроить параметры прокси-сервера для Microsoft Monitoring Agent с помощью панели управления. Эту процедуру необходимо использовать для каждого сервера. Если вам нужно настроить несколько серверов, возможно, проще использовать скрипт для автоматизации этого процесса. Далее описано, [как настроить параметры прокси-сервера для Microsoft Monitoring Agent с помощью скрипта](#to-configure-proxy-settings-for-the-microsoft-monitoring-agent-using-a-script).

### <a name="to-configure-proxy-settings-for-the-microsoft-monitoring-agent-using-control-panel"></a>Настройка параметров прокси-сервера для Microsoft Monitoring Agent с помощью панели управления
1. Откройте **Панель управления**.
2. Откройте **Microsoft Monitoring Agent**.
3. Перейдите на вкладку **Параметры прокси-сервера**.  
    ![вкладка параметров прокси-сервера](./media/log-analytics-windows-agents/proxy-direct-agent-proxy.png)
4. Выберите **Использовать прокси-сервер** и введите URL-адрес и номер порта (если он нужен), как показано в примере. Если для доступа к прокси-серверу требуется проверка подлинности, введите имя пользователя и пароль.


### <a name="verify-agent-connectivity-to-oms"></a>Проверка подключения к агенту OMS

Можно легко проверить, могут ли агенты обмениваться данными с OMS, воспользовавшись следующей процедурой.

1.  На компьютере, на котором установлен агент Windows, откройте панель управления.
2.  Откройте "Microsoft Monitoring Agent".
3.  Перейдите на вкладку "Azure Log Analytics (OMS)".
4.  В столбце "Состояние" вы увидите, что агент успешно подключен к службе Operations Management Suite.

![Агент подключен](./media/log-analytics-windows-agents/mma-connected.png)


### <a name="to-configure-proxy-settings-for-the-microsoft-monitoring-agent-using-a-script"></a>как настроить параметры прокси-сервера для Microsoft Monitoring Agent с помощью скрипта
Скопируйте следующий пример, измените его, указав данные своей среды, сохраните его в файл с расширением PS1, затем выполните сценарий на каждом компьютере, который подключается непосредственно к службе OMS.

    param($ProxyDomainName="http://proxy.contoso.com:80", $cred=(Get-Credential))

    # First we get the Health Service configuration object.  We need to determine if we
    #have the right update rollup with the API we need.  If not, no need to run the rest of the script.
    $healthServiceSettings = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'

    $proxyMethod = $healthServiceSettings | Get-Member -Name 'SetProxyInfo'

    if (!$proxyMethod)
    {
         Write-Output 'Health Service proxy API not present, will not update settings.'
         return
    }

    Write-Output "Clearing proxy settings."
    $healthServiceSettings.SetProxyInfo('', '', '')

    $ProxyUserName = $cred.username

    Write-Output "Setting proxy to $ProxyDomainName with proxy username $ProxyUserName."
    $healthServiceSettings.SetProxyInfo($ProxyDomainName, $ProxyUserName, $cred.GetNetworkCredential().password)



## <a name="install-the-agent-using-the-command-line"></a>Установка агента с помощью командной строки
- Измените, а затем используйте следующий пример для установки агента с помощью командной строки. В примере выполняется полностью автоматическая установка.

    >[!NOTE]
    Для обновления агента необходимо использовать API сценариев службы Log Analytics. Сведения об обновлении агента см. в следующем разделе.

    ```dos
    MMASetup-AMD64.exe /Q:A /R:N /C:"setup.exe /qn ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_ID=<your workspace id> OPINSIGHTS_WORKSPACE_KEY=<your workspace key> AcceptEndUserLicenseAgreement=1"
    ```

Агент использует IExpress в качестве самоизвлечения с помощью команды `/c`. Воспользуйтесь параметрами командной строки в разделе [Параметры командной строки для IExpress](https://support.microsoft.com/help/197147/command-line-switches-for-iexpress-software-update-packages) и затем измените пример в соответствии со своими потребностями.

|Параметры MMA                   |Примечания         |
|---------------------------------------|--------------|
|ADD_OPINSIGHTS_WORKSPACE               | 1 — настройка агента для передачи отчетов в рабочую область.                |
|OPINSIGHTS_WORKSPACE_ID                | Идентификатор добавляемой рабочей области (GUID).                    |
|OPINSIGHTS_WORKSPACE_KEY               | Ключ рабочей области, используемый для первоначальной аутентификации в рабочей области. |
|OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE  | Укажите облачную среду, в которой находится рабочая область. <br> 0 — коммерческое облако Azure (по умолчанию). <br> 1 — Azure для государственных организаций. |
|OPINSIGHTS_PROXY_URL               | Универсальный код ресурса (URI) для прокси-сервера. |
|OPINSIGHTS_PROXY_USERNAME               | Имя пользователя для доступа к прокси-серверу после аутентификации. |
|OPINSIGHTS_PROXY_PASSWORD               | Пароль для доступа к прокси-серверу после аутентификации. |

>[!NOTE]
Чтобы избежать достижения ограничения длины командной строки IExpress, установите агента, не настраивая рабочую область, и затем с помощью сценария настройте конфигурацию рабочей области.

>[!NOTE]
Если при использовании параметра `OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE` появляется сообщение `Command line option syntax error.`, можно выполнить приведенный ниже способ обхода.
```dos
MMASetup-AMD64.exe /C /T:.\MMAExtract
cd .\MMAExtract
setup.exe /qn ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE=1 OPINSIGHTS_WORKSPACE_ID=<your workspace id> OPINSIGHTS_WORKSPACE_KEY=<your workspace key> AcceptEndUserLicenseAgreement=1
```

## <a name="add-a-workspace-using-a-script"></a>Добавление рабочей области с помощью сценария
Добавьте рабочую область с помощью API сценариев службы Log Analytics, как показано в следующем примере.

```PowerShell
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.AddCloudWorkspace($workspaceId, $workspaceKey)
$mma.ReloadConfiguration()
```

Чтобы добавить рабочую область в Azure для государственных организаций США, используйте следующий сценарий.
```PowerShell
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.AddCloudWorkspace($workspaceId, $workspaceKey, 1)
$mma.ReloadConfiguration()
```

>[!NOTE]
Если ранее вы использовали командную строку или сценарий для установки или настройки агента, теперь вместо `EnableAzureOperationalInsights` используется `AddCloudWorkspace`.

## <a name="install-the-agent-using-dsc-in-azure-automation"></a>Установка агента с помощью DSC в службе автоматизации Azure

Вы можете использовать следующий пример сценария для установки агента с помощью DSC в службе автоматизации Azure. В примере выполняется установка 64-разрядного агента, идентифицируемого значением `URI`. Вы также можете использовать 32-разрядную версию, заменив значение универсального кода ресурса (URI). URI для обеих версий:

- 64-разрядный агент Windows: https://go.microsoft.com/fwlink/?LinkId=828603
- 32-разрядный агент Windows: https://go.microsoft.com/fwlink/?LinkId=828604


>[!NOTE]
В этом примере процедура и сценарий не выполняют обновление существующего агента.

1. Импортируйте модуль DSC xPSDesiredStateConfiguration со страницы [http://www.powershellgallery.com/packages/xPSDesiredStateConfiguration](http://www.powershellgallery.com/packages/xPSDesiredStateConfiguration) в службу автоматизации Azure.  
2.  Создайте в службе автоматизации Azure ресурсы-контейнеры для переменных *OPSINSIGHTS_WS_ID* и *OPSINSIGHTS_WS_KEY*. В качестве значения параметра *OPSINSIGHTS_WS_ID* укажите идентификатор рабочей области Log Analytics в OMS, а для параметра *OPSINSIGHTS_WS_KEY* — первичный ключ рабочей области.
3.  Скопируйте приведенный ниже сценарий и сохраните его как файл MMAgent.ps1.
4.  Измените, а затем используйте следующий пример для установки агента с помощью DSC в службе автоматизации Azure. Импортируйте MMAgent.ps1 в службу автоматизации Azure с помощью интерфейса службы или командлета.
5.  Назначьте узел конфигурации. В течение 15 минут узел проверит свою конфигурацию, а MMA будет помещен в узел.

```PowerShell
Configuration MMAgent
{
    $OIPackageLocalPath = "C:\MMASetup-AMD64.exe"
    $OPSINSIGHTS_WS_ID = Get-AutomationVariable -Name "OPSINSIGHTS_WS_ID"
    $OPSINSIGHTS_WS_KEY = Get-AutomationVariable -Name "OPSINSIGHTS_WS_KEY"


    Import-DscResource -ModuleName xPSDesiredStateConfiguration

    Node OMSnode {
        Service OIService
        {
            Name = "HealthService"
            State = "Running"
            DependsOn = "[Package]OI"
        }

        xRemoteFile OIPackage {
            Uri = "https://go.microsoft.com/fwlink/?LinkId=828603"
            DestinationPath = $OIPackageLocalPath
        }

        Package OI {
            Ensure = "Present"
            Path  = $OIPackageLocalPath
            Name = "Microsoft Monitoring Agent"
            ProductId = "8A7F2C51-4C7D-4BFD-9014-91D11F24AAE2"
            Arguments = '/C:"setup.exe /qn ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_ID=' + $OPSINSIGHTS_WS_ID + ' OPINSIGHTS_WORKSPACE_KEY=' + $OPSINSIGHTS_WS_KEY + ' AcceptEndUserLicenseAgreement=1"'
            DependsOn = "[xRemoteFile]OIPackage"
        }
    }
}


```

### <a name="get-the-latest-productid-value"></a>Получение последнего значения ProductId

`ProductId value` в сценарии MMAgent.ps1 уникален для каждой версии агента. При публикации обновленной версии каждого агента значение ProductId изменяется. Таким образом, когда ProductId изменится в будущем, версию агента можно найти с помощью простого сценария. Установив последнюю версию агента на тестовом сервере, получите установленное значение ProductId, выполнив следующий сценарий. Используя последнее значение ProductId, можно обновить значение в сценарии MMAgent.ps1.

```PowerShell
$InstalledApplications  = Get-ChildItem hklm:\SOFTWARE\Microsoft\Windows\CurrentVersion\Uninstall


foreach ($Application in $InstalledApplications)

{

     $Key = Get-ItemProperty $Application.PSPath

     if ($Key.DisplayName -eq "Microsoft Monitoring Agent")

     {

        $Key.DisplayName

        Write-Output ("Product ID is: " + $Key.PSChildName.Substring(1,$Key.PSChildName.Length -2))

     }

}  
```

## <a name="configure-an-agent-manually-or-add-additional-workspaces"></a>Настройка агента вручную или добавление дополнительных рабочих областей
Если вы установили, но не настроили агенты, или хотите, чтобы агенты передавали данные в несколько рабочих областей, вы можете использовать указанные ниже сведения для включения или перенастройки агентов. После настройки агент будет зарегистрирован в службе агента и получит необходимые данные конфигурации и пакеты управления, содержащие сведения о решении.

1. После установки Microsoft Monitoring Agent откройте **панель управления**.
2. Откройте агент **Microsoft Monitoring Agent** и перейдите на вкладку **Azure Log Analytics (OMS)**.   
3. Щелкните **Добавить**, чтобы открыть диалоговое окно **Добавление рабочей области Log Analytics**.
4. Вставьте **идентификатор рабочей области** и **ключ рабочей области (первичный ключ)** для добавляемой рабочей области, скопированные в Блокнот в предыдущей процедуре, и нажмите кнопку **ОК**.  
    ![Настройка Operational Insights](./media/log-analytics-windows-agents/add-workspace.png)

После сбора данных с компьютеров, отслеживаемых агентом, количество компьютеров, отслеживаемых OMS, отобразится на портале OMS на вкладке **Подключенные источники** в разделе **Параметры** как **Подключенные серверы**.


## <a name="to-disable-an-agent"></a>Отключение агента
1. После установки агента откройте **панель управления**.
2. Откройте агент Microsoft Monitoring Agent и перейдите на вкладку **Azure Log Analytics (OMS)** .
3. Выберите рабочую область и щелкните **Удалить**. Повторите этот шаг для всех рабочих областей.


## <a name="optionally-configure-agents-to-report-to-an-operations-manager-management-group"></a>Также можно настроить агенты таким образом, чтобы они отчитывались перед группой управления Operations Manager.

Если в ИТ-инфраструктуре используется Operations Manager, в качестве агента Operations Manager можно также использовать агент MMA.

### <a name="to-configure-mma-agents-to-report-to-an-operations-manager-management-group"></a>Настройка агентов MMA для отчетности перед группой управления Operations Manager
1.  На компьютере, где установлен агент, откройте **панель управления**.  
2.  Откройте **Microsoft Monitoring Agent** и перейдите на вкладку **Operations Manager**.  
    ![Вкладка "Operations Manager в Microsoft Monitoring Agent"](./media/log-analytics-windows-agents/om-mg01.png)
3.  Если серверы Operations Manager интегрированы с Active Directory, установите флажок **Автоматически обновлять назначения групп управления из AD DS**.
4.  Нажмите кнопку **Добавить**, чтобы открыть диалоговое окно **Добавление группы управления**.  
    ![Добавление группы управления в Microsoft Monitoring Agent](./media/log-analytics-windows-agents/oms-mma-om02.png)
5.  В поле **Имя группы управления** введите имя группы управления.
6.  В поле **Основной сервер управления** введите имя компьютера основного сервера управления.
7.  В поле **Порт сервера управления** введите номер порта TCP.
8.  На странице **Учетная запись действия агента**выберите учетную запись Local System или учетную запись локального домена.
9.  Нажмите кнопку **ОК**, чтобы закрыть диалоговое окно **Добавление группы управления**, и еще раз нажмите **ОК**, чтобы закрыть диалоговое окно **Свойства Microsoft Monitoring Agent**.


## <a name="next-steps"></a>Дальнейшие действия

- [добавьте решения Log Analytics из коллекции решений](log-analytics-add-solutions.md) .
