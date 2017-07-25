Версия 3.0 модуля AzureRm.Resources содержит значительные изменения, касающиеся работы с тегами. Прежде чем продолжить, проверьте версию:

```powershell
Get-Module -ListAvailable -Name AzureRm.Resources | Select Version
```

Если результаты показывают версию 3.0 и выше, примеры из этой статьи будут работать в вашей среде. Если у вас более старая версия, [обновите ее](/powershell/azureps-cmdlets-docs/) с помощью коллекции PowerShell или установщика веб-платформы, прежде чем продолжать работу с этой статьей.

```powershell
Version
-------
3.5.0
```

Чтобы просмотреть имеющиеся теги для **группы ресурсов**, используйте:

```powershell
(Get-AzureRmResourceGroup -Name examplegroup).Tags
```

Она вернет ответ в следующем формате:

```powershell
Name                           Value
----                           -----
Dept                           IT
Environment                    Test
```

Чтобы просмотреть имеющиеся теги для **ресурса с указанным идентификатором ресурса**, используйте:

```powershell
(Get-AzureRmResource -ResourceId {resource-id}).Tags
```

Или чтобы просмотреть имеющиеся теги для **ресурса с указанным именем и группой ресурсов**, используйте:

```powershell
(Get-AzureRmResource -ResourceName examplevnet -ResourceGroupName examplegroup).Tags
```

Чтобы получить **группы ресурсов с определенным тегом**, используйте:

```powershell
(Find-AzureRmResourceGroup -Tag @{ Dept="Finance" }).Name 
```

Чтобы получить **ресурсы с определенным тегом** , используйте:

```powershell
(Find-AzureRmResource -TagName Dept -TagValue Finance).Name
```

Каждый раз, когда вы добавляете теги к ресурсу или группе ресурсов, вы перезаписываете существующие теги в этом ресурсе или группе. Поэтому необходимо использовать другой подход, исходя из того, имеются ли теги в ресурсе или в группе ресурсов. 

Чтобы добавить теги в **группу ресурсов без тегов**, используйте:

```powershell
Set-AzureRmResourceGroup -Name examplegroup -Tag @{ Dept="IT"; Environment="Test" }
```

Чтобы добавить теги в **группу ресурсов с тегами**, извлеките их, добавьте новый тег и повторно примените теги:

```powershell
$tags = (Get-AzureRmResourceGroup -Name examplegroup).Tags
$tags += @{Status="Approved"}
Set-AzureRmResourceGroup -Tag $tags -Name examplegroup
```

Чтобы добавить теги в **ресурс без тегов**, используйте:

```powershell
Set-AzureRmResource -Tag @{ Dept="IT"; Environment="Test" } -ResourceName examplevnet -ResourceGroupName exampleroup
```

Чтобы добавить теги в **ресурс с тегами**, используйте:

```powershell
$tags = (Get-AzureRmResource -ResourceName examplevnet -ResourceGroupName examplegroup).Tags
$tags += @{Status="Approved"}
Set-AzureRmResource -Tag $tags -ResourceName examplevnet -ResourceGroupName examplegroup
```

Чтобы добавить все теги из группы ресурсов к ресурсам в этой группе, **не сохраняя существующие теги ресурсов**, используйте следующий сценарий.

```powershell
$groups = Get-AzureRmResourceGroup
foreach ($g in $groups) 
{
    Find-AzureRmResource -ResourceGroupNameEquals $g.ResourceGroupName | ForEach-Object {Set-AzureRmResource -ResourceId $_.ResourceId -Tag $g.Tags -Force } 
}
```

Чтобы добавить все теги из группы ресурсов к ресурсам в этой группе, **сохранив существующие теги ресурсов**, используйте приведенный ниже сценарий.

```powershell
$groups = Get-AzureRmResourceGroup
foreach ($g in $groups) 
{
    if ($g.Tags -ne $null) {
        $resources = Find-AzureRmResource -ResourceGroupNameEquals $g.ResourceGroupName 
        foreach ($r in $resources)
        {
            $resourcetags = (Get-AzureRmResource -ResourceId $r.ResourceId).Tags
            foreach ($key in $g.Tags.Keys)
            {
                if ($resourcetags.ContainsKey($key)) { $resourcetags.Remove($key) }
            }
            $resourcetags += $g.Tags
            Set-AzureRmResource -Tag $resourcetags -ResourceId $r.ResourceId -Force
        }
    }
}
```

Чтобы удалить все теги, передайте пустую хэш-таблицу.

```powershell
Set-AzureRmResourceGroup -Tag @{} -Name examplegroup
```



