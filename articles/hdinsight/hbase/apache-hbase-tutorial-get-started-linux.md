---
title: Начало работы с примером HBase в HDInsight в Azure | Документация Майкрософт
description: Ознакомьтесь с примером Apache HBase, чтобы начать использовать Hadoop в HDInsight. Создание таблиц из оболочки HBase и обращение к ним с помощью Hive.
keywords: hbasecommand, пример hbase
services: hdinsight
documentationcenter: ''
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 4d6a2658-6b19-4268-95ee-822890f5a33a
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: na
ms.topic: conceptual
ms.date: 02/22/2018
ms.author: jgao
ms.openlocfilehash: f85772aa74e876764181330b6d29c4f8d2b48508
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/18/2018
---
# <a name="get-started-with-an-apache-hbase-example-in-hdinsight"></a>Начало работы с примером Apache HBase в HDInsight

Узнайте, как создавать кластеры HBase в HDInsight, создавать таблицы HBase и запрашивать таблицы с помощью Hive. Для получения общих сведений по HBase обратитесь к разделу [Что такое HBase в HDInsight][hdinsight-hbase-overview].

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="prerequisites"></a>предварительным требованиям
Для работы с этим примером HBase вам понадобится:

* **Подписка Azure**. См. страницу [бесплатной пробной версии Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* [Secure Shell(SSH).](../hdinsight-hadoop-linux-use-ssh-unix.md) 
* [curl](http://curl.haxx.se/download.html).

## <a name="create-hbase-cluster"></a>Создание кластера HBase
В следующей процедуре используется шаблон Azure Resource Manager, чтобы создать кластер HBase и зависимую учетную запись хранения Azure по умолчанию. Описание параметров, используемых в процедуре, и других методов создания кластеров см. в статье [Создание кластеров Hadoop под управлением Linux в HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).

1. Щелкните следующее изображение, чтобы открыть шаблон на портале Azure. Шаблон находится в [шаблонах быстрого запуска Azure](https://azure.microsoft.com/resources/templates/).
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-hbase-linux%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hbase-tutorial-get-started-linux/deploy-to-azure.png" alt="Deploy to Azure"></a>
2. В колонке **Настраиваемое развертывание** укажите следующие значения.
   
   * **Подписка**: выберите подписку Azure, которая используется для создания этого кластера.
   * **Группа ресурсов**: создайте группу ресурсов Azure или выберите существующую.
   * **Расположение**: укажите расположение группы ресурсов. 
   * **Имя кластера**: укажите имя кластера HBase.
   * **Имя для входа и пароль кластера**: имя для входа по умолчанию — **admin**.
   * **Имя пользователя SSH и пароль**: по умолчанию используется имя **sshuser**.  Это имя можно изменить.
     
     Все остальные параметры являются необязательными.  
     
     У каждого кластера есть зависимость учетной записи хранения для службы хранилища Azure. После удаления кластера данные сохраняются в учетной записи хранения. Имя учетной записи хранения в кластере — это имя кластера, к которому добавлено слово store. Это прописано в разделе переменных в коде шаблона.
3. Установите флажок **Я принимаю указанные выше условия** и нажмите кнопку **Приобрести**. Процесс создания кластера занимает около 20 минут.

> [!NOTE]
> После удаления кластера HBase можно создать другой кластер HBase, использовав тот же контейнер BLOB-объектов по умолчанию. Новый кластер получит таблицы HBase, созданные в исходном кластере. Перед удалением кластера рекомендуется отключить таблицы HBase, чтобы избежать несогласованности.
> 
> 

## <a name="create-tables-and-insert-data"></a>Создание таблиц и вставка данных
Для подключения к кластерам HBase можно использовать протокол SSH, а для создания таблиц HBase, вставки данных и создания запросов к данным — оболочку HBase. Дополнительные сведения см. в статье [Использование SSH с Hadoop на основе Linux в HDInsight из Linux, Unix или OS X](../hdinsight-hadoop-linux-use-ssh-unix.md).

Для большинства пользователей данные отображаются в табличном формате:

![Табличные данные HDInsight HBase][img-hbase-sample-data-tabular]

В HBase (который является реализацией BigTable) те же данные выглядят следующим образом:

![Данные BigTable HDInsight HBase][img-hbase-sample-data-bigtable]


**Использование оболочки HBase**

1. Из SSH выполните следующую команду HBase:
   
    ```bash
    hbase shell
    ```

2. Создайте HBase с двумя столбцами:

    ```hbaseshell   
    create 'Contacts', 'Personal', 'Office'
    list
    ```
3. Вставьте какие-либо данные:
    
    ```hbaseshell   
    put 'Contacts', '1000', 'Personal:Name', 'John Dole'
    put 'Contacts', '1000', 'Personal:Phone', '1-425-000-0001'
    put 'Contacts', '1000', 'Office:Phone', '1-425-000-0002'
    put 'Contacts', '1000', 'Office:Address', '1111 San Gabriel Dr.'
    scan 'Contacts'
    ```
   
    ![Оболочка HDInsight Hadoop HBase][img-hbase-shell]
4. Получите одну строку
   
    ```hbaseshell
    get 'Contacts', '1000'
    ```
   
    Вы увидите те же результаты, что и при использовании команды сканирования, так как в таблице есть только одна строка.
   
    Дополнительную информацию о схеме таблицы HBase см. в статье с [общими сведениями о проектировании схемы HBase][hbase-schema]. Дополнительные команды HBase см. в [справочнике по Apache HBase][hbase-quick-start].
5. Выйдите из оболочки
   
    ```hbaseshell
    exit
    ```

**Для массовой загрузки данных в таблицу контактов HBase**

HBase включает несколько методов загрузки данных в таблицы.  Для получения дополнительных сведений обратитесь к разделу [Массовая загрузка](http://hbase.apache.org/book.html#arch.bulk.load).

Пример файла данных находится в общедоступном контейнере больших двоичных объектов *wasb://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt*.  Файл данных содержит:

    8396    Calvin Raji      230-555-0191    230-555-0191    5415 San Gabriel Dr.
    16600   Karen Wu         646-555-0113    230-555-0192    9265 La Paz
    4324    Karl Xie         508-555-0163    230-555-0193    4912 La Vuelta
    16891   Jonn Jackson     674-555-0110    230-555-0194    40 Ellis St.
    3273    Miguel Miller    397-555-0155    230-555-0195    6696 Anchor Drive
    3588    Osa Agbonile     592-555-0152    230-555-0196    1873 Lion Circle
    10272   Julia Lee        870-555-0110    230-555-0197    3148 Rose Street
    4868    Jose Hayes       599-555-0171    230-555-0198    793 Crawford Street
    4761    Caleb Alexander  670-555-0141    230-555-0199    4775 Kentucky Dr.
    16443   Terry Chander    998-555-0171    230-555-0200    771 Northridge Drive

При желании вы можете создать текстовый файл и отправить его в свою учетную запись хранения. Указания см. в статье [Отправка данных для заданий Hadoop в HDInsight][hdinsight-upload-data].

> [!NOTE]
> Эта процедура использует таблицу контактов HBase, созданную в предыдущей процедуре.
> 

1. Из SSH выполните указанную ниже команду, чтобы преобразовать файл данных в StoreFiles и сохранить его по относительному пути, указанному в Dimporttsv.bulk.output.  Если вы используете оболочку HBase, выйдите из нее, выполнив команду exit.

    ```bash   
    hbase org.apache.hadoop.hbase.mapreduce.ImportTsv -Dimporttsv.columns="HBASE_ROW_KEY,Personal:Name,Personal:Phone,Office:Phone,Office:Address" -Dimporttsv.bulk.output="/example/data/storeDataFileOutput" Contacts wasb://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt
    ```

2. Для передачи данных из /example/data/storeDataFileOutput в таблицу HBase выполните следующую команду:
   
    ```bash
    hbase org.apache.hadoop.hbase.mapreduce.LoadIncrementalHFiles /example/data/storeDataFileOutput Contacts
    ```

3. Откройте оболочку HBase и выполните команду сканирования для получения списка содержимого таблицы.

## <a name="use-hive-to-query-hbase"></a>Использование Hive для создания запросов к HBase

Можно запросить данные в таблицах HBase с помощью Hive. В этом разделе будет создана таблица Hive, которая сопоставляется с таблицей HBase и использует ее для формирования запросов к данным в таблице HBase.

1. Откройте **PuTTY** и подключитесь к кластеру.  См. инструкции в приведенной выше процедуре.
2. В сеансе SSH используйте следующую команду, чтобы запустить Beeline.

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -n admin
    ```

    Дополнительные сведения о Beeline см. в статье [Использование Hive с Hadoop в HDInsight с применением Beeline](../hadoop/apache-hadoop-use-hive-beeline.md).
       
3. Выполните приведенный ниже скрипт HiveQL, чтобы создать таблицу Hive, сопоставляемую с таблицей HBase. Перед выполнением этого оператора убедитесь, что вы создали упомянутый в этом учебнике пример таблицы с помощью оболочки HBase.

    ```hiveql   
    CREATE EXTERNAL TABLE hbasecontacts(rowkey STRING, name STRING, homephone STRING, officephone STRING, officeaddress STRING)
    STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'
    WITH SERDEPROPERTIES ('hbase.columns.mapping' = ':key,Personal:Name,Personal:Phone,Office:Phone,Office:Address')
    TBLPROPERTIES ('hbase.table.name' = 'Contacts');
    ```

4. Запустите приведенный ниже скрипт HiveQL, чтобы запросить данные в таблице HBase.

    ```hiveql   
    SELECT count(rowkey) FROM hbasecontacts;
    ```

## <a name="use-hbase-rest-apis-using-curl"></a>Использование API REST для HBase

REST API защищен с помощью [обычной проверки подлинности](http://en.wikipedia.org/wiki/Basic_access_authentication). Чтобы обеспечить безопасную отправку учетных данных на сервер, все запросы следует отправлять с помощью протокола HTTPS.

2. Для получения списка имеющихся таблиц HBase используйте следующую команду:

    ```bash
    curl -u <UserName>:<Password> \
    -G https://<ClusterName>.azurehdinsight.net/hbaserest/
    ```

3. Для создания новой таблицы HBase с двумя семействами столбцов используйте следующую команду:

    ```bash   
    curl -u <UserName>:<Password> \
    -X PUT "https://<ClusterName>.azurehdinsight.net/hbaserest/Contacts1/schema" \
    -H "Accept: application/json" \
    -H "Content-Type: application/json" \
    -d "{\"@name\":\"Contact1\",\"ColumnSchema\":[{\"name\":\"Personal\"},{\"name\":\"Office\"}]}" \
    -v
    ```

    Схема предоставляется в формате JSON.
4. Чтобы вставить какие-либо данные, используйте следующую команду:

    ```bash   
    curl -u <UserName>:<Password> \
    -X PUT "https://<ClusterName>.azurehdinsight.net/hbaserest/Contacts1/false-row-key" \
    -H "Accept: application/json" \
    -H "Content-Type: application/json" \
    -d "{\"Row\":[{\"key\":\"MTAwMA==\",\"Cell\": [{\"column\":\"UGVyc29uYWw6TmFtZQ==\", \"$\":\"Sm9obiBEb2xl\"}]}]}" \
    -v
    ```
   
    Необходимо закодировать значения, указанные в параметре -d, используя кодировку base64. Ознакомьтесь со следующим примером:
   
   * MTAwMA==: 1000;
   * UGVyc29uYWw6TmFtZQ==: Personal:Name
   * Sm9obiBEb2xl: John Dole.
     
     [false-row-key](https://hbase.apache.org/apidocs/org/apache/hadoop/hbase/rest/package-summary.html#operation_cell_store_single) позволяет вставить несколько (пакетных) значений.
5. Для получения строки используйте следующую команду:
   
    ```bash 
    curl -u <UserName>:<Password> \
    -X GET "https://<ClusterName>.azurehdinsight.net/hbaserest/Contacts1/1000" \
    -H "Accept: application/json" \
    -v
    ```

Дополнительные сведения см. в [справочнике по Apache HBase](https://hbase.apache.org/book.html#_rest).

> [!NOTE]
> Thrift не поддерживается HBase в HDInsight.
>
> При использовании Curl или любых других средств связи REST с WebHCat нужно выполнять аутентификацию запросов с помощью пароля и имени пользователя администратора кластера HDInsight. Имя кластера необходимо также использовать в составе универсального кода ресурса (URI), используемого для отправки запросов на сервер.
> 
>   
>        curl -u <UserName>:<Password> \
>        -G https://<ClusterName>.azurehdinsight.net/templeton/v1/status
>   
>    Вы должны получить ответ, аналогичный приведенному ниже.
>   
>        {"status":"ok","version":"v1"}
   


## <a name="check-cluster-status"></a>Проверка состояния кластера
HBase на HDInsight поставляется с веб-интерфейсом для наблюдения за кластерами. С помощью веб-интерфейса вы можете запросить статистику или сведения о регионах.

**Доступ к основному интерфейсу HBase**

1. Войдите в веб-интерфейс Ambari по ссылке https://&lt;имя_кластера>.azurehdinsight.net.
2. В меню слева щелкните **HBase**.
3. В верхней части страницы щелкните **Быстрые ссылки**, выберите ссылку на активный узел Zookeeper, а затем щелкните **HBase Master UI** (Основной интерфейс HBase).  Интерфейс откроется в новой вкладке браузера.

  ![Основной интерфейс HDInsight HBase](./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-hmaster-ui.png)

  Основной интерфейс HBase состоит из таких разделов:

  - региональные серверы;
  - главные узлы резервного копирования;
  - таблицы;
  - задачи;
  - атрибуты ПО.

## <a name="delete-the-cluster"></a>Удаление кластера
Перед удалением кластера рекомендуется отключить таблицы HBase, чтобы избежать несогласованности.

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="troubleshoot"></a>Устранение неполадок

Если при создании кластеров HDInsight возникли проблемы, см. раздел [Создание кластеров](../hdinsight-administer-use-portal-linux.md#create-clusters).

## <a name="next-steps"></a>Дополнительная информация
Из этой статьи вы узнали, как создать кластер HBase, а также как создавать таблицы и просматривать данные в них из оболочки HBase. Вы также узнали, как использовать Hive для запроса данных из таблиц HBase и как использовать интерфейсы REST API на C# для HBase для создания таблицы HBase и извлечения данных из нее.

Дополнительные сведения см. на следующих ресурсах:

* [Что такое HBase в HDInsight][hdinsight-hbase-overview]. HBase — это база данных NoSQL с открытым исходным кодом Apache, разработанная в рамках проекта Hadoop, которая обеспечивает произвольный доступ и строгую согласованность больших объемов неструктурированных и частично структурированных данных.

[hdinsight-manage-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-upload-data]: ../hdinsight-upload-data.md
[hbase-reference]: http://hbase.apache.org/book.html#importtsv
[hbase-schema]: http://0b4af6cdc2f0c5998459-c0245c5c937c5dedcca3f1764ecc9b2f.r43.cf2.rackcdn.com/9353-login1210_khurana.pdf
[hbase-quick-start]: http://hbase.apache.org/book.html#quickstart





[hdinsight-hbase-overview]:apache-hbase-overview.md
[hdinsight-hbase-provision-vnet]:apache-hbase-provision-vnet.md
[hdinsight-versions]: hdinsight-component-versioning.md
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-portal]: https://portal.azure.com/
[azure-create-storageaccount]: http://azure.microsoft.com/documentation/articles/storage-create-storage-account/

[img-hbase-shell]: ./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-shell.png
[img-hbase-sample-data-tabular]: ./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-contacts-tabular.png
[img-hbase-sample-data-bigtable]: ./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-contacts-bigtable.png
