<properties
   	pageTitle="Создание кластеров Hadoop, HBase и Storm на платформе Linux в HDInsight с помощью cURL и REST API Azure | Microsoft Azure"
   	description="Узнайте, как создать кластеры HDInsight под управлением Linux с помощью cURL, шаблонов диспетчера ресурсов Azure и Azure REST API. Вы можете указать тип кластера (Hadoop, HBase или Storm) либо использовать сценарии для установки настраиваемых компонентов."
   	services="hdinsight"
   	documentationCenter=""
   	authors="Blackmist"
   	manager="paulettm"
   	editor="cgronlun"
	tags="azure-portal"/>

<tags
   	ms.service="hdinsight"
   	ms.devlang="na"
   	ms.topic="article"
   	ms.tgt_pltfrm="na"
   	ms.workload="big-data"
   	ms.date="12/04/2015"
   	ms.author="larryfr"/>

#Создание кластеров под управлением Linux в HDInsight с помощью cURL и API REST Azure

[AZURE.INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

API REST Azure позволяет управлять службами, размещенными на платформе Azure, в том числе создавать новые ресурсы, например кластеры HDInsight под управлением Linux. Прочитав эту статью, вы научитесь создавать шаблоны диспетчера ресурсов Azure для настройки кластера HDInsight и связанных хранилищ и разворачивать шаблон в API REST Azure с помощью cURL для создания нового кластера HDInsight.

> [AZURE.IMPORTANT]При выполнении действий, описанных в этом документе, используется стандартное количество рабочих узлов (4) для кластера HDInsight. Если вы планируете использовать более 32 рабочих узлов (при создании кластера или в ходе масштабирования после создания кластера), для головного узла потребуется минимум 8-ядерный процессор и 14 ГБ ОЗУ.
>
> Дополнительные сведения о размерах узлов и их стоимости см. в статье [Сведения о ценах на HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

##Предварительные требования

- **Подписка Azure.**. См. [Бесплатная пробная версия Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- __Интерфейс командной строки Azure__. Интерфейс командной строки Azure используется для создания субъекта-службы, который затем используется для создания маркеров проверки подлинности для запросов к API REST Azure.

    Сведения об установке интерфейса командной строки Azure см. в разделе [Установка интерфейса командной строки Azure](../xplat-cli-install.md).

- __cURL__. Эту служебную программу можно установить с помощью системы управления пакетами или загрузить с сайта [http://curl.haxx.se/](http://curl.haxx.se/).

    > [AZURE.NOTE]Если для выполнения команд, приведенных в этом документе, вы используете PowerShell, необходимо сначала удалить псевдоним `curl`, который создается по умолчанию. Когда вы выполняете команду `curl` в командной строке PowerShell, этот псевдоним использует командлет Invoke-WebRequest вместо cURL, поэтому большинство описанных в этом документе команд будут завершены ошибкой.
    > 
    > Чтобы удалить этот псевдоним, выполните следующую команду в командной строке PowerShell:
    >
    > ```Remove-item alias:curl`
    >
    > После удаления псевдонима следует использовать версию cURL, установленную в вашей системе.

##Создание шаблона

Шаблоны управления ресурсами Azure — это документы JSON, описывающие __группу ресурсов__ и все входящие в нее ресурсы (например, HDInsight). Вы можете определить все необходимые для HDInsight ресурсы в одном шаблоне, а также централизованно управлять всеми изменениями в группе, используя __развертывания__ с применением изменений ко всей группе.

Шаблон обычно состоит из двух частей: собственно шаблона и файла параметров, в который вы записываете параметры своей конфигурации. Например, имя кластера, имя администратора и пароль. При использовании API REST напрямую эти две части необходимо объединить в один файл. Формат этого документа JSON следующий.

    {
        "properties": {
            "template": {
                contents of template file
            },
            "mode": "deploymentmode",
            "Parameters": {
                contents of the parameters element from parameters file
            }
        }
    }

Например, ниже приведен результат слияния файлов параметров и шаблона, доступного по адресу [https://github.com/Azure/azure-quickstart-templates/tree/master/hdinsight-linux-ssh-password](https://github.com/Azure/azure-quickstart-templates/tree/master/hdinsight-linux-ssh-password). Будет создан кластер под управлением Linux с паролем для защиты учетной записи пользователя SSH.

    {
        "properties": {
            "template": {
                "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                "contentVersion": "1.0.0.0",
                "parameters": {
                    "location": {
                        "type": "string",
                        "allowedValues": ["Central US",
                        "East Asia",
                        "East US",
                        "Japan East",
                        "Japan West",
                        "North Europe",
                        "South Central US",
                        "Southeast Asia",
                        "West Europe",
                        "West US"],
                        "metadata": {
                            "description": "The location where all azure resources will be deployed."
                        }
                    },
                    "clusterType": {
                        "type": "string",
                        "allowedValues": ["hadoop",
                        "hbase",
                        "storm",
                        "spark"],
                        "metadata": {
                            "description": "The type of the HDInsight cluster to create."
                        }
                    },
                    "clusterName": {
                        "type": "string",
                        "metadata": {
                            "description": "The name of the HDInsight cluster to create."
                        }
                    },
                    "clusterLoginUserName": {
                        "type": "string",
                        "metadata": {
                            "description": "These credentials can be used to submit jobs to the cluster and to log into cluster dashboards."
                        }
                    },
                    "clusterLoginPassword": {
                        "type": "securestring",
                        "metadata": {
                            "description": "The password must be at least 10 characters in length and must contain at least one digit, one non-alphanumeric character, and one upper or lower case letter."
                        }
                    },
                    "sshUserName": {
                        "type": "string",
                        "metadata": {
                            "description": "These credentials can be used to remotely access the cluster."
                        }
                    },
                    "sshPassword": {
                        "type": "securestring",
                        "metadata": {
                            "description": "The password must be at least 10 characters in length and must contain at least one digit, one non-alphanumeric character, and one upper or lower case letter."
                        }
                    },
                    "clusterStorageAccountName": {
                        "type": "string",
                        "metadata": {
                            "description": "The name of the storage account to be created and be used as the cluster's storage."
                        }
                    },
                    "clusterWorkerNodeCount": {
                        "type": "int",
                        "defaultValue": 4,
                        "metadata": {
                            "description": "The number of nodes in the HDInsight cluster."
                        }
                    }
                },
                "variables": {
                    "defaultApiVersion": "2015-05-01-preview",
                    "clusterApiVersion": "2015-03-01-preview"
                },
                "resources": [{
                    "name": "[parameters('clusterStorageAccountName')]",
                    "type": "Microsoft.Storage/storageAccounts",
                    "location": "[parameters('location')]",
                    "apiVersion": "[variables('defaultApiVersion')]",
                    "dependsOn": [],
                    "tags": {
                        
                    },
                    "properties": {
                        "accountType": "Standard_LRS"
                    }
                },
                {
                    "name": "[parameters('clusterName')]",
                    "type": "Microsoft.HDInsight/clusters",
                    "location": "[parameters('location')]",
                    "apiVersion": "[variables('clusterApiVersion')]",
                    "dependsOn": ["[concat('Microsoft.Storage/storageAccounts/',parameters('clusterStorageAccountName'))]"],
                    "tags": {
                        
                    },
                    "properties": {
                        "clusterVersion": "3.2",
                        "osType": "Linux",
                        "clusterDefinition": {
                            "kind": "[parameters('clusterType')]",
                            "configurations": {
                                "gateway": {
                                    "restAuthCredential.isEnabled": true,
                                    "restAuthCredential.username": "[parameters('clusterLoginUserName')]",
                                    "restAuthCredential.password": "[parameters('clusterLoginPassword')]"
                                }
                            }
                        },
                        "storageProfile": {
                            "storageaccounts": [{
                                "name": "[concat(parameters('clusterStorageAccountName'),'.blob.core.windows.net')]",
                                "isDefault": true,
                                "container": "[parameters('clusterName')]",
                                "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('clusterStorageAccountName')), variables('defaultApiVersion')).key1]"
                            }]
                        },
                        "computeProfile": {
                            "roles": [{
                                "name": "headnode",
                                "targetInstanceCount": "2",
                                "hardwareProfile": {
                                    "vmSize": "Standard_D3"
                                },
                                "osProfile": {
                                    "linuxOperatingSystemProfile": {
                                        "username": "[parameters('sshUserName')]",
                                        "password": "[parameters('sshPassword')]"
                                    }
                                }
                            },
                            {
                                "name": "workernode",
                                "targetInstanceCount": "[parameters('clusterWorkerNodeCount')]",
                                "hardwareProfile": {
                                    "vmSize": "Standard_D3"
                                },
                                "osProfile": {
                                    "linuxOperatingSystemProfile": {
                                        "username": "[parameters('sshUserName')]",
                                        "password": "[parameters('sshPassword')]"
                                    }
                                }
                            }]
                        }
                    }
                }],
                "outputs": {
                    "cluster": {
                        "type": "object",
                        "value": "[reference(resourceId('Microsoft.HDInsight/clusters',parameters('clusterName')))]"
                    }
                }
            },
            "mode": "incremental",
            "Parameters": {
                "location": {
                    "value": "North Europe"
                },
                "clusterName": {
                    "value": "newclustername"
                },
                "clusterType": {
                    "value": "hadoop"
                },
                "clusterStorageAccountName": {
                    "value": "newstoragename"
                },
                "clusterLoginUserName": {
                    "value": "admin"
                },
                "clusterLoginPassword": {
                    "value": "changeme"
                },
                "sshUserName": {
                    "value": "sshuser"
                },
                "sshPassword": {
                    "value": "changeme"
                }
            }
        }
    }

Этот пример будет использоваться при выполнении действий в этом документе. Заполнитель _values_ в разделе __Parameters__ (в конце сценария) необходимо заменить значениями для своего кластера.

##Вход в подписку Azure

Выполните действия, описанные в статье [Подключение к среде Azure с использованием интерфейса командной строки Azure (Azure CLI)](../xplat-cli-connect.md), и подключитесь к подписке с помощью метода __login__.

##Создание субъекта-службы

> [AZURE.IMPORTANT]При выполнении действий в статье, указанной ниже, необходимо выполнить следующее изменение:
> 
> * Когда в действиях необходимо использовать значение __reader__, используйте вместо него __owner__. Так будет создан субъект-служба, который сможет внести изменения в вашу подписку, которая необходима для создания кластера HDInsight.
>
> Также необходимо сохранить следующую информацию, используемую при выполнении действий:
> 
> * идентификатор подписки — вы получаете его при использовании `azure account list`;
> * идентификатор клиента — вы получаете его при использовании `azure account list`;
> * идентификатор приложения — возвращается при создании субъекта-службы;
> * пароль для субъекта-службы — используется при создании субъекта-службы.

Выполните действия, описанные в разделе _Проверка подлинности субъекта-службы с паролем — интерфейс командной строки Azure_ документа [Проверка подлинности субъекта-службы в диспетчере ресурсов Azure](https://azure.microsoft.com/documentation/articles/resource-group-authenticate-service-principal/#authenticate-service-principal-with-password---azure-cli). Будет создан новый субъект-служба, который может использоваться для проверки подлинности запроса на создание кластера.

##Получение маркера проверки подлинности

Используйте следующую команду для получения нового маркера от Azure. Замените __TENANTID__, __APPLICATIONID__ и __PASSWORD__ данными, сохраненными при создании субъекта-службы.

    curl -X "POST" "https://login.microsoftonline.com/TENANTID/oauth2/token" \
    -H "Cookie: flight-uxoptin=true; stsservicecookie=ests; x-ms-gateway-slice=productionb; stsservicecookie=ests" \
    -H "Content-Type: application/x-www-form-urlencoded" \
    --data-urlencode "client_id=APPLICATIONID" \
    --data-urlencode "grant_type=client_credentials" \
    --data-urlencode "client_secret=PASSWORD" \
    --data-urlencode "resource=https://management.azure.com/"

Если этот запрос завершится успешно, будет получен ответ серии 200, и тело ответа будет содержать документ JSON.

> [AZURE.IMPORTANT]Документ JSON, возвращаемый этим запросом, будет содержать элемент с именем __access\_token__. Значением этого элемента является маркер доступа, который необходимо использовать для проверки подлинности запросов, используемых в следующих разделах этого документа.

##Создание группы ресурсов

Для создания новой группы ресурсов выполните указанную ниже команду. Группу необходимо создать перед созданием ресурсов, таких как кластер HDInsight.

* Замените __SUBSCRIPTIONID__ идентификатором подписки, полученным при создании субъекта-службы.
* Замените __ACCESSTOKEN__ маркером доступа, полученным на предыдущем этапе.
* Замените __DATACENTERLOCATION__ центром обработки данных, в котором вы хотите создать группу ресурсов и ресурсы. Например, "Южно-центральный регион США". 
* Замените __RESOURCEGROUPNAME__ именем группы, которое вы хотите использовать.

    curl -X "PUT" "https://management.azure.com/subscriptions/SUBSCRIPTIONID/resourcegroups/GROUPNAME?api-version=2015-01-01" \\ -H "Authorization: Bearer ACCESSTOKEN" \\ -H "Content-Type: application/json" \\ -d $'{ "location": "DATACENTERLOCATION" }’

Если этот запрос завершится успешно, будет получен ответ серии 200, и тело ответа будет содержать документ JSON, содержащий информацию о группе. Элемент `"provisioningState"` будет содержать значение `"Succeeded"`.

##Создание развертывания

Используйте следующий код для развертывания конфигурации кластера (шаблона и значений параметров) в группе ресурсов.

* Замените __SUBSCRIPTIONID__ и __ACCESSTOKEN__ значениями, использованными ранее. 
* Замените __GROUPNAME__ именем группы ресурсов, созданной на предыдущем этапе.
* Замените __DEPLOYMENTNAME__ именем, которое вы хотите использовать для этого развертывания.

    curl -X "PUT" "https://management.azure.com/subscriptions/SUBSCRIPTIONID/resourcegroups/GROUPNAME/providers/microsoft.resources/deployments/DEPLOYMENTNAME?api-version=2015-01-01" \\ -H "Authorization: Bearer ACCESSTOKEN" \\ -H "Content-Type: application/json" \\ -d "{установите в теле строки необходимые шаблоны и параметры}"

> [AZURE.NOTE]При сохранении документа JSON, содержащего шаблон и параметры, в файл можно использовать следующие параметры вместо `-d "{шаблон и параметры}"':
>
> ```--data-binary "@/path/to/file.json"```

Если этот запрос завершится успешно, будет получен ответ серии 200, и тело ответа будет содержать документ JSON, содержащий информацию об операции развертывания.

> [AZURE.IMPORTANT]Обратите внимание, что в этот момент развертывание отправлено, но не завершено. Для завершения развертывания может потребоваться несколько минут, обычно около 15.

##Проверка состояния развертывания

Чтобы проверить состояние развертывания, используйте следующую команду.

* Замените __SUBSCRIPTIONID__ и __ACCESSTOKEN__ значениями, использованными ранее. 
* Замените __GROUPNAME__ именем группы ресурсов, созданной на предыдущем этапе.

    curl -X "GET" "https://management.azure.com/subscriptions/SUBSCRIPTIONID/resourcegroups/GROUPNAME/providers/microsoft.resources/deployments/DEPLOYMENTNAME?api-version=2015-01-01" \\ -H "Authorization: Bearer ACCESSTOKEN" \\ -H "Content-Type: application/json"

Эта команда возвращает документ JSON, содержащий сведения об операции развертывания. Элемент `"provisioningState"` будет содержать состояние развертывания; если значение равно `"Succeeded"`, это означает успешное выполнение развертывания. Теперь кластер должен быть готов к использованию.

##Дальнейшие действия

Теперь, когда вы успешно создали кластер HDInsight, обратитесь к следующим статьям, чтобы научиться работать с кластером.

###Кластеры Hadoop

* [Использование Hive с HDInsight](hdinsight-use-hive.md)
* [Использование Pig с HDInsight](hdinsight-use-pig.md)
* [Использование MapReduce с HDInsight](hdinsight-use-mapreduce.md)

###Кластеры HBase

* [Начало работы с HBase в HDInsight](hdinsight-hbase-tutorial-get-started-linux.md)
* [Разработка приложений Java для HBase в HDInsight](hdinsight-hbase-build-java-maven-linux.md)

###Кластеры Storm

* [Разработка приложений Java для Storm в HDInsight](hdinsight-storm-develop-java-topology.md)
* [Использование компонентов Python в Storm в HDInsight](hdinsight-storm-develop-python-topology.md)
* [Развертывание и мониторинг топологий с помощью Storm в HDInsight](hdinsight-storm-deploy-monitor-topology-linux.md)

<!---HONumber=AcomDC_1210_2015-->