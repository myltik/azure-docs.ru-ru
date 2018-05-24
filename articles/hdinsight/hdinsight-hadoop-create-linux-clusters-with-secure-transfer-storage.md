---
title: Создание кластера Hadoop с помощью учетных записей хранения с безопасной передачей в Azure HDInsight | Документация Майкрософт
description: Узнайте, как создать кластеры HDInsight с помощью учетных записей хранения Azure с включенной безопасной передачей.
keywords: hadoop getting started,hadoop linux,hadoop quickstart,secure transfer,azure storage account
services: hdinsight
documentationcenter: ''
author: mumian
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/14/2018
ms.author: jgao
ms.openlocfilehash: 7a2022fd5cd67120b8dc435a950533e07017218c
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34200526"
---
# <a name="create-hadoop-cluster-with-secure-transfer-storage-accounts-in-azure-hdinsight"></a>Создание кластера Hadoop с помощью учетных записей хранения с безопасной передачей в Azure HDInsight

Функция [Secure transfer required](../storage/common/storage-require-secure-transfer.md) (Необходима безопасная передача) усиливает безопасность учетной записи хранения Azure путем принудительной передачи всех запросов к вашей учетной записи через безопасное подключение. Эта функция и схема wasbs поддерживаются только кластером HDInsight версии 3.6 или более новым. 

## <a name="prerequisites"></a>предварительным требованиям
Для работы с этим руководством вам потребуется:

* **Подписка Azure**: чтобы создать бесплатную пробную учетную запись (на один месяц), перейдите по адресу [azure.microsoft.com/free](https://azure.microsoft.com/free).
* **Учетная запись хранения Azure с включенной безопасной передачей**. Инструкции см. в статье [Об учетных записях хранения Azure](../storage/common/storage-create-storage-account.md#create-a-storage-account) и [Требование безопасной передачи](../storage/common/storage-require-secure-transfer.md).
* **Контейнер больших двоичных объектов в учетной записи хранения**. 
## <a name="create-cluster"></a>Создание кластера

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]


В этом разделе вы создадите в HDInsight кластер Hadoop, используя [шаблон Azure Resource Manager](../azure-resource-manager/resource-group-template-deploy.md). (Шаблон также находится в [GitHub](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-with-existing-default-storage-account/).) Знакомство с шаблонами Resource Manager не является обязательным для работы с этим руководством. Сведения о других способах создания кластеров, а также о свойствах, используемых в этом руководстве, см. в статье [Создание кластеров Hadoop под управлением Linux в HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

1. Щелкните следующее изображение, чтобы войти в Azure и открыть шаблон Resource Manager на портале Azure. 
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-with-existing-default-storage-account%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-hadoop-linux-tutorial-get-started/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. Следуйте инструкциям, чтобы создать кластер со следующими характеристиками: 

    - Укажите HDInsight версии 3.6.  Требуется версия 3.6 или более новая.
    - Укажите учетную запись хранения с включенной безопасной передачей.
    - Введите короткое имя для учетной записи хранения.
    - Учетную запись хранения и контейнер больших двоичных объектов необходимо создать заранее. 

    Инструкции см. в [этой статье](hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster). 

При использовании действия скрипта для предоставления собственных файлов конфигурации необходимо использовать wasbs в следующих параметрах:

- fs.defaultFS (core-site)
- spark.eventLog.dir 
- spark.history.fs.logDirectory

## <a name="add-additional-storage-accounts"></a>Добавление дополнительных учетных записей хранения

Добавить дополнительные учетные записи хранения с включенной безопасной передачей можно несколькими способами:

- Измените шаблон Azure Resource Manager в предыдущем разделе.
- Создайте кластер с помощью [портала Azure](https://portal.azure.com) и укажите связанную учетную запись хранения.
- Используйте действия скриптов для добавления дополнительных учетных записей хранения с включенной безопасной передачей в имеющийся кластер HDInsight.  Дополнительные сведения см. в статье [Добавление дополнительных учетных записей хранения в HDInsight](hdinsight-hadoop-add-storage.md).

## <a name="next-steps"></a>Дополнительная информация
В этом руководстве вы узнали, как создавать кластер HDInsight и включать безопасную передачу для учетных записей хранения.

Дополнительные сведения об анализе данных с помощью HDInsight см. в следующих статьях.

* Дополнительные сведения об использовании Hive с HDInsight, включая выполнение запросов Hive из Visual Studio, см. в статье [Использование Hive с HDInsight][hdinsight-use-hive].
* Дополнительные сведения о Pig, языке, который используется для преобразования данных, см. в статье [Использование Pig с HDInsight][hdinsight-use-pig].
* Дополнительные сведения о MapReduce, способе записи программ, которые обрабатывают данные в Hadoop, см. в статье [Использование MapReduce с HDInsight][hdinsight-use-mapreduce].
* Дополнительные сведения об анализе данных в HDInsight с помощью средств HDInsight для Visual Studio см. в статье [Приступая к работе с инструментами Hadoop в Visual Studio для HDInsight для выполнения запроса Hive](hadoop/apache-hadoop-visual-studio-tools-get-started.md).

Чтобы узнать, как HDInsight хранит данные или получает их, ознакомьтесь со следующими статьями:

* Сведения о том, как HDInsight использует службу хранилища Azure, см. в статье [Использование HDFS-совместимой службы хранилища с Hadoop в HDInsight](hdinsight-hadoop-use-blob-storage.md).
* Сведения об отправке данных в HDInsight см. в статье [Отправка данных в HDInsight][hdinsight-upload-data].

Дополнительные сведения о создании кластера HDInsight и управлении этим кластером см. в следующих статьях:

* Сведения об управлении кластером HDInsight под управлением Linux см. в статье [Управление кластерами HDInsight с помощью веб-интерфейса Ambari](hdinsight-hadoop-manage-ambari.md).
* Дополнительные сведения о параметрах, которые можно выбрать при создании кластера HDInsight, см. в статье [Создание кластеров Hadoop под управлением Linux в HDInsight](hdinsight-hadoop-provision-linux-clusters.md).
* Если вы уже знакомы с Linux и Hadoop, но хотите узнать об особенностях Hadoop в HDInsight, см. статью [Сведения об использовании HDInsight в Linux](hdinsight-hadoop-linux-information.md). Эта статья содержит следующую информацию:
  
  * URL-адреса для служб, размещенных в кластере, например Ambari и WebHCat;
  * расположение файлов Hadoop и примеры в локальной файловой системе;
  * использование хранилища Azure (WASB) вместо HDFS в качестве хранилища данных по умолчанию.

[1]: ../HDInsight/hadoop/apache-hadoop-visual-studio-tools-get-started.md

[hdinsight-provision]: hdinsight-provision-linux-clusters.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-mapreduce]:hadoop/hdinsight-use-mapreduce.md
[hdinsight-use-hive]:hadoop/hdinsight-use-hive.md
[hdinsight-use-pig]:hadoop/hdinsight-use-pig.md


