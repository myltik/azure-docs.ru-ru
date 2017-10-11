---
title: "Настройка кластеров HDInsight с помощью действий скрипта — Azure | Документы Майкрософт"
description: "Добавление настраиваемых компонентов в кластеры HDInsight на основе Linux с помощью действий скриптов. Действия скриптов — это скрипты Bash, которые можно использовать для настройки конфигурации кластера или добавления дополнительных служб и служебных программ, таких как Hue, Solr или R."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 48e85f53-87c1-474f-b767-ca772238cc13
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/14/2017
ms.author: larryfr
ms.openlocfilehash: 0c5d00b6cb9f68a1a0e474f81c969eb1b5654c67
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/18/2017
---
# <a name="customize-linux-based-hdinsight-clusters-using-script-action"></a>Настройка кластеров HDInsight под управлением Linux с помощью действия сценария

В кластерах HDInsight есть параметр конфигурации **Действие скрипта** , вызывающий пользовательские скрипты, с помощью которых можно настроить кластер. Эти скрипты используются для установки дополнительных компонентов и изменения параметров конфигурации. Действия скрипта можно использовать во время или после создания кластера.

> [!IMPORTANT]
> Использовать действия скриптов в уже работающем кластере можно только в кластерах HDInsight под управлением Linux.
>
> Linux — единственная операционная система, используемая для работы с HDInsight 3.4 или более поздней версии. Дополнительные сведения см. в разделе [Приближается дата прекращения сопровождения HDI версии 3.3](hdinsight-component-versioning.md#hdinsight-windows-retirement).

Действия сценариев можно опубликовать в Azure Marketplace как приложение HDInsight. В некоторых примерах в этом документе показано, как можно установить приложение HDInsight с помощью команд действия сценария PowerShell и пакета SDK для .NET. Дополнительные сведения о приложениях HDInsight см. в статье [Публикация приложений HDInsight в Azure Marketplace](hdinsight-apps-publish-applications.md).

## <a name="permissions"></a>Разрешения

При использовании присоединенного к домену кластера HDInsight для выполнения действий скрипта необходимо задать два следующих расширения Ambari:

* **AMBARI.RUN\_CUSTOM\_COMMAND.** Это разрешение по умолчанию назначено для роли администратора Ambari.
* **CLUSTER.RUN\_CUSTOM\_COMMAND.** Это разрешение по умолчанию назначено для администратора кластера HDInsight и администратора Ambari.

Дополнительные сведения о работе с разрешениями в присоединенном к домену кластере HDInsight см. в статье [Управление присоединенными к домену кластерами HDInsight (предварительная версия)](hdinsight-domain-joined-manage.md).

## <a name="access-control"></a>управление доступом;

Если вы не администратор или владелец подписки Azure, ваша учетная запись должна иметь доступ к группе ресурсов, содержащий кластер HDInsight, по крайней мере с правами **участника**.

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

    * Большой двоичный объект в **учетной записи хранения Azure**, которая служит основной или дополнительной учетной записью хранения для кластера HDInsight. При создании кластера HDInsight получает доступ к обоим типам учетных записей хранения.

    * Служба совместного доступа к файлам, например хранилище BLOB-объектов Azure, GitHub, OneDrive, Dropbox и т. д.

        Примеры универсальных кодов ресурсов (URI) см. в разделе [Пример действий сценария](#example-script-action-scripts).

        > [!WARNING]
        > HDInsight поддерживает только учетные записи хранения Azure __общего назначения__. Учетные записи __хранилища BLOB-объектов__ сейчас не поддерживаются.

* Действия скрипта могут быть ограничены **запуском только на узлах определенного типа**, например головных или рабочих.

  > [!NOTE]
  > При использовании HDInsight уровня "Премиум" можно указать использование сценария на граничном узле.

* Действия скрипта могут быть **сохраняемыми** или **специализированными**.

    **Сохраняемые** — это скрипты, которые применяются на рабочих узлах, добавленных в кластер после выполнения скрипта, например при масштабировании кластера.

    Сохраняемый скрипт может также применять изменения к узлу другого типа, например головному.

  > [!IMPORTANT]
  > Действия сохраняемых скриптов должны иметь уникальные имена.

    **Специализированные** скрипты не сохраняются. Они не применяются на рабочих узлах, добавленных в кластер после выполнения скрипта. Специализированный скрипт можно впоследствии сделать сохраняемым (и наоборот).

  > [!IMPORTANT]
  > Действия скриптов, используемые при создании кластера, автоматически сохраняются.
  >
  > Скрипты, в которых произошла ошибка, не сохраняются, даже если вы отдельно указали, что они должны быть сохранены.

* Действия скрипта могут принимать **параметры**, используемые в процессе выполнения скрипта.
* Они выполняются с помощью **прав привилегированного пользователя** на узлах кластера.
* Действия скрипта могут выполняться с помощью **портала Azure**, **Azure PowerShell**, **интерфейса командной строки Azure** или **пакета SDK HDInsight для .NET**.

В кластере ведется журнал всех скриптов, которые в нем выполнялись. Это позволяет определить идентификатор скрипта для операции изменения типа.

> [!IMPORTANT]
> Не существует способа автоматической отмены изменений, внесенных действием скрипта. Отмените изменения вручную или предоставьте скрипт для их отмены.


### <a name="script-action-in-the-cluster-creation-process"></a>Действие сценария в процессе создания кластера

Действия скриптов, используемые в процессе создания кластера и выполняющиеся в существующем кластере, несколько отличаются:

* Скрипт **сохраняется автоматически**.
* **Ошибка** в скрипте может вызвать сбой создания кластера.

На следующей схеме показано, когда выполняется действие сценария в процессе создания.

![Настройка кластера HDInsight и этапы создания кластера][img-hdi-cluster-states]

Скрипт выполняется во время настройки HDInsight. На этом этапе скрипт выполняется параллельно на всех указанных узлах кластера с правами привилегированного пользователя на узлах.

> [!NOTE]
> Так как при выполнении скрипта у вас есть права привилегированного пользователя на узлах кластера, вы можете выполнять такие операции, как остановка и запуск служб, включая службы, связанные с Hadoop. Если вы останавливаете службы, перед завершением работы сценария необходимо запустить службу Ambari и другие службы, связанные с Hadoop. Эти службы нужны для определения работоспособности и состояния кластера при его создании.


При создании кластера можно использовать несколько действий скриптов одновременно. Они будут вызываться в том порядке, в котором были указаны.

> [!IMPORTANT]
> Действия сценария должны быть завершены в течение 60 минут. В противном случае возникнет ошибка времени ожидания. В процессе подготовки кластера скрипт запускается одновременно с другими процессами установки и настройки. Конкуренция за ресурсы, такие как ЦП или пропускная способность сети, может привести к затягиванию выполнения сценария по сравнению со временем его выполнения в среде разработки.
>
> Чтобы свести время выполнения сценария к минимуму, избегайте таких задач, как скачивание и компиляция приложений из исходного кода. Выполните предварительную компиляцию приложения и сохраните двоичный файл в службе хранилища Azure.


### <a name="script-action-on-a-running-cluster"></a>Действие скрипта в работающем кластере

В отличие от действий скриптов, используемых во время создания кластера, ошибка в выполняющемся скрипте в работающем кластере не приводит автоматически к сбою в работе кластера. После завершения скрипта кластер должен вернуться в рабочее состояние.

> [!IMPORTANT]
> Даже если кластер запущен, ошибка скрипта может привести к проблемам. К примеру, скрипт может удалить необходимые для работы кластера файлы.
>
> Действия скриптов выполняются с использованием привилегий корневой учетной записи, поэтому вы должны понимать, что делает скрипт, прежде чем применять его в кластере.

Когда сценарий применяется в кластере, состояние кластера изменяется с **Работает** на **Принято**, затем на **Конфигурация HDInsight** и снова на **Работает**, если выполнение сценария завершается успешно. Так как состояние скрипта регистрируется в журнале действий скриптов, вы можете определить успешность его выполнения. Например, командлет PowerShell `Get-AzureRmHDInsightScriptActionHistory` можно использовать для просмотра состояния сценария. Эта команда возвращает следующую информацию:

    ScriptExecutionId : 635918532516474303
    StartTime         : 8/14/2017 7:40:55 PM
    EndTime           : 8/14/2017 7:41:05 PM
    Status            : Succeeded

> [!NOTE]
> Если вы изменили пароль пользователя (администратора) кластера после создания кластера, может происходить сбой при выполнении действий скриптов для этого кластера. Если у вас есть сохраненные действия скриптов, нацеленные на рабочие узлы, они могут завершиться ошибкой при масштабировании кластера.

## <a name="example-script-action-scripts"></a>Пример действий сценария

Действия скриптов можно использовать с помощью следующих служебных средств:

* Портал Azure
* Azure PowerShell
* Инфраструктура CLI Azure
* Пакет SDK для HDInsight .NET

В HDInsight доступны скрипты для установки следующих компонентов в кластерах HDInsight.

| Имя | Скрипт |
| --- | --- |
| **Добавление учетной записи хранения Azure** |https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh. Ознакомьтесь со статьей [Добавление дополнительных учетных записей хранения Azure в HDInsight](hdinsight-hadoop-add-storage.md). |
| **Установка Hue** |https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh. См. статью [Установка и использование Hue на кластерах HDInsight Hadoop](hdinsight-hadoop-hue-linux.md). |
| **Установка Presto** |https://raw.githubusercontent.com/hdinsight/presto-hdinsight/master/installpresto.sh. См. статью [Установка и использование Presto в кластерах HDInsight](hdinsight-hadoop-install-presto.md). |
| **Установка Solr** |https://hdiconfigactions.blob.core.windows.net/linuxsolrconfigactionv01/solr-installer-v01.sh. Ознакомьтесь со статьей [Установка и использование Solr в кластерах HDInsight](hdinsight-hadoop-solr-install-linux.md). |
| **Установка Giraph** |https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh. Ознакомьтесь со статьей [Установка и использование Giraph в кластерах HDInsight](hdinsight-hadoop-giraph-install-linux.md). |
| **Предварительная загрузка библиотек Hive** |https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh. См статью [Добавление библиотек Hive в кластеры HDInsight](hdinsight-hadoop-add-hive-libraries.md). |
| **Установка или обновление Mono** | https://hdiconfigactions.blob.core.windows.net/install-mono/install-mono.bash См. статью [Установка или обновление Mono в HDInsight](hdinsight-hadoop-install-mono.md). |

## <a name="use-a-script-action-during-cluster-creation"></a>Использование действия скрипта при создании кластера

В этом разделе приведены примеры различных способов использования действий скриптов при создании кластера HDInsight.

### <a name="use-a-script-action-during-cluster-creation-from-the-azure-portal"></a>Использование действия сценария при создании кластера с портала Azure

1. Начните создание кластера, как описано в разделе [Создание кластеров Hadoop под управлением Windows в HDInsight](hdinsight-hadoop-provision-linux-clusters.md). Остановитесь, когда откроется колонка __Сводка по кластерам__.

2. В колонке __Сводка по кластерам__ щелкните ссылку __Изменить__ для элемента __Дополнительные параметры__.

    ![Ссылка "Дополнительные параметры"](./media/hdinsight-hadoop-customize-cluster-linux/advanced-settings-link.png)

3. В колонке __Дополнительные параметры__ выберите __Действия скрипта__. В колонке __Действия скрипта__ выберите __+Отправить новое__.

    ![Отправка нового действия скрипта](./media/hdinsight-hadoop-customize-cluster-linux/add-script-action.png)

4. Используйте запись __Выберите скрипт__, чтобы выбрать готовый скрипт. Чтобы использовать пользовательский скрипт, выберите запись __Пользовательский__, а затем укажите __имя__ и код __URI bash-скрипта__.

    ![Добавление скрипта в форме выбора скрипта](./media/hdinsight-hadoop-customize-cluster-linux/select-script.png)

    В приведенной ниже таблице описываются элементы формы.

    | Свойство | Значение |
    | --- | --- |
    | Выберите скрипт | Чтобы использовать собственный скрипт, выберите __Настраиваемый__. В противном случае выберите один из предоставленных скриптов. |
    | Имя |Укажите имя для действия сценария. |
    | URI bash-скрипта |Укажите URI для сценария, который вызывается для настройки кластера. |
    | Головной, рабочий или Zookeeper |Укажите узлы (**головной**, **рабочий** или **ZooKeeper**), на которых выполняется скрипт настройки. |
    | Параметры |Укажите параметры, если они требуются для сценария. |

    Используйте запись __Сохранить это действие скрипта__, чтобы скрипт применялся при масштабировании.

5. Чтобы сохранить скрипт, нажмите кнопку __Создать__. Чтобы добавить еще один скрипт, можно использовать элемент __+ Отправить новый__.

    ![Несколько действий скриптов](./media/hdinsight-hadoop-customize-cluster-linux/multiple-scripts.png)

    Завершив добавление скриптов, нажмите кнопку __Выбрать__, а затем — кнопку __Далее__, чтобы вернуться в раздел __Сводка по кластерам__.

3. Чтобы создать кластер, в разделе __Сводка по кластерам__ нажмите __Создать__.

### <a name="use-a-script-action-from-azure-resource-manager-templates"></a>Использование действия сценария на основе шаблонов диспетчера ресурсов Azure

Примеры в этом разделе демонстрируют использование действий скриптов с шаблонами Azure Resource Manager.

#### <a name="before-you-begin"></a>Перед началом работы

* Сведения о настройке рабочей станции для запуска командлетов HDInsight PowerShell см. в статье [Установка и настройка Azure PowerShell](/powershell/azure/overview).
* Инструкции по созданию шаблонов см. в статье [Authoring Azure Resource Manager templates](../azure-resource-manager/resource-group-authoring-templates.md) (Создание шаблонов Azure Resource Manager).
* Сведения об использовании Azure PowerShell с Resource Manager см. в [этой статье](../azure-resource-manager/powershell-azure-resource-manager.md).

#### <a name="create-clusters-using-script-action"></a>Создание кластеров с помощью действия скрипта

1. Скопируйте следующий шаблон в папку на своем компьютере. Он устанавливает Giraph на головные и рабочие узлы в кластере. Чтобы убедиться, что шаблон JSON является допустимым, Вставьте содержимое шаблона в [JSONLint](http://jsonlint.com/) — интерактивное средство проверки JSON.

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
    > Используйте `Get-AzureRmSubscription` для получения списка всех подписок, связанных с вашей учетной записью, в котором указан идентификатор каждой подписки.

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

5. Чтобы создать развертывание для группы ресурсов, выполните командлет **New-AzureRmResourceGroupDeployment** и укажите необходимые параметры. Параметры включают следующие данные:

    * имя развертывания;
    * имя группы ресурсов;
    * путь или URL-адрес к созданному шаблону.

  Если для вашего шаблона требуются какие-либо параметры, необходимо также передать их. В данном случае для действия сценария по установке R в кластере никакие параметры не требуются.

        New-AzureRmResourceGroupDeployment -Name mydeployment -ResourceGroupName myresourcegroup -TemplateFile <PathOrLinkToTemplate>

    Появится запрос на задание значений параметров, определенных в шаблоне.

1. После развертывания группы ресурсов вы увидите сводку по развертыванию.

          DeploymentName    : mydeployment
          ResourceGroupName : myresourcegroup
          ProvisioningState : Succeeded
          Timestamp         : 8/14/2017 7:00:27 PM
          Mode              : Incremental
          ...

2. Если развертывание завершается сбоем, вы можете использовать следующие командлеты для получения информации об ошибках.

        Get-AzureRmResourceGroupDeployment -ResourceGroupName myresourcegroup -ProvisioningState Failed

### <a name="use-a-script-action-during-cluster-creation-from-azure-powershell"></a>Использование действия скрипта при создании кластера с помощью Azure PowerShell

В этом разделе мы используем командлет [Add-AzureRmHDInsightScriptAction](https://msdn.microsoft.com/library/mt603527.aspx), чтобы вызвать скрипты с помощью действия скрипта для настройки кластера. Прежде чем продолжить, убедитесь, что вы установили и настроили Azure PowerShell. Сведения о настройке рабочей станции для запуска командлетов HDInsight PowerShell см. в статье [Установка и настройка Azure PowerShell](/powershell/azure/overview).

Следующий скрипт демонстрирует применение действия скрипта при создании кластера с помощью PowerShell:

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=5-90)]

Создание кластера может занять несколько минут.

### <a name="use-a-script-action-during-cluster-creation-from-the-hdinsight-net-sdk"></a>Использование действия скрипта при создании кластера с помощью пакета SDK HDInsight для .NET

Пакет SDK HDInsight для .NET предоставляет клиентские библиотеки, которые упрощают работу с кластерами HDInsight из приложения .NET. Пример кода см. в статье [Создание кластеров под управлением Linux в HDInsight с помощью пакета SDK для .NET](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md#use-script-action).

## <a name="apply-a-script-action-to-a-running-cluster"></a>Применение действия скрипта в работающем кластере

В этом разделе вы узнаете, как применять действия скриптов к работающему кластеру.

### <a name="apply-a-script-action-to-a-running-cluster-from-the-azure-portal"></a>Применение действия сценария в работающем кластере с портала Azure

1. На [портале Azure](https://portal.azure.com)выберите свой кластер HDInsight.

2. В представлении кластера HDInsight выберите плитку **Действия скрипта**.

    ![Элемент "Действия сценариев"](./media/hdinsight-hadoop-customize-cluster-linux/scriptactionstile.png)

   > [!NOTE]
   > Кроме того, можно щелкнуть **Все параметры**, а затем выбрать **Действия скрипта** в разделе "Параметры".

3. В верхней части раздела "Действия скрипта" выберите **+Отправить новое**.

    ![Добавление скрипта в работающий кластер](./media/hdinsight-hadoop-customize-cluster-linux/add-script-running-cluster.png)

4. Используйте запись __Выберите скрипт__, чтобы выбрать готовый скрипт. Чтобы использовать пользовательский скрипт, выберите запись __Пользовательский__, а затем укажите __имя__ и код __URI bash-скрипта__.

    ![Добавление скрипта в форме выбора скрипта](./media/hdinsight-hadoop-customize-cluster-linux/select-script.png)

    В приведенной ниже таблице описываются элементы формы.

    | Свойство | Значение |
    | --- | --- |
    | Выберите скрипт | Чтобы использовать собственный скрипт, выберите __Настраиваемый__. В противном случае выберите предоставленный скрипт. |
    | Имя |Укажите имя для действия сценария. |
    | URI bash-скрипта |Укажите URI для сценария, который вызывается для настройки кластера. |
    | Головной, рабочий или Zookeeper |Укажите узлы (**головной**, **рабочий** или **ZooKeeper**), на которых выполняется скрипт настройки. |
    | Параметры |Укажите параметры, если они требуются для сценария. |

    Используйте запись __Сохранить это действие скрипта__, чтобы скрипт применялся при масштабировании.

5. Наконец, нажмите кнопку **Создать**, чтобы применить скрипт к кластеру.

### <a name="apply-a-script-action-to-a-running-cluster-from-azure-powershell"></a>Применение действия скрипта в работающем кластере с помощью Azure PowerShell

Прежде чем продолжить, убедитесь, что вы установили и настроили Azure PowerShell. Сведения о настройке рабочей станции для запуска командлетов HDInsight PowerShell см. в статье [Установка и настройка Azure PowerShell](/powershell/azure/overview).

Следующий пример демонстрирует применение действия скрипта к работающему кластеру:

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=105-117)]

После завершения операции вы получите приблизительно такой текст:

    OperationState  : Succeeded
    ErrorMessage    :
    Name            : Giraph
    Uri             : https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh
    Parameters      :
    NodeTypes       : {HeadNode, WorkerNode}

### <a name="apply-a-script-action-to-a-running-cluster-from-the-azure-cli"></a>Применение действия сценария в работающем кластере с помощью Azure CLI

Прежде чем продолжить, убедитесь, что вы установили и настроили Azure CLI. Дополнительные сведения см. в статье [Установка Azure CLI](../cli-install-nodejs.md).

[!INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)]

1. Чтобы переключиться в режим Azure Resource Manager, выполните в командной строке следующую команду:

        azure config mode arm

2. Выполните приведенную далее команду для аутентификации в подписке Azure.

        azure login

3. Выполните приведенную далее команду, чтобы применить действие сценария к работающему кластеру.

        azure hdinsight script-action create <clustername> -g <resourcegroupname> -n <scriptname> -u <scriptURI> -t <nodetypes>

    Если параметры для этой команды не заданы, вам будет предложено сделать это. Если сценарий, указанный с помощью `-u`, принимает параметры, их можно задать с помощью параметра `-p`.

    Допустимые типы узлов: `headnode`, `workernode` и `zookeeper`. Если сценарий должен быть применен к нескольким типам узлов, укажите типы, разделив их точкой с запятой. Например, `-n headnode;workernode`.

    Чтобы сохранить сценарий, добавьте `--persistOnSuccess`. Чтобы сохранить скрипт позднее, используйте `azure hdinsight script-action persisted set`.

    По завершении задания выходные данные будут иметь примерно следующий вид:

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

2. В представлении кластера HDInsight выберите плитку **Действия скрипта**.

    ![Элемент "Действия сценариев"](./media/hdinsight-hadoop-customize-cluster-linux/scriptactionstile.png)

   > [!NOTE]
   > Кроме того, можно щелкнуть **Все параметры**, а затем выбрать **Действия скрипта** в разделе "Параметры".

4. Журнал скриптов для этого кластера отображается в разделе "Действия скрипта". Эти сведения включают в себя список сохраняемых скриптов. Как показано на снимке экрана ниже, в этом кластере был выполнен скрипт Solr. Сохраняемых скриптов на нем не видно.

    ![Раздел "Действия скриптов"](./media/hdinsight-hadoop-customize-cluster-linux/script-action-history.png)

5. При выборе сценария в журнале отображается соответствующий раздел "Свойства". В верхней части экрана можно повторно запустить скрипт или изменить его тип.

    ![Раздел "Свойства действий скрипта"](./media/hdinsight-hadoop-customize-cluster-linux/promote-script-actions.png)

6. Можно также использовать элемент **…** справа от записей в разделе "Действия скрипта" для выполнения действий.

    ![Использование действий скриптов](./media/hdinsight-hadoop-customize-cluster-linux/deletepromoted.png)

### <a name="using-azure-powershell"></a>Использование Azure PowerShell

| Командлет | Действие |
| --- | --- |
| Get-AzureRmHDInsightPersistedScriptAction |Получение сведений о действиях сохраняемого скрипта |
| Get-AzureRmHDInsightScriptActionHistory |Получение журнала действий скриптов, применяемых в кластере, или сведений о конкретном скрипте |
| Set-AzureRmHDInsightPersistedScriptAction |Изменение типа специального скрипта на сохраняемый скрипт |
| Remove-AzureRmHDInsightPersistedScriptAction |Изменение типа сохраняемого скрипта на специальный скрипт |

> [!IMPORTANT]
> При использовании `Remove-AzureRmHDInsightPersistedScriptAction` действия, выполненные скриптом, не отменяются. Этот командлет только удаляет флаг сохранения.

В приведенном ниже примере скрипта показано использование командлетов для изменения типа скрипта.

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=123-140)]

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
> При использовании `azure hdinsight script-action persisted delete` действия, выполненные скриптом, не отменяются. Этот командлет только удаляет флаг сохранения.

### <a name="using-the-hdinsight-net-sdk"></a>Использование пакета SDK HDInsight .NET

Пример использования пакета SDK для .NET для получения журнала скриптов из кластера см. на странице [https://github.com/Azure-Samples/hdinsight-dotnet-script-action](https://github.com/Azure-Samples/hdinsight-dotnet-script-action).

> [!NOTE]
> В этом примере также показано, как установить приложение HDInsight с помощью пакета SDK для .NET.

## <a name="support-for-open-source-software-used-on-hdinsight-clusters"></a>Поддержка программного обеспечения с открытым исходным кодом, используемого в кластере HDInsight

Служба Microsoft Azure HDInsight использует сформированную вокруг Hadoop экосистему технологий с открытым исходным кодом. Microsoft Azure предоставляет общий уровень поддержки для технологий с открытым исходным кодом. Дополнительные сведения см. в разделе, посвященном **области действия поддержки**, на [веб-сайте с часто задаваемыми вопросами о поддержке Azure](https://azure.microsoft.com/support/faq/). Служба HDInsight предоставляет дополнительный уровень поддержки для встроенных компонентов.

В службе HDInsight доступно два типа компонентов с открытым исходным кодом.

* **Встроенные компоненты.** Эти компоненты предварительно установлены в кластерах HDInsight и предоставляют его базовые функциональные возможности. Например, к этой категории относится диспетчер ресурсов YARN, язык запросов Hive (HiveQL) и библиотека Mahout. Полный список компонентов кластера доступен в статье [Что представляют собой различные компоненты Hadoop, доступные в HDInsight?](hdinsight-component-versioning.md)
* **Настраиваемые компоненты.** Как пользователь кластера вы можете установить или использовать в рабочей нагрузке любой компонент, полученный из сообщества или созданный самостоятельно.

> [!WARNING]
> Компоненты, поставляемые с кластером HDInsight, полностью поддерживаются. Служба поддержки Майкрософт помогает выявлять и устранять проблемы, связанные с этими компонентами.
>
> Настраиваемые компоненты получают ограниченную коммерчески оправданную поддержку, способствующую дальнейшей диагностике проблемы. Служба поддержки Майкрософт может устранить проблему ИЛИ вас могут попросить обратиться к специалистам по технологиям с открытым исходным кодом, используя доступные каналы связи. Можно использовать ряд сайтов сообществ, например [форум MSDN по HDInsight](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight) или [http://stackoverflow.com](http://stackoverflow.com). Кроме того, для проектов Apache есть соответствующие сайты, например [Hadoop](http://hadoop.apache.org/) на сайте [http://apache.org](http://apache.org).

Служба HDInsight позволяет использовать настраиваемые компоненты несколькими разными способами. Уровень поддержки не зависит от того, как компонент используется или устанавливается в кластере. Ниже приведен список самых распространенных способов использования настраиваемых компонентов в кластерах HDInsight.

1. Отправка задания. В кластер можно отправлять задания Hadoop или другие типы заданий, выполняющие или использующие настраиваемые компоненты.

2. Настройка кластера. Во время создания кластера можно указать дополнительные параметры и настраиваемые компоненты, устанавливаемые на узлах кластера.

3. Примеры. Для популярных настраиваемых компонентов корпорация Майкрософт и другие компании могут предоставлять примеры использования таких компонентов в кластерах HDInsight. Эти примеры представляются без поддержки.

## <a name="troubleshooting"></a>Устранение неполадок

Вы можете использовать веб-интерфейс Ambari для просмотра сведений, регистрируемых действиями скриптов. Если при создании кластера произошел сбой скрипта, вы можете просмотреть журналы в учетной записи хранения по умолчанию, связанной с кластером. Этот раздел содержит сведения о том, как получить журналы обоими этими способами.

### <a name="using-the-ambari-web-ui"></a>Использование веб-интерфейса Ambari

1. В браузере перейдите на сайт https://CLUSTERNAME.azurehdinsight.net. Параметр CLUSTERNAME нужно заменить именем кластера HDInsight.

    При появлении запроса введите имя (admin) и пароль учетной записи администратора кластера. Возможно, вам потребуется повторно ввести учетные данные администратора в веб-форме.

2. В панели вверху страницы выберите запись **ops**. Появится список текущих и предыдущих операций, выполняемых в кластере с помощью Ambari.

    ![Веб-панель Ambari с выбранной записью ops](./media/hdinsight-hadoop-customize-cluster-linux/ambari-nav.png)

3. Найдите записи, для которых в столбце **Операции** указано **run\_customscriptaction**. Такие записи создаются при выполнении действий скриптов.

    ![Снимок экрана операций](./media/hdinsight-hadoop-customize-cluster-linux/ambariscriptaction.png)

    Для просмотра выходных данных STDOUT и STDERR выберите запись run\customscriptaction и перейдите по ссылкам. Эти выходные данные формируются при запуске сценария и могут содержать полезные сведения.

### <a name="access-logs-from-the-default-storage-account"></a>Доступ к журналам из учетной записи хранения по умолчанию

При сбое создания кластера из-за ошибки в действии скрипта журналы по-прежнему будут доступными в учетной записи хранения кластера.

* Журналы хранилища находятся в `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\CLUSTER_NAME\DATE`.

    ![Снимок экрана операций](./media/hdinsight-hadoop-customize-cluster-linux/script_action_logs_in_storage.png)

    В этом каталоге журналы упорядочены по узлам headnode, workernode и zookeeper. Ниже приведены некоторые примеры.

    * **Головной узел:** - `<uniqueidentifier>AmbariDb-hn0-<generated_value>.cloudapp.net`

    * **Рабочий узел** - `<uniqueidentifier>AmbariDb-wn0-<generated_value>.cloudapp.net`

    * **Узел Zookeeper** - `<uniqueidentifier>AmbariDb-zk0-<generated_value>.cloudapp.net`

* Все stdout и stderr соответствующего узла передаются в учетную запись хранилища. Для каждого действия скрипта создается файл **output-\*.txt** и файл **errors-\*.txt**. Выходной TXT-файл содержит сведения об универсальном коде ресурса (URI) сценария, который был запущен на узле. Например:

        'Start downloading script locally: ', u'https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh'

* Возможно, повторно создан кластер действия сценария с тем же именем. В этом случае соответствующие журналы можно отличить по имени папки DATE. Например, структура папок для кластера (mycluster), созданного в другие дни, будет похожа на следующие записи журнала:

    `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-04` `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-05`

* При создании кластера действие сценария с таким же именем в тот же день можно использовать уникальный префикс для идентификации соответствующих файлов журнала.

* При создании кластера около полуночи (00:00) файлы журналов могут включать сведения за два дня. В таких случаях для одного кластера будут отображаться две разные папки даты.

* Передача файлов журнала в контейнер по умолчанию может занять до 5 минут, особенно для больших кластеров. Таким образом, если вы хотите получить доступ к журналам, не следует немедленно удалять кластер при сбое действия сценария.

### <a name="ambari-watchdog"></a>Модуль наблюдения Ambari

> [!WARNING]
> Не изменяйте пароль модуля наблюдения Ambari (hdinsightwatchdog) в кластере HDInsight под управлением Linux. Это не позволит выполнять новые действия сценария в кластере HDInsight.

### <a name="cant-import-name-blobservice"></a>Не удается импортировать BlobService имени

__Симптомы:__ сбой действия скрипта. При просмотре операции в Ambari выводится следующее сообщение об ошибке:

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

Сведения о подключении к кластеру по протоколу SSH см. в разделе [Подключение к HDInsight (Hadoop) с помощью SSH](hdinsight-hadoop-linux-use-ssh-unix.md).

### <a name="history-doesnt-show-scripts-used-during-cluster-creation"></a>Журнал не отображает скрипты, используемые во время создания кластера

Если кластер создан до 15 марта 2016 г., в журнале действий скриптов могут отсутствовать записи. Если размер кластера изменен после 15 марта 2016 г., скрипты, используемые при создании кластера, будут отображаться в журнале, так как они применялись на новых узлах кластера при изменении размера.

Есть два исключения.

* Если кластер создан до 1 сентября 2015 г. Это дата добавления действий скриптов. Если кластер был создан раньше, действия скриптов не могли использоваться при его создании.

* Если при создании кластера использовалось несколько действий скриптов, для которых использовалось одно имя, или же если для разных скриптов использовалось одно имя и один универсальный код ресурса (URI), но разные параметры. В таких случаях происходит следующая ошибка:

    В таком кластере из-за конфликта имен скриптов в существующих скриптах новые действия скриптов выполняться не будут. Имена скриптов, указанные при создании кластера, должны быть уникальными. Существующие скрипты выполняются при изменении размера.

## <a name="next-steps"></a>Дальнейшие действия

* [Разработка скриптов действия сценария для HDInsight](hdinsight-hadoop-script-actions-linux.md)
* [Установка и использование Solr в кластерах HDInsight](hdinsight-hadoop-solr-install-linux.md)
* [Установка и использование Giraph в кластерах HDInsight](hdinsight-hadoop-giraph-install-linux.md)
* [Добавление дополнительных учетных записей хранения Azure в HDInsight](hdinsight-hadoop-add-storage.md)

[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster-linux/HDI-Cluster-state.png "Этапы создания кластера"
