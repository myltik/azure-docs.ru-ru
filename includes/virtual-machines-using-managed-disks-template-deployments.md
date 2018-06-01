# <a name="using-managed-disks-in-azure-resource-manager-templates"></a>Использование управляемых дисков в шаблонах Resource Manager

В этом документе рассматриваются различия между управляемым и неуправляемыми дисками при использовании шаблонов Azure Resource Manager для подготовки виртуальных машин. Эти знания помогут вам обновить существующие шаблоны, в которых используются неуправляемые диски, заменив их управляемыми дисками. В качестве примера мы используем шаблон [101-vm-simple-windows](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows). Вы можете просмотреть шаблон, использующий [управляемые диски](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows/azuredeploy.json), и его предыдущую версию, использующую [неуправляемые диски](https://github.com/Azure/azure-quickstart-templates/tree/93b5f72a9857ea9ea43e87d2373bf1b4f724c6aa/101-vm-simple-windows/azuredeploy.json), если вы хотите непосредственно сравнить их.

## <a name="unmanaged-disks-template-formatting"></a>Формат шаблона с неуправляемыми дисками

Сначала рассмотрим, как развертываются неуправляемые диски. При создании неуправляемых дисков необходима учетная запись хранения для хранения файлов VHD-файлов. Можно создать новую учетную запись хранения или использовать существующую. В этой статье показано, как создать новую учетную запись хранения. Для этого требуется ресурс учетной записи хранения в блоке ресурсов, как показано ниже.

```
{
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2016-01-01",
    "location": "[resourceGroup().location]",
    "sku": {
        "name": "Standard_LRS"
    },
    "kind": "Storage",
    "properties": {}
}
```

В объекте виртуальной машины необходима зависимость от учетной записи хранения, чтобы гарантировать, что она будет создана до виртуальной машины. В разделе `storageProfile` мы укажем полный универсальный код ресурса (URI) расположения VHD, который ссылается на учетную запись хранения и требуется для диска ОС и дисков данных. 

```
{
    "apiVersion": "2015-06-15",
    "type": "Microsoft.Compute/virtualMachines",
    "name": "[variables('vmName')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
    "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
    ],
    "properties": {
        "hardwareProfile": {...},
        "osProfile": {...},
        "storageProfile": {
            "imageReference": {
                "publisher": "MicrosoftWindowsServer",
                "offer": "WindowsServer",
                "sku": "[parameters('windowsOSVersion')]",
                "version": "latest"
            },
            "osDisk": {
                "name": "osdisk",
                "vhd": {
                    "uri": "[concat(reference(resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))).primaryEndpoints.blob, 'vhds/osdisk.vhd')]"
                },
                "caching": "ReadWrite",
                "createOption": "FromImage"
            },
            "dataDisks": [
                {
                    "name": "datadisk1",
                    "diskSizeGB": 1023,
                    "lun": 0,
                    "vhd": {
                        "uri": "[concat(reference(resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))).primaryEndpoints.blob, 'vhds/datadisk1.vhd')]"
                    },
                    "createOption": "Empty"
                }
            ]
        },
        "networkProfile": {...},
        "diagnosticsProfile": {...}
    }
}
```

## <a name="managed-disks-template-formatting"></a>Формат шаблона с управляемыми дисками

При использовании службы "Управляемые диски Azure" диск становится ресурсом верхнего уровня и для него больше не требуется учетная запись хранения, созданная пользователем. Управляемые диски впервые были представлены в версии API `2016-04-30-preview`. Они доступны во всех последующих версиях API и теперь являются типом диска по умолчанию. В следующих разделах рассматриваются параметры по умолчанию и описывается дальнейшая настройка дисков.

> [!NOTE]
> Рекомендуем использовать более позднюю версию API, чем `2016-04-30-preview`, так как между версиями `2016-04-30-preview` и `2017-03-30` были внесены критически важные изменения.
>
>

### <a name="default-managed-disk-settings"></a>Параметры управляемого диска по умолчанию

Для создания виртуальной машины с управляемыми дисками больше не требуется создавать ресурс учетной записи хранения, и можно обновлять ресурс виртуальной машины следующим образом. В частности, обратите внимание на то, что `apiVersion` имеет значение `2017-03-30`, а `osDisk` и `dataDisks` больше не ссылаются на определенный универсальный код ресурса (URI) VHD. При развертывании без указания дополнительных свойств диск будет использовать [локально избыточное хранилище уровня "Стандартный"](../articles/storage/common/storage-redundancy.md). Если не указать имя, то диску ОС присваивается имя `<VMName>_OsDisk_1_<randomstring>`, а каждому диску данных — `<VMName>_disk<#>_<randomstring>`. По умолчанию шифрование диска Azure отключено. Кэширование чтения и записи включено для диска ОС, но не для дисков данных. Можно заметить, что в приведенном ниже примере осталась зависимость от учетной записи хранения, хотя она используется только для хранения диагностических данных и не требуется для хранения дисков.

```
{
    "apiVersion": "2017-03-30",
    "type": "Microsoft.Compute/virtualMachines",
    "name": "[variables('vmName')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
        "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
    ],
    "properties": {
        "hardwareProfile": {...},
        "osProfile": {...},
        "storageProfile": {
            "imageReference": {
                "publisher": "MicrosoftWindowsServer",
                "offer": "WindowsServer",
                "sku": "[parameters('windowsOSVersion')]",
                "version": "latest"
            },
            "osDisk": {
                "createOption": "FromImage"
            },
            "dataDisks": [
                {
                    "diskSizeGB": 1023,
                    "lun": 0,
                    "createOption": "Empty"
                }
            ]
        },
        "networkProfile": {...},
        "diagnosticsProfile": {...}
    }
}
```

### <a name="using-a-top-level-managed-disk-resource"></a>Использование ресурса управляемого диска верхнего уровня

В качестве альтернативы указанию конфигурации дисков в объекте виртуальной машины можно создать ресурс диска верхнего уровня и подключить его при создании виртуальной машины. Например, можно создать дисковый ресурс, как показано ниже, и использовать его в качестве диска данных.

```
{
    "type": "Microsoft.Compute/disks",
    "name": "[concat(variables('vmName'),'-datadisk1')]",
    "apiVersion": "2017-03-30",
    "location": "[resourceGroup().location]",
    "sku": {
        "name": "Standard_LRS"
    },
    "properties": {
        "creationData": {
            "createOption": "Empty"
        },
        "diskSizeGB": 1023
    }
}
```

Затем можно указать ссылку на него в объекте виртуальной машины, чтобы подключить этот объект диска. Указав идентификатор ресурса созданного управляемого диска в свойстве `managedDisk`, можно подключить этот диск при создании виртуальной машины. Обратите внимание, что для параметра `apiVersion` ресурса виртуальной машины задано значение `2017-03-30`. Кроме того, обратите внимание, что мы создали зависимость от дискового ресурса, чтобы обеспечить его успешное создание перед созданием виртуальной машины. 

```
{
    "apiVersion": "2017-03-30",
    "type": "Microsoft.Compute/virtualMachines",
    "name": "[variables('vmName')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
        "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]",
        "[resourceId('Microsoft.Compute/disks/', concat(variables('vmName'),'-datadisk1'))]"
    ],
    "properties": {
        "hardwareProfile": {...},
        "osProfile": {...},
        "storageProfile": {
            "imageReference": {
                "publisher": "MicrosoftWindowsServer",
                "offer": "WindowsServer",
                "sku": "[parameters('windowsOSVersion')]",
                "version": "latest"
            },
            "osDisk": {
                "createOption": "FromImage"
            },
            "dataDisks": [
                {
                    "lun": 0,
                    "name": "[concat(variables('vmName'),'-datadisk1')]",
                    "createOption": "attach",
                    "managedDisk": {
                        "id": "[resourceId('Microsoft.Compute/disks/', concat(variables('vmName'),'-datadisk1'))]"
                    }
                }
            ]
        },
        "networkProfile": {...},
        "diagnosticsProfile": {...}
    }
}
```

### <a name="create-managed-availability-sets-with-vms-using-managed-disks"></a>Создание управляемых групп доступности с виртуальными машинами, использующими управляемые диски

Чтобы создать управляемые группы доступности с виртуальными машинами, использующими управляемые диски, добавьте объект `sku` в ресурс группы доступности и задайте для свойства `name` значение `Aligned`. Это гарантирует, что диски каждой виртуальной машины будут достаточно изолированы друг от друга, чтобы избежать образования единых точек отказа. Также обратите внимание, что для параметра `apiVersion` ресурса группы доступности задано значение `2017-03-30`.

```
{
    "apiVersion": "2017-03-30",
    "type": "Microsoft.Compute/availabilitySets",
    "location": "[resourceGroup().location]",
    "name": "[variables('avSetName')]",
    "properties": {
        "PlatformUpdateDomainCount": 3,
        "PlatformFaultDomainCount": 2
    },
    "sku": {
        "name": "Aligned"
    }
}
```

### <a name="additional-scenarios-and-customizations"></a>Дополнительные сценарии и настройки

Полные сведения о спецификациях REST API можно найти в [документации по созданию управляемых дисков с помощью REST API](/rest/api/manageddisks/disks/disks-create-or-update). В ней вы найдете дополнительные сценарии, а также значения по умолчанию и допустимые значения, которые можно передавать в API при развертывании с помощью шаблонов. 

## <a name="next-steps"></a>Дополнительная информация

* Полные шаблоны, использующие управляемые диски, можно получить, перейдя по приведенным ниже ссылкам на репозиторий кратких руководств по Azure.
    * [Виртуальная машина Windows с управляемыми дисками](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows)
    * [Виртуальная машина Linux с управляемыми дисками](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-linux)
    * [Полный список шаблонов с управляемыми дисками](https://github.com/Azure/azure-quickstart-templates/blob/master/managed-disk-support-list.md)
* Ознакомьтесь с документом [Обзор управляемых дисков Azure](../articles/virtual-machines/windows/managed-disks-overview.md), чтобы узнать больше об управляемых дисках.
* Ознакомьтесь с информацией по шаблонам для ресурсов виртуальных машин, приведенной в [справочнике по шаблону Microsoft.Compute/virtualMachines](/azure/templates/microsoft.compute/virtualmachines).
* Ознакомьтесь с информацией по шаблонам дисковых ресурсов, приведенной в [справочнике по шаблону Microsoft.Compute/disks](/azure/templates/microsoft.compute/disks).
* Сведения об использовании управляемых дисков в масштабируемых наборах виртуальных машин Azure см. в документе [об использовании дисков данных с масштабируемыми наборами](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-attached-disks.md).
