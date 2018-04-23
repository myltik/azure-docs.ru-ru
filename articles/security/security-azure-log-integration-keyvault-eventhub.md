---
title: Интеграция журналов из Azure Key Vault с помощью концентраторов событий | Документы Майкрософт
description: Руководство, в котором приводятся необходимые действия для обеспечения доступности журналов Key Vault для SIEM с помощью интеграции журналов Azure.
services: security
author: barclayn
manager: MBaldwin
editor: TomShinder
ms.assetid: ''
ms.service: security
ms.topic: article
ms.date: 02/16/2018
ms.author: Barclayn
ms.custom: AzLog
ms.openlocfilehash: 42c30a825e44c289c42d0fb0a40a442c4ac950d7
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2018
---
# <a name="azure-log-integration-tutorial-process-azure-key-vault-events-by-using-event-hubs"></a>Руководство по интеграции журналов Azure. Обработка событий Azure Key Vault с помощью концентраторов событий

Службу интеграции журналов Azure можно использовать для получения зарегистрированных событий журнала и обеспечения их доступности для системы SIEM. В этом руководстве предоставляется поэтапный пример использования службы интеграции журналов Azure для обработки журналов, полученных из концентраторов событий.

>[!IMPORTANT]
>Предпочтительным методом интеграции журналов Azure является использование соединителя Azure Monitor от поставщика SIEM и выполнение следующих [инструкций](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md). Тем не менее, если поставщик SIEM не предоставляет соединитель для Azure Monitor, в качестве временного решения можно использовать службу "Интеграция журналов данных Azure" (если она поддерживает вашу систему SIEM).

 
Это руководство поможет вам ознакомиться с данными о совместной работе службы интеграции журналов Azure и концентраторов событий, выполнить шаги для примера и понять, как каждый шаг поддерживает решение. Затем вы сможете применить полученные здесь знания для создания собственной процедуры обеспечения соответствия уникальным требованиям компании.

>[!WARNING]
Действия и команды, используемые в этом руководстве, не предназначены для копирования и вставки. Они предоставляются только в качестве примеров. Не используйте команды PowerShell "как есть" в динамической среде. Их необходимо настраивать с учетом уникальной среды.


В этом руководстве описывается процесс использования действия Azure Key Vault, записанного в журнал концентратора событий, и обеспечения его доступности для системы SIEM в виде JSON-файлов. Затем можно настроить систему SIEM для обработки JSON-файлов.

>[!NOTE]
>В большинстве шагов в этом руководстве выполняется настройка хранилищ ключей, учетных записей хранения и концентраторов событий. Шаги по работе с интеграцией журналов Azure приводятся в конце руководства. Не выполняйте следующие действия в рабочей среде. Они предназначены для работы в лабораторной среде. Прежде чем выполнять эти действия в рабочей среде, их нужно настроить.

Сведения, предоставляемые по ходу процедур, помогут понять причины выполнения каждого шага. Перейдя по ссылкам на другие статьи, вы сможете получить более подробные сведения о конкретных разделах.

Дополнительные сведения о службах, которые упоминаются в этом учебнике, см. в указанных ниже разделах. 

- [Хранилище ключей Azure](../key-vault/key-vault-whatis.md)
- [Концентраторы событий Azure](../event-hubs/event-hubs-what-is-event-hubs.md)
- [Интеграция журналов Azure](security-azure-log-integration-overview.md)


## <a name="initial-setup"></a>Начальная настройка

Чтобы выполнить действия, описанные в этой статье, необходимо следующее.

1. Подписка Azure и учетная запись для этой подписки с правами администратора. Если у вас нет подписки, вы можете [создать бесплатную учетную запись](https://azure.microsoft.com/free/).
 
2. Система с доступом к Интернету, отвечающая требованиям к установке интеграции журналов Azure. Система может быть размещена в облачной службе или в локальной среде.

3. Установленная служба [интеграции журналов Azure](https://www.microsoft.com/download/details.aspx?id=53324). Чтобы установить службу:

   a. Подключитесь к системе, упомянутой в шаге 2, через удаленный рабочий стол.   
   Б. Скопируйте в систему установщик интеграции журналов Azure. Можно [скачать файлы установки](https://www.microsoft.com/download/details.aspx?id=53324).   
   c. Запустите установщик и примите условия лицензионного соглашения на использование программного обеспечения корпорации Майкрософт.   
   d. Если вы указываете данные телеметрии, оставьте флажок установленным. Если вы не хотите отправлять сведения об использовании в корпорацию Майкрософт, снимите флажок.
   
   Дополнительные сведения о службе интеграции журналов Azure и действиях по ее установке см. в статье [Интеграция журналов Azure с ведением журнала системы диагностики Azure и пересылкой событий Windows](security-azure-log-integration-get-started.md).

4. Последняя версия PowerShell.
 
   Если у вас установлен Windows Server 2016, по меньшей мере у вас есть PowerShell 5.0. Если вы используете другие версии Windows Server, может потребоваться установить более раннюю версию PowerShell. Чтобы проверить используемую версию, в окне PowerShell введите ```get-host```. Если версия PowerShell 5.0 не установлена, ее можно [скачать](https://www.microsoft.com/download/details.aspx?id=50395).

   При наличии PowerShell 5.0 (как минимум) перейдите к установке последней версии.
   
   a. В окне PowerShell введите команду ```Install-Module Azure```. Выполните действия по установке.    
   Б. Введите команду ```Install-Module AzureRM```. Выполните действия по установке.

   Дополнительные сведения см. в статье [Установка Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-4.0.0).


## <a name="create-supporting-infrastructure-elements"></a>Создание вспомогательных элементов инфраструктуры

1. Откройте окно PowerShell с повышенными привилегиями и перейдите в каталог **C:\Program Files\Microsoft Azure Log Integration**.
2. Импортируйте командлеты AzLog, выполнив сценарий LoadAzLogModule.ps1. Введите команду `.\LoadAzLogModule.ps1`. (Обратите внимание на ".\" в этой команде.) Вы увидите нечто вроде этого:</br>

   ![Список загруженных модулей](./media/security-azure-log-integration-keyvault-eventhub/loaded-modules.png)

3. Введите команду `Connect-AzureRmAccount`. В окне входа введите учетные данные для подписки, которая будет использоваться в этом руководстве.

   >[!NOTE]
   >Если вы выполняете вход в Azure с этого компьютера впервые, появится сообщение о предоставлении корпорации Майкрософт разрешения на сбор данных об использовании PowerShell. Рекомендуется включить эту функцию сбора данных, так как она будет использоваться для внесения улучшений в Azure PowerShell.

4. После успешной проверки подлинности вы войдете в систему и увидите сведения, аналогичные приведенным на следующем снимке экрана. Запишите идентификатор подписки и имя подписки, так как они потребуются вам на следующих этапах.

   ![Окно PowerShell](./media/security-azure-log-integration-keyvault-eventhub/login-azurermaccount.png)
5. Создайте переменные для хранения значений, которые будут использоваться дальше. Введите следующие строки PowerShell. Может потребоваться настроить параметры в соответствии с вашей средой.
    - ```$subscriptionName = ‘Visual Studio Ultimate with MSDN’``` (У вашей подписки может быть другое имя. Вы увидите его в составе выходных данных предыдущей команды.)
    - ```$location = 'West US'``` (Эта переменная будет использоваться для передачи расположения, в котором должны быть созданы ресурсы. Эту переменную можно изменить на любое необходимое расположение.)
    - ```$random = Get-Random```
    - ``` $name = 'azlogtest' + $random``` (Имя может быть любым, но оно должно включать только строчные буквы и цифры.)
    - ``` $storageName = $name``` (Эта переменная будет использоваться для имени учетной записи хранения.)
    - ```$rgname = $name ``` (Эта переменная будет использоваться для имени группы ресурсов.)
    - ``` $eventHubNameSpaceName = $name``` (Это имя пространства имен концентратора событий.)
6. Укажите подписку, с которой вы будете работать:
    
    ```Select-AzureRmSubscription -SubscriptionName $subscriptionName```
7. Создайте группу ресурсов:
    
    ```$rg = New-AzureRmResourceGroup -Name $rgname -Location $location```
    
   Если на этом этапе вы введете `$rg`, вы должны увидеть следующие выходные данные:

   ![Выходные данные после создания группы ресурсов](./media/security-azure-log-integration-keyvault-eventhub/create-rg.png)
8. Создайте учетную запись хранения, которая будет использоваться для отслеживания сведений о состоянии:
    
    ```$storage = New-AzureRmStorageAccount -ResourceGroupName $rgname -Name $storagename -Location $location -SkuName Standard_LRS```
9. Создайте пространство имен концентратора событий. Это необходимо для создания концентратора событий.
    
    ```$eventHubNameSpace = New-AzureRmEventHubNamespace -ResourceGroupName $rgname -NamespaceName $eventHubnamespaceName -Location $location```
10. Получите идентификатор правила, который будет использоваться с поставщиком Insights:
    
    ```$sbruleid = $eventHubNameSpace.Id +'/authorizationrules/RootManageSharedAccessKey' ```
11. Получите все возможные расположения Azure и добавьте имена в переменную, которая может использоваться в дальнейшем:
    
    a. ```$locationObjects = Get-AzureRMLocation```    
    Б. ```$locations = @('global') + $locationobjects.location```
    
    Если на этом этапе вы введете `$locations`, вы увидите имена расположений без дополнительных сведений, возвращенных командлетом Get-AzureRmLocation.
12. Создайте профиль журнала Azure Resource Manager: 
    
    ```Add-AzureRmLogProfile -Name $name -ServiceBusRuleId $sbruleid -Locations $locations```
    
    Дополнительные сведения о профиле журнала Azure см. в статье [Общие сведения о журнале действий Azure](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md).

> [!NOTE]
> При попытке создания профиля журнала может появиться сообщение об ошибке. Затем можно просмотреть в документацию по командлетам Get-AzureRmLogProfile и Remove-AzureRmLogProfile. При запуске командлета Get-AzureRmLogProfile будут выведены сведения о профиле журнала. Чтобы удалить имеющийся профиль журнала, введите команду ```Remove-AzureRmLogProfile -name 'Log Profile Name' ```.
>
>![Ошибка профиля Resource Manager](./media/security-azure-log-integration-keyvault-eventhub/rm-profile-error.png)

## <a name="create-a-key-vault"></a>Создайте хранилище ключей.

1. Создайте хранилище ключей:

   ```$kv = New-AzureRmKeyVault -VaultName $name -ResourceGroupName $rgname -Location $location ```

2. Настройте ведение журнала для хранилища ключей:

   ```Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -ServiceBusRuleId $sbruleid -Enabled $true ```

## <a name="generate-log-activity"></a>Создание действия журнала

Для создания действия журнала требуется отправка запросов в Key Vault. Действия, например создание ключей, хранение секретов или чтение секретов из Key Vault, будут создавать записи в журнале.

1. Отобразите текущие ключи хранилища:
    
   ```Get-AzureRmStorageAccountKey -Name $storagename -ResourceGroupName $rgname  | ft -a```
2. Создайте ключ **key2**:
    
   ```New-AzureRmStorageAccountKey -Name $storagename -ResourceGroupName $rgname -KeyName key2```
3. Отобразите ключи снова и обратите внимание, что ключ **key2** содержит другое значение:
    
   ```Get-AzureRmStorageAccountKey -Name $storagename -ResourceGroupName $rgname  | ft -a```
4. Задайте и считайте секрет для создания дополнительных записей журнала:
    
   a. ```Set-AzureKeyVaultSecret -VaultName $name -Name TestSecret -SecretValue (ConvertTo-SecureString -String 'Hi There!' -AsPlainText -Force)``` Б. ```(Get-AzureKeyVaultSecret -VaultName $name -Name TestSecret).SecretValueText```

   ![Возвращенный секрет](./media/security-azure-log-integration-keyvault-eventhub/keyvaultsecret.png)


## <a name="configure-azure-log-integration"></a>Настройка интеграции журналов Azure

После того как для всех необходимых элементов настроено ведение журнала Key Vault в концентраторе событий, следует настроить интеграцию журнала Azure.

1. ```$storage = Get-AzureRmStorageAccount -ResourceGroupName $rgname -Name $storagename```
2. ```$eventHubKey = Get-AzureRmEventHubNamespaceKey -ResourceGroupName $rgname -NamespaceName $eventHubNamespace.name -AuthorizationRuleName RootManageSharedAccessKey```
3. ```$storagekeys = Get-AzureRmStorageAccountKey -ResourceGroupName $rgname -Name $storagename```
4. ``` $storagekey = $storagekeys[0].Value```

Выполните команду AzLog для каждого концентратора событий:

1. ```$eventhubs = Get-AzureRmEventHub -ResourceGroupName $rgname -NamespaceName $eventHubNamespaceName```
2. ```$eventhubs.Name | %{Add-AzLogEventSource -Name $sub' - '$_ -StorageAccount $storage.StorageAccountName -StorageKey $storageKey -EventHubConnectionString $eventHubKey.PrimaryConnectionString -EventHubName $_}```

Приблизительно через минуту после выполнения двух последних команд вы должны увидеть созданные JSON-файлы. Их наличие можно проверить в каталоге **C:\users\AzLog\EventHubJson**.

## <a name="next-steps"></a>Дополнительная информация

- [Интеграция журналов Azure: часто задаваемые вопросы](security-azure-log-integration-faq.md)
- [Приступая к работе со службой интеграции журналов Azure](security-azure-log-integration-get-started.md)
- [Интеграция журналов из ресурсов Azure в системы SIEM](security-azure-log-integration-overview.md)
