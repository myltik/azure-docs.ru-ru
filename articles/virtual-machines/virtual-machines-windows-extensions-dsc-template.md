<properties
   pageTitle="Шаблон Resource Manager для Desired State Configuration | Microsoft Azure"
   description="Определение шаблона Resource Manager для Desired State Configuration, а также примеры и сведения об устранении проблем."
   services="virtual-machines-windows"
   documentationCenter=""
   authors="zjalexander"
   manager="timlt"
   editor=""
   tags="azure-service-management,azure-resource-manager"
   keywords=""/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="na"
   ms.date="09/15/2016"
   ms.author="zachal"/>

# Настройка масштабируемых наборов виртуальных машин Windows и Desired State Configuration с помощью шаблонов Azure Resource Manager
В этой статье описывается шаблон Resource Manager для обработчика расширения [Desired State Configuration](virtual-machines-windows-extensions-dsc-overview.md).

## Пример шаблона для виртуальной машины Windows

В приведенном ниже фрагменте кода показан раздел Resource шаблона.

```json
			"name": "Microsoft.Powershell.DSC",
			"type": "extensions",
             "location": "[resourceGroup().location]",
             "apiVersion": "2015-06-15",
             "dependsOn": [
                  "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
              ],
              "properties": {
                  "publisher": "Microsoft.Powershell",
                  "type": "DSC",
                  "typeHandlerVersion": "2.20",
                  "autoUpgradeMinorVersion": true,
                  "forceUpdateTag": "[parameters('dscExtensionUpdateTagVersion')]",
                  "settings": {
                      "configuration": {
                          "url": "[concat(parameters('_artifactsLocation'), '/', variables('dscExtensionArchiveFolder'), '/', variables('dscExtensionArchiveFileName'))]",
                          "script": "dscExtension.ps1",
                          "function": "Main"
                      },
                      "configurationArguments": {
                          "nodeName": "[variables('vmName')]"
                      }
                  },
                  "protectedSettings": {
                      "configurationUrlSasToken": "[parameters('_artifactsLocationSasToken')]"
                  }

```

## Пример шаблона для масштабируемых наборов виртуальных машин Windows

Узел масштабируемых наборов виртуальных машин содержит раздел properties с атрибутами VirtualMachineProfile и extensionProfile. Атрибут DSC находится в разделе extensions.

```json
"extensionProfile": {
            "extensions": [
                {
                    "name": "Microsoft.Powershell.DSC",
                    "properties": {
                        "publisher": "Microsoft.Powershell",
                        "type": "DSC",
                        "typeHandlerVersion": "2.20",
                        "autoUpgradeMinorVersion": true,
                        "forceUpdateTag": "[parameters('DscExtensionUpdateTagVersion')]",
                        "settings": {
                            "configuration": {
                                "url": "[concat(parameters('_artifactsLocation'), '/', variables('DscExtensionArchiveFolder'), '/', variables('DscExtensionArchiveFileName'))]",
                                "script": "DscExtension.ps1",
                                "function": "Main"
                            },
                            "configurationArguments": {
                                "nodeName": "localhost"
                            }
                        },
                        "protectedSettings": {
                            "configurationUrlSasToken": "[parameters('_artifactsLocationSasToken')]"
                        }
                    }
                }
            ]
```

## Подробные сведения о разделе settings

В приведенной ниже схеме показана часть settings расширения Azure DSC в шаблоне Azure Resource Manager.

```json

"settings": {
  "wmfVersion": "latest",
  "configuration": {
    "url": "http://validURLToConfigLocation",
    "script": "ConfigurationScript.ps1",
    "function": "ConfigurationFunction"
  },
  "configurationArguments": {
    "argument1": "Value1",
    "argument2": "Value2"
  },
  "configurationData": {
    "url": "https://foo.psd1"
  },
  "privacy": {
    "dataCollection": "enable"
  },
  "advancedOptions": {
    "downloadMappings": {
      "customWmfLocation": "http://myWMFlocation"
    }
  } 
},
"protectedSettings": {
  "configurationArguments": {
    "parameterOfTypePSCredential1": {
      "userName": "UsernameValue1",
      "password": "PasswordValue1"
    },
    "parameterOfTypePSCredential2": {
      "userName": "UsernameValue2",
      "password": "PasswordValue2"
    }
  },
  "configurationUrlSasToken": "?g!bber1sht0k3n",
  "configurationDataUrlSasToken": "?dataAcC355T0k3N"
}

```

## Сведения
| Имя свойства | Тип | Описание |
| --- | --- | --- |
| settings.wmfVersion | string | Указывает версию Windows Management Framework, которую необходимо установить на виртуальной машине. Если задать для этого свойства значение latest, будет установлена последняя версия Windows Management Framework. Для этого свойства доступны только такие значения: **4.0, 5.0, 5.0PP и latest**. Возможные значения зависят от обновлений. По умолчанию используется значение latest.|
| settings.configuration.url | string | Указывает URL-адрес расположения, из которого можно скачать ZIP-файл конфигурации DSC. Если для доступа к предоставленному URL-адресу требуется маркер SAS, необходимо задать для свойства protectedSettings.configurationUrlSasToken значение маркера SAS. Это свойство обязательное, если заданы свойства settings.configuration.script и (или) settings.configuration.function. |
| settings.configuration.script | string | Указывает имя файла скрипта, содержащего определение вашей конфигурации DSC. Этот скрипт должен находиться в корневом каталоге ZIP-файла, скачанного по URL-адресу, указанному в свойстве configuration.url. Это свойство обязательное, если заданы свойства settings.configuration.url и (или) settings.configuration.script. |
| settings.configuration.function | string | Указывает имя вашей конфигурации DSC. Указанную конфигурацию необходимо добавить в скрипт, заданный в свойстве configuration.script. Это свойство обязательное, если заданы свойства settings.configuration.url и (или) settings.configuration.function. |
| settings.configurationArguments | Коллекция | Определяет параметры, которые необходимо передать в конфигурацию DSC. Это свойство не зашифровано. |
| settings.configurationData.url | string | Указывает URL-адрес расположения, из которого можно скачать файл данных конфигурации (в формате PDS1), используемый в качестве входных данных для вашей конфигурации DSC. Если для доступа к предоставленному URL-адресу требуется маркер SAS, необходимо задать для свойства protectedSettings.configurationDataUrlSasToken значение маркера SAS.|
| settings.privacy.dataEnabled | string | Включает или отключает сбор данных телеметрии. Для этого свойства доступны только такие значения: **Enable, Disable или $null**. Если для этого свойства не задано значение или задано значение null, сбор данных телеметрии будет выполняться. Значение по умолчанию — ''. [Дополнительные сведения см. здесь.](https://blogs.msdn.microsoft.com/powershell/2016/02/02/azure-dsc-extension-data-collection-2/) |
| settings.advancedOptions.downloadMappings | Коллекция | Определяет альтернативные расположения для скачивания Windows Management Framework. [Дополнительные сведения см. здесь.](http://blogs.msdn.com/b/powershell/archive/2015/10/21/azure-dsc-extension-2-2-amp-how-to-map-downloads-of-the-extension-dependencies-to-your-own-location.aspx) |
| protectedSettings.configurationArguments | Коллекция | Определяет параметры, которые необходимо передать в конфигурацию DSC. Это свойство зашифровано. |
| protectedSettings.configurationUrlSasToken | string | Указывает маркер SAS для доступа к URL-адресу, определенному в свойстве configuration.url. Это свойство зашифровано. |
| protectedSettings.configurationDataUrlSasToken | string | Указывает маркер SAS для доступа к URL-адресу, определенному в свойстве configurationData.url. Это свойство зашифровано. |

## Сравнение разделов settings и protectedSettings
Все параметры сохраняются в текстовом файле параметров на виртуальной машине. Свойства в разделе settings общедоступные, так как они не зашифрованы в текстовом файле параметров. Свойства в разделе protectedSettings зашифрованы с помощью сертификата, а это значит, что они не отображаются как обычный текст в файле на виртуальной машине.

Если для конфигурации нужны учетные данные, их можно добавить в раздел protectedSettings.

```json
"protectedSettings": {
    "configurationArguments": {
        "parameterOfTypePSCredential1": {
       	    "userName": "UsernameValue1",
       	    "password": "PasswordValue1"
        }
    }
}
```

## Пример

Следующий пример основан на примере конфигурации из раздела "Приступая к работе" статьи [Общие сведения об обработчике расширения Desired State Configuration в Azure](virtual-machines-windows-extensions-dsc-overview.md). В этом примере для развертывания расширения используются шаблоны Resource Manager, а не командлеты. Сохраните конфигурацию IisInstall.ps1, добавьте ее в ZIP-файл и передайте файл на доступный URL-адрес. В этом примере используется хранилище BLOB-объектов Azure, но ZIP-файл можно скачать из любого произвольного расположения.

В шаблоне Azure Resource Manager следующий код указывает виртуальной машине скачать правильный файл и выполнить соответствующую функцию PowerShell.

```json
"settings": {
    "configuration": {
        "url": "https://demo.blob.core.windows.net/",
        "script": "IisInstall.ps1",
        "function": "IISInstall"
    }
    } 
},
"protectedSettings": {
    "configurationUrlSasToken": "odLPL/U1p9lvcnp..."
}
```

## Обновление из предыдущего формата
Все параметры в предыдущем формате (содержащие общедоступные свойства ModulesUrl, ConfigurationFunction, SasToken или Properties) автоматически адаптируются к текущему формату и выполняются в обычном режиме.

Раньше схема settings выглядела следующим образом:

```json
"settings": {
    "WMFVersion": "latest",
    "ModulesUrl": "https://UrlToZipContainingConfigurationScript.ps1.zip",
    "SasToken": "SAS Token if ModulesUrl points to private Azure Blob Storage",
    "ConfigurationFunction": "ConfigurationScript.ps1\\ConfigurationFunction",
    "Properties":  {
        "ParameterToConfigurationFunction1":  "Value1",
        "ParameterToConfigurationFunction2":  "Value2",
        "ParameterOfTypePSCredential1": {
            "UserName": "UsernameValue1",
            "Password": "PrivateSettingsRef:Key1" 
        },
        "ParameterOfTypePSCredential2": {
            "UserName": "UsernameValue2",
            "Password": "PrivateSettingsRef:Key2"
        }
    }
},
"protectedSettings": { 
    "Items": {
        "Key1": "PasswordValue1",
        "Key2": "PasswordValue2"
    },
    "DataBlobUri": "https://UrlToConfigurationDataWithOptionalSasToken.psd1"
}
```

Вот как меняется предыдущий формат:

| Имя свойства | Предыдущий эквивалент |
| --- | --- |
| settings.wmfVersion | settings.WMFVersion |
| settings.configuration.url | settings.ModulesUrl |
| settings.configuration.script | Первая часть свойства settings.ConfigurationFunction (перед \\\). |
| settings.configuration.function | Вторая часть свойства settings.ConfigurationFunction (после \\\). |
| settings.configurationArguments | settings.Properties |
| settings.configurationData.url | protectedSettings.DataBlobUri (без маркера SAS) |
| settings.privacy.dataEnabled | settings.Privacy.DataEnabled |
| settings.advancedOptions.downloadMappings | settings.AdvancedOptions.DownloadMappings |
| protectedSettings.configurationArguments | protectedSettings.Properties |
| protectedSettings.configurationUrlSasToken | settings.SasToken |
| protectedSettings.configurationDataUrlSasToken | Маркер SAS из свойства protectedSettings.DataBlobUri |


## Устранение неполадок — код ошибки 1100
Код ошибки 1100 указывает на проблему с входными данными пользователя в расширении DSC. Текст этих ошибок может меняться. Ниже приведены некоторые общие ошибки и способы их устранения.

### Недопустимые значения
"Privacy.dataCollection is '{0}'. The only possible values are '', 'Enable', and 'Disable'" (Значение свойства Privacy.dataCollection — "{0}". Единственные возможные значения: Enable и Disable). "WmfVersion is '{0}'. Only possible values are … and 'latest'" (Значение параметра WmfVersion — "{0}". Единственные возможные значения: … и latest).

Проблема. Указано неразрешенное значение.

Решение. Замените недопустимое значение допустимым. См. раздел "Сведения".

### Недопустимый URL-адрес
"ConfigurationData.url is '{0}'. This is not a valid URL" (Значение свойства configurationData.url — "{0}". Это недопустимый URL-адрес). "DataBlobUri is '{0}'. This is not a valid URL" (Значение свойства DataBlobUri — "{0}". Это недопустимый URL-адрес). "Configuration.url is '{0}'. This is not a valid URL" (Значение свойства Configuration.url — "{0}". Это недопустимый URL-адрес).

Проблема. Указан недопустимый URL-адрес.

Решение. Проверьте все указанные URL-адреса. Убедитесь, что все URL-адреса разрешаются в допустимые расположения, к которым расширение может получить доступ на удаленном компьютере.

### Недопустимый тип свойства ConfigurationArgument
"Invalid configurationArguments type {0}" (Недопустимый тип ConfigurationArgument: {0}).

Проблема. Невозможно разрешить свойство ConfigurationArguments в объект Hashtable.

Решение. Задайте для свойства ConfigurationArguments тип Hashtable. Следуйте формату из приведенного выше примера. Обращайте внимание на кавычки, запятые и скобки.

### Повторяющееся свойство ConfigurationArguments
"Found duplicate arguments '{0}' in both public and protected configurationArguments" (В общедоступном и защищенном свойстве configurationArguments найден повторяющийся аргумент "{0}").

Проблема. Свойства ConfigurationArguments из общедоступной и защищенной схемы settings содержат свойства с одинаковыми именами.

Решение. Удалите одно из повторяющихся свойств.

### Отсутствующие свойства
"Configuration.function requires that configuration.url or configuration.module is specified" (Для параметра configuration.function требуется указать свойство configuration.url или configuration.module).

"Configuration.url requires that configuration.script is specified" (Для параметра configuration.url требуется указать свойство configuration.script).

"Configuration.script requires that configuration.url is specified" (Для параметра configuration.script требуется указать свойство configuration.url).

"Configuration.url requires that configuration.function is specified" (Для параметра configuration.url требуется указать свойство configuration.function).

"ConfigurationUrlSasToken requires that configuration.url is specified" (Для параметра ConfigurationUrlSasToken требуется указать свойство configuration.url).

"ConfigurationDataUrlSasToken requires that configurationData.url is specified" (Для параметра ConfigurationDataUrlSasToken требуется указать свойство configurationData.url).

Проблема. Для заданного свойства требуется другое свойство, которое отсутствует.

Решения:
- Укажите отсутствующее свойство.
- Удалите свойство, требующее отсутствующего свойства.


## Дальнейшие действия
[Узнайте больше о DSC и наборах масштабирования виртуальных машин](virtual-machines-scale-sets-dsc.md).

Получите дополнительные сведения о [безопасном управлении учетными данными посредством DSC](virtual-machines-windows-extensions-dsc-credentials.md).

Дополнительные сведения об обработчике расширений DSC см. в статье [Общие сведения об обработчике расширения Desired State Configuration в Azure](virtual-machines-windows-extensions-dsc-overview.md).

Для получения дополнительных сведений о DSC PowerShell [посетите центр документации PowerShell](https://msdn.microsoft.com/powershell/dsc/overview).

<!---HONumber=AcomDC_0921_2016-->