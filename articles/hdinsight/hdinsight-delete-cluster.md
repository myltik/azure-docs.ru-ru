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
ms.date: 10/28/2016
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 67442bf4b04f6f3799d30f7ce26547c8145d9168


---
# <a name="how-to-delete-an-hdinsight-cluster"></a>Как удалить кластер HDInsight
Выставление счетов за использование кластера HDInsight начинается после его создания и прекращается после удаления кластера. Оплата начисляется поминутно, поэтому всегда следует удалять кластер, когда он больше не используется. В этом документе вы узнаете, как удалить кластер с помощью портала Azure, Azure PowerShell и Azure CLI.

> [!IMPORTANT]
> При удалении кластера HDInsight не происходит удаление связанных с ним учетных записей хранения Azure. Это позволяет сохранить и повторно использовать данные, хранящиеся в кластере.
> 
> 

## <a name="azure-portal"></a>Портал Azure
1. Войдите на [портал Azure](https://portal.azure.com) и выберите свой кластер HDInsight. Если кластер HDInsight не закреплен на панели мониторинга, его можно найти по имени с помощью поля поиска (значок увеличительного стекла), расположенного в правой части панели навигации.
   
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




<!--HONumber=Nov16_HO3-->


