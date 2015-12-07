<properties
  pageTitle="Создание кластера MongoDB в Ubuntu | Microsoft Azure"
  description="Создание кластера MongoDB в системе Ubuntu на основе шаблона диспетчера ресурсов Azure с использованием Azure PowerShell или интерфейса командной строки Azure"
  services="virtual-machines"
  documentationCenter=""
  authors="scoriani"
  manager="timlt"
  editor="tysonn"
  tags="azure-resource-manager"/>

<tags
  ms.service="virtual-machines"
  ms.workload="multiple"
  ms.tgt_pltfrm="vm-windows"
  ms.devlang="na"
  ms.topic="article"
  ms.date="04/29/2015"
  ms.author="scoriani"/>

# Создание кластера MongoDB в системе Ubuntu на основе шаблона диспетчера ресурсов Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]Классическая модель развертывания.


MongoDB — это база данных документов с открытым кодом, которая отличается высокой производительностью и доступностью, а также поддерживает автоматическое масштабирование. MongoDB можно установить как автономную базу данных либо в составе кластера, задействовав встроенные возможности репликации. В определенных ситуациях с помощью репликации можно повысить производительность операций чтения. Клиенты могут отправлять операции чтения и записи на различные серверы. Кроме того, можно поддерживать работу отдельных копий в разных центрах обработки данных, чтобы повысить степень локализации и доступности данных в распределенных приложениях. При использовании MongoDB репликация также обеспечивает избыточность и повышает доступность данных. Благодаря репликации копий на несколько серверов БД сведения защищены от потери на одном сервере. Репликация также позволяет восстанавливать данные после сбоев оборудования и нарушений в работе. Отдельные копии данных можно выделить для аварийного восстановления, формирования отчетов или резервного копирования.

Помимо различных возможностей, уже доступных в Azure Marketplace, теперь вы также можете легко развертывать кластер MongoDB на виртуальных машинах Ubuntu на базе шаблона диспетчера ресурсов Azure с помощью [Azure PowerShell](../powershell-install-configure.md) или [интерфейса командной строки Azure](../xplat-cli-install.md).

Топология новых кластеров, развертываемых на базе этого шаблона, представлена на следующей схеме, однако вы можете легко создавать и другие топологии, адаптировав с учетом своих требований шаблон, описанный в этой статье.

![cluster-architecture](media/virtual-machines-mongodb-template/cluster-architecture.png)

С помощью специального параметра можно задать количество узлов в новом кластере MongoDB, а еще один параметр позволяет развернуть в той же виртуальной сети экземпляр виртуальной машины (Jumpbox) с общедоступным IP-адресом. Благодаря этому вы сможете подключаться к кластеру из общедоступной части Интернета и выполнять с ним административные задачи. Также есть параметр, позволяющий добавить в набор реплик узел арбитра, что обычно рекомендуется делать для четного числа элементов. Дополнительные сведения о топологиях репликации MongoDB и другую информацию см. в официальной [документации по MongoDB](http://docs.mongodb.org/manual/core/replication-introduction/).

После развертывания экземпляр Jumpbox доступен по заданному DNS-адресу на порту SSH 22.

Прежде чем углубляться в дополнительную информацию, связанную с диспетчером ресурсов Azure и шаблоном, который мы использовали для этого развертывания, проверьте правильность настроек Azure PowerShell или интерфейса командной строки Azure.

[AZURE.INCLUDE [arm-getting-setup-powershell](../../includes/powershell-preview-inline-include.md)]

[AZURE.INCLUDE [xplat-getting-set-up-arm](../../includes/xplat-getting-set-up-arm.md)]

## Создание кластера MongoDB на основе шаблона диспетчера ресурсов

Чтобы создать кластер MongoDB с использованием шаблона диспетчера ресурсов из репозитория шаблонов Github, выполните перечисленные ниже действия. Для каждого этапа приведены инструкции как для среды Azure PowerShell, так и для интерфейса командной строки Azure.

### Шаг 1-а. Загрузка файлов шаблонов с помощью PowerShell

Создайте локальную папку для JSON-файлов шаблонов и других файлов (например, C:\\Azure\\Templates\\MongoDB).

В следующем примере подставьте имя своей локальной папки и выполните команды.

    $folderName="C:\Azure\Templates\MongoDB"
    $webclient = New-Object System.Net.WebClient
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/azuredeploy.json"
    $filePath = $folderName + "\azuredeploy.json"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/azuredeploy-parameters.json"
    $filePath = $folderName + "\azuredeploy-parameters.json"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/arbiter-resources.json"
    $filePath = $folderName + "\arbiter-resources.json"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/empty-resources.json"
    $filePath = $folderName + "\empty-resources.json"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/jumpbox-resources.json"
    $filePath = $folderName + "\jumpbox-resources.json"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/member-resources-D1.json"
    $filePath = $folderName + "\member-resources-D1.json"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/member-resources-D2.json"
    $filePath = $folderName + "\member-resources-D2.json"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/member-resources-D3.json"
    $filePath = $folderName + "\member-resources-D3.json"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/member-resources-D4.json"
    $filePath = $folderName + "\member-resources-D4.json"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/member-resources-D11.json"
    $filePath = $folderName + "\member-resources-D11.json"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/member-resources-D12.json"
    $filePath = $folderName + "\member-resources-D12.json"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/member-resources-D13.json"
    $filePath = $folderName + "\member-resources-D13.json"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/member-resources-D14.json"
    $filePath = $folderName + "\member-resources-D14.json"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/mongodb-ubuntu-install.sh"
    $filePath = $folderName + "\mongodb-ubuntu-install.sh"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/metadata.json"
    $filePath = $folderName + "\metadata.json"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/shared-resources.json"
    $filePath = $folderName + "\shared-resources.json"
    $webclient.DownloadFile($url,$filePath)  

### Шаг 1-б. Загрузите файлы шаблонов с помощью интерфейса командной строки Azure

В следующем примере показано, как клонировать репозиторий шаблона с помощью выбранного Git-клиента.

    git clone https://github.com/Azure/azure-quickstart-templates C:\Azure\Templates

После завершения найдите папку mongodb-high-availability в своем каталоге C:\\Azure\\Templates.

### Шаг 2 (необязательный). Изучение параметров шаблона

При развертывании нестандартных решений, таких как кластер MongoDB, вы должны указать набор параметров конфигурации для работы с рядом необходимых параметров. Объявив эти параметры в определении шаблона, во время развертывания можно указать их значения во внешнем файле или в командной строке.

В разделе parameters в верхней части файла azuredeploy.json есть набор параметров, необходимых шаблону для настройки кластера MongoDB. Следующий пример отображает раздел parameters файла azuredeploy.json из этого шаблона.

    "parameters": {
      "adminUsername": {
          "type": "string",
          "metadata": {
            "Description": "Administrator user name used when provisioning virtual machines (which also becomes a system user administrator in MongoDB)"
          }
        },
        "adminPassword": {
          "type": "securestring",
          "metadata": {
            "Description": "Administrator password used when provisioning virtual machines (which is also a password for the system administrator in MongoDB)"
          }
        },
        "storageAccountName": {
          "type": "string",
          "defaultValue": "",
          "metadata": {
            "Description": "Unique namespace for the storage account where the Virtual Machine's disks will be placed (this name will be used as a prefix to create one or more storage accounts as per t-shirt size)"
          }
        },
        "region": {
          "type": "string",
          "metadata": {
            "Description": "Location where resources will be provisioned"
          }
        },
        "virtualNetworkName": {
          "type": "string",
          "defaultValue": "mongodbVnet",
          "metadata": {
            "Description": "The arbitrary name of the virtual network provisioned for the MongoDB deployment"
          }
        },
        "subnetName": {
          "type": "string",
          "defaultValue": "mongodbSubnet",
          "metadata": {
            "Description": "Subnet name for the virtual network that resources will be provisioned in to"
          }
        },
        "addressPrefix": {
          "type": "string",
          "defaultValue": "10.0.0.0/16",
          "metadata": {
            "Description": "The network address space for the virtual network"
          }
        },
        "subnetPrefix": {
          "type": "string",
          "defaultValue": "10.0.0.0/24",
          "metadata": {
            "Description": "The network address space for the virtual subnet"
          }
        },
        "nodeAddressPrefix": {
          "type": "string",
          "defaultValue": "10.0.0.1",
          "metadata": {
            "Description": "The IP address prefix that will be used for constructing a static private IP address for each node in the cluster"
          }
        },
        "jumpbox": {
          "type": "string",
          "defaultValue": "Disabled",
          "allowedValues": [
          "Enabled",
          "Disabled"
          ],
          "metadata": {
            "Description": "The flag allowing to enable or disable provisioning of the jumpbox VM that can be used to access the MongoDB environment"
          }
        },
        "tshirtSize": {
          "type": "string",
          "defaultValue": "XSmall",
          "allowedValues": [
          "XSmall",
          "Small",
          "Medium",
          "Large",
          "XLarge",
          "XXLarge"
          ],
          "metadata": {
            "Description": "T-shirt size of the MongoDB deployment"
          }
        },
        "osFamily": {
          "type": "string",
          "defaultValue": "Ubuntu",
          "allowedValues": [
          "Ubuntu"
          ],
          "metadata": {
            "Description": "The target OS for the virtual machines running MongoDB"
          }
        },
        "mongodbVersion": {
          "type": "string",
          "defaultValue": "3.0.2",
          "metadata": {
            "Description": "The version of the MongoDB packages to be deployed"
          }
        },
        "replicaSetName": {
          "type": "string",
          "defaultValue": "rs0",
          "metadata": {
            "Description": "The name of the MongoDB replica set"
          }
        },
        "replicaSetKey": {
          "type": "string",
          "metadata": {
            "Description": "The shared secret key for the MongoDB replica set"
          }
        }
      },

У каждого параметра есть такие атрибуты, как тип данных и допустимые значения. На их основе можно проверять правильность параметров, передаваемых во время выполнения шаблона в интерактивном режиме (например, через Azure PowerShell или интерфейс командной строки Azure), а также динамически создавать пользовательские интерфейсы с возможностями обнаружения путем анализа списка обязательных параметров и их описаний.

### Шаг 3-а. Развертывание кластера MongoDB на основе шаблона с помощью PowerShell

Подготовьте файл параметров развертывания, создав JSON-файл со значениями времени выполнения для всех параметров. Он будет передаваться команде развертывания в качестве единого объекта. Если файл параметров не указан, PowerShell использует значения по умолчанию из шаблона и предлагает ввести те значения, которых там нет.

Следующий пример показывает набор параметров из файла azuredeploy-parameters.json.

    {
      "adminUsername": {
          "value": "MongoAdmin"
      },
      "adminPassword": {
          "value": ""
      },
      "storageAccountName": {
          "value": ""
      },
      "region": {
          "value": "West US"
      },
      "virtualNetworkName": {
          "value": "mongodbVnet"
      },
      "subnetName": {
          "value": "mongodbSubnet"
      },
      "addressPrefix": {
          "value": "10.0.0.0/16"
      },
      "subnetPrefix": {
          "value": "10.0.0.0/24"
      },
      "nodeAddressPrefix": {
          "value": "10.0.0.1"
      },
      "jumpbox": {
          "value": "Disabled"
      },
      "tshirtSize": {
          "value": "XSmall"
      },
      "osFamily": {
          "value": "Ubuntu"
      },
      "mongodbVersion": {
          "value": "3.0.2"
      },
      "replicaSetName": {
          "value": "rs0"
      },
      "replicaSetKey":  {
          "value": ""
      }
    }

Укажите имена развертывания Azure и группы ресурсов, расположение Azure и имя папки, в которой находится сохраненный JSON-файл. Затем выполните следующие команды:

    $deployName="<deployment name>"
    $RGName="<resource group name>"
    $locName="<Azure location, such as West US>"
    $folderName="<folder name, such as C:\Azure\Templates\MongoDB>"
    $templateFile= $folderName + "\azuredeploy.json"
    $templateParameterFile= $folderName + "\azuredeploy-parameters.json"

    New-AzureResourceGroup –Name $RGName –Location $locName

    New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateParameterFile $templateParameterFile -TemplateFile $templateFile

При запуске команды **New-AzureResourceGroupDeployment** значения параметров извлекаются из JSON-файла и начинается выполнение шаблона на их основе. Определение и использование нескольких файлов параметров для разных сред (например, тестовой, рабочей и т. д.) способствует повторному использованию шаблона и упрощает развертывание сложных решений для нескольких сред.

При развертывании следует помнить о том, что необходимо создать новую учетную запись хранения Azure. Поэтому имя, которое вы указываете в качестве параметра учетной записи хранения, должно быть уникальным и отвечать всем требованиям для такой учетной записи (содержать только буквы в нижнем регистре и цифры).

Во время и после развертывания можно проверить все запросы, сделанные на этапе подготовки, включая все возникшие ошибки.

Для этого перейдите на [портал предварительной версии Azure](https://portal.azure.com) и сделайте следующее:

- Нажмите кнопку **Обзор** в области навигации слева, прокрутите страницу вниз и щелкните элемент **Группы ресурсов**.
- Выбрав созданную группу ресурсов, вы увидите колонку "Группа ресурсов".
- Щелкнув гистограмму "События" в области "Мониторинг" колонки "Группа ресурсов", вы увидите события для своего развертывания. Щелкая отдельные события, можно просмотреть подробную информацию о каждой отдельной операции, выполненной с шаблоном.

Если после выполнения тестов необходимо удалить эту группу ресурсов и все ее ресурсы (учетную запись хранения, виртуальную машину и виртуальную сеть), выполните следующую команду:

    Remove-AzureResourceGroup –Name "<resource group name>" -Force

### Шаг 3-б. Развертывание кластера MongoDB на основе шаблона с помощью интерфейса командной строки Azure

Чтобы развернуть кластер MongoDB через интерфейс командной строки Azure, сначала создайте группу ресурсов, указав для нее имя и расположение с помощью следующей команды:

    azure group create mdbc "West US"

Передайте это имя группы ресурсов, расположение шаблона JSON-файла и расположение файла параметров следующей команде (более подробные сведения см. в предыдущем разделе для PowerShell):

    azure group deployment create mdbc -f .\azuredeploy.json -e .\azuredeploy-parameters.json

Проверить состояние развертывания отдельных ресурсов можно с помощью следующей команды:

    azure group deployment list mdbc

## Обзор структуры шаблона MongoDB и организации файла

Чтобы создать функциональный шаблон диспетчера ресурсов Azure, который можно будет использовать многократно, следует продумать выполнение нескольких сложных и взаимосвязанных задач, необходимых при развертывании комплексного решения, такого как MongoDB. Используя такие функциональные возможности диспетчера ресурсов Azure, как *связывание шаблонов*, *циклическое создание ресурсов* и запуск сценариев с помощью связанных расширений, можно реализовать модульный подход и использовать его практически для любого сложного развертывания на основе шаблона.

На следующей схеме представлены связи между всеми файлами, загруженными из репозитория GitHub для этого развертывания.

![mongodb-files](media/virtual-machines-mongodb-template/mongodb-files.png)

В этом разделе содержится описание структуры файла azuredeploy.json для кластера MongoDB.

### Раздел parameters

В разделе parameters файла azuredeploy.json задаются параметры, используемые в шаблоне. Значения в раздел parameters файла azuredeploy.json передаются во время выполнения шаблона с помощью файла azuredeploy-parameters.json, описанного выше в этом разделе.

### Раздел variables

В разделе variables указываются переменные, которые могут использоваться в данном шаблоне. Он содержит несколько полей (типы данных JSON или фрагменты), которым во время выполнения будут присвоены константы или вычисляемые значения. В следующем примере показано как выглядит раздел variables для данного шаблона MongoDB.

    "variables": {
          "_comment0": "/* T-shirt sizes may vary for different reasons, and some customers may want to modify these - so feel free to go ahead and define your favorite t-shirts */",
          "tshirtSizeXSmall": {
              "vmSizeMember": "Standard_D1",
              "vmSizeArbiter": "Standard_A1",
              "numberOfMembers": 1,
              "totalMemberCount": 2,
              "arbiter": "Enabled",
              "vmTemplate": "[concat(variables('templateBaseUrl'), 'member-resources-D1.json')]",
              "storageAccountCount": 1,
              "dataDiskSize": 100
          },
          "tshirtSizeSmall": {
              "vmSizeMember": "Standard_D1",
              "vmSizeArbiter": "Standard_A1",
              "numberOfMembers": 2,
              "totalMemberCount": 3,
              "arbiter": "Disabled",
              "vmTemplate": "[concat(variables('templateBaseUrl'), 'member-resources-D1.json')]",
              "storageAccountCount": 1,
              "dataDiskSize": 100
          },
          "tshirtSizeMedium": {
              "vmSizeMember": "Standard_D2",
              "vmSizeArbiter": "Standard_A1",
              "numberOfMembers": 3,
              "totalMemberCount": 4,
              "arbiter": "Enabled",
              "vmTemplate": "[concat(variables('templateBaseUrl'), 'member-resources-D2.json')]",
              "storageAccountCount": 2,
              "dataDiskSize": 250
          },
          "tshirtSizeLarge": {
              "vmSizeMember": "Standard_D2",
              "vmSizeArbiter": "Standard_A1",
              "numberOfMembers": 7,
              "totalMemberCount": 8,
              "arbiter": "Enabled",
              "vmTemplate": "[concat(variables('templateBaseUrl'), 'member-resources-D2.json')]",
              "storageAccountCount": 4,
              "dataDiskSize": 250
          },
          "tshirtSizeXLarge": {
              "vmSizeMember": "Standard_D3",
              "vmSizeArbiter": "Standard_A1",
              "numberOfMembers": 7,
              "totalMemberCount": 8,
              "arbiter": "Enabled",
              "vmTemplate": "[concat(variables('templateBaseUrl'), 'member-resources-D3.json')]",
              "storageAccountCount": 4,
              "dataDiskSize": 500
          },
          "tshirtSizeXXLarge": {
              "vmSizeMember": "Standard_D3",
              "vmSizeArbiter": "Standard_A1",
              "numberOfMembers": 15,
              "totalMemberCount": 16,
              "arbiter": "Disabled",
              "vmTemplate": "[concat(variables('templateBaseUrl'), 'member-resources-D3.json')]",
              "storageAccountCount": 8,
              "dataDiskSize": 500
          },
          "osFamilyUbuntu": {
              "osName": "ubuntu",
              "installerBaseUrl": "http://repo.mongodb.org/apt/ubuntu",
              "installerPackages": "mongodb-org",
              "imagePublisher": "Canonical",
              "imageOffer": "UbuntuServer",
              "imageSKU": "14.04.2-LTS"
          },
          "vmStorageAccountContainerName": "vhd-mongodb",
          "vmStorageAccountDomain": ".blob.core.windows.net",
          "vnetID": "[resourceId('Microsoft.Network/virtualNetworks', parameters('virtualNetworkName'))]",
          "sharedScriptUrl": "[concat('https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/shared_scripts/', variables('osFamilySpec').osName, '/')]",
          "scriptUrl": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-high-availability/",
          "templateBaseUrl": "[variables('scriptUrl')]",
          "jumpboxTemplateEnabled": "jumpbox-resources.json",
          "jumpboxTemplateDisabled": "empty-resources.json",
          "arbiterTemplateEnabled": "arbiter-resources.json",
          "arbiterTemplateDisabled": "empty-resources.json",
          "sharedTemplateUrl": "[concat(variables('templateBaseUrl'), 'shared-resources.json')]",
          "jumpboxTemplateUrl": "[concat(variables('templateBaseUrl'), variables(concat('jumpboxTemplate', parameters('jumpbox'))))]",
          "arbiterTemplateUrl": "[concat(variables('templateBaseUrl'), variables(concat('arbiterTemplate', variables('clusterSpec').arbiter)))]",
          "commonSettings": {
              "availabilitySetName": "mongodbAvailSet",
              "region": "[parameters('region')]"
          },
          "storageSettings": {
              "vhdStorageAccountName": "[parameters('storageAccountName')]",
              "vhdContainerName": "[variables('vmStorageAccountContainerName')]",
              "destinationVhdsContainer": "[concat('https://', parameters('storageAccountName'), variables('vmStorageAccountDomain'), '/', variables('vmStorageAccountContainerName'), '/')]",
              "storageAccountCount": "[variables('clusterSpec').storageAccountCount]"
          },
          "networkSettings": {
              "virtualNetworkName": "[parameters('virtualNetworkName')]",
              "addressPrefix": "[parameters('addressPrefix')]",
              "subnetName": "[parameters('subnetName')]",
              "subnetPrefix": "[parameters('subnetPrefix')]",
              "subnetRef": "[concat(variables('vnetID'), '/subnets/', parameters('subnetName'))]",
              "machineIpPrefix": "[parameters('nodeAddressPrefix')]"
          },
          "machineSettings": {
              "adminUsername": "[parameters('adminUsername')]",
              "adminPassword": "[parameters('adminPassword')]",
              "machineNamePrefix": "mongodb-",
              "osImageReference": {
                  "publisher": "[variables('osFamilySpec').imagePublisher]",
                  "offer": "[variables('osFamilySpec').imageOffer]",
                  "sku": "[variables('osFamilySpec').imageSKU]",
                  "version": "latest"
              }
          },
          "clusterSpec": "[variables(concat('tshirtSize', parameters('tshirtSize')))]",
          "osFamilySpec": "[variables(concat('osFamily', parameters('osFamily')))]",
          "installCommand": "[concat('bash mongodb-', variables('osFamilySpec').osName, '-install.sh', ' -i ', variables('osFamilySpec').installerBaseUrl, ' -b ', variables('osFamilySpec').installerPackages, ' -r ', parameters('replicaSetName'), ' -k ', parameters('replicaSetKey'), ' -u ', parameters('adminUsername'), ' -p ', parameters('adminPassword'), ' -x ', variables('networkSettings').machineIpPrefix, ' -n ', variables('clusterSpec').totalMemberCount)]",
          "vmScripts": {
              "scriptsToDownload": [
                  "[concat(variables('scriptUrl'), 'mongodb-', variables('osFamilySpec').osName, '-install.sh')]",
                  "[concat(variables('sharedScriptUrl'), 'vm-disk-utils-0.1.sh')]"
              ],
              "regularNodeInstallCommand": "[variables('installCommand')]",
              "lastNodeInstallCommand": "[concat(variables('installCommand'), ' -l')]",
              "arbiterNodeInstallCommand": "[concat(variables('installCommand'), ' -a')]"
          },
          "_comment1": "/* The list of values below helps partition VM disks across multiple storage accounts to achieve a better throughput */",
          "_comment2": "/* Feel free to modify the default allocations if you are comfortable and understand what you are doing */",
          "storageAccountForXSmall_0": "0",
          "storageAccountForXSmall_1": "0",
          "storageAccountForSmall_0": "0",
          "storageAccountForSmall_1": "0",
          "storageAccountForSmall_2": "0",
          "storageAccountForMedium_0": "0",
          "storageAccountForMedium_1": "0",
          "storageAccountForMedium_2": "0",
          "storageAccountForMedium_3": "0",
          "storageAccountForLarge_0": "0",
          "storageAccountForLarge_1": "1",
          "storageAccountForLarge_2": "2",
          "storageAccountForLarge_3": "3",
          "storageAccountForLarge_4": "0",
          "storageAccountForLarge_5": "1",
          "storageAccountForLarge_6": "2",
          "storageAccountForLarge_7": "3",
          "storageAccountForXLarge_0": "0",
          "storageAccountForXLarge_1": "1",
          "storageAccountForXLarge_2": "2",
          "storageAccountForXLarge_3": "3",
          "storageAccountForXLarge_4": "0",
          "storageAccountForXLarge_5": "1",
          "storageAccountForXLarge_6": "2",
          "storageAccountForXLarge_7": "3",
          "storageAccountForXXLarge_0": "0",
          "storageAccountForXXLarge_1": "1",
          "storageAccountForXXLarge_2": "2",
          "storageAccountForXXLarge_3": "3",
          "storageAccountForXXLarge_4": "4",
          "storageAccountForXXLarge_5": "5",
          "storageAccountForXXLarge_6": "6",
          "storageAccountForXXLarge_7": "7",
          "storageAccountForXXLarge_8": "0",
          "storageAccountForXXLarge_9": "1",
          "storageAccountForXXLarge_10": "2",
          "storageAccountForXXLarge_11": "3",
          "storageAccountForXXLarge_12": "4",
          "storageAccountForXXLarge_13": "5",
          "storageAccountForXXLarge_14": "6",
          "storageAccountForXXLarge_15": "7"
      },

В предыдущем примере можно увидеть два различных подхода. В первом фрагменте переменной osFamilyUbuntu будет присвоен элемент JSON, содержащий 6 пар "ключ-значение".

    "osFamilyUbuntu": {
      "osName": "ubuntu",
      "installerBaseUrl": "http://repo.mongodb.org/apt/ubuntu",
      "installerPackages": "mongodb-org",
      "imagePublisher": "Canonical",
      "imageOffer": "UbuntuServer",
      "imageSKU": "14.04.2-LTS"
    },

Во втором фрагменте переменная vmScripts назначается массиву JSON, в котором отдельные элементы будут вычисляться во время выполнения с помощью функции языка шаблонов (concat) и значения другой переменной, а также строковых констант.

    "vmScripts": {
      "scriptsToDownload": [
      "[concat(variables('scriptUrl'), 'mongodb-', variables('osFamilySpec').osName, '-install.sh')]",
      "[concat(variables('sharedScriptUrl'), 'vm-disk-utils-0.1.sh')]"
    ],

Важной концепцией, реализованной в этом шаблоне, является определение различных размеров (переменные tshirtSizeXXXX) для кластеров MongoDB. Обратив внимание на эти переменные, можно увидеть, что они описывают важные характеристики развертывания кластера. В следующем примере рассмотрим средний размер (Medium):

    "tshirtSizeMedium": {
      "vmSizeMember": "Standard_D2",
      "vmSizeArbiter": "Standard_A1",
      "numberOfMembers": 3,
      "totalMemberCount": 4,
      "arbiter": "Enabled",
      "vmTemplate": "[concat(variables('templateBaseUrl'), 'member-resources-D2.json')]",
      "storageAccountCount": 2,
      "dataDiskSize": 250
    },

Средний кластер MongoDB использует виртуальные машины размера D2 для трех узлов MongoDB, на которых размещены данные, а также четвертую виртуальную машину размера A1 в качестве арбитра для репликации. Для развертывания узлов с данными вызывается соответствующий подшаблон `member-resources-D2.json` и файлы данных (размером по 250 ГБ каждый) будут храниться в двух учетных записях хранения. Эти переменные будут использоваться в разделе resources для организации развертывания узлов и выполнения других задач.

### Раздел resources

Именно в разделе resources выполняется большая часть операций. Если внимательно просмотреть этот раздел, можно сразу обнаружить два различных элемента. Первый из них — это элемент типа `Microsoft.Resources/deployments`, который по сути означает вызов вложенного развертывания в основном развертывании. В элементе templateLink (и связанном с ним свойстве версии) можно указать файл связанного шаблона, который будет вызван при передаче набора параметров в качестве входных данных, как можно увидеть в следующем примере.

    {
      "name": "shared-resources",
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2015-01-01",
      "properties": {
        "mode": "Incremental",
        "templateLink": {
          "uri": "[variables('sharedTemplateUrl')]",
          "contentVersion": "1.0.0.0"
        },
        "parameters": {
          "commonSettings": {
            "value": "[variables('commonSettings')]"
          },
          "storageSettings": {
            "value": "[variables('storageSettings')]"
          },
          "networkSettings": {
            "value": "[variables('networkSettings')]"
          }
        }
      }
    },

Предыдущий пример позволяет понять, как файл azuredeploy.json в этом сценарии используется для организации взаимодействия, вызывая несколько других шаблонов, каждый из которых отвечает за часть операций в ходе развертывания.

В частности, для этого развертывания будут использоваться следующие связанные шаблоны:

-	**shared-resource.json**: содержит определения всех ресурсов, которые будут совместно использоваться в развертывании. Например, это учетные записи хранения, используемые для хранения дисков операционной системы виртуальной машины и виртуальных сетей.
-	**Jumpbox-resources.json**. Когда этот шаблон включен, он отвечает за развертывание всех ресурсов, связанных с виртуальной машиной Jumpbox с общедоступным IP-адресом, по которому кластер MongoDB доступен из внешней сети.
-	**Arbiter-resources.json**. Когда этот шаблон включен, он развертывает в кластере MongoDB элемент арбитра. Арбитр не содержит данных, однако используется при четном количестве узлов в наборе реплик для определения выбора.
-	**Member-resources-Dx.json**. Он указывает шаблоны ресурсов, которые фактически отвечают за развертывание узлов MongoDB. Конкретный файл выбирается в зависимости от выбранного определения t-shirt size, и разница между ними заключается только в количестве подключенных дисков для каждого узла.
-	**Mongodb-ubuntu-install.sh**. Файл сценария bash, который вызывается расширением CustomScriptForLinux на каждом узле в кластере. Он отвечает за подключение и форматирование дисков с данными, а также за установку фрагментов MongoDB на узле.

Для развертывания кластера MongoDB необходимо правильно настроить набор реплик по особому алгоритму. Следующий пример демонстрирует определенную последовательность действий, которую необходимо использовать во время развертывания:

РАЗВЕРТЫВАНИЕ ЭЛЕМЕНТОВ ДАННЫХ (параллельно) => РАЗВЕРТЫВАНИЕ ПОСЛЕДНГО ЭЛЕМЕНТА ДАННЫХ => РАЗВЕРТЫВАНИЕ АРБИТРА (необязательно)

В этой последовательности узлы данных развертываются параллельно за исключением последнего. Именно на этом этапе формируется кластер и развертывается новый набор реплик, поэтому к этому моменту все предыдущие узлы должны быть уже настроены и запущены. Последним этапом является развертывание необязательного узла арбитра (только для тех значений размера tshirtsize, для которых это необходимо).

Вернемся к основному шаблону (azuredeploy.json) и посмотрим, как реализован этот алгоритм для всех элементов данных в следующем примере:

    {
      "type": "Microsoft.Resources/deployments",
      "name": "[concat('member-resources', copyindex())]",
      "apiVersion": "2015-01-01",
      "dependsOn": [
        "[concat('Microsoft.Resources/deployments/', 'shared-resources')]"
      ],
      "copy": {
        "name": "memberNodesLoop",
        "count": "[variables('clusterSpec').numberOfMembers]"
      },
      "properties": {
        "mode": "Incremental",
        "templateLink": {
          "uri": "[variables('clusterSpec').vmTemplate]",
          "contentVersion": "1.0.0.0"
        },
        "parameters": {
          "commonSettings": {
            "value": "[variables('commonSettings')]"
          },
          "storageSettings": {
            "value": {
              "vhdStorageAccountName": "[concat(variables('storageSettings').vhdStorageAccountName, variables(concat('storageAccountFor', parameters('tshirtSize'), '_', copyindex())))]",
                              "vhdContainerName": "[variables('storageSettings').vhdContainerName]",
                              "destinationVhdsContainer": "[concat('https://', variables('storageSettings').vhdStorageAccountName, variables(concat('storageAccountFor', parameters('tshirtSize'), '_', copyindex())), variables('vmStorageAccountDomain'), '/', variables('storageSettings').vhdContainerName, '/')]"
            }
          },
          "networkSettings": {
            "value": "[variables('networkSettings')]"
          },
          "machineSettings": {
            "value": {
              "adminUsername": "[variables('machineSettings').adminUsername]",
                              "adminPassword": "[variables('machineSettings').adminPassword]",
                              "machineNamePrefix": "[variables('machineSettings').machineNamePrefix]",
                              "osImageReference": "[variables('machineSettings').osImageReference]",
                              "vmSize": "[variables('clusterSpec').vmSizeMember]",
                              "dataDiskSize": "[variables('clusterSpec').dataDiskSize]",
                              "machineIndex": "[copyindex()]",
                              "vmScripts": "[variables('vmScripts').scriptsToDownload]",
                              "commandToExecute": "[variables('vmScripts').regularNodeInstallCommand]"
            }
          }
        }
      }
    },

Обратите особое внимание на то, как можно развернуть несколько копий ресурсов одного типа и назначить каждому экземпляру уникальные значения для обязательных параметров. Этот принцип называется *циклическим созданием ресурсов*.

В предыдущем примере специальный параметр (количество узлов, развертываемых в кластере) используется для установки переменной (numberOfMembers), которая затем передается элементу **copy** для активации набора (цикла) вложенных развертываний, каждое из которых ведет к созданию экземпляра шаблона для каждого элемента кластера. Для настройки всех параметров, которые должны принимать уникальные значения в разных экземплярах, можно использовать функцию **copyindex()**, позволяющую получить числовое значение, которое обозначает текущий индекс в данной циклической последовательности создания ресурсов.

Еще один важный принцип при создании ресурсов — возможность задать для ресурсов зависимости и приоритеты, что демонстрирует нам массив JSON **dependsOn**. В этом шаблоне развертывание каждого узла зависит от успеха предыдущего развертывания **общих ресурсов**.

Подключенные диски форматируются в ходе операций по подготовке узла, которые запускаются при выполнении файла сценария mongodb-ubuntu-install.sh. В этом файле можно найти экземпляр следующего вызова:

    bash ./vm-disk-utils-0.1.sh -b $DATA_DISKS -s

Файл vm-disk-utils-0.1.sh находится в папке shared\_scripts\\ubunt в репозитории GitHub azure-quickstart-tempates и содержит очень полезные функции для подключения, форматирования и чередования дисков, которые можно использовать каждый раз при выполнении подобных задач в ходе создания шаблона.

Еще один интересный фрагмент, который стоит рассмотреть, связан с расширениями виртуальной машины CustomScriptForLinux. Они устанавливаются как отдельный тип ресурса с зависимостью от каждого шаблона развертывания узла кластера. Для примера ознакомьтесь со следующим фрагментом в конце каждого файла each member-resources-Dx.json.

    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "[concat('vmMember', parameters('machineSettings').machineIndex, '/installmongodb')]",
      "apiVersion": "2015-05-01-preview",
      "location": "[parameters('commonSettings').region]",
      "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', 'vmMember', parameters('machineSettings').machineIndex)]"
      ],
      "properties": {
        "publisher": "Microsoft.OSTCExtensions",
        "type": "CustomScriptForLinux",
        "typeHandlerVersion": "1.2",
        "settings": {
          "fileUris": "[parameters('machineSettings').vmScripts]", "commandToExecute":"[parameters('machineSettings').commandToExecute]"
        }
      }
    }

Ознакомившись с другими файлами в этом развертывании, можно понять, какие принципы и методы используются для организации сложных развертываний решений для нескольких узлов на базе любой технологии и управления ими с помощью шаблонов диспетчера ресурсов Azure. Хотя это не обязательно, мы советуем структурировать файлы шаблона с помощью подхода, указанного на следующей схеме.

![mongodb-template-structure](media/virtual-machines-mongodb-template/mongodb-template-structure.png)

По сути, в рамках этого подхода вам необходимо сделать следующее:

-	Определить основной файл шаблона в качестве центральной точки оркестрации для всех определенных операций развертывания за счет использования создания связей между шаблонами для вызова операций выполнения подшаблона.
-	Создать определенные файлы шаблона, которые развернут все ресурсы, используемые в остальных задачах конкретного развертывания (например, учетные записи хранения, конфигурация виртуальной сети и т. д.). Эти файлы можно повторно использовать в развертываниях со схожими требованиями в отношении общей инфраструктуры.
-	Включить необязательные шаблоны ресурсов для отдельных требований конкретного заданного ресурса.
-	создать специальные шаблоны для одинаковых элементов группы ресурсов (узлы в кластере и т. д.), которые используют циклическое создание ресурсов для развертывания нескольких экземпляров с уникальными свойствами;
-	Использовать расширения для развертывания сценариев и создать сценарии для каждой из технологий для всех задач, выполняемых после развертывания (например, установка продукта, конфигурация и т. д.).

Дополнительную информацию см. в разделе [Язык шаблонов диспетчера ресурсов Azure](../resource-group-authoring-templates.md).

<!---HONumber=AcomDC_1125_2015-->