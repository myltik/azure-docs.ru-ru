<properties
	pageTitle="Рекомендации по обработке состояния в шаблонах диспетчера ресурсов Azure"
	description="Здесь описываются рекомендуемые подходы к использованию составных объектов для совместного использования данных о состоянии с шаблонами диспетчера ресурсов Azure и связанными шаблонами."
	services="azure-resource-manager"
	documentationCenter=""
	authors="mmercuri"
	manager="georgem"
	editor="tysonn"/>

<tags
	ms.service="azure-resource-manager"
	ms.workload="multiple"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/02/2015"
	ms.author="mmercuri"/>

# Совместное использование состояния в шаблонах диспетчера ресурсов Azure

В этом разделе представлены рекомендации о том, как управлять состоянием и совместно использовать его в шаблоне диспетчера ресурсов Azure и в связанных шаблонах. Параметры и переменные, приведенные в этом разделе, представляют собой примеры типов объектов, которые можно определить для удобной организации требований к развертыванию. На основе этих примеров можно реализовать собственные объекты со значениями свойств, которые имеют смысл для вашей среды.

## Использование составных объектов для совместного использования состояния

Помимо параметров с единственным значением в качестве параметров в шаблоне диспетчера ресурсов Azure также можно использовать составные объекты. С помощью составных объектов можно внедрять коллекции данных для определенной области и ссылаться на них, например размер (для описания виртуальной машины), сетевые параметры, параметры операционной системы (ОС) и параметры доступности.

В следующем примере показано, как определять переменные, содержащие составные объекты, для представления коллекций данных. Коллекции определяют значения, используемые для размера виртуальной машины, сетевых параметров, параметров операционной системы и параметров доступности.

    "tshirtSizeLarge": {
      "vmSize": "Standard_A4",
      "diskSize": 1023,
      "vmTemplate": "[concat(variables('templateBaseUrl'), 'database-16disk-resources.json')]",
      "vmCount": 3,
      "slaveCount": 2,
      "storage": {
        "name": "[parameters('storageAccountNamePrefix')]",
        "count": 2,
        "pool": "db",
        "map": [0,1,1],
        "jumpbox": 0
      }
    },
    "osSettings": {
      "scripts": [
        "[concat(variables('templateBaseUrl'), 'install_postgresql.sh')]",
        "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/shared_scripts/ubuntu/vm-disk-utils-0.1.sh"
      ],
      "imageReference": {
				"publisher": "Canonical",
        "offer": "UbuntuServer",
        "sku": "14.04.2-LTS",
        "version": "latest"
      }
    },
    "networkSettings": {
      "vnetName": "[parameters('virtualNetworkName')]",
      "addressPrefix": "10.0.0.0/16",
      "subnets": {
        "dmz": {
          "name": "dmz",
          "prefix": "10.0.0.0/24",
          "vnet": "[parameters('virtualNetworkName')]"
        },
        "data": {
          "name": "data",
          "prefix": "10.0.1.0/24",
          "vnet": "[parameters('virtualNetworkName')]"
        }
      }
    },
    "availabilitySetSettings": {
      "name": "pgsqlAvailabilitySet",
      "fdCount": 3,
      "udCount": 5
    }

Затем можно ссылаться на эти переменные в шаблоне. Возможность ссылаться на именованные переменные и их свойства упрощает синтаксис шаблона и облегчает понимание контекста. В следующем примере определяется ресурс для развертывания с использованием показанных выше объектов для установки значения. Например, обратите внимание, что размер виртуальной машины задается при получении значения для `variables('tshirtSize').vmSize`, а значение для размера диска получается на основе `variables('tshirtSize').diskSize`. Кроме того, URI для связанного шаблона задается на основе значения для `variables('tshirtSize').vmTemplate`.

    "name": "master-node",
    "type": "Microsoft.Resources/deployments",
    "apiVersion": "2015-01-01",
    "dependsOn": [
        "[concat('Microsoft.Resources/deployments/', 'shared')]"
    ],
    "properties": {
        "mode": "Incremental",
        "templateLink": {
          "uri": "[variables('tshirtSize').vmTemplate]",
          "contentVersion": "1.0.0.0"
        },
        "parameters": {
          "adminPassword": {
            "value": "[parameters('adminPassword')]"
          },
          "replicatorPassword": {
            "value": "[parameters('replicatorPassword')]"
          },
          "osSettings": {
	    "value": "[variables('osSettings')]"
          },
          "subnet": {
            "value": "[variables('networkSettings').subnets.data]"
          },
          "commonSettings": {
            "value": {
              "region": "[parameters('region')]",
              "adminUsername": "[parameters('adminUsername')]",
              "namespace": "ms"
            }
          },
          "storageSettings": {
            "value":"[variables('tshirtSize').storage]"
          },
          "machineSettings": {
            "value": {
              "vmSize": "[variables('tshirtSize').vmSize]",
              "diskSize": "[variables('tshirtSize').diskSize]",
              "vmCount": 1,
              "availabilitySet": "[variables('availabilitySetSettings').name]"
            }
          },
          "masterIpAddress": {
            "value": "0"
          },
          "dbType": {
            "value": "MASTER"
          }
        }
      }
    }

## Передача состояния шаблону и связанным с ним шаблонам

Информацию о состоянии можно совместно использовать в шаблоне и связанных с ним шаблонах с помощью следующих элементов:

- параметров, передаваемых непосредственно в основной шаблон при развертывании;
- параметров, статических переменных и создаваемых переменных, которые основной шаблон совместно использует со связанными шаблонами.

### Общие параметры, передаваемые в основной шаблон

В следующей таблице приведены параметры, часто используемые в шаблонах.

**Часто используемые параметры, передаваемые в основной шаблон**

Имя | Значение | Описание
---- | ----- | -----------
location | Строка из ограниченного списка регионов Azure | Расположение, в котором будут развертываться ресурсы
storageAccountNamePrefix | Строка | Уникальное DNS-имя для учетной записи хранения, в которой будут размещаться диски виртуальной машины
domainName | Строка | Доменное имя общедоступной виртуальной машины, работающей в качестве основы, в формате **{domainName}.{расположение}.cloudapp.com**. Например: **mydomainname.westus.cloudapp.azure.com**
adminUsername | Строка | Имя пользователя для виртуальных машин
adminPassword | Строка | Пароль для виртуальных машин
tshirtSize | Строка из ограниченного списка предлагаемых размеров | Именованный размер единицы масштабирования, который нужно подготовить. Например, Small, Medium, Large
virtualNetworkName | Строка | Имя виртуальной сети, которую необходимо использовать
enableJumpbox | Строка из ограниченного списка (enabled, disabled) | Параметр, определяющий, следует ли включить основную виртуальную машину в среде. Значения: enabled, disabled

### Параметры, передаваемые связанным шаблонам

При подключении к связанным шаблонам часто используется сочетание статических и созданных переменных.

#### Статические переменные

Статические переменные часто используются для предоставления базовых значений, таких как URL-адреса, используемых в шаблоне, или в качестве значений, используемых для формирования значений для динамических переменных.

В следующем фрагменте шаблона *templateBaseUrl* указывает корневую папку для шаблона в GitHub. Следующая строка создает новую переменную *sharedTemplateUrl*, которая выполняет сцепку значения *templateBaseUrl* с известным именем шаблона общих ресурсов. Ниже используется переменная составного объекта для хранения размера, где *templateBaseUrl* объединяется, чтобы указать расположение известного шаблона конфигурации в свойстве *vmTemplate*.

Преимущество этого подхода заключается в том, что можно легко переместить, разветвить или использовать шаблон в качестве основы для создания нового шаблона. При изменении расположения шаблона необходимо изменить статическую переменную только в основном шаблоне, который передает ее во все шаблоны.

    "templateBaseUrl": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/postgresql-on-ubuntu/",
    "sharedTemplateUrl": "[concat(variables('templateBaseUrl'), 'shared-resources.json')]",
    "tshirtSizeSmall": {
      "vmSize": "Standard_A1",
      "diskSize": 1023,
      "vmTemplate": "[concat(variables('templateBaseUrl'), 'database-2disk-resources.json')]",
      "vmCount": 2,
      "slaveCount": 1,
      "storage": {
        "name": "[parameters('storageAccountNamePrefix')]",
        "count": 1,
        "pool": "db",
        "map": [0,0],
        "jumpbox": 0
      }
    }

#### Создаваемые переменные

Вдобавок к статическим переменным некоторое количество переменных создается динамически. В этом разделе приведены общие типы создаваемых переменных.

##### tshirtSize

При вызове основного шаблона можно выбрать размер из фиксированного числа параметров, которые обычно содержат значения, такие как *Small*, *Medium* и *Large*.

В основном шаблоне этот параметр отображается как параметр, например *tshirtSize*:

    "tshirtSize": {
      "type": "string",
      "defaultValue": "Small",
      "allowedValues": [
        "Small",
        "Medium",
        "Large"
      ],
      "metadata": {
        "Description": "T-shirt size of the MongoDB deployment"
      }
    }

В основном шаблоне переменные соответствуют каждому из размеров. Например, если доступны малый, средний и большой размеры, раздел переменных может содержать переменные с именем *tshirtSizeSmall*, *tshirtSizeMedium* и *tshirtSizeLarge*.

Как показано в следующем примере, эти переменные определяют свойства определенного размера. Каждая из них определяет тип виртуальной машины, размер диска, связанный шаблон ресурсов единицы масштабирования, с которым нужно создать связь, количество экземпляров, информацию об учетной записи и состояние основной виртуальной машины.

Префикс имени учетной записи хранения берется из предоставляемого пользователем параметра, а связанный шаблон представляет собой объединение базового URL-адреса для шаблона и имени файла конкретного шаблона ресурсов единицы масштабирования.

    "tshirtSizeSmall": {
      "vmSize": "Standard_A1",
			"diskSize": 1023,
      "vmTemplate": "[concat(variables('templateBaseUrl'), 'database-2disk-resources.json')]",
      "vmCount": 2,
      "storage": {
        "name": "[parameters('storageAccountNamePrefix')]",
        "count": 1,
        "pool": "db",
        "map": [0,0],
        "jumpbox": 0
      }
    },
    "tshirtSizeMedium": {
      "vmSize": "Standard_A3",
      "diskSize": 1023,
      "vmTemplate": "[concat(variables('templateBaseUrl'), 'database-8disk-resources.json')]",
      "vmCount": 2,
      "storage": {
        "name": "[parameters('storageAccountNamePrefix')]",
        "count": 2,
        "pool": "db",
        "map": [0,1],
        "jumpbox": 0
      }
    },
    "tshirtSizeLarge": {
      "vmSize": "Standard_A4",
      "diskSize": 1023,
      "vmTemplate": "[concat(variables('templateBaseUrl'), 'database-16disk-resources.json')]",
      "vmCount": 3,
      "storage": {
        "name": "[parameters('storageAccountNamePrefix')]",
        "count": 2,
        "pool": "db",
        "map": [0,1,1],
        "jumpbox": 0
      }
    }

Переменная *tshirtSize* отображается ниже в разделе переменных. Последний элемент указанного параметра размера (*Small*, *Medium*, *Large*) объединяется со значением *tshirtSize*, чтобы получить связанную переменную составного объекта для этого размера:

    "tshirtSize": "[variables(concat('tshirtSize', parameters('tshirtSize')))]",

Эта переменная передается в связанный шаблон ресурсов единицы масштабирования.

##### networkSettings

В шаблонах, созданных для использования емкости, возможностей или сквозных решений, связанные шаблоны обычно создают ресурсы, которые существуют в сети. Один простой подход заключается в использовании составного объекта для хранения сетевых параметров и их передаче связанным шаблонам.

Ниже приведен пример взаимодействующих сетевых параметров.

    "networkSettings": {
      "vnetName": "[parameters('virtualNetworkName')]",
      "addressPrefix": "10.0.0.0/16",
      "subnets": {
        "dmz": {
          "name": "dmz",
          "prefix": "10.0.0.0/24",
          "vnet": "[parameters('virtualNetworkName')]"
        },
        "data": {
          "name": "data",
          "prefix": "10.0.1.0/24",
          "vnet": "[parameters('virtualNetworkName')]"
        }
      }
    }

##### availabilitySettings

Ресурсы, созданные в связанных шаблонах, часто помещаются в группу доступности. В следующем примере указывается имя группы доступности, а также количество используемых доменов сбоя и доменов обновления.

    "availabilitySetSettings": {
      "name": "pgsqlAvailabilitySet",
      "fdCount": 3,
      "udCount": 5
    }

Если требуется несколько групп доступности (например, одна для основных узлов, а другая для узлов данных), можно использовать имя в качестве префикса, указать несколько групп доступности или использовать модель выше для создания переменной для определенного размера.

##### storageSettings

Информация о хранилище часто совместно используется со связанными шаблонами. В следующем примере объект *storageSettings* предоставляет данные об именах учетной записи хранения и контейнера.

    "storageSettings": {
        "vhdStorageAccountName": "[parameters('storageAccountName')]",
        "vhdContainerName": "[variables('vmStorageAccountContainerName')]",
        "destinationVhdsContainer": "[concat('https://', parameters('storageAccountName'), variables('vmStorageAccountDomain'), '/', variables('vmStorageAccountContainerName'), '/')]"
    }

##### osSettings

При использовании связанных шаблонов может потребоваться передать параметры операционной системы для узлов различных типов с помощью различных известных типов конфигураций. Составной объект — простой способ хранения и совместного использования информации операционной системы, который также упрощает поддержку нескольких операционных систем для развертывания.

В следующем примере показан объект для параметра *osSettings*:

    "osSettings": {
      "imageReference": {
        "publisher": "Canonical",
        "offer": "UbuntuServer",
        "sku": "14.04.2-LTS",
        "version": "latest"
      }
    }

##### machineSettings

Создаваемая переменная *machineSettings* представляет собой составной объект, содержащий набор основных переменных для создания новой виртуальной машины: имя пользователя и пароль администратора, префикс для имен виртуальных машин и ссылку на образ операционной системы, как показано ниже:

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

Обратите внимание, что *osImageReference* извлекает значения из переменной *osSettings*, определенной в основном шаблоне. Это означает, что вы можете легко изменить операционную систему для виртуальной машины. Ее можно изменить полностью или исходя из предпочтений клиента шаблона.

##### vmScripts

Объект *vmScripts* содержит информацию о сценариях для скачивания и выполнения в экземпляре виртуальной машины, включая внутренние и внешние ссылки. Внешние ссылки включают в себя инфраструктуру. Внутренние ссылки включают в себя установленное программное обеспечение и конфигурацию.

С помощью свойства *scriptsToDownload* можно указать сценарии для скачивания в виртуальную машину.

Как показано в следующем примере, этот объект также содержит ссылки на аргументы командной строки для различных типов действий. Эти действия включают выполнение установки по умолчанию для каждого отдельного узла; установки, которая запускается после развертывания всех узлов, и любые дополнительные сценарии для данного шаблона.

Этот пример взят из шаблона, используемого для развертывания MongoDB, который требует арбитра для обеспечения высокого уровня доступности. Параметр *arbiterNodeInstallCommand* был добавлен в *vmScripts* для установки арбитра.

Раздел переменных содержит переменные, которые определяют конкретный текст, используемый для выполнения сценария с соответствующими значениями.

    "vmScripts": {
        "scriptsToDownload": [
            "[concat(variables('scriptUrl'), 'mongodb-', variables('osFamilySpec').osName, '-install.sh')]",
            "[concat(variables('sharedScriptUrl'), 'vm-disk-utils-0.1.sh')]"
        ],
        "regularNodeInstallCommand": "[variables('installCommand')]",
        "lastNodeInstallCommand": "[concat(variables('installCommand'), ' -l')]",
        "arbiterNodeInstallCommand": "[concat(variables('installCommand'), ' -a')]"
    },


## Возвращение состояния из шаблона

Вы можете не только передавать данные в шаблон, но и предоставлять общий доступ к ним вызывающему шаблону. В разделе **outputs** связанного шаблона можно указать пары «ключ-значение», которые могут использоваться в исходном шаблоне.

В следующем примере показано, как передать частный IP-адрес, созданный в связанном шаблоне.

    "outputs": {
        "masterip": {
            "value": "[reference(concat(variables('nicName'),0)).ipConfigurations[0].privateIPAddress]",
            "type": "string"
         }
    }

В основном шаблоне можно использовать эти данные с помощью следующего синтаксиса:

    "masterIpAddress": {
        "value": "[reference('master-node').outputs.masterip.value]"
    }

## Дальнейшие действия
- [Создание шаблонов диспетчера ресурсов Azure](resource-group-authoring-templates.md)
- [Функции шаблонов в диспетчере ресурсов Azure](resource-group-template-functions.md)

<!---HONumber=September15_HO1-->