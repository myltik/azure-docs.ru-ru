---
title: "Часто задаваемые вопросы об интеграции журналов Azure | Документация Майкрософт"
description: "Эта статья содержит ответы на часто задаваемые вопросы об интеграции журналов Azure."
services: security
documentationcenter: na
author: TomShinder
manager: MBaldwin
editor: TerryLanfear
ms.assetid: d06d1ac5-5c3b-49de-800e-4d54b3064c64
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/26/2017
ms.author: TomSh
ms.custom: azlog
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: e6aefe5f16e7148f7837a8741355c61851618495
ms.contentlocale: ru-ru
ms.lasthandoff: 06/28/2017


---
# <a name="azure-log-integration-frequently-asked-questions-faq"></a>Интеграция журналов Azure: часто задаваемые вопросы
Здесь представлены ответы на часто задаваемые вопросы о службе интеграции журналов Azure, которая позволяет интегрировать необработанные журналы из ресурсов Azure с локальными системами SIEM (Security Information and Event Management). С помощью такой интеграции вы можете иметь доступ ко всем своим ресурсам, локальным или облачным, на единой панели мониторинга, что позволяет выполнять статистическую обработку, сопоставление и анализ, а также предупреждать о событиях безопасности, связанных с приложениями.

## <a name="is-the-azure-log-integration-software-free"></a>Предоставляется ли служба интеграции журналов Azure бесплатно?
Да. Плата за использование службы интеграции журналов Azure не взимается.

## <a name="where-is-azure-log-integration-available"></a>Где доступна служба интеграции журналов Azure?

В настоящее время служба доступна в рамках коммерческой лицензии Azure и лицензии Azure для государственных организаций и недоступна в Китае и в Германии.

## <a name="how-can-i-see-the-storage-accounts-from-which-azure-log-integration-is-pulling-azure-vm-logs-from"></a>Как увидеть учетные записи хранения, из которых служба интеграции журналов Azure извлекает журналы виртуальных машин Azure?
Выполните команду **azlog source list**.

## <a name="how-can-i-tell-which-subscription-the-azure-log-integration-logs-are-from"></a>Как определить, из каких подписок получены журналы службы интеграции журналов Azure?

Журналы аудита хранятся в каталогах AzureResourcemanagerJson. В их случае идентификатор подписки — это имя файла журнала. Это правило применяется и к журналам в папке AzureSecurityCenterJson. Например:

20170407T070805_2768037.0000000023.**1111e5ee-1111-111b-a11e-1e111e1111dc**.json

В состав имен журналов аудита Azure Active Directory входит идентификатор клиента.

В состав имен журналов диагностики, полученных из концентратора событий, не входит идентификатор клиента, но в их имени содержится понятное имя, указанное в процессе создания источника концентратора событий. 

## <a name="how-can-i-update-the-proxy-configuration"></a>Как обновить конфигурацию прокси-сервера?
Если параметры прокси-сервера не позволяют получать доступ к хранилищу Azure напрямую, то откройте файл **AZLOG.EXE.CONFIG** из расположения **c:\Program Files\Microsoft Azure Log Integration**. Обновите файл, включив в его раздел **defaultProxy** прокси-адрес вашей организации. После завершения обновления остановите и запустите службу с помощью команд **net stop azlog** и **net start azlog**.

    <?xml version="1.0" encoding="utf-8"?>
    <configuration>
      <system.net>
        <connectionManagement>
          <add address="*" maxconnection="400" />
        </connectionManagement>
        <defaultProxy>
          <proxy usesystemdefault="true"
          proxyaddress=http://127.0.0.1:8888
          bypassonlocal="true" />
        </defaultProxy>
      </system.net>
      <system.diagnostics>
        <performanceCounters filemappingsize="20971520" />
      </system.diagnostics>   

## <a name="how-can-i-see-the-subscription-information-in-windows-events"></a>Как увидеть сведения о подписке в событиях Windows?
При добавлении источника добавьте **subscriptionid** к понятному имени.

    Azlog source add <sourcefriendlyname>.<subscription id> <StorageName> <StorageKey>  
XML-файл события содержит метаданные, как показано ниже, включая идентификатор подписки.

![XML-файл события][1]

## <a name="error-messages"></a>Сообщения об ошибках
### <a name="when-running-command-azlog-createazureid-why-do-i-get-the-following-error"></a>Почему при выполнении команды **azlog createazureid**возникает следующая ошибка?
Ошибка:

  *Не удалось создать приложение AAD - Клиент 72f988bf-86f1-41af-91ab-2d7cd011db37 - Причина = 'Запрещено' - Сообщение = 'Недостаточно привилегий для выполнения операции.'*

**Azlog createazureid** пытается создать субъект-службу на всех клиентах Azure AD для подписок, к которым имеют доступ учетные данные Azure. Если учетные данные Azure принадлежат пользователю-гостю на этом клиенте Azure AD, то команда завершается ошибкой и отображается сообщение "Недостаточно привилегий для выполнения операции". Попросите администратора клиента добавить вашу учетную запись в качестве пользователя данного клиента.

### <a name="when-running-command-azlog-authorize-why-do-i-get-the-following-error"></a>Почему при выполнении команды **azlog authorize**возникает следующая ошибка?
Ошибка:

  *"Warning creating Role Assignment - AuthorizationFailed" (Предупреждение при создании назначения роли — AuthorizationFailed): Клиент "janedo@microsoft.com" с идентификатором объекта "fe9e03e4-4dad-4328-910f-fd24a9660bd2" не авторизован для выполнения действия "Microsoft.Authorization/roleAssignments/write" с областью "/subscriptions/70d95299-d689-4c97-b971-0d8ff0000000".*

Команда **azlog authorize** назначает роль читателя субъекту-службе Azure AD (созданному с помощью команды **azlog createazureid**) для предоставленных подписок. Если учетные данные Azure не принадлежат соадминистратору или владельцу подписки, то команда завершается ошибкой и отображается сообщение о сбое авторизации. Для выполнения этого действия используйте управление доступом на основе ролей (RBAC) в Azure, настроив роль соадминистратора или владельца.

## <a name="where-can-i-find-the-definition-of-the-properties-in-audit-log"></a>Где найти определения свойств в журнале аудита?
См.:

* [Операции аудита с помощью диспетчера ресурсов](../azure-resource-manager/resource-group-audit.md)
* [Список событий управления в подписке — справочник по REST API Azure Monitor](https://msdn.microsoft.com/library/azure/dn931934.aspx)

## <a name="where-can-i-find-details-on-azure-security-center-alerts"></a>Где найти подробные сведения об оповещениях центра безопасности Azure?
См. статью [Управление оповещениями безопасности в Центре безопасности Azure и реагирование на них](../security-center/security-center-managing-and-responding-alerts.md).

## <a name="how-can-i-modify-what-is-collected-with-vm-diagnostics"></a>Как изменить, что именно должна собирать система диагностики виртуальных машин?
Сведения о том, как получать, изменять и настраивать конфигурацию системы диагностики Azure в Windows *(WAD)*, см. в статье [Включение системы диагностики Azure на виртуальной машине под управлением Windows с помощью PowerShell](../virtual-machines/windows/ps-extensions-diagnostics.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Ниже приведен пример.

### <a name="get-the-wad-config"></a>Получение конфигурации WAD
    -AzureRmVMDiagnosticsExtension -ResourceGroupName AzLog-Integration -VMName AzlogClient
    $publicsettings = (Get-AzureRmVMDiagnosticsExtension -ResourceGroupName AzLog-Integration -VMName AzlogClient).PublicSettings
    $encodedconfig = (ConvertFrom-Json -InputObject $publicsettings).xmlCfg
    $xmlconfig = [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($encodedconfig))
    Write-Host $xmlconfig

    $xmlconfig | Out-File -Encoding utf8 -FilePath "d:\WADConfig.xml"

### <a name="modify-the-wad-config"></a>Изменение конфигурации WAD
В следующем примере показана конфигурация, в которой из журнала событий безопасности собираются только идентификаторы EventID 4624 и EventId 4625. События антивредоносного ПО Майкрософт собираются из журнала системных событий. Сведения об использовании выражений XPath см. в статье [Consuming Events] (Использование событий) (https://msdn.microsoft.com/library/windows/desktop/dd996910(v=vs.85).

    <WindowsEventLog scheduledTransferPeriod="PT1M">
        <DataSource name="Security!*[System[(EventID=4624 or EventID=4625)]]" />
        <DataSource name="System!*[System[Provider[@Name='Microsoft Antimalware']]]"/>
    </WindowsEventLog>

### <a name="set-the-wad-configuration"></a>Настройка конфигурации WAD
    $diagnosticsconfig_path = "d:\WADConfig.xml"
    Set-AzureRmVMDiagnosticsExtension -ResourceGroupName AzLog-Integration -VMName AzlogClient -DiagnosticsConfigurationPath $diagnosticsconfig_path -StorageAccountName log3121 -StorageAccountKey <storage key>

После внесения изменений проверьте учетную запись хранения, чтобы убедиться, что собираются именно те события, которые необходимы.

В случае проблем при установке и настройке создайте [запрос в службу поддержки](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request), указав **Интеграция журнала** в качестве службы, для которой запрашивается поддержка.

### <a name="can-i-use-azure-log-integration-to-integrate-network-watcher-logs-into-my-siem"></a>Можно ли интегрировать журналы Наблюдателя за сетями с системой SIEM с помощью службы интеграции журналов Azure?

Наблюдатель за сетями регистрирует в журналах большие объемы информации. Эти журналы не обязательно должны отправляться в систему SIEM. Журналы Наблюдателя за сетями отправляются только в учетную запись хранения. Azlog не поддерживает чтение этих журналов и не обеспечивает их доступность в системе SIEM.

<!--Image references-->
[1]: ./media/security-azure-log-integration-faq/event-xml.png

