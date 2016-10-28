### Изменения командлетов для работы с тегами в последней версии PowerShell

Выпуск [Azure PowerShell 2.0][powershell] за август 2016 года содержит значительные изменения, касающиеся работы с тегами. Прежде чем продолжить, проверьте версию модуля AzureRm.Resources.

    Get-Module -ListAvailable -Name AzureRm.Resources | Select Version

Если последнее обновление Azure PowerShell было установлено до августа 2016 года, то должна отобразиться версия ниже 3.0.

    Version
    -------
    2.0.2

Если вы обновляли Azure PowerShell в августе 2016 года или позже, то должна отобразиться версия 3.0.

    Version
    -------
    3.0.1
    
Если у вас установлен модуль версии 3.0.1 или более поздней версии, то вы сможете использовать последние командлеты для работы с тегами. Эта версия модуля ресурсов Azure устанавливается автоматически при установке или обновлении Azure PowerShell с помощью коллекции PowerShell, PowerShellGet или установщика веб-платформы. Если его версия ниже 3.0.1, то вы можете продолжать использовать ее. Тем не менее, следует рассмотреть возможность обновления данного модуля до последней версии. Последняя версия включает в себя изменения, которые упрощают работу с тегами. В этом разделе рассмотрены оба варианта.

### Обновление сценария согласно изменениям в последней версии 

В последнем выпуске имя параметра **Tags** изменено на **Tag**, а тип изменен с **Hashtable** на **Hashtable**. Больше не нужно указывать значения **Name** и **Value** для каждой записи. Вместо этого указываются пары "ключ-значение" в формате **Key = "Value"**.

Чтобы обновить существующий сценарий, измените параметр **Tags** на **Tag**, а также измените формат тега, как показано в следующем примере.

    # Old
    New-AzureRmResourceGroup -Tags @{ Name = "testtag"; Value = "testval" } -Name $resourceGroupName -Location $location

    # New
    New-AzureRmResourceGroup -Tag @{ testtag = "testval" } -Name $resourceGroupName -Location $location 

Однако следует обратить внимание, что группы ресурсов и ресурсы по-прежнему возвращают в своих метаданных свойство **Tags**. Это свойство не изменилось.

### Версия 3.0.1 или более поздняя версия

Существующие теги находятся непосредственно в ресурсах и группах ресурсов. Чтобы просмотреть существующие теги, просмотрите ресурс или группу ресурсов с помощью командлета **Get-AzureRmResource** или **Get-AzureRmResourceGroup**, соответственно.

Давайте начнем с группы ресурсов.

    Get-AzureRmResourceGroup -Name testrg1

Этот командлет возвращает небольшой объем метаданных о группе ресурсов, включая информацию о примененных тегах, если они есть.

    ResourceGroupName : testrg1
    Location          : westus
    ProvisioningState : Succeeded
    Tags              :
                    Name         Value
                    ===========  ==========
                    Dept         Finance
                    Environment  Production

Для получения метаданных ресурса с тегами используйте приведенный ниже пример.

    Get-AzureRmResource -ResourceName tfsqlserver -ResourceGroupName testrg1

В результатах отображаются имена тегов.

    Name              : tfsqlserver
    ResourceId        : /subscriptions/{guid}/resourceGroups/tag-demo-group/providers/Microsoft.Sql/servers/tfsqlserver
    ResourceName      : tfsqlserver
    ResourceType      : Microsoft.Sql/servers
    Kind              : v12.0
    ResourceGroupName : testrg1
    Location          : westus
    SubscriptionId    : {guid}
    Tags              : {Dept, Environment}

Используйте свойство **Tags** для получения имен и значений тегов.

    (Get-AzureRmResource -ResourceName tfsqlserver -ResourceGroupName testrg1).Tags

Так можно получить следующие результаты.

    Name                   Value
    ----                   -----
    Dept                   Finance
    Environment            Production

Вместо просмотра тегов для конкретного ресурса или группы ресурсов часто требуется получить все ресурсы или группы ресурсов с определенным тегом и значением. Чтобы получить ресурсы или группы ресурсов с определенным тегом, используйте командлет **Find-AzureRmResourceGroup** с параметром **-Tag**.

Чтобы получить группы ресурсов со значением тега, используйте приведенный формат.

    (Find-AzureRmResourceGroup -Tag @{ Dept="Finance" }).Name 

Чтобы получить все ресурсы с определенным тегом и значением, используйте командлет **Find-AzureRmResource**.

    (Find-AzureRmResource -TagName Dept -TagValue Finance).Name
    
Чтобы добавить тег к группе ресурсов, в которой отсутствуют теги, воспользуйтесь командой **Set-AzureRmResourceGroup** и укажите объект тега.

    Set-AzureRmResourceGroup -Name test-group -Tag @{ Dept="IT"; Environment="Test" }

Отобразится группа ресурсов с новыми значениями тегов.

    ResourceGroupName : test-group
    Location          : southcentralus
    ProvisioningState : Succeeded
    Tags              :
                    Name          Value
                    =======       =====
                    Dept          IT
                    Environment   Test
                    
С помощью команды **Set-AzureRmResource** можно добавить теги к ресурсу, который не содержит теги.

    Set-AzureRmResource -Tag @{ Dept="IT"; Environment="Test" } -ResourceId /subscriptions/{guid}/resourceGroups/test-group/providers/Microsoft.Web/sites/examplemobileapp

При этом обновляются все теги. Чтобы добавить один тег к ресурсу, который уже содержит теги, используйте массив для сохранения всех необходимых тегов. Сначала выберите существующие теги, добавьте в этот набор новый тег и повторно примените все теги.

    $tags = (Get-AzureRmResourceGroup -Name tag-demo).Tags
    $tags += @{Status="approved"}
    Set-AzureRmResourceGroup -Name test-group -Tag $tags

Чтобы удалить тег, достаточно сохранить массив без него.

Для ресурсов этот процесс аналогичен, за исключением того, что используются командлеты **Get-AzureRmResource** и **Set-AzureRmResource**.

Чтобы получить список всех тегов в подписке с помощью PowerShell, используйте командлет **Get-AzureRmTag**.

    Get-AzureRmTag
    
Он возвращает имена тегов и считает число ресурсов и групп ресурсов с каждым тегом.

    Name                      Count
    ----                      ------
    Dept                       8
    Environment                8

Вы можете увидеть теги, начинающиеся с hidden- и link:. Это внутренние теги, которые вы должны игнорировать. Изменять их не следует.

С помощью командлета **New-AzureRmTag** в таксономию можно добавлять новые теги. Эти теги включаются в автозаполнение, даже если они еще не были применены для ресурсов или групп ресурсов. Чтобы удалить имя или значение тега, сначала необходимо удалить тег из всех ресурсов, в которых он может применяться, а затем воспользоваться командлетом **Remove-AzureRmTag** для удаления тега из таксономии.

### Версии ниже 3.0.1

Существующие теги находятся непосредственно в ресурсах и группах ресурсов. Чтобы просмотреть существующие теги, просмотрите ресурс или группу ресурсов с помощью командлета **Get-AzureRmResource** или **Get-AzureRmResourceGroup**, соответственно.

Давайте начнем с группы ресурсов.

    Get-AzureRmResourceGroup -Name testrg1

Этот командлет возвращает небольшой объем метаданных о группе ресурсов, включая информацию о примененных тегах, если они есть.

    ResourceGroupName : testrg1
    Location          : westus
    ProvisioningState : Succeeded
    Tags              :
                    Name         Value
                    ===========  ==========
                    Dept         Finance
                    Environment  Production
                    
Для получения метаданных ресурса используйте приведенный ниже пример. В метаданных ресурса теги не отображаются напрямую.

    Get-AzureRmResource -ResourceName tfsqlserver -ResourceGroupName testrg1

В результатах вы увидите, что теги отображаются только как объект Hashtable.

    Name              : tfsqlserver
    ResourceId        : /subscriptions/{guid}/resourceGroups/tag-demo-group/providers/Microsoft.Sql/servers/tfsqlserver
    ResourceName      : tfsqlserver
    ResourceType      : Microsoft.Sql/servers
    Kind              : v12.0
    ResourceGroupName : tag-demo-group
    Location          : westus
    SubscriptionId    : {guid}
    Tags              : {System.Collections.Hashtable}

Чтобы просмотреть реальные теги, нужно извлечь свойство **Tags**.

    (Get-AzureRmResource -ResourceName tfsqlserver -ResourceGroupName tag-demo-group).Tags | %{ $_.Name + ": " + $_.Value }
   
Отобразятся форматированные результаты:
    
    Dept: Finance
    Environment: Production
    
Вместо просмотра тегов для конкретного ресурса или группы ресурсов часто требуется получить все ресурсы или группы ресурсов с определенным тегом и значением. Чтобы получить ресурсы или группы ресурсов с определенным тегом, используйте командлет **Find-AzureRmResourceGroup** с параметром **-Tag**.

Чтобы получить группы ресурсов со значением тега, используйте приведенный формат.

    Find-AzureRmResourceGroup -Tag @{ Name="Dept"; Value="Finance" } | %{ $_.Name }
    
Чтобы получить все ресурсы с определенным тегом и значением, используйте командлет Find-AzureRmResource.

    Find-AzureRmResource -TagName Dept -TagValue Finance | %{ $_.ResourceName }

Чтобы добавить тег к группе ресурсов, в которой отсутствуют теги, просто воспользуйтесь командой Set-AzureRmResourceGroup и укажите объект тега.

    Set-AzureRmResourceGroup -Name test-group -Tag @( @{ Name="Dept"; Value="IT" }, @{ Name="Environment"; Value="Test"} )
    
Отобразится группа ресурсов с новыми значениями тегов.

    ResourceGroupName : test-group
    Location          : southcentralus
    ProvisioningState : Succeeded
    Tags              :
                Name          Value
                =======       =====
                Dept          IT
                Environment   Test

С помощью команды Set-AzureRmResource можно добавить теги к ресурсу, который не содержит тегов.

    Set-AzureRmResource -Tag @( @{ Name="Dept"; Value="IT" }, @{ Name="Environment"; Value="Test"} ) -ResourceId /subscriptions/{guid}/resourceGroups/test-group/providers/Microsoft.Web/sites/examplemobileapp

При этом обновляются все теги. Чтобы добавить один тег к ресурсу, который уже содержит теги, используйте массив для сохранения всех необходимых тегов. Сначала выберите существующие теги, добавьте в этот набор новый тег и повторно примените все теги.

    $tags = (Get-AzureRmResourceGroup -Name tag-demo).Tags
    $tags += @{Name="status";Value="approved"}
    Set-AzureRmResourceGroup -Name test-group -Tag $tags

Чтобы удалить тег, достаточно сохранить массив без него.

Для ресурсов этот процесс аналогичен, за исключением того, что используются командлеты Get-AzureRmResource и Set-AzureRmResource.

Чтобы получить список всех тегов в подписке с помощью PowerShell, используйте командлет **Get-AzureRmTag**.

    Get-AzureRmTag
    
Он возвращает имена тегов и считает число ресурсов и групп ресурсов с каждым тегом.

    Name                      Count
    ----                      ------
    Dept                       8
    Environment                8

Вы можете увидеть теги, начинающиеся с hidden- и link:. Это внутренние теги, которые вы должны игнорировать. Изменять их не следует.

С помощью командлета **New-AzureRmTag** в таксономию можно добавлять новые теги. Эти теги включаются в автозаполнение, даже если они еще не были применены для ресурсов или групп ресурсов. Чтобы удалить имя или значение тега, сначала необходимо удалить тег из всех ресурсов, в которых он может применяться, а затем воспользоваться командлетом **Remove-AzureRmTag** для удаления тега из таксономии.


[powershell]: https://msdn.microsoft.com/library/mt619274(v=azure.200).aspx

<!---HONumber=AcomDC_0907_2016-->