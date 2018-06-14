---
title: Управление агентом Azure Log Analytics | Документация Майкрософт
description: В этой статье описаны разные задачи управления, которые обычно можно выполнять в течение жизненного цикла развернутого на компьютере агента Microsoft Monitoring Agent (MMA).
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/30/2018
ms.author: magoedte
ms.openlocfilehash: 5ff4f79a607143683b37726f1c02a6057dc6b9b0
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/03/2018
ms.locfileid: "30320090"
---
# <a name="managing-and-maintaining-the-log-analytics-agent-for-windows-and-linux"></a>Обслуживание агента Log Analytics для Windows и Linux и управление им

После первоначального развертывания агента Windows или Linux для Log Analytics вам может понадобиться перенастроить его или удалить из компьютера после завершения жизненного цикла (стадия прекращения применения и списания).  Вы можете легко управлять этими задачами обслуживания вручную или автоматизировать этот процесс, сократив при этом эксплуатационные расходы и количество ошибок.

## <a name="adding-or-removing-a-workspace"></a>Добавление или удаление рабочей области 

### <a name="windows-agent"></a>Агент Windows

#### <a name="update-settings-from-control-panel"></a>Обновление параметров на панели управления

1. Войдите в систему компьютера, используя учетную запись с правами администратора.
2. Откройте **Панель управления**.
3. Выберите **Microsoft Monitoring Agent** и перейдите на вкладку **Azure Log Analytics (OMS)**.
4. Чтобы удалить рабочую область, выберите нужную и нажмите кнопку **Удалить**. Повторите этот шаг для всех рабочих областей, которые этот агент больше не должен отслеживать.
5. Чтобы добавить рабочую область, нажмите кнопку **Добавить**, а затем в диалоговом окне **Add a Log Analytics Workspace** (Добавление рабочей области Log Analytics) введите идентификатор и ключ (первичный) рабочей области. Если компьютер должен передавать данные в рабочую область Log Analytics в облаке Azure для государственных организаций, выберите "Azure для государственных организаций США" из раскрывающегося списка "Облако Azure". 
6. Нажмите кнопку **ОК** , чтобы сохранить изменения.

#### <a name="remove-a-workspace-using-powershell"></a>Удаление рабочей области с помощью PowerShell 

```PowerShell
$workspaceId = "<Your workspace Id>"
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.RemoveCloudWorkspace($workspaceId)
$mma.ReloadConfiguration()
```

#### <a name="add-a-workspace-in-azure-commercial-using-powershell"></a>Добавление рабочей области в Azure для коммерческих организаций с помощью PowerShell 

```PowerShell
$workspaceId = "<Your workspace Id>"
$workspaceKey = "<Your workspace Key>”
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.AddCloudWorkspace($workspaceId, $workspaceKey)
$mma.ReloadConfiguration()
```

#### <a name="add-a-workspace-in-azure-for-us-government-using-powershell"></a>Добавление рабочей области в Azure для государственных организаций США с помощью PowerShell 

```PowerShell
$workspaceId = "<Your workspace Id>"
$workspaceKey = "<Your workspace Key>”
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.AddCloudWorkspace($workspaceId, $workspaceKey, 1)
$mma.ReloadConfiguration()
```

>[!NOTE]
>Если ранее вы устанавливали или настраивали агент с помощью командной строки или сценария, теперь вместо `EnableAzureOperationalInsights` используется `AddCloudWorkspace` и `RemoveCloudWorkspace`.
>

### <a name="linux-agent"></a>Агент Linux
Ниже описывается, как перенастроить агент для Linux, если вы решили зарегистрировать его в другой рабочей области или хотите удалить рабочую область из его конфигурации.  

1.  Чтобы убедиться, что он зарегистрирован в рабочей области, выполните следующую команду.

    `/opt/microsoft/omsagent/bin/omsadmin.sh -l` 

    Результат выполнения команды должен иметь следующий вид. 

    `Primary Workspace: <workspaceId>   Status: Onboarded(OMSAgent Running)`

    Важно, чтобы по состоянию было видно, что агент выполняется. В противном случае приведенные ниже шаги по перенастройке агент выполнить не удастся.  

2. Если он уже зарегистрирован в рабочей области, удалите ее, выполнив следующую команду.  Если агент еще не зарегистрирован, перейдите к следующему шагу.

    `/opt/microsoft/omsagent/bin/omsadmin.sh -X`  
    
3. Чтобы зарегистрировать агент в другой рабочей области, выполните команду `/opt/microsoft/omsagent/bin/omsadmin.sh -w <workspace id> -s <shared key> [-d <top level domain>]`. 
4. Чтобы проверить внесенные изменения, выполните следующую команду.

    `/opt/microsoft/omsagent/bin/omsadmin.sh -l` 

    Результат выполнения команды должен иметь следующий вид. 

    `Primary Workspace: <workspaceId>   Status: Onboarded(OMSAgent Running)`

Службу агента не нужно перезапускать, чтобы изменения вступили в силу.

## <a name="update-proxy-settings"></a>Обновление параметров прокси-сервера 
Чтобы настроить взаимодействие агента после развертывания со службой через прокси-сервер или [шлюз OMS](log-analytics-oms-gateway.md), используйте один из приведенных ниже способов.

### <a name="windows-agent"></a>Агент Windows

#### <a name="update-settings-using-control-panel"></a>Обновление параметров с помощью панели управления

1. Войдите в систему компьютера, используя учетную запись с правами администратора.
2. Откройте **Панель управления**.
3. Выберите **Microsoft Monitoring Agent** и перейдите на вкладку **Параметры прокси-сервера**.
4. Щелкните **Использовать прокси-сервер** и укажите URL-адрес и номер порта прокси-сервера или шлюза. Если для доступа к прокси-серверу или шлюзу OMS требуется аутентификация, введите имя пользователя и пароль для аутентификации, а затем нажмите кнопку **ОК**. 

#### <a name="update-settings-using-powershell"></a>Обновление параметров с помощью PowerShell 

Скопируйте следующий образец кода PowerShell, измените в нем сведения, относящиеся к используемой среде, и сохраните его в файле с расширением PS1.  Выполните скрипт на каждом компьютере, подключенном напрямую к службе Log Analytics.

```PowerShell
param($ProxyDomainName="https://proxy.contoso.com:30443", $cred=(Get-Credential))

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
```  

### <a name="linux-agent"></a>Агент Linux
Если компьютерам Linux требуется обмениваться данными с Log Analytics через прокси-сервер или шлюз OMS, выполните приведенные ниже действия.  Значение конфигурации прокси-сервера имеет следующий синтаксис: `[protocol://][user:password@]proxyhost[:port]`.  Свойство *proxyhost* принимает полное доменное имя или IP-адрес прокси-сервера.

1. Измените файл `/etc/opt/microsoft/omsagent/proxy.conf`, выполнив следующие команды и указав собственные значения параметров.

    ```
    proxyconf="https://proxyuser:proxypassword@proxyserver01:30443"
    sudo echo $proxyconf >>/etc/opt/microsoft/omsagent/proxy.conf
    sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/proxy.conf 
    ```

2. Перезапустите агент, выполнив следующую команду. 

    ```
    sudo /opt/microsoft/omsagent/bin/service_control restart [<workspace id>]
    ``` 

## <a name="uninstall-agent"></a>Удаление агента
Чтобы удалить агент Windows или Linux с помощью мастера установки или командной строки, используйте одну из приведенных ниже процедур.

### <a name="windows-agent"></a>Агент Windows

#### <a name="uninstall-from-control-panel"></a>Удаление из панели управления
1. Войдите в систему компьютера, используя учетную запись с правами администратора.  
2. На **панели управления** выберите **Программы и компоненты**.
3. В окне **Программы и компоненты** выберите **Microsoft Monitoring Agent**, щелкните **Удалить**, а затем нажмите кнопку **Да**.

>[!NOTE]
>Мастер настройки агента можно также запустить, дважды щелкнув файл **MMASetup-<platform>.exe**, который можно скачать из рабочей области на портале Azure.

#### <a name="uninstall-from-the-command-line"></a>Удаление из командной строки
Скачанный файл агента — это автономный пакет установки, созданный IExpress.  В нем содержатся программа установки агента и вспомогательные файлы. Чтобы удалить агент с помощью командной строки, содержимое этого пакета нужно извлечь. Ниже описано, как это сделать. 

1. Войдите в систему компьютера, используя учетную запись с правами администратора.  
2. Чтобы извлечь файлы установки агента из командной строки с повышенными привилегиями, запустите файл `extract MMASetup-<platform>.exe`. После этого вы сможете выбрать путь для извлечения файлов.  Кроме того, путь можно указать, передав аргументы `extract MMASetup-<platform>.exe /c:<Path> /t:<Path>`.  Дополнительные сведения о параметрах командной строки, поддерживаемых IExpress, см. в [этой статье](https://support.microsoft.com/help/197147/command-line-switches-for-iexpress-software-update-packages). Затем обновите пример в соответствии со своими потребностями.
3. В командной строке введите следующую команду: `%WinDir%\System32\msiexec.exe /x <Path>:\MOMAgent.msi /qb`.  

### <a name="linux-agent"></a>Агент Linux
Чтобы удалить агент, выполните следующую команду на компьютере Linux.  Аргумент *--purge* полностью удаляет агент и его конфигурацию.

   `wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh --purge`

## <a name="configure-agent-to-report-to-an-operations-manager-management-group"></a>Настройка передачи агентом отчетов в группу управления Operations Manager

### <a name="windows-agent"></a>Агент Windows
Чтобы настроить передачу отчетов агентом OMS для Windows в группу управления System Center Operations Manager, выполните приведенные ниже действия. 

1. Войдите в систему компьютера, используя учетную запись с правами администратора.
2. Откройте **Панель управления**. 
3. Щелкните **Microsoft Monitoring Agent** и перейдите на вкладку **Operations Manager**.
4. Если серверы Operations Manager интегрированы с Active Directory, установите флажок **Автоматически обновлять назначения групп управления из AD DS**.
5. Нажмите кнопку **Добавить**, чтобы открыть диалоговое окно **Добавление группы управления**.
6. В поле **Имя группы управления** введите имя группы управления.
7. В поле **Основной сервер управления** введите имя компьютера основного сервера управления.
8. В поле **Порт сервера управления** введите номер порта TCP.
9. На странице **Учетная запись действия агента**выберите учетную запись Local System или учетную запись локального домена.
10. Нажмите кнопку **ОК**, чтобы закрыть диалоговое окно **Добавление группы управления**, и еще раз нажмите **ОК**, чтобы закрыть диалоговое окно **Свойства Microsoft Monitoring Agent**.

### <a name="linux-agent"></a>Агент Linux
Чтобы настроить агент OMS для Linux и передать отчет в группу управления System Center Operations Manager, сделайте следующее. 

1. Измените файл `/etc/opt/omi/conf/omiserver.conf`
2. Укажите для строки `httpsport=` номер порта 1270. Например: `httpsport=1270`.
3. Перезапустите сервер OMI: `sudo /opt/omi/bin/service_control restart`.

## <a name="next-steps"></a>Дополнительная информация

Если во время установки или настройки агента возникают какие-либо проблемы, см. статью об [устранении неполадок с агентом Linux для Log Analytics](log-analytics-agent-linux-support.md).  