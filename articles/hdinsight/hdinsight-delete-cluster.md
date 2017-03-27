---
title: "Как удалить кластер HDInsight | Документация Майкрософт"
description: "Сведения о различных способах удаления кластера HDInsight."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 55f7838b-9786-47ff-96db-1b64437bd0bb
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/08/2017
ms.author: larryfr
ms.custom: H1Hack27Feb2017,hdinsightactive
translationtype: Human Translation
ms.sourcegitcommit: d391c5c6289aa63e969f63f189eb5db680883f0a
ms.openlocfilehash: fbb561f4bfda27c74ffdc08c1b07f4adb83286ab
ms.lasthandoff: 03/01/2017

---
# <a name="delete-an-hdinsight-cluster-using-your-browser-powershell-or-the-azure-cli"></a>Удаление кластера HDInsight с помощью браузера, PowerShell или Azure CLI

Начисление оплаты начинается после создания кластера HDInsight и прекращается только после его удаления. Кластеры оплачиваются поминутно, поэтому всегда следует удалять кластер, когда он больше не нужен. В этом документе вы узнаете, как удалить кластер с помощью портала Azure, Azure PowerShell и Azure CLI.

> [!IMPORTANT]
> При удалении кластера HDInsight не происходит удаление связанных с ним учетных записей хранения Azure. Так как учетная запись хранения не удаляется, данные сохраняются и их можно использовать в будущем.

## <a name="azure-portal"></a>Портал Azure

1. Войдите на [портал Azure](https://portal.azure.com) и выберите свой кластер HDInsight. Если кластер HDInsight не закреплен на панели мониторинга, его можно найти по имени с помощью поля поиска.
   
    ![поиск по порталу](./media/hdinsight-delete-cluster/navbar.png)

2. Открыв колонку кластера, выберите значок **Удалить**. При появлении запроса выберите **Да**, чтобы удалить кластер.
   
    ![значок удаления](./media/hdinsight-delete-cluster/deletecluster.png)

## <a name="azure-powershell"></a>Azure PowerShell

Введите следующую команду в командной строке PowerShell, чтобы удалить кластер.

    Remove-AzureRmHDInsightCluster -ClusterName CLUSTERNAME

Замените **CLUSTERNAME** именем кластера HDInsight.

## <a name="azure-cli"></a>Инфраструктура CLI Azure

Введите следующую команду в командной строке, чтобы удалить кластер.

    azure hdinsight cluster delete CLUSTERNAME

Замените **CLUSTERNAME** именем кластера HDInsight.


