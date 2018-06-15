---
title: Добавление дополнительных учетных записей хранения Azure в HDInsight | Документация Майкрософт
description: Сведения о добавлении дополнительных учетных записей хранения Azure в существующий кластер HDInsight.
services: hdinsight
documentationCenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.devlang: ''
ms.topic: conceptual
ms.date: 04/23/2018
ms.author: larryfr
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: 1404b37725362a71ccb4a0a84dff0c7c4ca591e2
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32170107"
---
# <a name="add-additional-storage-accounts-to-hdinsight"></a>Добавление дополнительных учетных записей хранения в HDInsight

Узнайте, как использовать действия сценариев для добавления дополнительных учетных записей хранения Azure в кластер HDInsight. В этом документе описаны действия по добавлению учетной записи хранения в существующий кластер HDInsight под управлением Linux.

> [!IMPORTANT]
> В этом документе описано, как добавить дополнительное хранилище в кластер после его создания. Сведения о добавлении учетных записей хранения во время создания кластера см. в статье о [настройке кластеров HDInsight с Hadoop, Spark, Kafka и другими платформами](hdinsight-hadoop-provision-linux-clusters.md).

## <a name="how-it-works"></a>Принцип работы

Этот скрипт принимает следующие параметры.

* __Имя учетной записи хранения Azure__: имя учетной записи хранения, добавляемой в кластер HDInsight. После выполнения сценария HDInsight сможет считывать и записывать данные, хранящиеся в этой учетной записи хранения.

* __Ключ учетной записи хранения Azure__: ключ, который предоставляет доступ к учетной записи хранения.

* __-p__ (необязательно): если этот параметр указан, ключ не шифруется и хранится в файле core-site.xml как обычный текст.

При обработке скрипт выполняет следующие действия.

* Если учетная запись хранения уже существует в конфигурации core-site.xml для кластера, скрипт завершает работу и дальнейшие действия не выполняются.

* Проверяет существование учетной записи хранения и ее доступность с помощью ключа.

* Шифрует ключ с использованием учетных данных кластера.

* Добавляет учетную запись хранилища в файл core-site.xml.

* Останавливает и перезапускает службы Oozie, YARN, MapReduce2 и HDFS. Остановка и запуск этих служб позволяет им использовать новую учетную запись хранения.

> [!WARNING]
> Использование учетной записи хранения, расположение которой отличается от расположения кластера HDInsight, не поддерживается.

## <a name="the-script"></a>Сценарий

__Расположение скрипта__: [https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh](https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh)

__Требования__

* Скрипт должен применяться к __головным узлам__.

## <a name="to-use-the-script"></a>Использование скрипта

Этот сценарий можно использовать с помощью портала Azure, Azure PowerShell или Azure CLI 1.0. Дополнительные сведения см. в документе [Настройка кластеров HDInsight под управлением Linux с помощью действия сценария](hdinsight-hadoop-customize-cluster-linux.md#apply-a-script-action-to-a-running-cluster).

> [!IMPORTANT]
> Следуя инструкциям из документации по настройке, используйте следующие сведения для применения этого сценария:
>
> * Замените любой URI действия в примере скрипта кодом URI для этого скрипта (https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh).
> * Замените все параметры в примере соответствующим именем учетной записи хранения Azure и ключом учетной записи хранения, добавляемой в кластер. При использовании портала Azure эти параметры должны быть разделены пробелом.
> * Вам не нужно отмечать этот скрипт как __сохраняемый__, так как он непосредственно обновляет конфигурацию Ambari для кластера.

## <a name="known-issues"></a>Известные проблемы

### <a name="storage-accounts-not-displayed-in-azure-portal-or-tools"></a>Учетные записи хранения не отображаются на портале Azure или в Azure Tools

При просмотре кластера HDInsight на портале Azure (для этого нужно щелкнуть __Учетные записи хранения__ в разделе __Свойства__) не отображаются учетные записи хранения, добавленные с помощью действия сценария. Azure PowerShell и Azure CLI также не отображают дополнительные учетные записи хранения.

Информация о хранилище не отображается, так как сценарий изменяет только конфигурацию core-site.xml для кластера. Эта информация не используется при получении сведений о кластере с помощью интерфейсов API управления Azure.

Чтобы просмотреть сведения об учетной записи хранения, добавленной в кластер с помощью этого сценария, используйте REST API Ambari. Чтобы получить эти сведения для кластера, выполните следующие команды:

```PowerShell
$creds = Get-Credential -UserName "admin" -Message "Enter the cluster login credentials"
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" `
    -Credential $creds
$respObj = ConvertFrom-Json $resp.Content
$respObj.items.configurations.properties."fs.azure.account.key.$storageAccountName.blob.core.windows.net"
```

> [!NOTE]
> Замените `$clusterName` именем кластера HDInsight. Замените `$storageAccountName` именем учетной записи хранения. При появлении запроса введите имя для входа и пароль администратора для кластера.

```Bash
curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["fs.azure.account.key.$STORAGEACCOUNTNAME.blob.core.windows.net"] | select(. != null)'
```

> [!NOTE]
> Замените `$PASSWORD` паролем учетной записи администратора для входа на кластер. Замените `$CLUSTERNAME` именем кластера HDInsight. Замените `$STORAGEACCOUNTNAME` именем учетной записи хранения.
>
> В этом примере для извлечения и анализа данных JSON используются [curl (http://curl.haxx.se/)](http://curl.haxx.se/) и [jq (https://stedolan.github.io/jq/)](https://stedolan.github.io/jq/).

Используя эту команду, замените __CLUSTERNAME__ именем кластера HDInsight. Замените __PASSWORD__ паролем администратора для входа в кластер по протоколу HTTP. Замените __STORAGEACCOUNT__ именем учетной записи хранилища, добавленной с помощью действия скрипта. Выходные данные этой команды выглядят так:

    "MIIB+gYJKoZIhvcNAQcDoIIB6zCCAecCAQAxggFaMIIBVgIBADA+MCoxKDAmBgNVBAMTH2RiZW5jcnlwdGlvbi5henVyZWhkaW5zaWdodC5uZXQCEA6GDZMW1oiESKFHFOOEgjcwDQYJKoZIhvcNAQEBBQAEggEATIuO8MJ45KEQAYBQld7WaRkJOWqaCLwFub9zNpscrquA2f3o0emy9Vr6vu5cD3GTt7PmaAF0pvssbKVMf/Z8yRpHmeezSco2y7e9Qd7xJKRLYtRHm80fsjiBHSW9CYkQwxHaOqdR7DBhZyhnj+DHhODsIO2FGM8MxWk4fgBRVO6CZ5eTmZ6KVR8wYbFLi8YZXb7GkUEeSn2PsjrKGiQjtpXw1RAyanCagr5vlg8CicZg1HuhCHWf/RYFWM3EBbVz+uFZPR3BqTgbvBhWYXRJaISwssvxotppe0ikevnEgaBYrflB2P+PVrwPTZ7f36HQcn4ifY1WRJQ4qRaUxdYEfzCBgwYJKoZIhvcNAQcBMBQGCCqGSIb3DQMHBAhRdscgRV3wmYBg3j/T1aEnO3wLWCRpgZa16MWqmfQPuansKHjLwbZjTpeirqUAQpZVyXdK/w4gKlK+t1heNsNo1Wwqu+Y47bSAX1k9Ud7+Ed2oETDI7724IJ213YeGxvu4Ngcf2eHW+FRK"

Это — пример ключа шифрования, который используется для доступа к учетной записи хранения.

### <a name="unable-to-access-storage-after-changing-key"></a>Не удается получить доступ к хранилищу после изменения ключа

Если вы измените ключ для учетной записи хранения, HDInsight больше не сможет обращаться к этой учетной записи. HDInsight использует кэшированную копию ключа в core-site.xml для кластера. Эту копию нужно обновить в соответствии с новым ключом.

При повторном запуске действия сценария ключ __не__ обновляется, так как сценарий проверяет наличие записи для учетной записи хранения. Если запись уже существует, он не вносит какие-либо изменения.

Чтобы решить эту проблему, необходимо удалить существующую запись для учетной записи хранения. Чтобы удалить имеющуюся запись, выполните указанные ниже действия.

1. Откройте в браузере веб-интерфейс Ambari для кластера HDInsight. Значение URI — https://CLUSTERNAME.azurehdinsight.net. Замените __CLUSTERNAME__ именем кластера.

    При появлении запроса введите имя пользователя и пароль для входа в кластер по протоколу HTTP.

2. В списке служб в левой части страницы выберите __HDFS__. В центральной области откройте вкладку __Конфигурации__.

3. В поле __Фильтр__ введите значение __fs.azure.account__. Будут возвращены записи для всех дополнительных учетных записей хранения, добавленных в кластер. Есть два типа записей: __keyprovider__ и __key__. Оба содержат имя учетной записи хранения в имени ключа.

    Ниже представлены примеры записей для учетной записи хранения с именем __mystorage__:

        fs.azure.account.keyprovider.mystorage.blob.core.windows.net
        fs.azure.account.key.mystorage.blob.core.windows.net

4. Определив ключи для учетной записи хранения, используйте красный значок с минусом (-) справа от записи, чтобы удалить ее. Нажмите кнопку __Сохранить__, чтобы сохранить изменения.

5. Сохранив изменения, используйте действие скрипта, чтобы добавить учетную запись хранения и новое значение ключа кластера.

### <a name="poor-performance"></a>Низкая производительность

Если учетная запись хранения и кластер HDInsight расположены в разных регионах, это может негативно повлиять на производительность. Для доступа к данным в другом регионе сетевой трафик отправляется за пределы центра обработки данных Azure через общедоступный сегмент Интернета, что может приводить к задержкам.

> [!WARNING]
> Использование учетной записи хранения, размещенной в разных регионах с кластером HDInsight, не поддерживается.

### <a name="additional-charges"></a>Дополнительные расходы

Если учетная запись хранения и кластер HDInsight расположены в разных регионах, в ваш счет за использование Azure будет включена дополнительная плата за исходящий трафик. Когда данные покидают центр обработки данных, исходящий трафик тарифицируется. Эта плата взимается, даже если трафик предназначается для другого центра обработки данных Azure в другом регионе.

> [!WARNING]
> Использование учетной записи хранения, размещенной в разных регионах с кластером HDInsight, не поддерживается.

## <a name="next-steps"></a>Дополнительная информация

Вы узнали, как добавлять дополнительные учетные записи хранения Azure в существующий кластер HDInsight. Дополнительные сведения о действиях скриптов см. в статье [Настройка кластеров HDInsight под управлением Linux с помощью действия сценария](hdinsight-hadoop-customize-cluster-linux.md).
