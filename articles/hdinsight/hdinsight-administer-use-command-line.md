---
title: Управление кластерами Hadoop с помощью Azure CLI — Azure HDInsight | Документы Майкрософт
description: Узнайте, как управлять кластерами Hadoop в Azure HDInsight с помощью интерфейса командной строки Azure. Azure CLI работает в Windows, Mac и Linux.
services: hdinsight
editor: cgronlun
manager: jhubbard
author: mumian
tags: azure-portal
documentationcenter: ''
ms.assetid: 4f26c79f-8540-44bd-a470-84722a9e4eca
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: na
ms.topic: conceptual
ms.date: 05/14/2018
ms.author: jgao
ms.openlocfilehash: 18901c3e99b1c67d01c091918a6abdd2f298defa
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34200985"
---
# <a name="manage-hadoop-clusters-in-hdinsight-using-the-azure-cli"></a>Управление кластерами Hadoop в HDInsight с помощью интерфейса командной строки (CLI) Azure
[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Узнайте, как использовать [интерфейс командной строки Azure](../cli-install-nodejs.md) для управления кластерами Hadoop в Azure HDInsight. Интерфейс командной строки (CLI) Azure реализован в Node.js. Его можно использовать на любой платформе, которая поддерживает Node.js, включая Windows, Mac и Linux. В настоящее время HDInsight не поддерживает [Azure CLI 2.0](https://docs.microsoft.com/cli/azure).

В этой статье описывается только использование CLI Azure с HDInsight. Общее руководство по использованию Azure CLI см. в статье [Установка и настройка Azure CLI][azure-command-line-tools].

## <a name="prerequisites"></a>предварительным требованиям
Перед началом работы с этой статьей необходимо иметь следующее:

* **Подписка Azure**. См. страницу [бесплатной пробной версии Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **Интерфейс командной строки Azure CLI** — сведения об установке и настройке CLI Azure см. в статье [Установка Azure CLI](../cli-install-nodejs.md).
* **Подключитесь к Azure**, выполнив следующую команду.

    ```cli
    azure login
    ```
  
    Дополнительные сведения об аутентификации с помощью рабочей или учебной учетной записи см. в статье [Подключение к среде Azure с использованием интерфейса командной строки Azure (Azure CLI)](/cli/azure/authenticate-azure-cli).
* **Переключитесь в режим диспетчера ресурсов Azure**с помощью следующей команды.
  
    ```cli
    azure config mode arm
    ```

Чтобы получить справку, используйте параметр **-h** .  Например: 

```cli
azure hdinsight cluster create -h
```

## <a name="create-clusters-with-the-cli"></a>Создание кластеров с помощью интерфейса командной строки
Дополнительные сведения см. в статье [Создание кластеров HDInsight с помощью интерфейса командной строки Azure](hdinsight-hadoop-create-linux-clusters-azure-cli.md).

## <a name="list-and-show-cluster-details"></a>Отображение сведений о кластере
Используйте следующие команды для отображения сведений о кластере:

```cli
azure hdinsight cluster list
azure hdinsight cluster show <Cluster Name>
```

![Представление командной строки списка кластеров][image-cli-clusterlisting]

## <a name="delete-clusters"></a>Удаление кластеров
Используйте следующую команду для удаления кластера:

```cli
azure hdinsight cluster delete <Cluster Name>
```

Можно также удалить кластер, удалив группу ресурсов, которая содержит этот кластер. Обратите внимание, это приведет к удалению всех ресурсов в группе, включая учетную запись хранения по умолчанию.

```cli
azure group delete <Resource Group Name>
```

## <a name="scale-clusters"></a>Масштабирование кластеров
Изменение размера кластера Hadoop

```cli
azure hdinsight cluster resize [options] <clusterName> <Target Instance Count>
```


## <a name="enabledisable-http-access-for-a-cluster"></a>Включение или отключение доступа по протоколу HTTP для кластера

```cli
azure hdinsight cluster enable-http-access [options] <Cluster Name> <userName> <password>
azure hdinsight cluster disable-http-access [options] <Cluster Name>
```

## <a name="enabledisable-rdp-access-for-a-cluster"></a>Включение или отключение доступа по протоколу RDP для кластера

```cli
azure hdinsight cluster enable-rdp-access [options] <Cluster Name> <rdpUserName> <rdpPassword> <rdpExpiryDate>
azure hdinsight cluster disable-rdp-access [options] <Cluster Name>
```

## <a name="next-steps"></a>Дополнительная информация
В этой статье вы узнали, как выполнять различные административные задачи в кластере HDInsight. Для получения дополнительных сведений ознакомьтесь со следующими статьями:

* [Администрирование HDInsight с помощью портала Azure][hdinsight-admin-portal]
* [Управление кластерами Hadoop в HDInsight с помощью Azure PowerShell][hdinsight-admin-powershell]
* [Руководство по Hadoop. Начало работы с Hadoop в HDInsight на платформе Linux][hdinsight-get-started]
* [Установка Azure CLI][azure-command-line-tools]

[azure-command-line-tools]: ../cli-install-nodejs.md
[azure-create-storageaccount]:../storage/common/storage-create-storage-account.md
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/


[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-get-started]:hadoop/apache-hadoop-linux-tutorial-get-started.md

[image-cli-account-download-import]: ./media/hdinsight-administer-use-command-line/HDI.CLIAccountDownloadImport.png
[image-cli-clustercreation]: ./media/hdinsight-administer-use-command-line/HDI.CLIClusterCreation.png
[image-cli-clustercreation-config]: ./media/hdinsight-administer-use-command-line/HDI.CLIClusterCreationConfig.png
[image-cli-clusterlisting]: ./media/hdinsight-administer-use-command-line/command-line-list-of-clusters.png "Отображение кластеров"
