---
title: "Расширение Desired State Configuration (DSC) с использованием шаблонов Azure Resource Manager | Документация Майкрософт"
description: "Определение шаблона Resource Manager для Desired State Configuration в Azure"
services: virtual-machines-windows
documentationcenter: 
author: mgreenegit
manager: timlt
editor: 
tags: azure-resource-manager
keywords: DSC
ms.assetid: b5402e5a-1768-4075-8c19-b7f7402687af
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: na
ms.date: 02/02/2018
ms.author: migreene
ms.openlocfilehash: f638d1530541526316f6e409f1efd44f136992a5
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/09/2018
---
# <a name="desired-state-configuration-extension-with-azure-resource-manager-templates"></a>Расширение Desired State Configuration (DSC) с использованием шаблонов Azure Resource Manager

В этой статье описывается шаблон Azure Resource Manager для [обработчика расширения Desired State Configuration](extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

*Примечание. Вы можете встретить немного отличающиеся примеры схемы.*
*Изменение в схеме произошло в выпуске за октябрь 2016 года.*
*Сведения указываются в разделе *
*[Обновление предыдущего формата](##Updating-from-the-Previous-Format)*.

## <a name="template-example-for-a-windows-vm"></a>Пример шаблона для виртуальной машины Windows

В приведенном ниже фрагменте кода показан раздел Resource шаблона.
Расширение DSC наследует свойства расширения по умолчанию, как описано в документе [о классе VirtualMachineExtension](https://docs.microsoft.com/en-us/dotnet/api/microsoft.azure.management.compute.models.virtualmachineextension?view=azure-dotnet.).

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
                  "typeHandlerVersion": "2.72",
                  "autoUpgradeMinorVersion": true,
                  "forceUpdateTag": "[parameters('dscExtensionUpdateTagVersion')]",
                  "settings": {
                    "configurationArguments": {
                        {
                            "Name": "RegistrationKey",
                            "Value": {
                                "UserName": "PLACEHOLDER_DONOTUSE",
                                "Password": "PrivateSettingsRef:registrationKeyPrivate"
                            },
                        },
                        "RegistrationUrl" : "[parameters('registrationUrl1')]",
                        "NodeConfigurationName" : "nodeConfigurationNameValue1"
                        }
                        },
                        "protectedSettings": {
                            "Items": {
                                        "registrationKeyPrivate": "[parameters('registrationKey1']"
                                    }
                        }
                    }
```

## <a name="template-example-for-windows-vmss"></a>Пример шаблона для масштабируемых наборов виртуальных машин Windows

Узел масштабируемых наборов виртуальных машин содержит раздел properties с атрибутами VirtualMachineProfile и extensionProfile. Атрибут DSC находится в разделе extensions.

Расширение DSC наследует свойства расширения по умолчанию, как описано в документе [о классе VirtualMachineScaleSetExtension](https://docs.microsoft.com/en-us/dotnet/api/microsoft.azure.management.compute.models.virtualmachinescalesetextension?view=azure-dotnet).

```json
"extensionProfile": {
            "extensions": [
                {
                    "name": "Microsoft.Powershell.DSC",
                    "properties": {
                        "publisher": "Microsoft.Powershell",
                        "type": "DSC",
                        "typeHandlerVersion": "2.72",
                        "autoUpgradeMinorVersion": true,
                        "forceUpdateTag": "[parameters('DscExtensionUpdateTagVersion')]",
                        "settings": {
                            "configurationArguments": {
                                {
                                    "Name": "RegistrationKey",
                                    "Value": {
                                        "UserName": "PLACEHOLDER_DONOTUSE",
                                        "Password": "PrivateSettingsRef:registrationKeyPrivate"
                                    },
                                },
                                "RegistrationUrl" : "[parameters('registrationUrl1')]",
                                "NodeConfigurationName" : "nodeConfigurationNameValue1"
                        }
                        },
                        "protectedSettings": {
                            "Items": {
                                        "registrationKeyPrivate": "[parameters('registrationKey1']"
                                    }
                        }
                    }
                ]
            }
        }
```

## <a name="detailed-settings-information"></a>Подробные сведения о разделе settings

В приведенной ниже схеме показана часть settings расширения Azure DSC в шаблоне Azure Resource Manager.

*Список доступных аргументов для сценария конфигурации по умолчанию*
* см. в разделе ниже, который называется *
*[Сценарий конфигурации по умолчанию](##Default-Configuration-Script)*.

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

## <a name="details"></a>Сведения

| Имя свойства | type | ОПИСАНИЕ |
| --- | --- | --- |
| settings.wmfVersion |строка |Указывает версию Windows Management Framework, которую необходимо установить на виртуальной машине. Если задать для этого свойства значение latest, будет установлена последняя версия Windows Management Framework. Для этого свойства доступны только такие значения: **4.0, 5.0, 5.0PP и latest**. Возможные значения зависят от обновлений. По умолчанию используется значение latest. |
| settings.configuration.url |строка |Указывает URL-адрес расположения, из которого можно скачать ZIP-файл конфигурации DSC. Если для доступа к предоставленному URL-адресу требуется маркер SAS, необходимо задать для свойства protectedSettings.configurationUrlSasToken значение маркера SAS. Это свойство обязательное, если заданы свойства settings.configuration.script и (или) settings.configuration.function. Если для этих свойств значение не задано, необходимо предоставить расширение, которое будет вызывать сценарий конфигурации по умолчанию для установки метаданных LCM и аргументов. |
| settings.configuration.script |строка |Указывает имя файла скрипта, содержащего определение вашей конфигурации DSC. Этот скрипт должен находиться в корневом каталоге ZIP-файла, скачанного по URL-адресу, указанному в свойстве configuration.url. Это свойство обязательное, если заданы свойства settings.configuration.url и (или) settings.configuration.script. Если для этих свойств не задано значение, необходимо применить расширение, которое будет вызывать сценарий конфигурации по умолчанию для установки метаданных LCM и аргументов. |
| settings.configuration.function |строка |Указывает имя вашей конфигурации DSC. Указанную конфигурацию необходимо добавить в скрипт, заданный в свойстве configuration.script. Это свойство обязательное, если заданы свойства settings.configuration.url и (или) settings.configuration.function. Если для этих свойств значение не задано, необходимо предоставить расширение, которое будет вызывать сценарий конфигурации по умолчанию для установки метаданных LCM и аргументов. |
| settings.configurationArguments |Коллекция |Определяет параметры, которые необходимо передать в конфигурацию DSC. Это свойство не зашифровано. |
| settings.configurationData.url |строка |Указывает URL-адрес расположения, из которого можно скачать файл данных конфигурации (в формате PDS1), используемый в качестве входных данных для вашей конфигурации DSC. Если для доступа к предоставленному URL-адресу требуется маркер SAS, необходимо задать для свойства protectedSettings.configurationDataUrlSasToken значение маркера SAS. |
| settings.privacy.dataEnabled |строка |Включает или отключает сбор данных телеметрии. Для этого свойства доступны только такие значения: **Enable, Disable или $null**. Если для этого свойства не задано значение или задано значение null, сбор данных телеметрии будет выполняться. Значение по умолчанию — ''. [Дополнительные сведения см. здесь.](https://blogs.msdn.microsoft.com/powershell/2016/02/02/azure-dsc-extension-data-collection-2/) |
| settings.advancedOptions.downloadMappings |Коллекция |Определяет альтернативные расположения для скачивания Windows Management Framework. [Дополнительные сведения см. здесь.](http://blogs.msdn.com/b/powershell/archive/2015/10/21/azure-dsc-extension-2-2-amp-how-to-map-downloads-of-the-extension-dependencies-to-your-own-location.aspx) |
| protectedSettings.configurationArguments |Коллекция |Определяет параметры, которые необходимо передать в конфигурацию DSC. Это свойство зашифровано. |
| protectedSettings.configurationUrlSasToken |строка |Указывает маркер SAS для доступа к URL-адресу, определенному в свойстве configuration.url. Это свойство зашифровано. |
| protectedSettings.configurationDataUrlSasToken |строка |Указывает маркер SAS для доступа к URL-адресу, определенному в свойстве configurationData.url. Это свойство зашифровано. |

## <a name="default-configuration-script"></a>Сценарий конфигурации по умолчанию

Дополнительные сведения об этих значениях см. в разделе [Базовые параметры](https://docs.microsoft.com/en-us/powershell/dsc/metaconfig#basic-settings).
С помощью сценария конфигурации по умолчанию расширения DSC можно настроить только свойства LCM в следующей таблице.

| Имя свойства | type | ОПИСАНИЕ |
| --- | --- | --- |
| settings.configurationArguments.RegistrationKey |SecureString |Обязательное свойство. Определяет ключ, используемый узлом для регистрации в службе автоматизации Azure в качестве пароля для объекта учетных данных PowerShell. Это значение можно автоматически обнаружить с помощью метода listkeys для учетной записи службы автоматизации. Его необходимо защитить в качестве защищенного параметра. |
| settings.configurationArguments.RegistrationUrl |строка |Обязательное свойство. Указывает URL-адрес конечной точки службы автоматизации Azure, в которой пытаются зарегистрировать узел. Это значение можно автоматически обнаружить с помощью метода Reference в учетной записи службы автоматизации. |
| settings.configurationArguments.NodeConfigurationName |строка |Обязательное свойство. Указывает конфигурацию узла в учетной записи службы автоматизации, которая будет присвоена узлу. |
| settings.configurationArguments.ConfigurationMode |строка |Задает режим для локального диспетчера конфигураций. Допустимые значения: "ApplyOnly", "ApplyandMonitior" и "ApplyandAutoCorrect".  "ApplyAndMonitor" — значение по умолчанию. |
| settings.configurationArguments.RefreshFrequencyMins | uint32 | Указывает, как часто LCM будет пытаться проверить учетную запись службы автоматизации на наличие обновлений.  Значение по умолчанию — 30.  Минимальное значение — 15. |
| settings.configurationArguments.ConfigurationModeFrequencyMins | uint32 | Указывает, как часто LCM будет проверять текущую конфигурацию.  Значение по умолчанию — 15.  Минимальное значение — 15. |
| settings.configurationArguments.RebootNodeIfNeeded | Логическое | Указывает, может ли узел автоматически перезагружаться по запросу операции DSC.  Значение по умолчанию — false. |
| settings.configurationArguments.ActionAfterReboot | строка | Указывает, что происходит после перезагрузки при применении конфигурации. Допустимые значения: "ContinueConfiguration" и "StopConfiguration". Значение по умолчанию: "ContinueConfiguration". |
| settings.configurationArguments.AllowModuleOverwrite | Логическое | Указывает, будет ли LCM перезаписывать имеющиеся модули на узле.  Значение по умолчанию — false. |

## <a name="settings-vs-protectedsettings"></a>Сравнение разделов settings и protectedSettings

Все параметры сохраняются в текстовом файле параметров на виртуальной машине.
Свойства в разделе settings общедоступные, так как они не зашифрованы в текстовом файле параметров.
Свойства в разделе protectedSettings зашифрованы с помощью сертификата, а это значит, что они не отображаются как обычный текст в файле на виртуальной машине.

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

## <a name="example"></a>Пример

Следующим примером является поведение по умолчанию расширения DSC, которое заключается в предоставлении параметров метаданных локальному диспетчеру конфигурации и регистрации в службе автоматизации DSC.
Аргументы конфигурации необходимы и будут переданы в сценарий конфигурации по умолчанию, чтобы задать метаданные LCM.

```json
"settings": {
    "configurationArguments": {
        {
            "Name": "RegistrationKey",
            "Value": {
                "UserName": "PLACEHOLDER_DONOTUSE",
                "Password": "PrivateSettingsRef:registrationKeyPrivate"
            },
        },
        "RegistrationUrl" : "[parameters('registrationUrl1')]",
        "NodeConfigurationName" : "nodeConfigurationNameValue1"
  }
},
"protectedSettings": {
    "Items": {
                "registrationKeyPrivate": "[parameters('registrationKey1']"
            }
}
```

## <a name="example-using-configuration-script-in-azure-storage"></a>Пример с использованием сценария конфигурации в службе хранилища Azure

Следующий пример основан на примере конфигурации из раздела "Приступая к работе" статьи [Общие сведения об обработчике расширения Desired State Configuration в Azure](extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
В этом примере для развертывания расширения используются шаблоны Resource Manager, а не командлеты.
Сохраните конфигурацию IisInstall.ps1, добавьте ее в ZIP-файл и передайте файл на доступный URL-адрес.
В этом примере используется хранилище BLOB-объектов Azure, но ZIP-файл можно скачать из любого произвольного расположения.

В шаблоне Azure Resource Manager следующий код указывает виртуальной машине скачать правильный файл и выполнить соответствующую функцию PowerShell.

```json
"settings": {
    "configuration": {
        "url": "https://demo.blob.core.windows.net/",
        "script": "IisInstall.ps1",
        "function": "IISInstall"
    }
},
"protectedSettings": {
    "configurationUrlSasToken": "odLPL/U1p9lvcnp..."
}
```

## <a name="updating-from-the-previous-format"></a>Обновление из предыдущего формата

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
| settings.wmfVersion |settings.wmfVersion |
| settings.configuration.url |settings.ModulesUrl |
| settings.configuration.script |Первая часть свойства settings.ConfigurationFunction (перед \\\\). |
| settings.configuration.function |Вторая часть свойства settings.ConfigurationFunction (после \\\\). |
| settings.configurationArguments |settings.Properties |
| settings.configurationData.url |protectedSettings.DataBlobUri (без маркера SAS) |
| settings.privacy.dataEnabled |settings.privacy.dataEnabled |
| settings.advancedOptions.downloadMappings |settings.advancedOptions.downloadMappings |
| protectedSettings.configurationArguments |protectedSettings.Properties |
| protectedSettings.configurationUrlSasToken |settings.SasToken |
| protectedSettings.configurationDataUrlSasToken |Маркер SAS из свойства protectedSettings.DataBlobUri |

## <a name="troubleshooting---error-code-1100"></a>Устранение неполадок — код ошибки 1100

Код ошибки 1100 указывает на проблему с входными данными пользователя в расширении DSC.
Текст этих ошибок может меняться.
Ниже приведены некоторые общие ошибки и способы их устранения.

### <a name="invalid-values"></a>Недопустимые значения

"Privacy.dataCollection is '{0}'.
Единственные возможные значения это "Enable" и "Disable".
"WmfVersion имеет значение '{0}'.
Возможные значения: … и "latest".

Проблема. Указано неразрешенное значение.

Решение. Замените недопустимое значение допустимым.
См. раздел "Сведения".

### <a name="invalid-url"></a>Недопустимый URL-адрес

"ConfigurationData.url is '{0}'. This is not a valid URL" (Значение свойства configurationData.url — "{0}". Это недопустимый URL-адрес). "DataBlobUri is '{0}'. This is not a valid URL" (Значение свойства DataBlobUri — "{0}". Это недопустимый URL-адрес). "Configuration.url is '{0}'. This is not a valid URL" (Значение свойства Configuration.url — "{0}". Это недопустимый URL-адрес).

Проблема. Указан недопустимый URL-адрес.

Решение. Проверьте все указанные URL-адреса.
Убедитесь, что все URL-адреса разрешаются в допустимые расположения, к которым расширение может получить доступ на удаленном компьютере.

### <a name="invalid-configurationargument-type"></a>Недопустимый тип свойства ConfigurationArgument

"Invalid configurationArguments type {0}" (Недопустимый тип ConfigurationArgument: {0}).

Проблема. Невозможно разрешить свойство ConfigurationArguments в объект Hashtable.

Решение. Задайте для свойства ConfigurationArguments тип Hashtable.
Следуйте формату из приведенного выше примера.
Обращайте внимание на кавычки, запятые и скобки.

### <a name="duplicate-configurationarguments"></a>Повторяющееся свойство ConfigurationArguments

"Found duplicate arguments '{0}' in both public and protected configurationArguments" (В общедоступном и защищенном свойстве configurationArguments найден повторяющийся аргумент "{0}").

Проблема. Свойства ConfigurationArguments из общедоступной и защищенной схемы settings содержат свойства с одинаковыми именами.

Решение. Удалите одно из повторяющихся свойств.

### <a name="missing-properties"></a>Отсутствующие свойства
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

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о DSC и масштабируемых наборах виртуальных машин см. в статье [Использование наборов масштабирования виртуальных машин с помощью расширения Azure DSC](../../virtual-machine-scale-sets/virtual-machine-scale-sets-dsc.md).

Получите дополнительные сведения о [безопасном управлении учетными данными посредством DSC](extensions-dsc-credentials.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Дополнительные сведения об обработчике расширений DSC см. в статье [Общие сведения об обработчике расширения для настройки требуемого состояния в Azure](extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Для получения дополнительных сведений о DSC PowerShell [посетите центр документации PowerShell](https://msdn.microsoft.com/powershell/dsc/overview).
