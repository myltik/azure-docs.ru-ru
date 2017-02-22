---
title: "Использование пустых граничных узлов в HDInsight | Документация Майкрософт"
description: "Сведения о том, как добавить пустой граничный узел в кластер HDInsight, который может использоваться в качестве клиента, а также тестировать и разместить приложения HDInsight."
services: hdinsight
editor: cgronlun
manager: jhubbard
author: mumian
tags: azure-portal
documentationcenter: 
ms.assetid: cdc7d1b4-15d7-4d4d-a13f-c7d3a694b4fb
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/14/2016
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: a344a3a05c2a27e6b6500a6560c7fbb2096c9bfc
ms.openlocfilehash: 0303145ba0ce611242f218cdf6a2274e9e393fb4


---
# <a name="use-empty-edge-nodes-in-hdinsight"></a>Использование пустых граничных узлов в HDInsight
Cведения о том, как добавить пустой граничный узел в кластер HDInsight под управлением Linux. Пустой граничный узел — это виртуальная машина Linux, где установлены и настроены те же клиентские средства, что и на головных узлах, но без служб Hadoop. Граничный узел можно использовать для доступа к кластеру, а также тестирования и размещения клиентских приложений. 

Пустой граничный узел можно добавить в имеющийся кластер HDInsight или в новый кластер при его создании. Добавление пустого граничного узла осуществляется с помощью шаблона Azure Resource Manager.  В следующем примере показано, как это сделать:

    "resources": [
        {
            "name": "[concat(parameters('clusterName'),'/', variables('applicationName'))]",
            "type": "Microsoft.HDInsight/clusters/applications",
            "apiVersion": "2015-03-01-preview",
            "dependsOn": [ "[concat('Microsoft.HDInsight/clusters/',parameters('clusterName'))]" ],
            "properties": {
                "marketPlaceIdentifier": "EmptyNode",
                "computeProfile": {
                    "roles": [{
                        "name": "edgenode",
                        "targetInstanceCount": 1,
                        "hardwareProfile": {
                            "vmSize": "Standard_D3"
                        }
                    }]
                },
                "installScriptActions": [{
                    "name": "[concat('emptynode','-' ,uniquestring(variables('applicationName')))]",
                    "uri": "[parameters('installScriptAction')]",
                    "roles": ["edgenode"]
                }],
                "uninstallScriptActions": [],
                "httpsEndpoints": [],
                "applicationType": "CustomApplication"
            }
        }
    ],

Как показано в примере, при необходимости можно вызвать [действие скрипта](hdinsight-hadoop-customize-cluster-linux.md), чтобы выполнить дополнительную конфигурацию, такую как установка [Apache Hue](hdinsight-hadoop-hue-linux.md) на граничный узел.

После создания граничного узла можно подключиться к нему с помощью SSH и запустить клиентские средства для доступа к кластеру Hadoop в HDInsight.

## <a name="add-an-edge-node-to-an-existing-cluster"></a>Добавление граничного узла в имеющийся кластер
В этом разделе описано, как использовать шаблон Resource Manager, чтобы добавить граничный узел в имеющийся кластер HDInsight.  Шаблон Resource Manager можно найти в [GitHub](https://github.com/hdinsight/Iaas-Applications/tree/master/EmptyNode). Шаблон Resource Manager вызывает сценарий действия сценария, находящийся по адресу https://raw.githubusercontent.com/hdinsight/Iaas-Applications/master/EmptyNode/scripts/EmptyNodeSetup.sh. Этот скрипт не выполняет никаких действий.  Он необходим для демонстрации вызова действия скрипта с помощью шаблона Resource Manager.

**Добавление граничного узла в имеющийся кластер**

1. Создайте кластер HDInsight, если его еще нет.  Дополнительные сведения см. в статье [Руководство по Hadoop. Начало работы с Hadoop в HDInsight на платформе Linux](hdinsight-hadoop-linux-tutorial-get-started.md).
2. Нажмите следующую кнопку, чтобы войти в Azure и открыть шаблон Azure Resource Manager на портале Azure. 
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fhdinsight%2FIaas-Applications%2Fmaster%2FEmptyNode%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apps-use-edge-node/deploy-to-azure.png" alt="Deploy to Azure"></a>
3. Настройте следующие свойства:
   
   * **Подписка.** Выберите подписку Azure, используемую для создания кластера.
   * **Группа ресурсов.** Выберите группу ресурсов, используемую для имеющегося кластера HDInsight.
   * **Расположение.** Выберите расположение для имеющегося кластера HDInsight.
   * **Имя кластера.** Введите имя имеющегося кластера HDInsight.
   * **Edge Node Size** (Размер граничного узла). Выберите один из размеров виртуальной машины.
   * **Edge Node Prefix** (Префикс граничного узла). Значение по умолчанию — **new**.  Имя граничного узла по умолчанию — **new-edgenode**.  Префикс можно настроить на портале. В шаблоне также можно настроить полное имя.
4. Установите флажок **Я принимаю указанные выше условия** и нажмите кнопку **Приобрести**, чтобы создать граничный узел.

## <a name="add-an-edge-node-when-creating-a-cluster"></a>Добавление граничного узла при создании кластера
В этом разделе описано, как использовать шаблон Resource Manager, чтобы создать кластер HDInsight с граничным узлом.  Шаблон Resource Manager можно найти в [коллекции шаблонов быстрого запуска Azure](https://azure.microsoft.com/documentation/templates/101-hdinsight-linux-with-edge-node/). Шаблон Resource Manager вызывает скрипт действия скрипта, находящегося по адресу https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-hdinsight-linux-with-edge-node/scripts/EmptyNodeSetup.sh. Этот скрипт не выполняет никаких действий.  Он необходим для демонстрации вызова действия скрипта с помощью шаблона Resource Manager.

**Добавление граничного узла в имеющийся кластер**

1. Создайте кластер HDInsight, если его еще нет.  Дополнительные сведения см. в статье [Руководство по Hadoop. Начало работы с Hadoop в HDInsight на платформе Linux](hdinsight-hadoop-linux-tutorial-get-started.md).
2. Нажмите следующую кнопку, чтобы войти в Azure и открыть шаблон Azure Resource Manager на портале Azure. 
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-with-edge-node%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apps-use-edge-node/deploy-to-azure.png" alt="Deploy to Azure"></a>
3. Настройте следующие свойства:
   
   * **Подписка.** Выберите подписку Azure, используемую для создания кластера.
   * **Группа ресурсов.** Создайте группу ресурсов для кластера.
   * **Расположение**. Выберите расположение группы ресурсов.
   * **Имя кластера.** Введите имя для нового кластера.
   * **Cluster Login User Name** (Имя пользователя для входа в кластер). Введите имя пользователя HTTP для Hadoop.  Имя по умолчанию — **admin**.
   * **Пароль для входа в кластер.** Введите пароль HTTP для Hadoop.
   * **Имя пользователя SSH.** Введите имя пользователя SSH. Имя по умолчанию — **sshuser**.
   * **Пароль SSH.** Введите пароль пользователя SSH.
   * **Install Script Action** (Установка действия скрипта). Для прохождения этого руководства оставьте значение по умолчанию.
     
     Некоторые свойства жестко запрограммированы в шаблоне: "Тип кластера", Cluster worker node count (Количество рабочих узлов кластера), Edge node size (Размер граничного узла) и Edge node name (Имя граничного узла).
4. Установите флажок **Я принимаю указанные выше условия** и нажмите кнопку **Приобрести**, чтобы создать кластер с граничным узлом.

## <a name="access-an-edge-node"></a>Доступ к граничному узлу
Конечная точка SSH граничного узла указывается в формате &lt;имя_граничного узла>.&lt;имя_кластера>-ssh.azurehdinsight.net:22.  Например, new-edgenode.myedgenode0914-ssh.azurehdinsight.net:22.

Граничный узел отображается в виде приложения на портале Azure.  На портале представлены сведения для доступа к граничному узлу с помощью SSH.

**Проверка конечной точки SSH граничного узла**

1. Выполните вход на [портал Azure](https://portal.azure.com).
2. Откройте кластер HDInsight с граничным узлом.
3. В колонке кластера щелкните **Приложения** . Затем вы увидите граничный узел.  Его имя по умолчанию — **new-edgenode**.
4. Щелкните граничный узел. Отобразится конечная точка SSH.

**Использование Hive в граничном узле**

1. К граничному узлу можно подключиться с помощью SSH.  Дополнительные сведения см. в статье [Использование SSH с Hadoop на основе Linux в HDInsight из Windows](hdinsight-hadoop-linux-use-ssh-unix.md) или [Использование SSH с Hadoop на основе Linux в HDInsight из Windows](hdinsight-hadoop-linux-use-ssh-windows.md).
2. После подключения к граничному узлу с помощью SSH откройте консоль Hive, используя следующую команду:
   
        hive
3. Чтобы показать таблицы Hive в кластере, выполните следующую команду:
   
        show tables;

## <a name="delete-an-edge-node"></a>Удаление граничного узла
Граничный узел можно удалить на портале Azure.

**Доступ к граничному узлу**

1. Выполните вход на [портал Azure](https://portal.azure.com).
2. Откройте кластер HDInsight с граничным узлом.
3. В колонке кластера щелкните **Приложения** . Появится список граничных узлов.  
4. Щелкните правой кнопкой мыши граничный узел, который нужно удалить, и выберите пункт **Удалить**.
5. Нажмите кнопку **Да** для подтверждения.

## <a name="next-steps"></a>Дальнейшие действия
В этой статье вы узнали, как добавить граничный узел и как получить к нему доступ. Для получения дополнительных сведений ознакомьтесь со следующими статьями:

* [Установка приложений HDInsight](hdinsight-apps-install-applications.md)— узнайте, как устанавливать в кластер приложения HDInsight.
* [Установка пользовательских приложений HDInsight](hdinsight-apps-install-custom-applications.md)— узнайте, как развернуть в HDInsight неопубликованное приложение HDInsight.
* [Публикация приложений HDInsight в Azure Marketplace](hdinsight-apps-publish-applications.md)— узнайте, как опубликовать пользовательские приложения HDInsight в Azure Marketplace.
* [Install an HDInsight application](https://msdn.microsoft.com/library/mt706515.aspx)(Установка приложения HDInsight) — узнайте, как определить приложения HDInsight.
* [Настройка кластеров HDInsight под управлением Linux с помощью действия сценария](hdinsight-hadoop-customize-cluster-linux.md)— узнайте, как использовать действие скрипта для установки дополнительных приложений.
* [Создание кластеров Hadoop под управлением Linux в HDInsight с помощью шаблонов ARM](hdinsight-hadoop-create-linux-clusters-arm-templates.md)— узнайте, как вызывать шаблоны Resource Manager для создания кластеров HDInsight.




<!--HONumber=Feb17_HO3-->


