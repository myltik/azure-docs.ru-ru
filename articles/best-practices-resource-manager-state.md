<properties
	pageTitle="Обработка состояний в шаблонах Resource Manager | Microsoft Azure"
	description="Здесь описываются рекомендуемые подходы к использованию составных объектов для совместного использования данных о состоянии с шаблонами диспетчера ресурсов Azure и связанными шаблонами."
	services="azure-resource-manager"
	documentationCenter=""
	authors="tfitzmac"
	manager="timlt"
	editor="tysonn"/>

<tags
	ms.service="azure-resource-manager"
	ms.workload="multiple"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/06/2016"
	ms.author="tomfitz"/>

# Совместное использование состояния в шаблонах диспетчера ресурсов Azure

В этой статье приводятся рекомендации по управлению и совместному использованию состояния в шаблонах. Параметры и переменные, приведенные в этом разделе, представляют собой примеры типов объектов, которые можно определить для удобной организации требований к развертыванию. На основе этих примеров можно реализовать собственные объекты со значениями свойств, которые имеют смысл для вашей среды.

Данная тема является частью другого технического документа. Чтобы ознакомиться с полным текстом этого документа, загрузите файл [Рекомендации по работе с шаблонами ARM мирового класса](http://download.microsoft.com/download/8/E/1/8E1DBEFA-CECE-4DC9-A813-93520A5D7CFE/World Class ARM Templates — Considerations and Proven Practices.pdf).


## Указание стандартных параметров конфигурации

Распространенной альтернативой использованию шаблона, который обеспечивает гибкость и множество вариантов, является предоставление возможности выбора известных конфигураций. Фактически размеры песочницы определяются по аналогии со стандартными размерами футболки (малый, средний и большой). Другие примеры использования этого подхода включают такие продукты, как Community Edition или Enterprise Edition. Другие случаи могут быть представлены конфигурациями технологий, зависящими от рабочей нагрузки, включая решения Map/Reduce или NoSQL.

Со сложными объектами можно создавать переменные, содержащие наборы данных, которые иногда называют «контейнерами свойств», и использовать эти данные в объявлении ресурса в шаблоне. Такой подход позволяет использовать хорошие и известные конфигурации разных размеров, предварительно настроенные для клиентов. Если пользователи не используют известные конфигурации, им необходимо определять размеры кластеров самостоятельно, учитывая ограничения ресурсов платформы и выполняя математические вычисления для определения итогового разделения учетных записей хранилища и других ресурсов (из-за ограничений, связанных с размерами кластеров и ресурсами). Кроме оптимизации возможностей для клиента, небольшое количество известных конфигураций также проще обслуживать. Кроме того, вы получаете более высокий уровень плотности.

В следующем примере показано, как определять переменные, содержащие составные объекты, для представления коллекций данных. Коллекции определяют значения, используемые для размера виртуальной машины, сетевых параметров, параметров операционной системы и параметров доступности.

    "variables": {
      "tshirtSize": "[variables(concat('tshirtSize', parameters('tshirtSize')))]",
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
    }

Обратите внимание, что переменная **tshirtSize** присоединяет размер, указанный с помощью параметра (**Small**, **Medium**, **Large**) к тексту **tshirtSize**. Эта переменная используется для получения связанного составного объекта для этого размера.

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

## Передача состояния в шаблон

Общий доступ к состоянию в шаблоне предоставляется с помощью параметров, которые вы указываете во время развертывания.

В следующей таблице приведены параметры, часто используемые в шаблонах.

Имя | Значение | Описание
---- | ----- | -----------
location | Строка из ограниченного списка регионов Azure | Расположение, в котором будут развертываться ресурсы
storageAccountNamePrefix | Строка | Уникальное DNS-имя для учетной записи хранения, в которой будут размещаться диски виртуальной машины
domainName | Строка | Доменное имя общедоступной виртуальной машины, работающей в качестве основы, в формате **{доменное\_имя}.{расположение}.cloudapp.com**. Например: **mydomainname.westus.cloudapp.azure.com**
adminUsername | Строка | Имя пользователя для виртуальных машин
adminPassword | Строка | Пароль для виртуальных машин
tshirtSize | Строка из ограниченного списка предлагаемых размеров | Именованный размер единицы масштабирования, который нужно подготовить. Например, Small, Medium, Large
virtualNetworkName | Строка | Имя виртуальной сети, которую необходимо использовать
enableJumpbox | Строка из ограниченного списка (enabled, disabled) | Параметр, определяющий, следует ли включить основную виртуальную машину в среде. Значения: enabled, disabled

Параметр **tshirtSize**, используемый в предыдущем разделе, определяется так:

    "parameters": {
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
    }


## Передача состояния в связанные шаблоны

При подключении к связанным шаблонам часто используется сочетание статических и созданных переменных.

### Статические переменные

Статические переменные часто используются для указания базовых значений, таких как URL-адреса, используемые в шаблоне.

В следующем фрагменте шаблона переменная `templateBaseUrl` указывает корневую папку для шаблона в GitHub. Следующая строка создает новую переменную `sharedTemplateUrl`, которая присоединяет значение базового URL-адреса к известному имени шаблона общих ресурсов. Ниже для хранения размера используется переменная составного объекта, в которой базовый URL-адрес соединяется с известным расположением шаблона конфигурации и хранится в свойстве `vmTemplate`.

Такой способ хранения очень удобен: если расположение шаблона меняется, вам нужно изменить статическую переменную только в одном месте, откуда она передается во все связанные шаблоны.

    "variables": {
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
    }

### Создаваемые переменные

Вдобавок к статическим переменным некоторое количество переменных создается динамически. В этом разделе приведены общие типы создаваемых переменных.

#### tshirtSize

Вы уже знакомы с этой переменной, созданной в приведенных выше примерах.

#### networkSettings

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

#### availabilitySettings

Ресурсы, созданные в связанных шаблонах, часто помещаются в группу доступности. В следующем примере указывается имя группы доступности, а также количество используемых доменов сбоя и доменов обновления.

    "availabilitySetSettings": {
      "name": "pgsqlAvailabilitySet",
      "fdCount": 3,
      "udCount": 5
    }

Если требуется несколько групп доступности (например, одна для основных узлов, а другая для узлов данных), можно использовать имя в качестве префикса, указать несколько групп доступности или использовать модель выше для создания переменной для определенного размера.

#### storageSettings

Информация о хранилище часто совместно используется со связанными шаблонами. В следующем примере объект *storageSettings* предоставляет данные об именах учетной записи хранения и контейнера.

    "storageSettings": {
        "vhdStorageAccountName": "[parameters('storageAccountName')]",
        "vhdContainerName": "[variables('vmStorageAccountContainerName')]",
        "destinationVhdsContainer": "[concat('https://', parameters('storageAccountName'), variables('vmStorageAccountDomain'), '/', variables('vmStorageAccountContainerName'), '/')]"
    }

#### osSettings

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

#### machineSettings

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

#### vmScripts

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

Вы можете не только передавать данные в шаблон, но и предоставлять общий доступ к ним вызывающему шаблону. В разделе **outputs** связанного шаблона можно указать пары «ключ — значение», которые могут использоваться в исходном шаблоне.

В следующем примере показано, как передать частный IP-адрес, созданный в связанном шаблоне.

    "outputs": {
        "masterip": {
            "value": "[reference(concat(variables('nicName'),0)).ipConfigurations[0].properties.privateIPAddress]",
            "type": "string"
         }
    }

В основном шаблоне можно использовать эти данные с помощью следующего синтаксиса:

    "[reference('master-node').outputs.masterip.value]"

Вы можете использовать это выражение в разделе выходных данных или в разделе ресурсов основного шаблона. Это выражение нельзя использовать в разделе переменных, так как оно зависит от состояния среды выполнения. Чтобы получить это значение из основного шаблона, используйте следующий код:

    "outputs": { 
      "masterIpAddress": {
        "value": "[reference('master-node').outputs.masterip.value]",
        "type": "string"
      }
     
Пример использования раздела выходных данных связанного шаблона для возврата дисков данных виртуальной машины см. в разделе [Создание нескольких дисков данных для виртуальной машины](resource-group-create-multiple.md#creating-multiple-data-disks-for-a-virtual-machine).

## Определение параметров проверки подлинности для виртуальной машины

Чтобы настроить параметры проверки подлинности для виртуальной машины, используйте тот же шаблон, приведенный выше для параметров конфигурации. Создайте параметр для передачи в тип проверки подлинности.

    "parameters": {
      "authenticationType": {
        "allowedValues": [
          "password",
          "sshPublicKey"
        ],
        "defaultValue": "password",
        "metadata": {
          "description": "Authentication type"
        },
        "type": "string"
      }
    }

Добавьте переменные для различных типов проверки подлинности и переменную для хранения типа проверки подлинности, используемой для данного развертывания на основе значения параметра.

    "variables": {
      "osProfile": "[variables(concat('osProfile', parameters('authenticationType')))]",
      "osProfilepassword": {
        "adminPassword": "[parameters('adminPassword')]",
        "adminUsername": "notused",
        "computerName": "[parameters('vmName')]",
        "customData": "[base64(variables('customData'))]"
      },
      "osProfilesshPublicKey": {
        "adminUsername": "notused",
        "computerName": "[parameters('vmName')]",
        "customData": "[base64(variables('customData'))]",
        "linuxConfiguration": {
          "disablePasswordAuthentication": "true",
          "ssh": {
            "publicKeys": [
              {
                "keyData": "[parameters('sshPublicKey')]",
                "path": "/home/notused/.ssh/authorized_keys"
              }
            ]
          }
        }
      }
    }

Во время определения виртуальной машины укажите значение **osProfile** для созданной переменной.

    {
      "type": "Microsoft.Compute/virtualMachines",
      ...
      "osProfile": "[variables('osProfile')]"
    }


## Дальнейшие действия
- Дополнительную информацию о разделах в шаблоне см. в статье, посвященной [созданию шаблонов Azure Resource Manager](resource-group-authoring-templates.md).
- Список функций, которые можно использовать в шаблоне, см. в статье, описывающей [функции шаблонов Azure Resource Manager](resource-group-template-functions.md).

<!---HONumber=AcomDC_0518_2016-->