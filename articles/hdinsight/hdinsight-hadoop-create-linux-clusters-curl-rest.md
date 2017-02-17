---
title: "Создание кластеров Azure HDInsight (Hadoop) с помощью cURL и REST | Документация Майкрософт"
description: "Узнайте, как создавать кластеры HDInsight с помощью cURL, шаблонов Azure Resource Manager и Azure REST API. Вы можете указать тип кластера (Hadoop, HBase или Storm) либо использовать сценарии для установки настраиваемых компонентов."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 98be5893-2c6f-4dfa-95ec-d4d8b5b7dcb5
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/28/2016
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: bb700c7de96712666bc4be1f8e430a2e94761f69
ms.openlocfilehash: a4dc3d4599cfe2c6dd7580c423987f6173a9c5ba


---
# <a name="create-hdinsight-clusters-using-curl-and-the-azure-rest-api"></a>Создание кластеров HDInsight с помощью cURL и Azure REST API

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Azure REST API позволяет управлять службами, размещенными на платформе Azure, в том числе создавать ресурсы, например кластеры HDInsight. Прочитав эту статью, вы научитесь создавать шаблоны диспетчера ресурсов Azure для настройки кластера HDInsight и связанных хранилищ и разворачивать шаблон в API REST Azure с помощью cURL для создания нового кластера HDInsight.

> [!IMPORTANT]
> Linux — единственная операционная система, используемая для работы с HDInsight 3.4 или более поздней версии. См. дополнительные сведения о [нерекомендуемых версиях HDInsight в Windows](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date).

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

* **Подписка Azure**. Ознакомьтесь с [бесплатной пробной версией Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

* **Azure CLI 2.0 (предварительная версия)**. Интерфейс командной строки Azure используется для создания субъекта-службы, который затем используется для создания маркеров проверки подлинности для запросов к API REST Azure. Дополнительные сведения о предварительной версии Azure CLI 2.0 см. в статье [Get started with Azure CLI 2.0 (Preview)](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2) (Приступая к работе с Azure CLI 2.0 (предварительная версия)).

* **cURL**. Эту утилиту можно установить с помощью системы управления пакетами или загрузить с сайта [http://curl.haxx.se/](http://curl.haxx.se/).

  > [!NOTE]
  > Если для выполнения команд, приведенных в этом документе, вы используете PowerShell, необходимо сначала удалить псевдоним `curl`, который создается по умолчанию. Когда вы выполняете команду `curl` в командной строке PowerShell, этот псевдоним использует командлет Invoke-WebRequest вместо cURL, поэтому большинство описанных в этом документе команд будут завершены ошибкой.
  >
  > Чтобы удалить этот псевдоним, выполните следующую команду в командной строке PowerShell:
  >
  > `Remove-item alias:curl`
  >
  > После удаления псевдонима следует использовать версию cURL, установленную в вашей системе.

### <a name="access-control-requirements"></a>Требования к контролю доступа
[!INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="create-a-template"></a>Создание шаблона

Шаблоны Azure Resource Manager — это документы JSON, описывающие **группу ресурсов** и все входящие в нее ресурсы (например, HDInsight). Вы можете определить все необходимые для HDInsight ресурсы в одном шаблоне, а также централизованно управлять всеми изменениями в группе, используя **развертывания** с применением изменений ко всей группе.

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

Этот пример будет использоваться при выполнении действий в этом документе. Заполнитель *values* в разделе **Parameters** (в конце сценария) необходимо заменить значениями для своего кластера.

> [!IMPORTANT]
> Шаблон использует стандартное количество рабочих узлов (4) для кластера HDInsight. Если вы планируете использовать более 32 рабочих узлов (при создании кластера или в ходе масштабирования после создания кластера), для головного узла потребуется минимум 8-ядерный процессор и 14 ГБ ОЗУ.
>
> Дополнительные сведения о размерах узлов и их стоимости см. на странице с [ценами на HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="login-to-your-azure-subscription"></a>Вход в подписку Azure

Выполните действия, описанные в статье [Get started with Azure CLI 2.0 (Preview)](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2) (Приступая к работе с Azure CLI 2.0 (предварительная версия)) и подключитесь к подписке, используя команду `az login`.

## <a name="create-a-service-principal"></a>Создание субъекта-службы

> [!NOTE]
> Приведенные действия представляют собой сокращенные сведения из раздела *Создание субъекта-службы с паролем* статьи [Создание субъекта-службы для доступа к данным с помощью интерфейса командной строки Azure](../azure-resource-manager/resource-group-authenticate-service-principal-cli.md#create-service-principal-with-password). С помощью этих действий создается новый субъект-служба, который может использоваться для аутентификации запросов REST API, используемых для создания ресурсов Azure, таких как кластер HDInsight.

1. Используйте следующую команду в командной строке, чтобы вывести список подписок Azure.

         az account list --query '[].{Subscription_ID:id,Tenant_ID:tenantId,Name:name}'  --output table

    Из списка выберите подписку, которую нужно использовать, и обратите внимание на столбцы **Subscription_ID** и __Tenant_ID__. Сохраните эти значения.

2. Используйте следующую команду, чтобы создать приложение в Azure Active Directory:

        az ad app create --display-name "exampleapp" --homepage "https://www.contoso.org" --identifier-uris "https://www.contoso.org/example" --password <Your password> --query 'appId'

    Замените значения `--display-name`, `--homepage` и `--identifier-uris` собственными. Введите пароль для новой записи Active Directory.

   > [!NOTE]
   > Так как вы создаете это приложение для аутентификации через субъект-службу, значения `--home-page` и `--identifier-uris` не обязательно должны ссылаться на фактическую веб-страницу, размещенную в Интернете. Это просто должны быть уникальные универсальные коды ресурса (URI).

   Эта команда возвращает значение __App ID__ для нового приложения. Сохраните его.

3. Используйте следующую команду, чтобы создать субъект-службу с помощью **App ID**:

        az ad sp create --id <App ID> --query 'objectId'

     Эта команда возвращает значение __Object ID__. Сохраните его.

4. Назначьте роль **Владелец** субъекту-службе, используя значение **Object ID**. Необходимо также использовать **идентификатор подписки** , полученный ранее.

        az role assignment create --assignee <Object ID> --role Owner --scope /subscriptions/<Subscription ID>/

## <a name="get-an-authentication-token"></a>Получение маркера проверки подлинности

Используйте следующую команду, чтобы получить маркер аутентификации:

    curl -X "POST" "https://login.microsoftonline.com/TenantID/oauth2/token" \
    -H "Cookie: flight-uxoptin=true; stsservicecookie=ests; x-ms-gateway-slice=productionb; stsservicecookie=ests" \
    -H "Content-Type: application/x-www-form-urlencoded" \
    --data-urlencode "client_id=AppID" \
    --data-urlencode "grant_type=client_credentials" \
    --data-urlencode "client_secret=password" \
    --data-urlencode "resource=https://management.azure.com/"

    Replace **TenantID**, **AppID**, and **password** with the values obtained or used previously.

    If this request is successful, you will receive a 200 series response and the response body will contain a JSON document.

    The JSON document returned by this request will contain an element named **access_token**; the value of this element is the access token you must use to authentication the requests used in the next sections of this document.

        {
            "token_type":"Bearer",
            "expires_in":"3599",
            "expires_on":"1463409994",
            "not_before":"1463406094",
            "resource":"https://management.azure.com/","access_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWoNBVGZNNXBPWWlKSE1iYTlnb0VLWSIsImtpZCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSJ9.eyJhdWQiOiJodHRwczovL21hbmFnZW1lbnQuYXp1cmUuY29tLyIsImlzcyI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI2Ny8iLCJpYXQiOjE0NjM0MDYwOTQsIm5iZiI6MTQ2MzQwNjA5NCwiZXhwIjoxNDYzNDA5OTk5LCJhcHBpZCI6IjBlYzcyMzM0LTZkMDMtNDhmYi04OWU1LTU2NTJiODBiZDliYiIsImFwcGlkYWNyIjoiMSIsImlkcCI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0Ny8iLCJvaWQiOiJlNjgxZTZiMi1mZThkLTRkZGUtYjZiMS0xNjAyZDQyNWQzOWYiLCJzdWIiOiJlNjgxZTZiMi1mZThkLTRkZGUtYjZiMS0xNjAyZDQyNWQzOWYiLCJ0aWQiOiI3MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDciLCJ2ZXIiOiIxLjAifQ.nJVERbeDHLGHn7ZsbVGBJyHOu2PYhG5dji6F63gu8XN2Cvol3J1HO1uB4H3nCSt9DTu_jMHqAur_NNyobgNM21GojbEZAvd0I9NY0UDumBEvDZfMKneqp7a_cgAU7IYRcTPneSxbD6wo-8gIgfN9KDql98b0uEzixIVIWra2Q1bUUYETYqyaJNdS4RUmlJKNNpENllAyHQLv7hXnap1IuzP-f5CNIbbj9UgXxLiOtW5JhUAwWLZ3-WMhNRpUO2SIB7W7tQ0AbjXw3aUYr7el066J51z5tC1AK9UC-mD_fO_HUP6ZmPzu5gLA6DxkIIYP3grPnRVoUDltHQvwgONDOw"
        }

## <a name="create-a-resource-group"></a>Создание группы ресурсов

Для создания новой группы ресурсов выполните указанную ниже команду. Группу необходимо создать перед созданием ресурсов, таких как кластер HDInsight.

* Замените **SubscriptionID** идентификатором подписки, полученным при создании субъекта-службы.
* Замените **AccessToken** маркером доступа, полученным на предыдущем этапе.
* Замените **DataCenterLocation** центром обработки данных, в котором вы хотите создать группу ресурсов и ресурсы. Например, "Южно-центральный регион США".
* Замените **ResourceGroupName** именем, которое хотите использовать для этой группы.

```
curl -X "PUT" "https://management.azure.com/subscriptions/SubscriptionID/resourcegroups/ResourceGroupName?api-version=2015-01-01" \
    -H "Authorization: Bearer AccessToken" \
    -H "Content-Type: application/json" \
    -d $'{
"location": "DataCenterLocation"
}'
```

Если этот запрос завершится успешно, будет получен ответ серии 200, и тело ответа будет содержать документ JSON, содержащий информацию о группе. Элемент `"provisioningState"` будет содержать значение `"Succeeded"`.

## <a name="create-a-deployment"></a>Создание развертывания

Используйте следующий код для развертывания конфигурации кластера (шаблона и значений параметров) в группе ресурсов.

* Замените **SubscriptionID** и **AccessToken** значениями, использованными ранее.
* Замените **ResourceGroupName** именем группы ресурсов, созданной в предыдущем разделе.
* Замените **DeploymentName** именем, которое вы хотите использовать для этого развертывания.

```
curl -X "PUT" "https://management.azure.com/subscriptions/SubscriptionID/resourcegroups/ResourceGroupName/providers/microsoft.resources/deployments/DeploymentName?api-version=2015-01-01" \
-H "Authorization: Bearer AccessToken" \
-H "Content-Type: application/json" \
-d "{set your body string to the template and parameters}"
```

> [!NOTE]
> При сохранении документа JSON, содержащего шаблон и параметры, в файл можно использовать следующие параметры вместо `-d "{ template and parameters}"`.
>
> `--data-binary "@/path/to/file.json"`

Если этот запрос завершится успешно, будет получен ответ серии 200, и тело ответа будет содержать документ JSON, содержащий информацию об операции развертывания.

> [!IMPORTANT]
> Обратите внимание, что в этот момент развертывание отправлено, но не завершено. Для завершения развертывания может потребоваться несколько минут, обычно около 15.

## <a name="check-the-status-of-a-deployment"></a>Проверка состояния развертывания

Чтобы проверить состояние развертывания, используйте следующую команду.

* Замените **SubscriptionID** и **AccessToken** значениями, использованными ранее.
* Замените **ResourceGroupName** именем группы ресурсов, созданной в предыдущем разделе.

```
curl -X "GET" "https://management.azure.com/subscriptions/SubscriptionID/resourcegroups/ResourceGroupName/providers/microsoft.resources/deployments/DeploymentName?api-version=2015-01-01" \
-H "Authorization: Bearer AccessToken" \
-H "Content-Type: application/json"
```

Эта команда возвращает документ JSON, содержащий сведения об операции развертывания. Элемент `"provisioningState"` будет содержать состояние развертывания; если значение равно `"Succeeded"`, это означает успешное выполнение развертывания. Теперь кластер должен быть готов к использованию.

## <a name="next-steps"></a>Дальнейшие действия

Теперь, когда вы успешно создали кластер HDInsight, обратитесь к следующим статьям, чтобы научиться работать с кластером.

### <a name="hadoop-clusters"></a>Кластеры Hadoop

* [Использование Hive с HDInsight](hdinsight-use-hive.md)
* [Использование Pig с HDInsight](hdinsight-use-pig.md)
* [Использование MapReduce с HDInsight](hdinsight-use-mapreduce.md)

### <a name="hbase-clusters"></a>Кластеры HBase

* [Начало работы с HBase в HDInsight](hdinsight-hbase-tutorial-get-started-linux.md)
* [Разработка приложений Java для HBase в HDInsight](hdinsight-hbase-build-java-maven-linux.md)

### <a name="storm-clusters"></a>Кластеры Storm

* [Разработка приложений Java для Storm в HDInsight](hdinsight-storm-develop-java-topology.md)
* [Использование компонентов Python в Storm в HDInsight](hdinsight-storm-develop-python-topology.md)
* [Развертывание и мониторинг топологий с помощью Storm в HDInsight](hdinsight-storm-deploy-monitor-topology-linux.md)



<!--HONumber=Jan17_HO4-->


