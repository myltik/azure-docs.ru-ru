---
title: "Добавление дополнительных учетных записей хранения Azure в HDInsight | Документация Майкрософт"
description: "Сведения о добавлении дополнительных учетных записей хранения Azure в существующий кластер HDInsight."
services: hdinsight
documentationCenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/28/2016
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 8c07f0da21eab0c90ad9608dfaeb29dd4a01a6b7
ms.openlocfilehash: 18545981a21736d9673ce19ae2325ba5e4a67ff6


---

# <a name="add-additional-azure-storage-accounts-to-hdinsight"></a>Добавление дополнительных учетных записей хранения Azure в HDInsight

Узнайте, как использовать действия сценария для добавления дополнительных учетных записей хранения Azure в существующий кластер HDInsight с операционной системой Linux.

> [!IMPORTANT]
> В этом документе описано, как добавить дополнительное хранилище в кластер после его создания. Сведения о добавлении дополнительных учетных записей хранения во время создания кластера см. в разделе __Использование дополнительного объема хранилища__ в статье [Создание кластеров Hadoop под управлением Linux в HDInsight](hdinsight-hadoop-provision-linux-clusters.md#use-additional-storage).

## <a name="how-it-works"></a>Принцип работы

Этот скрипт принимает следующие параметры.

* __Имя учетной записи хранения Azure__: имя учетной записи хранения, добавляемой в кластер HDInsight. Когда скрипт будет выполнен, HDInsight сможет считывать и записывать данные, хранящиеся в этой учетной записи хранения.

* __Ключ учетной записи хранения Azure__: ключ, который предоставляет доступ к учетной записи хранения.

* __-p__ (необязательно): если этот параметр указан, ключ не шифруется и хранится в файле core-site.xml как обычный текст.

При обработке скрипт выполняет следующие действия.

* Если учетная запись хранения уже существует в конфигурации core-site.xml для кластера, скрипт завершает работу и дальнейшие действия не выполняются.

* Проверяет существование учетной записи хранения и ее доступность с помощью ключа.

* Шифрует ключ с использованием учетных данных кластера. Из-за этого пользователи HDInsight не смогут извлекать и использовать ключ учетной записи хранения из Ambari.

* Добавляет учетную запись хранилища в файл core-site.xml.

* Останавливает и перезапускает службы Oozie, YARN, MapReduce2 и HDFS, чтобы они получили новые сведения об учетной записи хранения.

> [!WARNING]
> Если учетная запись хранения и кластер HDInsight расположены в разных регионах, это может негативно повлиять на производительность. Для доступа к данным в другом регионе сетевой трафик отправляется за пределы центра обработки данных Azure через общедоступный сегмент Интернета, что может приводить к задержкам. Кроме того, отправка данных за пределы региональных центров обработки данных может быть связана с дополнительными расходами: когда данные покидают центр обработки данных, исходящий трафик тарифицируется.

## <a name="the-script"></a>Сценарий

__Расположение скрипта__: [https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh](https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh)

__Требования__

* Скрипт должен применяться к __головным узлам__.

## <a name="to-use-the-script"></a>Использование скрипта

Дополнительные сведения об использовании действий скрипта с помощью портала Azure, Azure PowerShell и интерфейса командной строки Azure см. в разделе "Использование действия скрипта при создании кластера" в статье [Настройка кластеров HDInsight под управлением Linux с помощью действия сценария](hdinsight-hadoop-customize-cluster-linux.md#apply-a-script-action-to-a-running-cluster).

Следуя инструкциям из документации по настройке, замените URI для действия скрипта в примере соответствующим URI для этого скрипта (https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh). Замените все параметры в примере соответствующим именем учетной записи хранения Azure и ключом учетной записи хранения, добавляемой в кластер.

> [!NOTE]
> Вам не нужно отмечать этот скрипт как __сохраняемый__, так как он непосредственно обновляет конфигурацию Ambari для кластера.

## <a name="known-issues"></a>Известные проблемы

### <a name="storage-accounts-not-displayed-in-azure-portal-or-tools"></a>Учетные записи хранения не отображаются на портале Azure или в Azure Tools

При просмотре кластера HDInsight на портале Azure (для этого нужно щелкнуть __Учетные записи хранения__ в разделе __Свойства__) не отображаются учетные записи хранения, добавленные с помощью действия скрипта. Azure PowerShell и интерфейс командной строки Azure также не отображают дополнительные учетные записи хранения.

Это происходит, так как скрипт изменяет только конфигурацию core-site.xml для кластера. Сейчас эта информация не используется при получении сведений о кластере с помощью API-интерфейсов управления Azure.

Чтобы просмотреть сведения об учетной записи хранения, добавленной в кластер с помощью этого сценария, используйте REST API Ambari. Следующая команда демонстрирует, как использовать [cURL (http://curl.haxx.se/)](http://curl.haxx.se/) и [jq (https://stedolan.github.io/jq/)](https://stedolan.github.io/jq/) для получения и синтаксического анализа данных JSON из Ambari:

> [!div class="tabbedCodeSnippets" data-resources="OutlookServices.Calendar"]
> ```PowerShell
> curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["""fs.azure.account.key.STORAGEACCOUNT.blob.core.windows.net"""] | select(. != null)'
> ```
> ```Bash
> curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["fs.azure.account.key.STORAGEACCOUNT.blob.core.windows.net"] | select(. != null)'
> ```

Используя эту команду, замените __CLUSTERNAME__ именем кластера HDInsight. Замените __PASSWORD__ паролем администратора для входа в кластер по протоколу HTTP. Замените __STORAGEACCOUNT__ именем учетной записи хранилища, добавленной с помощью действия скрипта. Выходные данные этой команды выглядят так:

    "MIIB+gYJKoZIhvcNAQcDoIIB6zCCAecCAQAxggFaMIIBVgIBADA+MCoxKDAmBgNVBAMTH2RiZW5jcnlwdGlvbi5henVyZWhkaW5zaWdodC5uZXQCEA6GDZMW1oiESKFHFOOEgjcwDQYJKoZIhvcNAQEBBQAEggEATIuO8MJ45KEQAYBQld7WaRkJOWqaCLwFub9zNpscrquA2f3o0emy9Vr6vu5cD3GTt7PmaAF0pvssbKVMf/Z8yRpHmeezSco2y7e9Qd7xJKRLYtRHm80fsjiBHSW9CYkQwxHaOqdR7DBhZyhnj+DHhODsIO2FGM8MxWk4fgBRVO6CZ5eTmZ6KVR8wYbFLi8YZXb7GkUEeSn2PsjrKGiQjtpXw1RAyanCagr5vlg8CicZg1HuhCHWf/RYFWM3EBbVz+uFZPR3BqTgbvBhWYXRJaISwssvxotppe0ikevnEgaBYrflB2P+PVrwPTZ7f36HQcn4ifY1WRJQ4qRaUxdYEfzCBgwYJKoZIhvcNAQcBMBQGCCqGSIb3DQMHBAhRdscgRV3wmYBg3j/T1aEnO3wLWCRpgZa16MWqmfQPuansKHjLwbZjTpeirqUAQpZVyXdK/w4gKlK+t1heNsNo1Wwqu+Y47bSAX1k9Ud7+Ed2oETDI7724IJ213YeGxvu4Ngcf2eHW+FRK"

Это — пример ключа шифрования, который используется для доступа к учетной записи хранения.

### <a name="unable-to-access-storage-after-changing-key"></a>Не удается получить доступ к хранилищу после изменения ключа

Если вы измените ключ для учетной записи хранения, HDInsight больше не сможет обращаться к этой учетной записи.

Это происходит, так как хранимый в файле core-site.xml кластера ключ является старым.

При повторном запуске действия скрипта ключ __не__ обновляется, так как скрипт проверяет наличие записи для учетной записи хранения. Если запись существует, скрипт ее не изменяет.

Чтобы решить эту проблему, необходимо удалить существующую запись для учетной записи хранения. Это можно сделать следующим образом.

1. Откройте в браузере веб-интерфейс Ambari для кластера HDInsight. URI — https://CLUSTERNAME.azurehdinsight.net. Замените __CLUSTERNAME__ именем кластера.

    При появлении запроса введите имя пользователя и пароль для входа в кластер по протоколу HTTP.

2. В списке служб в левой части страницы выберите __HDFS__. В центральной области откройте вкладку __Конфигурации__.

3. В поле __Фильтр__ введите значение __fs.azure.account__. Будут возвращены записи для всех дополнительных учетных записей хранения, добавленных в кластер. Есть два типа записей: __keyprovider__ и __key__. Оба содержат имя учетной записи хранения как часть имени ключа. 

    Ниже представлены примеры записей для учетной записи хранения с именем __mystorage__:

        fs.azure.account.keyprovider.mystorage.blob.core.windows.net
        fs.azure.account.key.mystorage.blob.core.windows.net

4. Определив ключи для учетной записи хранения, используйте красный значок с минусом (-) справа от записи, чтобы удалить ее. Нажмите кнопку __Сохранить__, чтобы сохранить изменения.

5. Сохранив изменения, используйте действие скрипта, чтобы добавить учетную запись хранения и новое значение ключа кластера.

### <a name="poor-performance"></a>Низкая производительность

Если учетная запись хранения и кластер HDInsight расположены в разных регионах, это может негативно повлиять на производительность. Для доступа к данным в другом регионе сетевой трафик отправляется за пределы центра обработки данных Azure через общедоступный сегмент Интернета, что может приводить к задержкам.

### <a name="additional-charges"></a>Дополнительные расходы

Если учетная запись хранения и кластер HDInsight расположены в разных регионах, в ваш счет за использование Azure будет включена дополнительная плата за исходящий трафик. За исходящий трафик взимается плата, когда данные покидают региональный центр обработки данных, даже если трафик предназначается для другого центра обработки данных Azure в другом регионе.

## <a name="next-steps"></a>Дальнейшие действия

Из этой статьи вы узнали, как добавлять дополнительные учетные записи хранения Azure в существующий кластер HDInsight. Дополнительные сведения о действиях скриптов см. в статье [Настройка кластеров HDInsight под управлением Linux с помощью действия сценария](hdinsight-hadoop-customize-cluster-linux.md).


<!--HONumber=Jan17_HO3-->


