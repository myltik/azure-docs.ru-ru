---
title: "Управление агент Azure Log Analytics | Документы Microsoft"
description: "В этой статье описываются задачи управления, которые обычно выполняются во время жизненного цикла наблюдения Майкрософт агента (MMA) развернуты на компьютере."
services: log-analytics
documentationcenter: 
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/08/2017
ms.author: magoedte
ms.openlocfilehash: e917b64c32c2f99c9a8b0397e70a32b2dbb2e3cf
ms.sourcegitcommit: d247d29b70bdb3044bff6a78443f275c4a943b11
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/13/2017
---
# <a name="managing-and-maintaining-the-log-analytics-agent-for-windows-and-linux"></a>Управление и обслуживание анализа журналов агент для Windows и Linux

После первоначального развертывания агента Windows или Linux для анализа журналов может потребоваться перенастройка агента в зависимости от ситуации или удалите его с компьютера, если достигнут рабочей области вывода из эксплуатации в его жизненного цикла.  Эти задачи регламентного обслуживания можно легко управлять, вручную или с помощью автоматизации, уменьшая сбои и расходы.

## <a name="adding-or-removing-a-workspace"></a>Добавление или удаление рабочей области 

### <a name="windows-agent"></a>Агент Windows

#### <a name="update-settings-from-control-panel"></a>Обновление параметров с помощью панели управления

1. Войдите на компьютер с учетной записью, обладающей правами администратора.
2. Откройте **Панель управления**.
3. Выберите **Microsoft Monitoring Agent** и нажмите кнопку **анализа журналов Azure (OMS)** вкладки.
4. Если удаление рабочей области, выберите его и нажмите кнопку **удалить**. Повторите этот шаг для любой другой рабочей области, агент прекратит передавать.
5. Если Добавление рабочей области, щелкните **добавить** и на **добавьте рабочей областью аналитики журналов** диалоговое окно «», вставьте идентификатор рабочей области и ключ рабочей области (первичный ключ). Если компьютер следует сообщать с рабочей областью аналитики журналов в облаке Azure для государственных, выберите правительства США из раскрывающегося списка облако Azure. 
6. Нажмите кнопку **ОК** , чтобы сохранить изменения.

#### <a name="remove-a-workspace-using-powershell"></a>Удаление рабочей области с помощью PowerShell 

```PowerShell
$workspaceId = "<Your workspace Id>"
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.RemoveCloudWorkspace($workspaceId)
$mma.ReloadConfiguration()
```

#### <a name="add-a-workspace-in-azure-commercial-using-powershell"></a>Добавить рабочую область в Azure коммерческих с помощью PowerShell 

```PowerShell
$workspaceId = "<Your workspace Id>"
$workspaceKey = "<Your workspace Key>”
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.AddCloudWorkspace($workspaceId, $workspaceKey)
$mma.ReloadConfiguration()
```

#### <a name="add-a-workspace-in-azure-for-us-government-using-powershell"></a>Добавить рабочую область в Azure для правительства США с помощью PowerShell 

```PowerShell
$workspaceId = "<Your workspace Id>"
$workspaceKey = "<Your workspace Key>”
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.AddCloudWorkspace($workspaceId, $workspaceKey, 1)
$mma.ReloadConfiguration()
```

>[!NOTE]
>Если вы использовали командной строки или скрипта ранее для установки или настройки агента, `EnableAzureOperationalInsights` была заменена `AddCloudWorkspace` и `RemoveCloudWorkspace`.
>

## <a name="update-proxy-settings"></a>Обновить параметры прокси-сервера 
Чтобы настроить агент для связи со службой через прокси-сервер или [шлюза OMS](log-analytics-oms-gateway.md) после развертывания, воспользуйтесь одним из следующих методов для выполнения этой задачи.

### <a name="windows-agent"></a>Агент Windows

#### <a name="update-settings-using-control-panel"></a>Обновление параметров с помощью панели управления

1. Войдите на компьютер с учетной записью, обладающей правами администратора.
2. Откройте **Панель управления**.
3. Выберите **Microsoft Monitoring Agent** и нажмите кнопку **параметры прокси-сервера** вкладки.
4. Нажмите кнопку **использовать прокси-сервер** и укажите URL-адрес и порт номер прокси-сервер или шлюз. Если прокси-сервер или шлюз OMS требует проверки подлинности, введите имя пользователя и пароль для проверки подлинности и нажмите кнопку **ОК**. 

#### <a name="update-settings-using-powershell"></a>Обновление параметров с помощью PowerShell 

Скопируйте следующий образец кода PowerShell, обновить его, используя сведения, относящиеся к используемой среде и сохраните его с расширением PS1.  Запустите сценарий на каждом компьютере, который подключается непосредственно к службе анализа журналов.

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
Если требуется взаимодействовать через прокси-сервер или шлюз OMS для анализа журналов компьютеров Linux, выполните следующие действия.  Значение конфигурации прокси-сервера имеет следующий синтаксис: `[protocol://][user:password@]proxyhost[:port]`.

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
Чтобы удалить агент Windows или Linux, с помощью мастера установки или командной строки, используйте одну из следующих процедур.

### <a name="windows-agent"></a>Агент Windows

#### <a name="uninstall-from-control-panel"></a>Удаление с панели управления
1. Войдите на компьютер с учетной записью, обладающей правами администратора.  
2. В **панели управления**, нажмите кнопку **программы и компоненты**.
3. В **программы и компоненты**, нажмите кнопку **Microsoft Monitoring Agent**, нажмите кнопку **удаления**, а затем нажмите кнопку **Да**.

>[!NOTE]
>Мастер установки агента можно также можно запустить, дважды щелкнув **MMASetup -<platform>.exe**, которая доступна для загрузки из рабочей области на портале Azure.

#### <a name="uninstall-from-the-command-line"></a>Удаление из командной строки
Загружаемый файл для агента представляет независимый установочный пакет, создаваемый IExpress.  Программа установки для агента и вспомогательные файлы содержатся в пакете и должны извлекаться для правильного удаления с помощью командной строки, показано в следующем примере. 

1. Войдите на компьютер с учетной записью, обладающей правами администратора.  
2. Чтобы извлечь файлы установки агента, из командной строки с повышенными привилегиями запустите `extract MMASetup-<platform>.exe` и вам будет предложено для пути, чтобы извлечь файлы.  Кроме того, можно указать путь, передавая аргументы `extract MMASetup-<platform>.exe /c:<Path> /t:<Path>`.  Дополнительные сведения о swtiches командной строки, поддерживаемые IExpress см. в разделе [параметры командной строки для IExpress](https://support.microsoft.com/help/197147/command-line-switches-for-iexpress-software-update-packages) и обновите пример в соответствии с потребностями.
3. В командной строке введите `%WinDir%\System32\msiexec.exe /x <Path>:\MOMAgent.msi /qb`.  

### <a name="linux-agent"></a>Агент Linux
Чтобы удалить агент, выполните указанные ниже действия.

1. Скачайте [универсальный сценарий](https://github.com/Microsoft/OMS-Agent-for-Linux/releases) агента Linux на компьютер.
2. Запустите SH-файл пакета с аргументом *--purge* на компьютере. Агент и его конфигурация будут полностью удалены.

    `sudo sh ./omsagent-<version>.universal.x64.sh --purge`

## <a name="configure-agent-to-report-to-an-operations-manager-management-group"></a>Настройка агента для занесения в группе управления Operations Manager

### <a name="windows-agent"></a>Агент Windows
Выполните следующие действия для настройки OMS агент для Windows для занесения в группу управления System Center Operations Manager. 

1. Войдите на компьютер с учетной записью, обладающей правами администратора.
2. Откройте **Панель управления**. 
3. Нажмите кнопку **Microsoft Monitoring Agent** и нажмите кнопку **Operations Manager** вкладки.
4. Если серверы Operations Manager интегрированы с Active Directory, установите флажок **Автоматически обновлять назначения групп управления из AD DS**.
5. Нажмите кнопку **Добавить**, чтобы открыть диалоговое окно **Добавление группы управления**.
6. В **имя группы управления** введите имя группы управления.
7. В **основного сервера управления** введите имя компьютера основного сервера управления.
8. В **порт сервера управления** введите номер порта TCP.
9. На странице **Учетная запись действия агента**выберите учетную запись Local System или учетную запись локального домена.
10. Нажмите кнопку **ОК**, чтобы закрыть диалоговое окно **Добавление группы управления**, и еще раз нажмите **ОК**, чтобы закрыть диалоговое окно **Свойства Microsoft Monitoring Agent**.

### <a name="linux-agent"></a>Агент Linux
Чтобы настроить агент OMS для Linux и передать отчет в группу управления System Center Operations Manager, сделайте следующее. 

1. Измените файл `/etc/opt/omi/conf/omiserver.conf`
2. Убедитесь, что строки, начинающиеся с `httpsport=` порт 1270. Например: `httpsport=1270`.
3. Перезапустите сервер OMI: `sudo /opt/omi/bin/service_control restart`.

## <a name="next-steps"></a>Дальнейшие действия

Просмотрите [Устранение неполадок агента Linux](log-analytics-agent-linux-support.md) при возникновении проблем при установке или управления агента.  