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

Каждый раз, когда вы добавляете теги к ресурсу или группе ресурсов, вы перезаписываете существующие теги в этом ресурсе или группе. Если существующие теги нужно сохранить, необходимо использовать другой подход. Ниже приведены командлеты для разных ситуаций.

* Добавление тегов к группе ресурсов, у которой нет тегов:

  ```powershell
  Set-AzureRmResourceGroup -Name TagTestGroup -Tag @{ Dept="IT"; Environment="Test" }
  ```

* Добавление тегов к группе ресурсов, у которой уже есть теги:

  ```powershell
  $tags = (Get-AzureRmResourceGroup -Name TagTestGroup).Tags
  $tags += @{Status="Approved"}
  Set-AzureRmResourceGroup -Tag $tags -Name TagTestGroup
  ```

* Добавление тегов к ресурсу, у которого нет тегов:

  ```powershell
  Set-AzureRmResource -Tag @{ Dept="IT"; Environment="Test" } -ResourceName storageexample -ResourceGroupName TagTestGroup -ResourceType Microsoft.Storage/storageAccounts
  ```

* Добавление тегов к ресурсу, у которого уже есть теги:

  ```powershell
  $tags = (Get-AzureRmResource -ResourceName storageexample -ResourceGroupName TagTestGroup).Tags
  $tags += @{Status="Approved"}
  Set-AzureRmResource -Tag $tags -ResourceName storageexample -ResourceGroupName TagTestGroup -ResourceType Microsoft.Storage/storageAccounts
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
Set-AzureRmResourceGroup -Tag @{} -Name TagTestGgroup
```

Чтобы получить группы ресурсов с определенным тегом, используйте командлет `Find-AzureRmResourceGroup`.

```powershell
(Find-AzureRmResourceGroup -Tag @{ Dept="Finance" }).Name 
```

Чтобы получить все ресурсы с определенным тегом и значением, используйте командлет `Find-AzureRmResource`.

```powershell
(Find-AzureRmResource -TagName Dept -TagValue Finance).Name
```

