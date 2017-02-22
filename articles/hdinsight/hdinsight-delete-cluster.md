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
translationtype: Human Translation
ms.sourcegitcommit: 5ec4b964066687b506686709c3dc5ed5b402fbaf
ms.openlocfilehash: 8b1fbc541b3123961d092c63441b804109865f60


---
# <a name="how-to-delete-an-hdinsight-cluster"></a>Как удалить кластер HDInsight

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




<!--HONumber=Feb17_HO2-->


