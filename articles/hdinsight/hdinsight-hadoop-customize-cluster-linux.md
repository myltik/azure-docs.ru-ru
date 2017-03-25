---
title: "Настройка кластеров HDInsight с помощью действий скрипта | Документация Майкрософт"
description: "Узнайте, как добавить настраиваемые компоненты в кластеры HDInsight Hadoop на основе Linux с помощью действий сценария. Действия скриптов — это сценарии Bash для узлов кластера, которые могут использоваться для настройки конфигурации кластера или добавления дополнительных служб и служебных программ, таких как Hue, Solr или R."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 48e85f53-87c1-474f-b767-ca772238cc13
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: b615f97484033bb406022e84fbcf50f88458de3c
ms.lasthandoff: 03/21/2017


---
# <a name="customize-linux-based-hdinsight-clusters-using-script-action"></a>Настройка кластеров HDInsight под управлением Linux с помощью действия сценария

В кластерах HDInsight есть параметр конфигурации **Действие скрипта** , вызывающий пользовательские скрипты, с помощью которых можно настроить кластер. Эти скрипты можно использовать при создании кластера или с кластером, который уже работает; они также используются для установки дополнительных компонентов или изменения параметров конфигурации.

> [!IMPORTANT]
> Использовать действия скриптов в уже работающем кластере можно только в кластерах HDInsight под управлением Linux.
>
> Linux — единственная операционная система, используемая для работы с HDInsight 3.4 или более поздней версии. См. дополнительные сведения о [нерекомендуемых версиях HDInsight в Windows](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date).


Действия сценариев можно опубликовать в Azure Marketplace как приложение HDInsight. В некоторых примерах в этом документе показано, как можно установить приложение HDInsight с помощью команд действия сценария PowerShell и пакета SDK для .NET. Дополнительные сведения о приложениях HDInsight см. в статье [Публикация приложений HDInsight в Azure Marketplace](hdinsight-apps-publish-applications.md).

## <a name="permissions"></a>Разрешения

При использовании присоединенного к домену кластера HDInsight для выполнения действий скрипта необходимо задать два следующих расширения Ambari:

* **AMBARI.RUN\_CUSTOM\_COMMAND.** Это разрешение по умолчанию назначено для роли администратора Ambari.
* **CLUSTER.RUN\_CUSTOM\_COMMAND.** Это разрешение по умолчанию назначено для администратора кластера HDInsight и администратора Ambari.

Дополнительные сведения о работе с разрешениями в присоединенном к домену кластере HDInsight см. в статье [Управление присоединенными к домену кластерами HDInsight (предварительная версия)](hdinsight-domain-joined-manage.md).

## <a name="access-control"></a>управление доступом;

При использовании подписки Azure без прав администратора или владельца (например, подписка организации) для доступа к группе ресурсов Azure, в которой содержится кластер HDInsight, вашей учетной записи Azure должна быть назначена как минимум роль **участника**.

Кроме того, при создании кластера HDInsight другой пользователь, которому назначена по крайней мере роль **участника** в рамках подписки Azure, должен заранее зарегистрировать поставщика ресурсов для HDInsight. Поставщик регистрируется тогда, когда пользователь с правами участника подписки впервые создает ресурс в подписке. Регистрацию также можно выполнить без создания ресурса [с помощью REST](https://msdn.microsoft.com/library/azure/dn790548.aspx).

Дополнительные сведения о работе с управлением доступом см. в следующих документах:

* [Начало работы с управлением доступом на портале Azure](../active-directory/role-based-access-control-what-is.md)
* [Использование назначений ролей для управления доступом к ресурсам в подписке Azure](../active-directory/role-based-access-control-configure.md)

## <a name="understanding-script-actions"></a>Что такое действия скриптов

Действие скрипта — это просто скрипт Bash, в который вы вводите универсальный код ресурса (URI) и для которого вы указываете параметры. Скрипт выполняется на узлах в кластере HDInsight. Ниже приведены характеристики и возможности действий скриптов.

* Они должны храниться в URI, доступном из кластера HDInsight. Возможные места хранения:

    * **учетная запись Azure Data Lake Store**, доступная из кластера HDInsight. Дополнительные сведения об использовании Azure Data Lake Store с HDInsight см. в статье [Создание кластера HDInsight с Data Lake Store с помощью портала Azure](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).

        Если скрипт находится в Data Lake Store, универсальный код ресурса (URI) имеет следующий формат: `adl://DATALAKESTOREACCOUNTNAME.azuredatalakestore.net/path_to_file`.

        > [!NOTE]
        > Кластер HDInsight субъекта-службы с доступом к Data Lake Store должен иметь доступ на чтение к скрипту.

    * Большой двоичный объект в **учетной записи хранения Azure**, которая служит основной или дополнительной учетной записью хранения для кластера HDInsight. При создании кластера HDInsight получает доступ к обоим видам учетных записей хранения, что позволяет использовать действия сценариев, которые не являются общедоступными.

    * Служба совместного доступа к файлам, например большой двоичный объект Azure, GitHub, OneDrive, Dropbox и т. д.

        Примеры универсальных кодов ресурса (URI) для скриптов, хранящихся в контейнере больших двоичных объектов (общедоступном для чтения), см. в разделе [Пример действий сценария](#example-script-action-scripts).

        > [!WARNING]
        > HDInsight поддерживает только учетные записи хранения Azure __общего назначения__. Учетные записи __хранилища BLOB-объектов__ сейчас не поддерживаются.

* Действия скрипта могут быть ограничены **запуском только на узлах определенного типа**, например головных или рабочих.

  > [!NOTE]
  > При использовании HDInsight уровня "Премиум" можно указать использование сценария на граничном узле.

* Действия скрипта могут быть **сохраняемыми** или **специализированными**.

    **Сохраняемые** — это скрипты, которые применяются на рабочих узлах и автоматически запускаются на новых узлах, созданных при масштабировании кластера.

    Сохраняемый скрипт может также применять изменения на узле другого типа (например, головном), но в плане функциональности единственной причиной для сохранения скрипта является возможность его применения на новых рабочих узлах, созданных при масштабировании кластера.

  > [!IMPORTANT]
  > Действия сохраняемых скриптов должны иметь уникальные имена.

    **Специализированные** скрипты не сохраняются, но такой скрипт можно впоследствии сделать сохраняемым (и наоборот).

  > [!IMPORTANT]
  > Действия скриптов, используемые при создании кластера, автоматически сохраняются.
  >
  > Скрипты, в которых произошла ошибка, не сохраняются, даже если вы отдельно указали, что они должны быть сохранены.

* Действия скрипта могут принимать **параметры**, используемые в процессе выполнения скрипта.
* Они выполняются с помощью **прав привилегированного пользователя** на узлах кластера.
* Действия скрипта могут выполняться с помощью **портала Azure**, **Azure PowerShell**, **интерфейса командной строки Azure** или **пакета SDK HDInsight для .NET**.

Чтобы вы могли узнать, какие скрипты применялись в кластере, а также определить идентификатор скриптов для изменения их типа, кластер ведет журнал всех скриптов, которые на нем выполнялись.

> [!IMPORTANT]
> Не существует способа автоматической отмены изменений, внесенных действием скрипта. Если необходимо отменить результаты действия скрипта, нужно определить, какие изменения были внесены, а затем отменить их вручную. Также можно воспользоваться действием сценария, которое отменит эти изменения.


### <a name="script-action-in-the-cluster-creation-process"></a>Действие сценария в процессе создания кластера

Действия скриптов, используемые в процессе создания кластера и выполняющиеся в существующем кластере, несколько отличаются:

* Скрипт **сохраняется автоматически**.
* **Ошибка** в скрипте может вызвать сбой создания кластера.

На следующей схеме показано, когда выполняется действие сценария в процессе создания.

![Настройка кластера HDInsight и этапы создания кластера][img-hdi-cluster-states]

Скрипт выполняется во время настройки HDInsight. На этом этапе скрипт выполняется параллельно на всех указанных узлах кластера с правами привилегированного пользователя на узлах.

> [!NOTE]
> Так как при выполнении скрипта у вас есть права привилегированного пользователя на узлах кластера, вы можете выполнять такие операции, как остановка и запуск служб, включая службы, связанные с Hadoop. Если вы останавливаете службы, перед завершением работы сценария необходимо запустить службу Ambari и другие службы, связанные с Hadoop. Эти службы нужны для определения работоспособности и состояния кластера при его создании.


При создании кластера можно указать несколько действий скриптов. Они будут вызываться в том порядке, в котором были указаны.

> [!IMPORTANT]
> Действия сценария должны быть завершены в течение 60 минут. В противном случае возникнет ошибка времени ожидания. В процессе подготовки кластера скрипт запускается одновременно с другими процессами установки и настройки. Конкуренция за ресурсы, такие как ЦП или пропускная способность сети, может привести к затягиванию выполнения сценария по сравнению со временем его выполнения в среде разработки.
>
> Чтобы свести время выполнения сценария к минимуму, избегайте таких задач, как скачивание и компиляция приложений из исходного кода. Вместо этого выполните предварительную компиляцию приложения и сохраните двоичный файл в службе хранилища Azure, чтобы его можно быстро скачать в кластер.


### <a name="script-action-on-a-running-cluster"></a>Действие скрипта в работающем кластере

В отличие от действий скриптов, используемых во время создания кластера, ошибка в выполняющемся скрипте в работающем кластере не приводит автоматически к сбою в работе кластера. После завершения скрипта кластер должен вернуться в рабочее состояние.

> [!IMPORTANT]
> Это означает, что работающий кластер неуязвим для скриптов, которые могут навредить. К примеру, скрипт может удалить необходимые для работы кластера файлы или изменить конфигурацию, вызвав сбой служб, и т. д.
>
> Действия скриптов выполняются с использованием привилегий корневой учетной записи, поэтому вы должны понимать, что делает скрипт, прежде чем применять его в кластере.

Когда сценарий применяется в кластере, состояние кластера изменяется с **Работает** на **Принято**, затем на **Конфигурация HDInsight** и снова на **Работает**, если выполнение сценария завершается успешно. Так как состояние скрипта регистрируется в журнале действий скриптов, вы можете определить успешность его выполнения. Например, командлет PowerShell `Get-AzureRmHDInsightScriptActionHistory` можно использовать для просмотра состояния сценария. Он возвращает следующие сведения:

    ScriptExecutionId : 635918532516474303
    StartTime         : 2/23/2016 7:40:55 PM
    EndTime           : 2/23/2016 7:41:05 PM
    Status            : Succeeded

> [!NOTE]
> Если вы изменили пароль пользователя (администратора) кластера после создания кластера, это может привести к сбою при выполнении действий сценария для этого кластера. Если у вас есть сохраненные действия сценария, нацеленные на рабочие узлы, они может завершиться ошибкой при добавлении узлов в кластер в ходе изменения размера.

## <a name="example-script-action-scripts"></a>Пример действий сценария

Действия сценария можно использовать с портала Azure, из Azure PowerShell, Azure CLI или пакета SDK HDInsight для .NET. В HDInsight доступны скрипты для установки следующих компонентов в кластерах HDInsight.

| Имя | Скрипт |
| --- | --- |
| **Добавление учетной записи хранения Azure** |https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh. Ознакомьтесь со статьей [Добавление дополнительных учетных записей хранения Azure в HDInsight](hdinsight-hadoop-add-storage.md). |
| **Установка Hue** |https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh. См. статью [Установка и использование Hue на кластерах HDInsight Hadoop](hdinsight-hadoop-hue-linux.md). |
| **Установка R** |https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh. См. статью [Установка и использование R на кластерах HDInsight Hadoop](hdinsight-hadoop-r-scripts-linux.md). |
| **Установка Solr** |https://hdiconfigactions.blob.core.windows.net/linuxsolrconfigactionv01/solr-installer-v01.sh. Ознакомьтесь со статьей [Установка и использование Solr в кластерах HDInsight](hdinsight-hadoop-solr-install-linux.md). |
| **Установка Giraph** |https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh. Ознакомьтесь со статьей [Установка и использование Giraph в кластерах HDInsight](hdinsight-hadoop-giraph-install-linux.md). |
| **Предварительная загрузка библиотек Hive** |https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh. Ознакомьтесь со статьей [Добавление библиотек Hive в кластеры HDInsight](hdinsight-hadoop-add-hive-libraries.md) |

## <a name="use-a-script-action-during-cluster-creation"></a>Использование действия скрипта при создании кластера

В этом разделе описывается, как можно использовать действия сценария при создании кластера HDInsight: с помощью портала Azure, шаблона Azure Resource Manager, командлетов PowerShell и пакета SDK для .NET.

### <a name="use-a-script-action-during-cluster-creation-from-the-azure-portal"></a>Использование действия сценария при создании кластера с портала Azure

1. Начните создание кластера, как описано в разделе [Создание кластеров Hadoop под управлением Windows в HDInsight](hdinsight-provision-clusters.md).
2. В разделе **Необязательная настройка** в колонке **Действия скрипта** щелкните **Добавить действие скрипта**, чтобы указать сведения об этом действии скрипта.

    ![Использование действия сценария для настройки кластера](./media/hdinsight-hadoop-customize-cluster-linux/HDI.CreateCluster.8.png)

    | Свойство | Значение |
    | --- | --- |
    | Имя |Укажите имя для действия сценария. |
    | URI-адрес сценария |Укажите URI для сценария, который вызывается для настройки кластера. |
    | Головной/рабочий |Укажите узлы (**головной**, **рабочий** или **ZooKeeper**), на которых выполняется скрипт настройки. |
    | Параметры |Укажите параметры, если они требуются для сценария. |

    Нажмите клавишу ВВОД, чтобы добавить несколько действий сценария для установки нескольких компонентов в кластере.

3. Нажмите кнопку **Выбрать** , чтобы сохранить конфигурацию и продолжить создание кластера.

### <a name="use-a-script-action-from-azure-resource-manager-templates"></a>Использование действия сценария на основе шаблонов диспетчера ресурсов Azure
В этом разделе для создания кластера HDInsight используются шаблоны Azure Resource Manager, а для установки пользовательских компонентов в кластере (в данном примере — R) — действие сценария. Ниже приведен пример шаблона для создания кластера с помощью действия сценария.

> [!NOTE]
> В этом разделе демонстрируется, как создать кластер с помощью действия сценария. Пример создания кластера из шаблона с помощью приложения HDInsight доступен в разделе [Установка пользовательских приложений HDInsight](hdinsight-apps-install-custom-applications.md).

#### <a name="before-you-begin"></a>Перед началом работы

* Сведения о настройке рабочей станции для запуска командлетов HDInsight PowerShell см. в статье [Установка и настройка Azure PowerShell](/powershell/azureps-cmdlets-docs).
* Инструкции по созданию шаблонов см. в статье [Authoring Azure Resource Manager templates](../azure-resource-manager/resource-group-authoring-templates.md) (Создание шаблонов Azure Resource Manager).
* Сведения об использовании Azure PowerShell с Resource Manager см. в [этой статье](../azure-resource-manager/powershell-azure-resource-manager.md).

#### <a name="create-clusters-using-script-action"></a>Создание кластеров с помощью действия скрипта

1. Скопируйте следующий шаблон в папку на своем компьютере. Он устанавливает Giraph на головной и рабочие узлы в кластере. Чтобы убедиться, что шаблон JSON является допустимым, Вставьте содержимое шаблона в [JSONLint](http://jsonlint.com/) — интерактивное средство проверки JSON.

            {
            "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
            "contentVersion": "1.0.0.0",
            "parameters": {
                "clusterLocation": {
                    "type": "string",
                    "defaultValue": "West US",
                    "allowedValues": [ "West US" ]
                },
                "clusterName": {
                    "type": "string"
                },
                "clusterUserName": {
                    "type": "string",
                    "defaultValue": "admin"
                },
                "clusterUserPassword": {
                    "type": "securestring"
                },
                "sshUserName": {
                    "type": "string",
                    "defaultValue": "username"
                },
                "sshPassword": {
                    "type": "securestring"
                },
                "clusterStorageAccountName": {
                    "type": "string"
                },
                "clusterStorageAccountResourceGroup": {
                    "type": "string"
                },
                "clusterStorageType": {
                    "type": "string",
                    "defaultValue": "Standard_LRS",
                    "allowedValues": [
                        "Standard_LRS",
                        "Standard_GRS",
                        "Standard_ZRS"
                    ]
                },
                "clusterStorageAccountContainer": {
                    "type": "string"
                },
                "clusterHeadNodeCount": {
                    "type": "int",
                    "defaultValue": 1
                },
                "clusterWorkerNodeCount": {
                    "type": "int",
                    "defaultValue": 2
                }
            },
            "variables": {
            },
            "resources": [
                {
                    "name": "[parameters('clusterStorageAccountName')]",
                    "type": "Microsoft.Storage/storageAccounts",
                    "location": "[parameters('clusterLocation')]",
                    "apiVersion": "2015-05-01-preview",
                    "dependsOn": [ ],
                    "tags": { },
                    "properties": {
                        "accountType": "[parameters('clusterStorageType')]"
                    }
                },
                {
                    "name": "[parameters('clusterName')]",
                    "type": "Microsoft.HDInsight/clusters",
                    "location": "[parameters('clusterLocation')]",
                    "apiVersion": "2015-03-01-preview",
                    "dependsOn": [
                        "[concat('Microsoft.Storage/storageAccounts/', parameters('clusterStorageAccountName'))]"
                    ],
                    "tags": { },
                    "properties": {
                        "clusterVersion": "3.2",
                        "osType": "Linux",
                        "clusterDefinition": {
                            "kind": "hadoop",
                            "configurations": {
                                "gateway": {
                                    "restAuthCredential.isEnabled": true,
                                    "restAuthCredential.username": "[parameters('clusterUserName')]",
                                    "restAuthCredential.password": "[parameters('clusterUserPassword')]"
                                }
                            }
                        },
                        "storageProfile": {
                            "storageaccounts": [
                                {
                                    "name": "[concat(parameters('clusterStorageAccountName'),'.blob.core.windows.net')]",
                                    "isDefault": true,
                                    "container": "[parameters('clusterStorageAccountContainer')]",
                                    "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('clusterStorageAccountName')), '2015-05-01-preview').key1]"
                                }
                            ]
                        },
                        "computeProfile": {
                            "roles": [
                                {
                                    "name": "headnode",
                                    "targetInstanceCount": "[parameters('clusterHeadNodeCount')]",
                                    "hardwareProfile": {
                                        "vmSize": "Large"
                                    },
                                    "osProfile": {
                                        "linuxOperatingSystemProfile": {
                                            "username": "[parameters('sshUserName')]",
                                            "password": "[parameters('sshPassword')]"
                                        }
                                    },
                                    "scriptActions": [
                                        {
                                            "name": "installGiraph",
                                            "uri": "https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh",
                                            "parameters": ""
                                        }
                                    ]
                                },
                                {
                                    "name": "workernode",
                                    "targetInstanceCount": "[parameters('clusterWorkerNodeCount')]",
                                    "hardwareProfile": {
                                        "vmSize": "Large"
                                    },
                                    "osProfile": {
                                        "linuxOperatingSystemProfile": {
                                            "username": "[parameters('sshUserName')]",
                                            "password": "[parameters('sshPassword')]"
                                        }
                                    },
                                    "scriptActions": [
                                        {
                                            "name": "installR",
                                            "uri": "https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh",
                                            "parameters": ""
                                        }
                                    ]
                                }
                            ]
                        }
                    }
                }
            ],
            "outputs": {
                "cluster":{
                    "type" : "object",
                    "value" : "[reference(resourceId('Microsoft.HDInsight/clusters',parameters('clusterName')))]"
                }
            }
        }
2. Запустите Azure PowerShell и войдите в учетную запись Azure. После предоставления учетных данных команда возвращает информацию о вашей учетной записи.

        Add-AzureRmAccount

        Id                             Type       ...
        --                             ----
        someone@example.com            User       ...
3. Если у вас несколько подписок, укажите подписку, которую вы хотите использовать для развертывания.

        Select-AzureRmSubscription -SubscriptionID <YourSubscriptionId>

    > [!NOTE]
    > `Get-AzureRmSubscription` можно использовать для получения списка всех подписок, связанных с вашей учетной записью, в котором указан идентификатор каждой подписки.

4. Создайте группу ресурсов, если у вас нет существующей группы ресурсов. Введите имя группы ресурсов и расположение, необходимое для решения. Появится сводка по новой группе ресурсов.

        New-AzureRmResourceGroup -Name myresourcegroup -Location "West US"

        ResourceGroupName : myresourcegroup
        Location          : westus
        ProvisioningState : Succeeded
        Tags              :
        Permissions       :
                            Actions  NotActions
                            =======  ==========
                            *
        ResourceId        : /subscriptions/######/resourceGroups/ExampleResourceGroup

5. Чтобы создать новое развертывание для группы ресурсов, выполните командлет **New-AzureRmResourceGroupDeployment** и укажите необходимые параметры. Параметры включают в себя имя развертывания, имя группы ресурсов, а также путь к созданному шаблону или его URL-адрес. Если для вашего шаблона требуются какие-либо параметры, необходимо также передать их. В данном случае для действия сценария по установке R в кластере никакие параметры не требуются.

        New-AzureRmResourceGroupDeployment -Name mydeployment -ResourceGroupName myresourcegroup -TemplateFile <PathOrLinkToTemplate>

    Появится запрос на задание значений параметров, определенных в шаблоне.

1. После развертывания группы ресурсов вы увидите сводку по развертыванию.

          DeploymentName    : mydeployment
          ResourceGroupName : myresourcegroup
          ProvisioningState : Succeeded
          Timestamp         : 8/17/2015 7:00:27 PM
          Mode              : Incremental
          ...

2. Если развертывание завершается сбоем, вы можете использовать следующие командлеты для получения информации об ошибках.

        Get-AzureRmResourceGroupDeployment -ResourceGroupName myresourcegroup -ProvisioningState Failed

### <a name="use-a-script-action-during-cluster-creation-from-azure-powershell"></a>Использование действия скрипта при создании кластера с помощью Azure PowerShell

В этом разделе мы используем командлет [Add-AzureRmHDInsightScriptAction](https://msdn.microsoft.com/library/mt603527.aspx), чтобы вызвать скрипты с помощью действия скрипта для настройки кластера. Прежде чем продолжить, убедитесь, что вы установили и настроили Azure PowerShell. Сведения о настройке рабочей станции для запуска командлетов HDInsight PowerShell см. в статье [Установка и настройка Azure PowerShell](/powershell/azureps-cmdlets-docs).

Выполните следующие действия:

1. Откройте консоль Azure PowerShell и с помощью следующей команды войдите в подписку Azure. Затем объявите несколько переменных PowerShell.

        # LOGIN TO ZURE
        Login-AzureRmAccount

        # PROVIDE VALUES FOR THESE VARIABLES
        $subscriptionId = "<SubscriptionId>"        # ID of the Azure subscription
        $clusterName = "<HDInsightClusterName>"            # HDInsight cluster name
        $storageAccountName = "<StorageAccountName>"    # Azure storage account that hosts the default container
        $storageAccountKey = "<StorageAccountKey>"      # Key for the storage account
        $containerName = $clusterName
        $location = "<MicrosoftDataCenter>"                # Location of the HDInsight cluster. It must be in the same data center as the storage account.
        $clusterNodes = <ClusterSizeInNumbers>            # The number of nodes in the HDInsight cluster.
        $resourceGroupName = "<ResourceGroupName>"      # The resource group that the HDInsight cluster is created in

2. Укажите значения конфигурации (например, узлы в кластере) и хранилище для использования по умолчанию.

        # SPECIFY THE CONFIGURATION OPTIONS
        Select-AzureRmSubscription -SubscriptionId $subscriptionId
        $config = New-AzureRmHDInsightClusterConfig
        $config.DefaultStorageAccountName="$storageAccountName.blob.core.windows.net"
        $config.DefaultStorageAccountKey=$storageAccountKey

3. Используйте командлет **Add-AzureRmHDInsightScriptAction** для вызова сценария. В следующем примере используется сценарий, устанавливающий Giraph в кластере.

        # INVOKE THE SCRIPT USING THE SCRIPT ACTION FOR HEADNODE AND WORKERNODE
        $config = Add-AzureRmHDInsightScriptAction -Config $config -Name "Install Giraph"  -NodeType HeadNode -Uri https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh
        $config = Add-AzureRmHDInsightScriptAction -Config $config -Name "Install Giraph"  -NodeType WorkerNode -Uri https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh

    Командлет **Add-AzureRmHDInsightScriptAction** принимает следующие параметры.

    | Параметр | Определение |
    | --- | --- |
    | Настройка |Объект конфигурации, к которому добавляются сведения о действии скрипта. |
    | Имя |Имя действия скрипта |
    | NodeType |Указывает узел, на котором выполняется сценарий настройки. Допустимые значения: **HeadNode** (для установки на головном узле), **WorkerNode** (для установки на всех узлах данных) или **Zookeeper** (для установки на узле Zookeeper). |
    | Параметры |Параметры, необходимые для скрипта. |
    | URI |Задает универсальный код ресурса для выполняемого сценария. |

4. Задание пользователя admin и HTTPS для кластера:

        $httpCreds = get-credential

    При появлении запроса введите имя "admin" и укажите пароль.

5. Задайте учетные данные SSH:

        $sshCreds = get-credential

    При появлении запроса введите имя пользователя и пароль SSH. Если учетную запись SSH нужно защитить с помощью сертификата вместо пароля, возьмите пустой пароль и присвойте `$sshPublicKey` содержимое открытого ключа сертификата, который следует использовать. Например:

        $sshPublicKey = Get-Content .\path\to\public.key -Raw

6. Наконец, создайте кластер:

        New-AzureRmHDInsightCluster -config $config -clustername $clusterName -DefaultStorageContainer $containerName -Location $location -ResourceGroupName $resourceGroupName -ClusterSizeInNodes $clusterNodes -HttpCredential $httpCreds -SshCredential $sshCreds -OSType Linux

    При использовании открытого ключа для защиты учетной записи SSH также необходимо указать `-SshPublicKey $sshPublicKey` в качестве параметра.

Создание кластера может занять несколько минут.

### <a name="use-a-script-action-during-cluster-creation-from-the-hdinsight-net-sdk"></a>Использование действия скрипта при создании кластера с помощью пакета SDK HDInsight для .NET

Пакет SDK HDInsight для .NET предоставляет клиентские библиотеки, которые упрощают работу с кластерами HDInsight из приложения .NET. Пример кода см. в статье [Создание кластеров под управлением Linux в HDInsight с помощью пакета SDK для .NET](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md#use-script-action).

## <a name="apply-a-script-action-to-a-running-cluster"></a>Применение действия скрипта в работающем кластере

В этом разделе описывается, как применять действия сценариев в работающем кластере HDInsight: с помощью портала Azure, командлетов PowerShell, кроссплатформенного Azure CLI и пакета SDK для .NET. Действие сохраняемого сценария, используемое в данном разделе, добавляет существующую учетную запись хранения Azure в работающий кластер. Вы также можете воспользоваться другими действиями сценария из раздела [Пример действий сценария](#example-script-action-scripts).

### <a name="apply-a-script-action-to-a-running-cluster-from-the-azure-portal"></a>Применение действия сценария в работающем кластере с портала Azure

1. На [портале Azure](https://portal.azure.com)выберите свой кластер HDInsight.

2. В колонке кластера HDInsight выберите элемент **Действия скрипта**.

    ![Элемент "Действия сценариев"](./media/hdinsight-hadoop-customize-cluster-linux/scriptactionstile.png)

   > [!NOTE]
   > Кроме того, можно щелкнуть **Все параметры**, а затем выбрать **Действия скрипта** в колонке "Параметры".

3. В верхней части колонки "Действия скрипта" выберите **Отправить новое**.

    ![Значок "Отправить новое"](./media/hdinsight-hadoop-customize-cluster-linux/newscriptaction.png)

4. В колонке "Добавление действия скрипта" введите следующие сведения.

   * **Имя** — понятное имя для этого действия скрипта. В этом примере — `Add Storage account`.

   * **Универсальный код ресурса скрипта** — универсальный код ресурса (URI) для скрипта. В этом примере — `https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh`

   * **Головной**, **Рабочий** и **Zookeeper** — установите флажки на узлах, к которым должен применяться этот скрипт. В этом примере отмечены узлы "Головной", "Рабочий" и "Zookeeper".

   * **Параметры** — если скрипт принимает параметры, укажите их здесь. В этом примере введите имя учетной записи хранения и ключ учетной записи хранения:

       ![действие сохраняемого сценария hdinsight добавляет учетную запись хранения в работающие кластеры](./media/hdinsight-hadoop-customize-cluster-linux/hdinsight-persisted-script-action-add-storage-account.png)

       На снимке экрана `contosodata` является существующей учетной записью хранения Azure, а вторая строка — ключом учетной записи хранения.

   * **Сохраненное** — установите флажок для этой записи, если необходимо сохранить сценарий, чтобы он применялся на новых рабочих узлах при увеличении масштаба кластера.

5. Наконец, нажмите кнопку **Создать**, чтобы применить скрипт к кластеру.

### <a name="apply-a-script-action-to-a-running-cluster-from-azure-powershell"></a>Применение действия скрипта в работающем кластере с помощью Azure PowerShell

Прежде чем продолжить, убедитесь, что вы установили и настроили Azure PowerShell. Сведения о настройке рабочей станции для запуска командлетов HDInsight PowerShell см. в статье [Установка и настройка Azure PowerShell](/powershell/azureps-cmdlets-docs).

1. Откройте консоль Azure PowerShell и с помощью следующей команды войдите в подписку Azure. Затем объявите несколько переменных PowerShell.

        # LOGIN TO ZURE
        Login-AzureRmAccount

        # PROVIDE VALUES FOR THESE VARIABLES
        $clusterName = "<HDInsightClusterName>"            # HDInsight cluster name
        $saName = "<ScriptActionName>"                  # Name of the script action
        $saURI = "<URI to the script>"                  # The URI where the script is located
        $nodeTypes = "headnode", "workernode"

   > [!NOTE]
   > Если используется кластер HDInsight уровня "Премиум", можно использовать значение nodetype `"edgenode"`, чтобы выполнить сценарий на граничном узле.

2. Чтобы применить скрипт в кластере, используйте следующую команду:

        Submit-AzureRmHDInsightScriptAction -ClusterName $clusterName -Name $saName -Uri $saURI -NodeTypes $nodeTypes -PersistOnSuccess

    После завершения задания вы должны получить следующую информацию:

        OperationState  : Succeeded
        ErrorMessage    :
        Name            : Giraph
        Uri             : https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh
        Parameters      :
        NodeTypes       : {HeadNode, WorkerNode}

### <a name="apply-a-script-action-to-a-running-cluster-from-the-azure-cli"></a>Применение действия сценария в работающем кластере с помощью Azure CLI

Прежде чем продолжить, убедитесь, что вы установили и настроили Azure CLI. Дополнительные сведения см. в статье [Установка Azure CLI](../cli-install-nodejs.md).

[!INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)]

1. Откройте сеанс работы с оболочкой, терминал или командную строку для системы и используйте указанную далее команду, чтобы переключиться в режим Azure Resource Manager.

        azure config mode arm

2. Выполните приведенную далее команду для аутентификации в подписке Azure.

        azure login

3. Выполните приведенную далее команду, чтобы применить действие сценария к работающему кластеру.

        azure hdinsight script-action create <clustername> -g <resourcegroupname> -n <scriptname> -u <scriptURI> -t <nodetypes>

    Если параметры для этой команды не заданы, вам будет предложено сделать это. Если сценарий, указанный с помощью `-u`, принимает параметры, их можно задать с помощью параметра `-p`.

    Допустимые значения для параметра **nodetype**: **headnode**, **workernode** и **zookeeper**. Если сценарий должен быть применен к нескольким типам узлов, укажите типы, разделив их точкой с запятой. Например, `-n headnode;workernode`.

    Чтобы сохранить сценарий, добавьте `--persistOnSuccess`. Чтобы сохранить сценарий позднее, используйте `azure hdinsight script-action persisted set`.

    По завершении задания выходные данные будут иметь примерно следующий вид.

        info:    Executing command hdinsight script-action create
        + Executing Script Action on HDInsight cluster
        data:    Operation Info
        data:    ---------------
        data:    Operation status:
        data:    Operation ID:  b707b10e-e633-45c0-baa9-8aed3d348c13
        info:    hdinsight script-action create command OK

### <a name="apply-a-script-action-to-a-running-cluster-using-rest-api"></a>Применение действия сценария в работающем кластере с помощью REST API

См. статью [Run Script Actions on a running cluster](https://msdn.microsoft.com/library/azure/mt668441.aspx) (Выполнение действий скрипта в работающем кластере).

### <a name="apply-a-script-action-to-a-running-cluster-from-the-hdinsight-net-sdk"></a>Применение действия скрипта в работающем кластере с помощью пакета SDK HDInsight для .NET

Пример использования пакета SDK для .NET для применения скриптов к кластеру см. на странице [https://github.com/Azure-Samples/hdinsight-dotnet-script-action](https://github.com/Azure-Samples/hdinsight-dotnet-script-action).

## <a name="view-history-promote-and-demote-script-actions"></a>Просмотр журнала и изменения типа действий скриптов

### <a name="using-the-azure-portal"></a>Использование портала Azure

1. На [портале Azure](https://portal.azure.com)выберите свой кластер HDInsight.

2. В колонке кластера HDInsight выберите **Параметры**.

    ![Значок "Параметры"](./media/hdinsight-hadoop-customize-cluster-linux/settingsicon.png)

3. В колонке "Параметры" выберите **Действия скрипта**.

    ![Ссылка на действия скриптов](./media/hdinsight-hadoop-customize-cluster-linux/settings.png)

4. В колонке "Действия скриптов" отображается список сохраняемых скриптов, а также журнал скриптов, применяемых в кластере. Как показано на снимке экрана ниже, в этом кластере был выполнен скрипт Solr, но действия скрипта не были сохранены.

    ![Колонка "Действия скриптов"](./media/hdinsight-hadoop-customize-cluster-linux/scriptactionhistory.png)

5. При выборе сценария в журнале отображается колонка свойств для него. В верхней части колонки можно повторно запустить скрипт или изменить его тип.

    ![Колонка свойств действий скриптов](./media/hdinsight-hadoop-customize-cluster-linux/scriptactionproperties.png)

6. Можно также использовать элемент **…** справа от записей в колонке "Действия скрипта" для выполнения таких действий, как повторное выполнение, сохранение или удаление (для сохраняемых действий).

    ![Использование действий скриптов](./media/hdinsight-hadoop-customize-cluster-linux/deletepromoted.png)

### <a name="using-azure-powershell"></a>Использование Azure PowerShell

| Командлет | Действие |
| --- | --- |
| Get-AzureRmHDInsightPersistedScriptAction |Получение сведений о действиях сохраняемого скрипта |
| Get-AzureRmHDInsightScriptActionHistory |Получение журнала действий скриптов, применяемых в кластере, или сведений о конкретном скрипте |
| Set-AzureRmHDInsightPersistedScriptAction |Изменение типа специального скрипта на сохраняемый скрипт |
| Remove-AzureRmHDInsightPersistedScriptAction |Изменение типа сохраняемого скрипта на специальный скрипт |

> [!IMPORTANT]
> Использование `Remove-AzureRmHDInsightPersistedScriptAction` не отменяет действия, выполняемые сценарием, — удаляется только флаг сохраняемости, в результате чего сценарий не будет выполняться на новых рабочих узлах, добавляемых в кластер.

В приведенном ниже примере скрипта показано использование командлетов для изменения типа скрипта.

    # Get a history of scripts
    Get-AzureRmHDInsightScriptActionHistory -ClusterName mycluster

    # From the list, we want to get information on a specific script
    Get-AzureRmHDInsightScriptActionHistory -ClusterName mycluster -ScriptExecutionId 635920937765978529

    # Promote this to a persisted script
    # Note: the script must have a unique name to be promoted
    # if the name is not unique, you receive an error
    Set-AzureRmHDInsightPersistedScriptAction -ClusterName mycluster -ScriptExecutionId 635920937765978529

    # Demote the script back to ad hoc
    # Note that demotion uses the unique script name instead of
    # execution ID.
    Remove-AzureRmHDInsightPersistedScriptAction -ClusterName mycluster -Name "Install Giraph"

### <a name="using-the-azure-cli"></a>Использование Azure CLI

| Командлет | Действие |
| --- | --- |
| `azure hdinsight script-action persisted list <clustername>` |Получение списка сохраняемых действий сценария |
| `azure hdinsight script-action persisted show <clustername> <scriptname>` |Получение сведений о конкретном сохраняемом действии сценария |
| `azure hdinsight script-action history list <clustername>` |Получение журнала действий сценария, применяемых в кластере |
| `azure hdinsight script-action history show <clustername> <scriptname>` |Получение сведений о конкретном действии сценария |
| `azure hdinsight script action persisted set <clustername> <scriptexecutionid>` |Изменение типа специального скрипта на сохраняемый скрипт |
| `azure hdinsight script-action persisted delete <clustername> <scriptname>` |Изменение типа сохраняемого скрипта на специальный скрипт |

> [!IMPORTANT]
> Использование `azure hdinsight script-action persisted delete` не отменяет действия, выполняемые сценарием, — удаляется только флаг сохраняемости, в результате чего сценарий не будет выполняться на новых рабочих узлах, добавляемых в кластер.

### <a name="using-the-hdinsight-net-sdk"></a>Использование пакета SDK HDInsight .NET

Пример использования пакета SDK для .NET для получения журнала скриптов из кластера см. на странице [https://github.com/Azure-Samples/hdinsight-dotnet-script-action](https://github.com/Azure-Samples/hdinsight-dotnet-script-action).

> [!NOTE]
> В этом примере также показано, как установить приложение HDInsight с помощью пакета SDK для .NET.

## <a name="support-for-open-source-software-used-on-hdinsight-clusters"></a>Поддержка программного обеспечения с открытым исходным кодом, используемого в кластере HDInsight

Служба Microsoft Azure HDInsight — это гибкая платформа, которая позволяет создавать приложения для работы с данными большого размера в облаке, используя сформированную вокруг Hadoop экосистему технологий с открытым исходным кодом. Microsoft Azure предоставляет общий уровень поддержки для технологий с открытым исходным кодом, как описано в статье **Объем поддержки** на [веб-сайте часто задаваемых вопросов о поддержке Azure](https://azure.microsoft.com/support/faq/). Служба HDInsight предоставляет дополнительный уровень поддержки для некоторых описанных ниже компонентов.

В службе HDInsight доступно два типа компонентов с открытым исходным кодом.

* **Встроенные компоненты.** Эти компоненты предварительно установлены в кластерах HDInsight и предоставляют его базовые функциональные возможности. Например, к этой категории относится диспетчер ресурсов YARN, язык запросов Hive (HiveQL) и библиотека Mahout. Полный список компонентов кластера доступен в статье [Новые возможности версий кластеров Hadoop, предоставляемых HDInsight](hdinsight-component-versioning.md).
* **Настраиваемые компоненты.** Как пользователь кластера вы можете установить или использовать в рабочей нагрузке любой компонент, полученный из сообщества или созданный самостоятельно.

> [!WARNING]
> Компоненты, предоставляемые вместе с кластером HDInsight, поддерживаются в полном объеме. Служба технической поддержки Майкрософт помогает выявить и решить проблемы, связанные с этими компонентами.
>
> Настраиваемые компоненты получают ограниченную коммерчески оправданную поддержку, способствующую дальнейшей диагностике проблемы. В результате проблема может быть устранена, либо вас могут попросить воспользоваться доступными каналами по технологиям с открытым исходным кодом, чтобы связаться с экспертами в данной области. Можно использовать ряд сайтов сообществ, например [форум MSDN по HDInsight](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight) или [http://stackoverflow.com](http://stackoverflow.com). Кроме того, для проектов Apache есть соответствующие сайты, например [Hadoop](http://hadoop.apache.org/) на сайте [http://apache.org](http://apache.org).

Служба HDInsight позволяет использовать настраиваемые компоненты несколькими разными способами. Уровень поддержки не зависит от того, как компонент используется или устанавливается в кластере. Ниже приведен список самых распространенных способов использования настраиваемых компонентов в кластерах HDInsight.

1. Отправка задания. В кластер можно отправлять задания Hadoop или другие типы заданий, выполняющие или использующие настраиваемые компоненты.

2. Настройка кластера. Во время создания кластера можно указать дополнительные параметры и настраиваемые компоненты, устанавливаемые на узлах кластера.

3. Примеры. Для популярных настраиваемых компонентов корпорация Майкрософт и другие компании могут предоставлять примеры использования таких компонентов в кластерах HDInsight. Эти примеры представляются без поддержки.

## <a name="troubleshooting"></a>Устранение неполадок

Вы можете использовать веб-интерфейс Ambari для просмотра сведений, регистрируемых действиями скриптов. Если при создании кластера использовался скрипт, из-за ошибки в котором произошел сбой создания, вы можете просмотреть журналы в учетной записи хранения по умолчанию, связанной с кластером. Этот раздел содержит сведения о том, как получить журналы обоими этими способами.

### <a name="using-the-ambari-web-ui"></a>Использование веб-интерфейса Ambari

1. В браузере перейдите на сайт https://CLUSTERNAME.azurehdinsight.net. Параметр CLUSTERNAME нужно заменить именем кластера HDInsight.

    При появлении запроса введите имя (admin) и пароль учетной записи администратора кластера. Возможно, вам потребуется повторно ввести учетные данные администратора в веб-форме.

2. В панели вверху страницы выберите запись **ops**. Появится список текущих и предыдущих операций, выполняемых в кластере с помощью Ambari.

    ![Веб-панель Ambari с выбранной записью ops](./media/hdinsight-hadoop-customize-cluster-linux/ambari-nav.png)

3. Найдите записи, для которых в столбце **Операции** указано **run\_customscriptaction**. Такие записи создаются при выполнении действий сценария.

    ![Снимок экрана операций](./media/hdinsight-hadoop-customize-cluster-linux/ambariscriptaction.png)

    Выберите эту запись run\customscriptaction и перейдите по ссылкам для просмотра выходных данных STDOUT и STDERR. Эти выходные данные формируются при запуске сценария и могут содержать полезные сведения.

### <a name="access-logs-from-the-default-storage-account"></a>Доступ к журналам из учетной записи хранения по умолчанию

В случае сбоя создания кластера из-за ошибки в действии сценария журналы действий сценария по-прежнему доступны в связанной с кластером учетной записи хранения по умолчанию.

* Журналы хранилища находятся в `\STORAGE_ACOCUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\CLUSTER_NAME\DATE`.

    ![Снимок экрана операций](./media/hdinsight-hadoop-customize-cluster-linux/script_action_logs_in_storage.png)

    В этом расположении журналы упорядочены по узлам headnode, workernode и zookeeper. Ниже приведены некоторые примеры.

    * **Головной узел:** - `<uniqueidentifier>AmbariDb-hn0-<generated_value>.cloudapp.net`

    * **Рабочий узел** - `<uniqueidentifier>AmbariDb-wn0-<generated_value>.cloudapp.net`

    * **Узел Zookeeper** - `<uniqueidentifier>AmbariDb-zk0-<generated_value>.cloudapp.net`

* Все stdout и stderr соответствующего узла передаются в учетную запись хранилища. Для каждого действия скрипта создается файл **output-\*.txt** и файл **errors-\*.txt**. Выходной TXT-файл содержит сведения об универсальном коде ресурса (URI) сценария, который был запущен на узле. Например:

        'Start downloading script locally: ', u'https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh'

* Возможно, повторно создан кластер действия сценария с тем же именем. В этом случае соответствующие журналы можно отличить по имени папки DATE. Например, структура папок для кластера (mycluster), созданного в другие дни, будет иметь следующий вид:

    * `\STORAGE_ACOCUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-04`

    * `\STORAGE_ACOCUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-05`

* При создании кластера действие сценария с таким же именем в тот же день можно использовать уникальный префикс для идентификации соответствующих файлов журнала.

* При создании кластера в конце дня файлы журналов могут охватить два дня. В таких случаях для одного кластера будут отображаться две разные папки даты.

* Передача файлов журнала в контейнер по умолчанию может занять до 5 минут, особенно для больших кластеров. Таким образом, если вы хотите получить доступ к журналам, не следует немедленно удалять кластер при сбое действия сценария.

### <a name="ambari-watchdog"></a>Модуль наблюдения Ambari

> [!WARNING]
> Не изменяйте пароль модуля наблюдения Ambari (hdinsightwatchdog) в кластере HDInsight под управлением Linux. Это не позволит выполнять новые действия сценария в кластере HDInsight.

### <a name="cannot-import-name-blobservice"></a>Не удается импортировать BlobService имени

__Симптомы.__ Происходит сбой действия сценария и при просмотре операции в Ambari выводится следующее сообщение об ошибке:

```
Traceback (most recent call list):
  File "/var/lib/ambari-agent/cache/custom_actions/scripts/run_customscriptaction.py", line 21, in <module>
    from azure.storage.blob import BlobService
ImportError: cannot import name BlobService
```

__Причина.__ Эта ошибка возникает при обновлении клиента Python службы хранилища Azure, который входит в состав кластера HDInsight. HDInsight требуется клиент службы хранилища Azure версии 0.20.0.

__Способ устранения.__ Чтобы устранить эту ошибку, подключитесь к каждому узлу кластера вручную, используя `ssh`, и выполните следующую команду, чтобы переустановить клиент службы хранилища требуемой версии:

```
sudo pip install azure-storage==0.20.0
```

Дополнительные сведения о подключении к кластеру с помощью SSH см. в следующих документах:

* [Использование SSH с HDInsight (Hadoop) на платформе Windows, Linux, Unix или OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

* [Использование SSH с Hadoop на основе Linux в HDInsight из Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

### <a name="history-doesnt-show-scripts-used-during-cluster-creation"></a>Журнал не отображает скрипты, используемые во время создания кластера

Если кластер был создан до 15 марта 2016 г., в журнале действий скриптов могут отсутствовать соответствующие записи обо всех скриптах, которые использовались во время создания кластера. Но если размер кластера был изменен после 15 марта 2016 г., сценарии, используемые при создании кластера, отображаются в журнале, если они применялись на новых узлах кластера во время операции по изменению размера.

Есть два исключения.

* Если кластер был создан до 1 сентября 2015 г. Это дата выпуска этой функции, поэтому если кластер был создан раньше, действия скриптов не могли использоваться при его создании.

* Если при создании кластера использовалось несколько действий скриптов, для которых использовалось одно имя, или же если для разных скриптов использовалось одно имя и один универсальный код ресурса (URI), но разные параметры. В таких случаях происходит следующая ошибка.

    В таком кластере из-за конфликта имен скриптов в существующих скриптах новые действия скриптов выполняться не будут. Имена скриптов, указанные при создании кластера, должны быть уникальными. Существующие скрипты будут по-прежнему выполняться после изменения размера.

## <a name="next-steps"></a>Дальнейшие действия

Ниже приведены ресурсы с информацией о создании и настройке кластера с помощью сценариев и соответствующими примерами.

* [Разработка скриптов действия сценария для HDInsight](hdinsight-hadoop-script-actions-linux.md)
* [Установка и использование Solr в кластерах HDInsight](hdinsight-hadoop-solr-install-linux.md)
* [Установка и использование Giraph в кластерах HDInsight](hdinsight-hadoop-giraph-install-linux.md)
* [Добавление дополнительных учетных записей хранения Azure в HDInsight](hdinsight-hadoop-add-storage.md)

[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster-linux/HDI-Cluster-state.png "Этапы создания кластера"

