<properties
   	pageTitle="Создание кластеров Hadoop, HBase и Storm на платформе Linux в HDInsight с помощью cURL и REST API Azure | Microsoft Azure"
   	description="Узнайте, как создать кластеры HDInsight под управлением Linux с помощью cURL, шаблонов диспетчера ресурсов Azure и Azure REST API. Вы можете указать тип кластера (Hadoop, HBase или Storm) либо использовать сценарии для установки настраиваемых компонентов."
   	services="hdinsight"
   	documentationCenter=""
   	authors="Blackmist"
   	manager="jhubbard"
   	editor="cgronlun"
	tags="azure-portal"/>

<tags
   	ms.service="hdinsight"
   	ms.devlang="na"
   	ms.topic="article"
   	ms.tgt_pltfrm="na"
   	ms.workload="big-data"
   	ms.date="07/27/2016"
   	ms.author="larryfr"/>

#Создание кластеров под управлением Linux в HDInsight с помощью cURL и API REST Azure

[AZURE.INCLUDE [selector](../../includes/hdinsight-selector-create-clusters.md)]

API REST Azure позволяет управлять службами, размещенными на платформе Azure, в том числе создавать новые ресурсы, например кластеры HDInsight под управлением Linux. Прочитав эту статью, вы научитесь создавать шаблоны диспетчера ресурсов Azure для настройки кластера HDInsight и связанных хранилищ и разворачивать шаблон в API REST Azure с помощью cURL для создания нового кластера HDInsight.

> [AZURE.IMPORTANT] При выполнении действий, описанных в этом документе, используется стандартное количество рабочих узлов (4) для кластера HDInsight. Если вы планируете использовать более 32 рабочих узлов (при создании кластера или в ходе масштабирования после создания кластера), для головного узла потребуется минимум 8-ядерный процессор и 14 ГБ ОЗУ.
>
> Дополнительные сведения о размерах узлов и их стоимости см. в статье [Сведения о ценах на HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

##Предварительные требования

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]


- **Подписка Azure.**. См. [Бесплатная пробная версия Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- __Интерфейс командной строки Azure__. Интерфейс командной строки Azure используется для создания субъекта-службы, который затем используется для создания маркеров проверки подлинности для запросов к API REST Azure.

    [AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)]

- __cURL__. Эту утилиту можно установить с помощью системы управления пакетами или загрузить с сайта [http://curl.haxx.se/](http://curl.haxx.se/).

    > [AZURE.NOTE] Если для выполнения команд, приведенных в этом документе, вы используете PowerShell, необходимо сначала удалить псевдоним `curl`, который создается по умолчанию. Когда вы выполняете команду `curl` в командной строке PowerShell, этот псевдоним использует командлет Invoke-WebRequest вместо cURL, поэтому большинство описанных в этом документе команд будут завершены ошибкой.
    > 
    > Чтобы удалить этот псевдоним, выполните следующую команду в командной строке PowerShell:
    >
    > `Remove-item alias:curl`
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

Например, ниже приведен результат слияния файлов параметров и шаблона, доступного по адресу [https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-linux-ssh-password](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-linux-ssh-password). Будет создан кластер под управлением Linux с паролем для защиты учетной записи пользователя SSH.

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

Выполните действия, описанные в статье [Подключение к среде Azure с использованием интерфейса командной строки Azure (Azure CLI)](../xplat-cli-connect.md), и подключитесь к подписке с помощью команды `azure login`.

##Создание субъекта-службы

> [AZURE.NOTE] Приведенные действия представляют собой сокращенные сведения из раздела _Проверка подлинности субъекта-службы с паролем — интерфейс командной строки Azure_ документа [Проверка подлинности субъекта-службы в диспетчере ресурсов Azure](../resource-group-authenticate-service-principal.md#authenticate-service-principal-with-password---azure-cli). С помощью этих действий создается новый субъект-служба, который может использоваться для аутентификации запросов REST API, используемых для создания ресурсов Azure, таких как кластер HDInsight.

1. Выполните в командной строке, сеансе терминала или оболочке следующую команду, чтобы вывести список подписок Azure.

        azure account list
        
    Из списка выберите подписку, которую вы хотите использовать, и обратите внимание на столбец __Идентификатор__. Это __идентификатор подписки__, и он будет использоваться в большинстве действий, описанных в этом документе.

2. Создайте новое приложение в Azure Active Directory.

        azure ad app create --name "exampleapp" --home-page "https://www.contoso.org" --identifier-uris "https://www.contoso.org/example" --password <Your_Password>
        
    Замените значения `--name`, `--home-page` и `--identifier-uris` собственными. Введите пароль для новой записи Active Directory.
    
    > [AZURE.NOTE] Так как вы создаете это приложение для аутентификации через субъект-службу, значения `--home-page` и `--identifier-uris` не обязательно должны ссылаться на фактическую веб-страницу, размещенную в Интернете. Это просто должны быть уникальные универсальные коды ресурса (URI).
    
    Сохраните значение __AppId__ из полученных данных.
    
        data:    AppId:          4fd39843-c338-417d-b549-a545f584a745
        data:    ObjectId:       4f8ee977-216a-45c1-9fa3-d023089b2962
        data:    DisplayName:    exampleapp
        ...
        info:    ad app create command OK
    
3. Создайте субъект-службу с помощью возвращенного ранее значения __AppId__.

        azure ad sp create 4fd39843-c338-417d-b549-a545f584a745
        
     Сохраните значение __Object Id__ из полученных данных.
     
        info:    Executing command ad sp create
        - Creating service principal for application 4fd39843-c338-417d-b549-a545f584a74+
        data:    Object Id:        7dbc8265-51ed-4038-8e13-31948c7f4ce7
        data:    Display Name:     exampleapp
        data:    Service Principal Names:
        data:                      4fd39843-c338-417d-b549-a545f584a745
        data:                      https://www.contoso.org/example
        info:    ad sp create command OK
        
4. Назначьте роль __Владелец__ субъекту-службе, используя возвращенное ранее значение __Object ID__. Необходимо также использовать __идентификатор подписки__, полученный ранее.
    
        azure role assignment create --objectId 7dbc8265-51ed-4038-8e13-31948c7f4ce7 -o Owner -c /subscriptions/{SubscriptionID}/
        
    После выполнения этой команды у субъекта-службы будет доступ владельца для указанного идентификатора подписки.

##Получение маркера проверки подлинности

1. Используйте следующую команду, чтобы найти __идентификатор клиента__ для своей подписки.

        azure account show -s <subscription ID>
        
    В возвращенных данных найдите __идентификатор клиента__.
    
        info:    Executing command account show
        data:    Name                        : MyAzureAccount
        data:    ID                          : 45a1014d-0f27-25d2-b838-b8f373d6d52e
        data:    State                       : Enabled
        data:    Tenant ID                   : 22f988bf-56f1-41af-91ab-3d7cd011db47
        data:    Is Default                  : true
        data:    Environment                 : AzureCloud
        data:    Has Certificate             : No
        data:    Has Access Token            : Yes
        data:    User name                   : myname@contoso.org
        data:    
        info:    account show command OK

2. Создайте новый маркер с помощью Azure REST API.

        curl -X "POST" "https://login.microsoftonline.com/TenantID/oauth2/token" \
        -H "Cookie: flight-uxoptin=true; stsservicecookie=ests; x-ms-gateway-slice=productionb; stsservicecookie=ests" \
        -H "Content-Type: application/x-www-form-urlencoded" \
        --data-urlencode "client_id=AppID" \
        --data-urlencode "grant_type=client_credentials" \
        --data-urlencode "client_secret=password" \
        --data-urlencode "resource=https://management.azure.com/"
    
    Замените __TenantID__, __AppID__, и __password__ значениями, полученными или указанными ранее.

    Если этот запрос завершится успешно, будет получен ответ серии 200, и тело ответа будет содержать документ JSON.

    Документ JSON, возвращаемый этим запросом, будет содержать элемент с именем __access\_token__. Значением этого элемента является маркер доступа, который необходимо использовать для проверки подлинности запросов, используемых в следующих разделах этого документа.
    
        {
            "token_type":"Bearer",
            "expires_in":"3599",
            "expires_on":"1463409994",
            "not_before":"1463406094",
            "resource":"https://management.azure.com/","access_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWoNBVGZNNXBPWWlKSE1iYTlnb0VLWSIsImtpZCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSJ9.eyJhdWQiOiJodHRwczovL21hbmFnZW1lbnQuYXp1cmUuY29tLyIsImlzcyI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI2Ny8iLCJpYXQiOjE0NjM0MDYwOTQsIm5iZiI6MTQ2MzQwNjA5NCwiZXhwIjoxNDYzNDA5OTk5LCJhcHBpZCI6IjBlYzcyMzM0LTZkMDMtNDhmYi04OWU1LTU2NTJiODBiZDliYiIsImFwcGlkYWNyIjoiMSIsImlkcCI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0Ny8iLCJvaWQiOiJlNjgxZTZiMi1mZThkLTRkZGUtYjZiMS0xNjAyZDQyNWQzOWYiLCJzdWIiOiJlNjgxZTZiMi1mZThkLTRkZGUtYjZiMS0xNjAyZDQyNWQzOWYiLCJ0aWQiOiI3MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDciLCJ2ZXIiOiIxLjAifQ.nJVERbeDHLGHn7ZsbVGBJyHOu2PYhG5dji6F63gu8XN2Cvol3J1HO1uB4H3nCSt9DTu_jMHqAur_NNyobgNM21GojbEZAvd0I9NY0UDumBEvDZfMKneqp7a_cgAU7IYRcTPneSxbD6wo-8gIgfN9KDql98b0uEzixIVIWra2Q1bUUYETYqyaJNdS4RUmlJKNNpENllAyHQLv7hXnap1IuzP-f5CNIbbj9UgXxLiOtW5JhUAwWLZ3-WMhNRpUO2SIB7W7tQ0AbjXw3aUYr7el066J51z5tC1AK9UC-mD_fO_HUP6ZmPzu5gLA6DxkIIYP3grPnRVoUDltHQvwgONDOw"
        }

##Создание группы ресурсов

Для создания новой группы ресурсов выполните указанную ниже команду. Группу необходимо создать перед созданием ресурсов, таких как кластер HDInsight.

* Замените __SubscriptionID__ идентификатором подписки, полученным при создании субъекта-службы.
* Замените __AccessToken__ маркером доступа, полученным на предыдущем этапе.
* Замените __DataCenterLocation__ центром обработки данных, в котором вы хотите создать группу ресурсов и ресурсы. Например, "Южно-центральный регион США".
* Замените __ResourceGroupName__ именем, которое хотите использовать для этой группы.

```
curl -X "PUT" "https://management.azure.com/subscriptions/SubscriptionID/resourcegroups/ResourceGroupName?api-version=2015-01-01" \
    -H "Authorization: Bearer AccessToken" \
    -H "Content-Type: application/json" \
    -d $'{
"location": "DataCenterLocation"
}'
```

Если этот запрос завершится успешно, будет получен ответ серии 200, и тело ответа будет содержать документ JSON, содержащий информацию о группе. Элемент `"provisioningState"` будет содержать значение `"Succeeded"`.

##Создание развертывания

Используйте следующий код для развертывания конфигурации кластера (шаблона и значений параметров) в группе ресурсов.

* Замените __SubscriptionID__ и __AccessToken__ значениями, использованными ранее.
* Замените __ResourceGroupName__ именем группы ресурсов, созданной в предыдущем разделе.
* Замените __DeploymentName__ именем, которое вы хотите использовать для этого развертывания.

```
curl -X "PUT" "https://management.azure.com/subscriptions/SubscriptionID/resourcegroups/ResourceGroupName/providers/microsoft.resources/deployments/DeploymentName?api-version=2015-01-01" \
-H "Authorization: Bearer AccessToken" \
-H "Content-Type: application/json" \
-d "{set your body string to the template and parameters}"
```

> [AZURE.NOTE] При сохранении документа JSON, содержащего шаблон и параметры, в файл можно использовать следующие параметры вместо `-d "{ template and parameters}"`.
>
> `--data-binary "@/path/to/file.json"`

Если этот запрос завершится успешно, будет получен ответ серии 200, и тело ответа будет содержать документ JSON, содержащий информацию об операции развертывания.

> [AZURE.IMPORTANT] Обратите внимание, что в этот момент развертывание отправлено, но не завершено. Для завершения развертывания может потребоваться несколько минут, обычно около 15.

##Проверка состояния развертывания

Чтобы проверить состояние развертывания, используйте следующую команду.

* Замените __SubscriptionID__ и __AccessToken__ значениями, использованными ранее.
* Замените __ResourceGroupName__ именем группы ресурсов, созданной в предыдущем разделе.

```
curl -X "GET" "https://management.azure.com/subscriptions/SubscriptionID/resourcegroups/ResourceGroupName/providers/microsoft.resources/deployments/DeploymentName?api-version=2015-01-01" \
-H "Authorization: Bearer AccessToken" \
-H "Content-Type: application/json"
```

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

<!---HONumber=AcomDC_0914_2016-->