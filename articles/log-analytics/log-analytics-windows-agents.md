---
title: "Подключение компьютеров Windows к Log Analytics | Документация Майкрософт"
description: "В этой статье показано, как подключать компьютеры Windows в локальной инфраструктуре непосредственно к OMS, используя настраиваемую версию Microsoft Monitoring Agent (MMA)."
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: jwhit
editor: 
ms.assetid: 932f7b8c-485c-40c1-98e3-7d4c560876d2
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/08/2016
ms.author: banders
translationtype: Human Translation
ms.sourcegitcommit: 6836cd4c1f9fe53691ae8330b25e497da4c2e0d5
ms.openlocfilehash: 161bb18579db7a4615cbc62c539e8b6286a424ac


---
# <a name="connect-windows-computers-to-log-analytics"></a>Подключение компьютеров Windows к Log Analytics

В этой статье приведена процедура подключения компьютеров Windows в локальной инфраструктуре непосредственно к рабочим областям OMS с помощью настраиваемой версии Microsoft Monitoring Agent (MMA). Необходимо установить и подключить агенты для всех компьютеров, которые должны входить в OMS, чтобы те могли отправлять данные в OMS, просматривать эти данные на портале OMS и выполнять операции с этими данными. Каждый агент может передавать данные в несколько рабочих областей.

Установить агенты можно с помощью программы установки, командной строки или DSC (настройки требуемого состояния) в службе автоматизации Azure.  

>[!NOTE]
Для виртуальных машин, запущенных в Azure, установку можно упростить с помощью [расширения виртуальной машины](log-analytics-azure-vm-extension.md).

На компьютерах, подключенных к Интернету, агент будет использовать интернет-подключение для отправки данных в OMS. Для компьютеров, не подключенных к Интернету, можно использовать прокси-сервер или [шлюз OMS](log-analytics-oms-gateway.md).

Подключение компьютеров Windows к OMS выполняется в три простых этапа:

1. Скачайте файл установки агента на портале OMS.
2. Установка агента выбранным способом
3. Настройка агента или добавление дополнительных рабочих областей (при необходимости)

На следующей схеме показана связь между компьютерами Windows и OMS после установки и настройки агентов.

![oms-direct-agent-diagram](./media/log-analytics-windows-agents/oms-direct-agent-diagram.png)


## <a name="system-requirements-and-required-configuration"></a>Системные требования и необходимая конфигурация
Перед установкой или развертыванием агентов просмотрите следующие сведения и убедитесь в том, что необходимые требования выполнены.

- MMA OMS можно устанавливать на компьютеры под управлением Windows Server 2008 SP 1 или более поздней версии либо Windows 7 SP1 или более поздней версии.
- Вам потребуется подписка OMS.  Дополнительные сведения см. в статье [Начало работы с Log Analytics](log-analytics-get-started.md).
- Каждый компьютер Windows должен иметь доступ к Интернету по протоколу HTTPS или к шлюзу OMS. Подключение может быть установлено напрямую, через прокси-сервер или через шлюз OMS.
- MMA OMS можно устанавливать на автономные компьютеры, серверы и виртуальные машины. Если вы хотите подключить к OMS виртуальные машины, размещенные в Azure, см. статью [Подключение виртуальных машин Azure к службе Log Analytics](log-analytics-azure-vm-extension.md).
- Агенту необходимо использовать TCP-порт 443 для различных ресурсов. Дополнительные сведения можно получить в статье [Настройка параметров прокси-сервера и брандмауэра в службе Log Analytics](log-analytics-proxy-firewall.md).

## <a name="download-the-agent-setup-file-from-oms"></a>Загрузка файла установки агента из OMS
1. На портале OMS на странице **Обзор** щелкните плитку **Параметры**.  Откройте расположенную сверху вкладку **Подключенные источники**.  
    ![Вкладка "Подключенные источники"](./media/log-analytics-windows-agents/oms-direct-agent-connected-sources.png)
2. Чтобы скачать файл установки, щелкните **Серверы с Windows** и нажмите кнопку **Скачать агент для Windows**, соответствующую типу процессора на вашем компьютере.
3. Справа от **идентификатора рабочей области**нажмите значок копирования и вставьте идентификатор в Блокнот.
4. Справа от **первичного ключа**нажмите значок копирования и вставьте ключ в Блокнот.     

## <a name="install-the-agent-using-setup"></a>Установка агента с помощью программы установки
1. Запустите программу установки, чтобы установить агент на компьютер, которым требуется управлять.
2. На странице приветствия нажмите кнопку **Далее**.
3. На странице "Условия лицензии" прочтите лицензию и нажмите кнопку **Принимаю**.
4. На странице "Папка назначения" измените или оставьте папку установки по умолчанию и нажмите кнопку **Далее**.
5. На странице "Параметры установки агента" можно подключить агент к Azure Log Analytics (OMS) или Operations Manager либо оставить поле пустым, чтобы настроить агент позднее. Нажмите кнопку **Далее**.   
    - Если выбрано подключение к Azure Log Analytics (OMS), вставьте **идентификатор рабочей области** и **ключ рабочей области (первичный ключ)**, скопированные в Блокнот на предыдущих шагах, и нажмите кнопку **Далее**.  
        ![Вставка идентификатора рабочей области и первичного ключа](./media/log-analytics-windows-agents/connect-workspace.png)
    - Если выбрано подключение к Operations Manager, введите **имя группы управления**, имя **сервера управления** и **порт сервера управления**, затем нажмите кнопку **Далее**. На странице "Учетная запись действия агента" выберите учетную запись Local System или учетную запись локального домена и нажмите кнопку **Далее**.  
        ![конфигурация группы управления](./media/log-analytics-windows-agents/oms-mma-om-setup01.png)![учетная запись действия агента](./media/log-analytics-windows-agents/oms-mma-om-setup02.png)

6. На странице "Готовность к установке" просмотрите выбранные параметры и нажмите кнопку **Установить**.
7. На странице "Настройка успешно завершена" нажмите кнопку **Готово**.
8. После завершения установки на **панели управления** появится **Microsoft Monitoring Agent**. Здесь можно просмотреть конфигурацию и проверить, подключен ли агент к Operational Insights (OMS). При подключении к OMS агент выдает следующее сообщение: **Microsoft Monitoring Agent успешно подключен к службе Microsoft Operations Management Suite.**

## <a name="install-the-agent-using-the-command-line"></a>Установка агента с помощью командной строки
- Измените, а затем используйте следующий пример для установки агента с помощью командной строки.

    >[!NOTE]
    Для обновления агента необходимо использовать API сценариев службы Log Analytics. Сведения об обновлении агента см. в следующем разделе.

    ```
    MMASetup-AMD64.exe /Q:A /R:N /C:"setup.exe /qn ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_ID=<your workspace id> OPINSIGHTS_WORKSPACE_KEY=<your workspace key> AcceptEndUserLicenseAgreement=1"
    ```

## <a name="upgrade-the-agent-and-add-a-workspace-using-a-script"></a>Обновление агента и добавление рабочей области с помощью сценария
Можно обновить агент и добавить рабочую область с помощью API сценариев службы Log Analytics, как показано в следующем примере команды PowerShell.

```
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.AddCloudWorkspace($workspaceId, $workspaceKey)
$mma.ReloadConfiguration()
```

>[!NOTE]
Если ранее вы использовали командную строку или сценарий для установки или настройки агента, теперь вместо `EnableAzureOperationalInsights` используется `AddCloudWorkspace`.

## <a name="install-the-agent-using-dsc-in-azure-automation"></a>Установка агента с помощью DSC в службе автоматизации Azure

>[!NOTE]
В этом примере процедура и сценарий не выполняют обновление существующего агента.

1. Импортируйте модуль DSC xPSDesiredStateConfiguration со страницы [http://www.powershellgallery.com/packages/xPSDesiredStateConfiguration](http://www.powershellgallery.com/packages/xPSDesiredStateConfiguration) в службу автоматизации Azure.  
2.  Создайте в службе автоматизации Azure ресурсы-контейнеры для переменных *OPSINSIGHTS_WS_ID* и *OPSINSIGHTS_WS_KEY*. В качестве значения параметра *OPSINSIGHTS_WS_ID* укажите идентификатор рабочей области Log Analytics в OMS, а для параметра *OPSINSIGHTS_WS_KEY* — первичный ключ рабочей области.
3.  Воспользуйтесь приведенным ниже сценарием и сохраните его под именем MMAgent.ps1
4.  Измените, а затем используйте следующий пример для установки агента с помощью DSC в службе автоматизации Azure. Импортируйте MMAgent.ps1 в службу автоматизации Azure с помощью интерфейса службы или командлета.
5.  Назначьте узел конфигурации. В течение 15 минут узел проверит свою конфигурацию, а MMA будет помещен в узел.

```
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
            Uri = "http://download.microsoft.com/download/0/C/0/0C072D6E-F418-4AD4-BCB2-A362624F400A/MMASetup-AMD64.exe"
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

## <a name="optionally-configure-agents-to-use-the-oms-gateway"></a>Настройка агентов на использование шлюза OMS (необязательно)

Серверы и клиенты, не подключенные к Интернету, могут отправлять данные в OMS через шлюз OMS.  При использовании шлюза все данные из агентов отправляются через один и тот же сервер с доступом к Интернету. Шлюз передает данные из агентов в OMS напрямую, не анализируя их.

Дополнительные сведения о шлюзе, а также о его установке и настройке см. в статье [Подключения компьютеров и устройств к OMS с помощью шлюза OMS](log-analytics-oms-gateway.md).

Сведения о настройке агентов для использования прокси-сервера (в нашем случае шлюза OMS) см. в статье [Настройка параметров прокси-сервера и брандмауэра в службе Log Analytics](log-analytics-proxy-firewall.md).

## <a name="optionally-configure-proxy-and-firewall-settings"></a>Настройка параметров прокси-сервера и брандмауэра (необязательно)
Если в вашей среде есть прокси-серверы или брандмауэры, ограничивающие доступ к Интернету, включите агенты для обмена данными со службой OMS, как указано в статье [Настройка параметров прокси-сервера и брандмауэра в службе Log Analytics](log-analytics-proxy-firewall.md) .

## <a name="next-steps"></a>Дальнейшие действия

- [добавьте решения Log Analytics из коллекции решений](log-analytics-add-solutions.md) .
- [Настройка параметров прокси-сервера и брандмауэра в службе Log Analytics](log-analytics-proxy-firewall.md) , чтобы агенты могли взаимодействовать со службой Log Analytics.



<!--HONumber=Nov16_HO3-->


