<properties
	pageTitle="Автомасштабирование в масштабируемом наборе виртуальных машин | Microsoft Azure"
	description="Приступите к созданию своего первого масштабируемого набора виртуальных машин Azure и научитесь управлять им"
	services="virtual-machines"
	documentationCenter=""
	authors="gbowerman"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/11/2015"
	ms.author="guybo"/>

# Автоматическое масштабирование машин в масштабируемом наборе виртуальных машин

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](virtual-machines-create-windows-powershell-service-manager.md).


В этом документе приведено краткое руководство по созданию масштабируемых наборов виртуальных машин Azure в общедоступной предварительной версии, а также описано управление ими.

Предполагается, что вы уже знакомы с использованием шаблонов диспетчера ресурсов Azure (ARM) для развертывания виртуальных машин и сетей. Если нет, перейдите по первым трем ссылкам в разделе ресурсов, чтобы получить общее представление о диспетчере ресурсов.

## Что такое масштабируемые наборы ВМ и зачем они нужны?

Масштабируемые наборы ВМ относятся к вычислительным ресурсам Azure. Их можно использовать для развертывания набора идентичных виртуальных машин и управления им.

Масштабируемые наборы, в которые входят абсолютно одинаково настроенные ВМ, поддерживают настоящее автомасштабирование — без необходимости предварительной подготовки виртуальных машин. Благодаря этому упрощается создание крупномасштабных служб для больших вычислений, больших данных, контейнерных рабочих нагрузок и любых приложений, которые требуют постоянного изменения количества вычислительных ресурсов. Операции масштабирования неявно распределяются между доменами сбоя и обновления. Общие сведения о масштабируемых наборах виртуальных машин см. в последних [записях блога Azure](https://azure.microsoft.com/blog/azure-vm-scale-sets-public-preview).

## Создание масштабируемых наборов ВМ и работа с ними

Масштабируемые наборы ВМ можно задать и развернуть с помощью шаблонов JSON и вызовов REST точно так же, как и отдельные ВМ диспетчера ресурсов Azure. Следовательно, можно использовать все стандартные методы развертывания диспетчера ресурсов Azure. Некоторые практические примеры приведены в этом документе.

Отдельные области интеграции масштабируемых наборов ВМ, в частности поддержка императивных команд в скриптах и языках программирования и полная интеграция с порталом, находятся в процессе разработки и будут выпускаться в предварительной версии поэтапно.

## Развертывание масштабируемых наборов ВМ и управление ими с помощью портала Azure

Создать масштабируемый набор ВМ с нуля на портале Azure пока невозможно. Служба поддержки сейчас работает над этой функциональностью. На первом этапе можно увидеть только масштабируемые наборы, уже созданные вами, а для создания новых вам потребуется использовать шаблоны или скрипты. Во всех случаях под «порталом» имеется в виду новый портал Azure: [https://portal.azure.com/](https://portal.azure.com/).

Возможность развертывания шаблонов на портале можно использовать для развертывания любых ресурсов, в том числе масштабируемых наборов. Простой масштабируемый набор можно очень легко развернуть с помощью следующей ссылки:

_https://portal.azure.com/#create/Microsoft.Template/uri/<link to VM Scale Set JSON template>_

Примеры шаблонов для масштабируемых наборов ВМ можно найти в репозитории шаблонов быстрого запуска Azure на GitHub: [https://github.com/Azure/azure-quickstart-templates](https://github.com/Azure/azure-quickstart-templates). Ищите шаблоны с _vmss_ в названии.

В файлах сведений этих шаблонов есть кнопка, подобная этой, со ссылкой на компонент развертывания портала.

Чтобы развернуть масштабируемый набор, нажмите кнопку, а затем укажите на портале все необходимые параметры. Примечание. Если вы не уверены, поддерживает ли ресурс верхний или смешанный регистр, для надежности всегда вводите значения параметров в нижнем регистре.

**Мониторинг масштабируемого набора ВМ на портале или в обозревателе ресурсов**

После развертывания масштабируемого набора на портале будет отображаться его общее представление. Однако в предварительной версии сначала будет невозможно просмотреть подробные свойства или детализировать ВМ в масштабируемом наборе ВМ.

Чтобы просматривать масштабируемые наборы ВМ до завершения полной интеграции с порталом, рекомендуем использовать **Обозреватель ресурсов Azure**: [https://resources.azure.com](https://resources.azure.com). Масштабируемые наборы ВМ принадлежат к ресурсам Microsoft.Compute. Чтобы просмотреть их на этом сайте, следует перейти по следующим ссылкам:

Подписки -> ваша подписка -> resourceGroups -> поставщики -> Microsoft.Compute -> virtualMachineScaleSets -> ваш масштабируемый набор ВМ -> и т. д.

## Развертывание масштабируемых наборов ВМ и управление ими с помощью PowerShell

Вы можете развертывать шаблоны масштабируемых наборов ВМ и отправлять запросы на ресурсы с помощью любой текущей версии Azure PowerShell.

**Важное примечание.** Приведенные ниже примеры предназначены для платформы [Azure PowerShell 1.0](https://azure.microsoft.com/blog/azps-1-0-pre/), в которой во многих командах используется префикс _AzureRm_. Если используется более ранняя версия PowerShell, например 0.9.8 или ниже, удалите **Rm** из примеров команд. В примерах также предполагается, что подключение к Azure с помощью имени входа уже установлено в вашей среде PowerShell (_Login- AzureRmAccount_).

Примеры:

&#35; **Создание группы ресурсов**

New-AzureRmResourcegroup -name myrg -location 'Southeast Asia'

&#35; **Создание масштабируемого набора из двух ВМ**

New-AzureRmResourceGroupDeployment -name dep1 -vmSSName myvmss -instanceCount 3 -ResourceGroupName myrg -TemplateUri [https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-linux-nat/azuredeploy.json](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-linux-nat/azuredeploy.json)

Появится запрос на ввод параметров, которые вы пропустили (например, расположение, имя пользователя, пароль в этом примере).

&#35; **Получение подробных сведений о масштабируемых наборах ВМ**

Get-AzureRmResource -name myvmss -ResourceGroupName myrg -ResourceType Microsoft.Compute/virtualMachineScaleSets -ApiVersion 2015-06-15

& #35; или для получения подробных сведений пропустите данные через команду | ConvertTo-Json -Depth 10

&#35; а для получения еще более подробных сведений добавьте в команду –debug.

&#35; уменьшение или увеличение масштаба

New-AzureRmResourceGroupDeployment -name dep2 -vmSSName myvmss -instanceCount 2 -ResourceGroupName myrg –TemplateUri [https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-scale-in-or-out/azuredeploy.json](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-scale-in-or-out/azuredeploy.json)

&#35; **Удаление масштабируемого набора**

&#35; Общее: удаление всей группы ресурсов и всего ее содержимого:

Remove-AzureRmResourceGroup -Name myrg

&#35; Точное: удаление определенного ресурса:

Remove-AzureRmResource -Name myvmss -ResourceGroupName myrg -ApiVersion 2015-06-15 -ResourceType Microsoft.Compute/virtualMachineScaleSets

### Императивные команды PowerShell для масштабируемых наборов

В предстоящую сборку Azure PowerShell войдет ряд императивных команд для создания масштабируемых наборов и управления ими без использования шаблонов. Предварительную версию этой сборки можно просмотреть здесь: [https://github.com/AzureRT/azure-powershell/releases](https://github.com/AzureRT/azure-powershell/releases). Примеры использования императивного API можно найти здесь:

[https://github.com/huangpf/azure-powershell/blob/vmss/src/ResourceManager/Compute/Commands.Compute.Test/ScenarioTests/VirtualMachineScaleSetTests.ps1](https://github.com/huangpf/azure-powershell/blob/vmss/src/ResourceManager/Compute/Commands.Compute.Test/ScenarioTests/VirtualMachineScaleSetTests.ps1)

## Развертывание масштабируемых наборов ВМ и управление ими с помощью Azure CLI

Вы можете развертывать шаблоны масштабируемых наборов ВМ и запрашивать ресурсы с помощью любой текущей версии Azure CLI.

Проще всего установить CLI из контейнера Docker. Инструкции по установке см. здесь: [https://azure.microsoft.com/blog/run-azure-cli-as-a-docker-container-avoid-installation-and-setup/](https://azure.microsoft.com/blog/run-azure-cli-as-a-docker-container-avoid-installation-and-setup/)

Инструкции по использованию CLI см. в этой статье: [https://azure.microsoft.com/documentation/articles/xplat-cli/](https://azure.microsoft.com/documentation/articles/xplat-cli/)

### Примеры CLI для масштабируемых наборов ВМ

&#35; **Создание группы ресурсов**

azure group create myrg "Southeast Asia"

&#35; **Создание масштабируемого набора ВМ**

azure group deployment create -g myrg -n dep2 --template-uri [https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-linux-nat/azuredeploy.json](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-linux-nat/azuredeploy.json)

&#35; эта команда запрашивает параметры динамически, но их также можно передать в качестве аргументов

&#35; **Получение сведений о масштабируемых наборах ВМ**

azure resource show -n vmssname -r Microsoft.Compute/virtualMachineScaleSets -o 2015-06-15 -g myrg

&#35; Получение более подробных сведений

azure resource show –n vmssname –r Microsoft.Compute/virtualMachineScaleSets –o 2015-06-15 –g myrg –json –vv

В предстоящей сборке Azure CLI появятся императивные команды для развертывания масштабируемых наборов ВМ и непосредственного управления ими без шаблонов, а также выполнения операций на виртуальных машинах в наборах ВМ. Сборку можно отслеживать здесь: [https://github.com/AzureRT/azure-xplat-cli/releases/](https://github.com/AzureRT/azure-xplat-cli/releases/)

## Шаблоны масштабируемых наборов ВМ

В этом разделе мы рассмотрим простой шаблон Azure для создания масштабируемых наборов ВМ. Он отличается от шаблонов, которые используются для создания одного экземпляра виртуальных машин. Видео с подробным рассмотрением шаблона масштабируемого набора ВМ см. здесь: [https://channel9.msdn.com/Blogs/Windows-Azure/VM-Scale-Set-Template-Dissection/player](https://channel9.msdn.com/Blogs/Windows-Azure/VM-Scale-Set-Template-Dissection/player)

### Структура шаблона

Начнем с простого шаблона, который создает масштабируемый набор виртуальных машин под управлением ОС Ubuntu, и разобьем его на составляющие. В этом примере также создаются ресурсы, от которых зависит масштабируемый набор ВМ: виртуальная сеть, учетная запись хранения и т. д. Пример можно найти здесь: [https://raw.githubusercontent.com/gbowerman/azure-myriad/master/vmss-ubuntu-vnet-storage.json](https://raw.githubusercontent.com/gbowerman/azure-myriad/master/vmss-ubuntu-vnet-storage.json). Обратите внимание, что в этом примере «hello world» не используется прямой способ подключения к виртуальным машинам в масштабируемом наборе ВМ из-за пределов виртуальной сети, так как виртуальным машинам назначаются внутренние IP-адреса. Способы подключения из-за пределов сети с помощью внешнего балансировщика нагрузки или пограничных серверов см. в разделе «Сценарии» и в примерах в статье [Шаблоны быстрого запуска Azure](https://github.com/Azure/azure-quickstart-templates).

### Заголовок шаблона.

Укажите схему и версию содержимого:

{

   "$schema": "http://schema.management.azure.com/schemas/2015-01-01-preview/deploymentTemplate.json",

   "contentVersion": "1.0.0.0",

### Параметры

Как и в любом другом шаблоне диспетчера ресурсов Azure (ARM), в этом разделе определяются параметры, которые задаются во время развертывания. В нашем примере это имя масштабируемого набора ВМ, начальное количество экземпляров ВМ, уникальная строка, используемая при создании учетных записей хранения. Если неизвестно, как обрабатывается регистр, всегда вводите в нижнем регистре значения для таких объектов, как учетные записи хранения.

````
 "parameters": {

    "vmSSName": {

      "type": "string",

      "metadata": {

        "description": "Scale Set name, also used in this template as a base for naming resources (hence limited less than 10 characters)."

      },

      "maxLength": 10

    },

    "instanceCount": {

      "type": "int",

      "metadata": {

        "description": "Number of VM instances"

      },

      "maxValue": 100

    },

    "adminUsername": {

      "type": "string",

      "metadata": {

        "description": "Admin username on all VMs."

      }

    },

    "adminPassword": {

      "type": "securestring",

      "metadata": {

        "description": "Admin password on all VMs."

      }

    }

  },
````

### Переменные

Стандартный компонент шаблона ARM, определяющий переменные, на которые мы будем ссылаться позже в этом шаблоне. В этом примере мы будем использовать переменные для задания необходимой ОС, некоторых сведений о конфигурации сети, параметров хранилища и расположения. Для выбора расположения из группы ресурсов, в которой оно развернуто, будет использоваться функция _location()_.

Обратите внимание, что для префикса учетной записи хранения задан массив уникальных строк. Подробное пояснение см. в разделе «Хранилище».

````
  "variables": {

    "apiVersion": "2015-06-15",

    "newStorageAccountSuffix": "[concat(parameters('vmssName'), 'sa')]",

    "uniqueStringArray": [

      "[concat(uniqueString(concat(resourceGroup().id, deployment().name, variables('newStorageAccountSuffix'), '0')))]",

      "[concat(uniqueString(concat(resourceGroup().id, deployment().name, variables('newStorageAccountSuffix'), '1')))]",

      "[concat(uniqueString(concat(resourceGroup().id, deployment().name, variables('newStorageAccountSuffix'), '2')))]",

      "[concat(uniqueString(concat(resourceGroup().id, deployment().name, variables('newStorageAccountSuffix'), '3')))]",

      "[concat(uniqueString(concat(resourceGroup().id, deployment().name, variables('newStorageAccountSuffix'), '4')))]"

    ],

    "vmSize": "Standard\_A1",

    "vhdContainerName": "[concat(parameters('vmssName'), 'vhd')]",

    "osDiskName": "[concat(parameters('vmssName'), 'osdisk')]",

    "virtualNetworkName": "[concat(parameters('vmssName'), 'vnet')]",

    "subnetName": "[concat(parameters('vmssName'), 'subnet')]",

    "nicName": "[concat(parameters('vmssName'), 'nic')]",

    "ipConfigName": "[concat(parameters('vmssName'), 'ipconfig')]",

    "addressPrefix": "10.0.0.0/16",

    "subnetPrefix": "10.0.0.0/24",

    "storageAccountType": "Standard\_LRS",

    "location": "[resourceGroup().location]",

    "osType": {

      "publisher": "Canonical",

      "offer": "UbuntuServer",

      "sku": "15.10",

      "version": "latest"

    },

    "imageReference": "[variables('osType')]"

  },
````

### Ресурсы

В этом разделе определяется каждый тип ресурса.

````
   "resources": [
````


**Хранилище.** Во время создания масштабируемого набора ВМ задается массив учетных записей хранения. Затем диски ОС для экземпляров ВМ равномерно распределяются между всеми учетными записями. В будущем масштабируемые наборы ВМ перейдут на использование управляемого диска. Это означает, что вместо управления учетными записями хранения просто нужно будет описывать свойства хранилища в определении масштабируемого набора. Сейчас нужно создать необходимое количество учетных записей хранения заранее. Рекомендуем создать 5 учетных записей хранения, которые смогут без проблем поддерживать до 100 виртуальных машин в масштабируемом наборе (текущий максимум).

При создании нескольких учетных записей хранения они распределяются по разделам в кластере хранилища. Схема их распределения зависит от первой буквы имени учетной записи хранения. Поэтому для достижения оптимальной производительности рекомендуем начинать имена новых учетных записей хранения с неиспользуемых ранее букв алфавита. Имя можно задать вручную или, как в нашем примере, используя функцию uniqueString() для псевдослучайного распределения.

````
    {

      "type": "Microsoft.Storage/storageAccounts",

      "name": "[concat(variables('uniqueStringArray')[copyIndex()], variables('newStorageAccountSuffix'))]",

      "location": "[variables('location')]",

      "apiVersion": "[variables('apiVersion')]",

      "copy": {

        "name": "storageLoop",

        "count": 5

      },

      "properties": {

        "accountType": "[variables('storageAccountType')]"

      }

    },
````

**Виртуальная сеть.** Создайте виртуальную сеть. Обратите внимание, что у вас может быть несколько масштабируемых наборов, как и отдельных виртуальных машин, в одной виртуальной сети.

````
    {

      "type": "Microsoft.Network/virtualNetworks",

      "name": "[variables('virtualNetworkName')]",

      "location": "[variables('location')]",

      "apiVersion": "[variables('apiVersion')]",

      "properties": {

        "addressSpace": {

          "addressPrefixes": [

            "[variables('addressPrefix')]"

          ]

        },

        "subnets": [

          {

            "name": "[variables('subnetName')]",

            "properties": {

              "addressPrefix": "[variables('subnetPrefix')]"

            }

          }

        ]

      }

    },
````

### Ресурс virtualMachineScaleSets

Ресурс _virtualMachineScaleSets_ во многом похож на _virtualMachines_. Эти ресурсы предоставляются поставщиком ресурсов Microsoft.Compute и находятся на одном уровне. Основное отличие заключается в значении _capacity_, которое определяет количество создаваемых ВМ, а также в том, что оно применяется ко всем виртуальным машинам в масштабируемом наборе.

Обратите внимание, что раздел _dependsOn_ ссылается на учетные записи хранения и виртуальную сеть, созданную ранее, а также на то, что capacity ссылается на параметр _instanceCount_.

````
    {

      "type": "Microsoft.Compute/virtualMachineScaleSets",

      "name": "[parameters('vmssName')]",

      "location": "[variables('location')]",

      "apiVersion": "[variables('apiVersion')]",

      "dependsOn": [

        "[concat('Microsoft.Storage/storageAccounts/', variables('uniqueStringArray')[0], variables('newStorageAccountSuffix'))]",

        "[concat('Microsoft.Storage/storageAccounts/', variables('uniqueStringArray')[1], variables('newStorageAccountSuffix'))]",

        "[concat('Microsoft.Storage/storageAccounts/', variables('uniqueStringArray')[2], variables('newStorageAccountSuffix'))]",

        "[concat('Microsoft.Storage/storageAccounts/', variables('uniqueStringArray')[3], variables('newStorageAccountSuffix'))]",

        "[concat('Microsoft.Storage/storageAccounts/', variables('uniqueStringArray')[4], variables('newStorageAccountSuffix'))]",

        "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"

      ],

      "sku": {

        "name": "[variables('vmSize')]",

        "tier": "Standard",

        "capacity": "[parameters('instanceCount')]"

      },
````

**Свойства**

В масштабируемом наборе ВМ есть параметр upgradePolicy. Будущие версии будут поддерживать частичные обновления (например, изменение конфигурации 20 % виртуальных машин одновременно). Но пока для upgradePolicy можно задать только значения manual или automatic. Если при значении manual изменить шаблон и повторно развернуть его, изменения вступят в силу только после создания новых виртуальных машин или обновления их расширений, например при увеличении _capacity_. Значение automatic предусматривает «мгновенное» обновление всех виртуальных машин и их полную перезагрузку. Как правило, значение manual является более безопасным решением. Если необходимо постепенное обновление, отдельные виртуальные машины можно удалить, а затем повторно развернуть их.

````
      "properties": {

         "upgradePolicy": {

         "mode": "Manual"

        },
````

**Properties->virtualMachineProfile**

Здесь вы ссылаетесь на учетные записи хранения, созданные ранее, как на контейнеры для виртуальных машин. То есть вам нужно создать только учетные записи, а не фактические контейнеры.

````
        "virtualMachineProfile": {

          "storageProfile": {

            "osDisk": {

              "vhdContainers": [

                "[concat('https://', variables('uniqueStringArray')[0], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]",

                "[concat('https://', variables('uniqueStringArray')[1], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]",

                "[concat('https://', variables('uniqueStringArray')[2], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]",

                "[concat('https://', variables('uniqueStringArray')[3], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]",

                "[concat('https://', variables('uniqueStringArray')[4], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]"

              ],

              "name": "[variables('osDiskName')]",

              "caching": "ReadOnly",

              "createOption": "FromImage"

            },

            "imageReference": "[variables('imageReference')]"

          },
````

**Properties->osProfile**

Масштабируемые наборы ВМ от отдельных ВМ отличает только префикс в виде имени компьютера. Экземпляры ВМ в масштабируемом наборе ВМ будут создаваться с такими именами: myvm\_0, myvm\_1 и т. д.

````
          "osProfile": {

            "computerNamePrefix": "[parameters('vmSSName')]",

            "adminUsername": "[parameters('adminUsername')]",

            "adminPassword": "[parameters('adminPassword')]"

          },
````

**Properties->networkProfile**

Определяя сетевой профиль для масштабируемого набора ВМ, не забывайте о том, что конфигурации NIC и IP имеют имена. В конфигурации NIC есть параметр _primary_ и идентификатор подсети, который ссылается на ресурс подсети, созданный в указанной выше виртуальной сети.

````
          "networkProfile": {

            "networkInterfaceConfigurations": [

              {

                "name": "[variables('nicName')]",

                "properties": {

                  "primary": "true",

                  "ipConfigurations": [

                    {

                      "name": "[variables('ipConfigName')]",

                      "properties": {

                        "subnet": {

                          "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'), '/subnets/', variables('subnetName'))]"

                        }

                      }

                    }

                  ]

                }

              }

            ]

          }
````

**Properties->extensionProfile**

Простой пример, приведенный выше, не требует профиля расширения. Его можно увидеть в действии в этом шаблоне, который разворачивает Apache и PHP с помощью расширения CustomScript: [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-lapstack-autoscale](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-lapstack-autoscale). Обратите внимание, что в этом примере свойства сетевого IP-адреса также ссылаются на балансировщик нагрузки. Балансировщики нагрузки будут описаны более подробно в разделе «Сценарии масштабируемого набора ВМ».

## Сценарии масштабируемого набора ВМ

В этом разделе рассматриваются некоторые типичные сценарии масштабируемых наборов ВМ и примеры шаблонов. Хотя сейчас для сценариев требуются шаблоны, некоторые из них будут интегрированы с порталом в будущем. Тем не менее некоторые службы более высокого уровня Azure (например пакетная служба, Service Fabric, Azure Container Service) будут использовать эти сценарии

## Подключение по RDP или SSH к экземплярам масштабируемых наборов ВМ

Масштабируемый набор ВМ создается внутри виртуальной сети, при этом отдельным виртуальным машинам не выделяются общедоступные IP-адреса. Это хорошее решение, так как оно избавляет вас от дополнительных затрат и расходов на управление, связанных с выделением отдельных IP-адресов для всех ресурсов без состояния в сетке вычислений. К этим ВМ можно легко подключиться с других ресурсов в виртуальной сети, в том числе с ресурсов с общедоступными IP-адресами, например с балансировщиков нагрузки или отдельных виртуальных машин.

Ниже описываются два простых способа подключения к виртуальным машинам масштабируемого набора.

### Подключение к ВМ с помощью правил преобразования сетевых адресов (NAT)

Можно создать общедоступный IP-адрес, назначить его балансировщику нагрузки и определить правила для входящих подключений NAT, которые сопоставляют порт на IP-адресе с портом виртуальной машины из масштабируемого набора ВМ. Например,

Public IP->Port 50000 -> vmss\_0->Port 22 Public IP->Port 50001 -> vmss\_1->Port 22 Public IP->Port 50002-> vmss\_2->Port 22

В следующем примере показано создание масштабируемого набора ВМ, который использует правила NAT для установки SSH-подключения к каждой виртуальной машине из набора с помощью одного общедоступного IP-адреса: [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat)

Далее приведен пример организации такой же схемы для RDP и Windows: [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-nat](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-nat)

### Подключение к ВМ с помощью «пограничного сервера»

Если в одной виртуальной сети создается масштабируемый набор ВМ и автономная ВМ, эта машина и машины из набора могут подключаться друг к другу с помощью своих внутренних IP-адресов в соответствии с определением виртуальной сети или подсети.

После создания и назначения общедоступного IP-адреса автономной ВМ к ней можно будет подключиться по RDP или SSH, а затем с нее подключиться к своим экземплярам масштабируемого набора ВМ. На этом этапе можно заметить, что простой масштабируемый набор ВМ надежнее, чем автономная ВМ с общедоступным IP-адресом, сконфигурированная по умолчанию.

В качестве примера этот шаблон создает простой кластер Mesos с автономной основной ВМ, которая управляет кластером виртуальных машин на основе масштабируемого набора ВМ: [https://github.com/gbowerman/azure-myriad/blob/master/mesos-vmss-simple-cluster.json](https://github.com/gbowerman/azure-myriad/blob/master/mesos-vmss-simple-cluster.json)

## Балансировка нагрузки циклическим перебором для экземпляров масштабируемого набора ВМ

Чтобы передать работу вычислительному кластеру виртуальных машин с помощью «циклического перебора», следует настроить балансировщик нагрузки Azure с соответствующими правилами балансировки. Также можно настроить тестеры для проверки работы приложения с помощью проверки связи с портами с указанными протоколом, интервалом и путем запроса.

В следующем примере создается масштабируемый набор ВМ с машинами, на которых запущен веб-сервер IIS и используется балансировщик нагрузки для балансировки нагрузки, получаемой каждой ВМ. В примере также используется протокол HTTP для проверки связи с определенным URL-адресом на каждой ВМ: [https://github.com/gbowerman/azure-myriad/blob/master/vmss-win-iis-vnet-storage-lb.json](https://github.com/gbowerman/azure-myriad/blob/master/vmss-win-iis-vnet-storage-lb.json). Посмотрите на тип ресурса Microsoft.Network/loadBalancers networkProfile и extensionProfile в virtualMachineScaleSet.

## Экземпляры масштабируемого набора ВМ с автомасштабированием Azure

Если требуется изменять количество экземпляров (_capacity_) масштабируемого набора ВМ в зависимости от использования ЦП, памяти, диска или каких-либо других событий, поставщик ресурсов Microsoft.Insights предоставляет широкий набор параметров автомасштабирования. Дополнительные сведения о доступных параметрах см. в документации к Insights REST: [https://msdn.microsoft.com/library/azure/dn931953.aspx](https://msdn.microsoft.com/library/azure/dn931953.aspx).

Автомасштабирование Insights интегрируется непосредственно с масштабируемыми наборами ВМ. Для его настройки определите тип ресурса Microsoft.Insights или autoscaleSettings с _targetResourceUri_ и _metricResourceUri_, которые ссылаются на масштабируемый набор. При определении масштабируемого набора включите _extensionProfile_, который устанавливает агент системы диагностики Azure (WAD) в ОС Windows или Linux Diagnostic Extension (LDE) в ОС Linux. В следующем примере для ОС Linux виртуальные машины добавляются до предварительно заданного предела, когда средняя загрузка ЦП превышает 50 %. Это происходит со временным разбиением в 1 минуту и периодом выборки 5 минут:

[https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-lapstack-autoscale](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-lapstack-autoscale).

В будущем автомасштабирование в масштабируемых наборах ВМ также будет интегрировано непосредственно с порталом Azure.

## Развертывание масштабируемого набора ВМ в качестве вычислительного кластера в диспетчере кластеров PaaS

Иногда масштабируемые наборы ВМ называют рабочими ролями следующего поколения. Это вполне допустимое название, но вместе с тем существует риск перепутать функции масштабируемого набора ВМ с функциями рабочей роли PaaS v1.

В этом смысле масштабируемые наборы ВМ предоставляют настоящую «рабочую роль» или рабочий ресурс. Они являются обобщенным вычислительным ресурсом, независимым от платформ и сред выполнения, который может настраиваться и интегрироваться с диспетчером ресурсов Azure IaaS.

Рабочая роль PaaS v1, ограниченная с точки зрения поддержки платформ и сред выполнения (только для образов платформы Windows), также включает такие службы: переключение виртуального IP-адреса, настраиваемые параметры обновления, определенные параметры развертывания приложения или среды выполнения. Они либо _еще_ не доступны в масштабируемых наборах ВМ, либо будут предоставляться другими службами PaaS более высокого уровня, например Service Fabric. Учитывая эти факторы, масштабируемые наборы ВМ можно рассматривать как инфраструктуру, поддерживающую PaaS. Например, такие решения PaaS, как Service Fabric, или диспетчеры кластера, как Mesos, можно построить на основе масштабируемых наборов ВМ в качестве масштабируемого уровня вычислений.

В следующем примере кластер Mesos развертывает масштабируемый набор ВМ в одну виртуальную сеть с автономной ВМ. Образец Mesos является автономной ВМ, а масштабируемый набор ВМ представляет набор подчиненных узлов: [https://github.com/gbowerman/azure-myriad/blob/master/mesos-vmss-simple-cluster.json](https://github.com/gbowerman/azure-myriad/blob/master/mesos-vmss-simple-cluster.json). Будущие версии [Azure Container Service](https://azure.microsoft.com/blog/azure-container-service-now-and-the-future/) будут разворачивать более сложные или фиксированные версии этого сценария на основе масштабируемых наборов ВМ.

## Уменьшение или увеличение масштаба набора ВМ

Чтобы увеличить или уменьшить число виртуальных машин в масштабируемом наборе ВМ, необходимо просто изменить свойство _capacity_ и повторно развернуть шаблон. Это упрощает создание собственного пользовательского уровня масштабирования, если требуется задать события пользовательского масштабирования, которые не поддерживаются автомасштабированием Azure.

При повторном развертывании шаблона для изменения емкости можно задать гораздо меньший шаблон, который будет включать только номер SKU и обновленную емкость. Пример приведен здесь: [https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-scale-in-or-out/azuredeploy.json](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-linux-nat/azuredeploy.json).

## Рекомендации по производительности и масштабированию набора ВМ

- В общедоступной предварительной версии не создавайте более 500 виртуальных машин в нескольких масштабируемых наборах ВМ одновременно. В будущем это количество будет увеличено.
- Планируйте не более 40 виртуальных машин на учетную запись хранения.
- Максимально распределяйте первые буквы имен учетных записей хранения. Примеры шаблонов масштабируемых наборов ВМ в разделе [Шаблоны быстрого запуска Azure](https://github.com/Azure/azure-quickstart-templates/) демонстрируют, как это сделать.
- При использовании пользовательских виртуальных машин планируйте не более 40 ВМ в наборе ВМ для одной учетной записи хранения. Прежде чем начинать развертывание масштабируемого набора ВМ, необходимо предварительно скопировать образ в учетную запись хранения. Дополнительные сведения см. в разделе ответов и вопросов.
- Планируйте не более 2048 виртуальных машин в одной виртуальной сети. В будущем это количество будет увеличено.
- Допустимое число виртуальных машин, которые можно создать, ограничивается вашей квотой для вычислительных ядер в регионе. Возможно, придется обратиться в службу поддержки, чтобы увеличить квоту вычислений, даже если сейчас у вас установлен высокий предел ядер для использования с облачными службами или IaaS v1. Запрос на квоту можно отправить с помощью команды Azure CLI _azure vm list-usage_ и команды PowerShell _Get AzureRmVMUsage_ (если используется версия PowerShell ниже 1.0, используйте _Get AzureVMUsage_).


## Часто задаваемые вопросы о масштабируемом наборе ВМ

**Вопрос. Сколько виртуальных машин может входить в масштабируемый набор ВМ?**

О. 100, если использовать образы платформы, которые можно распределить между несколькими учетными записями хранения. При использовании пользовательских образов — до 40, так как в предварительной версии они ограничены одной учетной записью хранения.

**Какие другие ограничения по ресурсам существуют для масштабируемых наборов ВМ?**

О. Существующие ограничения служб Azure см. в следующей статье: [https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/)

В предварительной версии можно создавать не более 500 виртуальных машин в нескольких масштабируемых наборах ВМ в одном регионе.

**Вопрос. Поддерживаются ли диски данных в масштабируемых наборах ВМ?**

О. В первом выпуске — нет. Ниже перечислены варианты хранения данных.

- Файлы Azure (общие диски SMB).

- Диск операционной системы.

- Временный диск (локальный, не поддерживаемый хранилищем Azure).

- Служба внешних данных (например удаленная база данных, таблицы Azure, BLOB-объекты Azure).

**Вопрос. Какие регионы Azure поддерживают масштабируемые наборы ВМ?**

О. Любой регион, поддерживающий диспетчер ресурсов Azure, поддерживает масштабируемые наборы ВМ.

**Вопрос. Как создать масштабируемый набор ВМ с помощью пользовательского образа?**

О. Оставьте значение свойства vhdContainers пустым:

````
"storageProfile": {
   "osDisk": {
      "name": "vmssosdisk",
      "caching": "ReadOnly",
      "createOption": "FromImage",
      "image": {
         "uri": [https://mycustomimage.blob.core.windows.net/system/Microsoft.Compute/Images/mytemplates/template-osDisk.vhd](https://mycustomimage.blob.core.windows.net/system/Microsoft.Compute/Images/mytemplates/template-osDisk.vhd)
     },
     "osType": "Windows"
  }
},
````

**Вопрос. Если уменьшить емкость масштабируемого набора ВМ с 20 до 15, какие виртуальные машины будут удалены?**

О. Последние 5 виртуальных машин (с наибольшими индексами) будут удалены.

**Вопрос. Что будет, если затем я увеличу емкость с 15 до 18?**

После увеличения емкости до 18 будут созданы виртуальные машины с индексами 15, 16 и 17. В обоих случаях виртуальные машины распределяются между доменами сбоя и обновления.

**Вопрос. Можно ли задать последовательность выполнения при использовании нескольких расширений в масштабируемом наборе ВМ?**

О. Не напрямую, но для расширения customScript скрипт может ожидать завершения другого расширения (например, отслеживая журнал расширений). Дополнительную информацию см. в статье [https://github.com/Azure/azure-quickstart-templates/blob/master/201-vmss-lapstack-autoscale/install\_lap.sh](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vmss-lapstack-autoscale/install_lap.sh)).

**Вопрос. Работают ли масштабируемые наборы ВМ с группами доступности Azure?**

О. Да. Масштабируемый набор является неявной группой доступности с 3 доменами сбоя и 5 доменами обновления. Ничего настраивать в virtualMachineProfile не нужно. В будущих выпусках масштабируемые наборы, скорее всего, будут охватывать несколько клиентов, но пока масштабируемый набор представляет собой одну группу доступности.

## Дальнейшие действия

В предварительной версии масштабируемых наборов ВМ документация будет расширяться. Кроме этого появятся дополнительные возможности интеграции, в частности портал и автомасштабирование.

Ваши отзывы очень важны для создания службы, в которой будут все необходимые вам функции. Пожалуйста, сообщите нам, какие функции работают, какие не работают и какие следует улучшить. Отправляйте свои отзывы по адресу [vmssfeedback@microsoft.com](mailto:vmssfeedback@microsoft.com).

## Ресурсы

| **Раздел** | **Ссылка** |
| --- | --- |
| Общие сведения о диспетчере ресурсов Azure | [https://azure.microsoft.com/documentation/articles/resource-group-overview/](https://azure.microsoft.com/documentation/articles/resource-group-overview/) |
| Шаблоны диспетчера ресурсов Azure | [https://azure.microsoft.com/documentation/articles/resource-group-authoring-templates/](https://azure.microsoft.com/documentation/articles/resource-group-authoring-templates/) |
| Функции шаблонов в диспетчере ресурсов Azure | [https://azure.microsoft.com/documentation/articles/resource-group-template-functions/](https://azure.microsoft.com/documentation/articles/resource-group-template-functions/) |
| Примеры шаблонов (github) | [https://github.com/Azure/azure-quickstart-templates](https://github.com/Azure/azure-quickstart-templates) |
| Руководство по REST API для масштабируемых наборов ВМ | [https://msdn.microsoft.com/library/mt589023.aspx](https://msdn.microsoft.com/library/mt589023.aspx) |
| Видеоролики о масштабируемых наборах ВМ | [https://channel9.msdn.com/Blogs/Regular-IT-Guy/Mark-Russinovich-Talks-Azure-Scale-Sets/](https://channel9.msdn.com/Blogs/Regular-IT-Guy/Mark-Russinovich-Talks-Azure-Scale-Sets/) [https://channel9.msdn.com/Shows/Cloud+Cover/Episode-191-Virtual-Machine-Scale-Sets-with-Guy-Bowerman](https://channel9.msdn.com/Shows/Cloud+Cover/Episode-191-Virtual-Machine-Scale-Sets-with-Guy-Bowerman) [https://channel9.msdn.com/Blogs/Windows-Azure/VM-Scale-Set-Template-Dissection/player](https://channel9.msdn.com/Blogs/Windows-Azure/VM-Scale-Set-Template-Dissection/player) |
| Параметры автомасштабирования (MSDN) | [https://msdn.microsoft.com/library/azure/dn931953.aspx](https://msdn.microsoft.com/library/azure/dn931953.aspx) |
| Устранение неполадок при развертывании групп ресурсов в Azure | [https://azure.microsoft.com/documentation/articles/resource-group-deploy-debug/](https://azure.microsoft.com/documentation/articles/resource-group-deploy-debug/) |

<!---HONumber=Nov15_HO3-->