---
title: Перенос данных кластера HBase в новую версию в Azure HDInsight | Документация Майкрософт
description: Сведения о перемещении данных кластеров HBase в новую версию.
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: ashishthaps
manager: jhubbard
editor: cgronlun
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.date: 01/22/2018
ms.author: ashishth
ms.openlocfilehash: 2ab0bc792777b73f878bc1728c0d8310ecf41167
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/14/2018
ms.locfileid: "34165100"
---
# <a name="migrate-an-hbase-cluster-to-a-new-version"></a>Перенос данных кластера HBase в новую версию

Кластеры на основе заданий, такие как Spark и Hadoop, можно быстро обновить (см. сведения в статье [Обновление кластера HDInsight до более новой версии](../hdinsight-upgrade-cluster.md)):

1. Создайте резервную копию временных (хранимых локально) данных.
2. Удалите существующий кластер.
3. Создайте кластер в той же подсети виртуальной сети.
4. Импортируйте временные данные.
5. Запустите задания и продолжите обработку в новом кластере.

Чтобы обновить кластер HBase, требуются некоторые дополнительные действия, описанные в этой статье.

> [!NOTE]
> Время простоя при обновлении должно быть минимальным, порядка нескольких минут. Простой связан с действиями по записи всех данных в памяти на диск, а также настройкой и перезапуском служб в новом кластере. Результаты будут различаться в зависимости от количества узлов, объема данных и других переменных.

## <a name="review-hbase-compatibility"></a>Проверка совместимости HBase

Перед обновлением HBase убедитесь, что версии HBase в исходном и конечном кластерах совместимы. Дополнительные сведения см. в статье [Что представляют собой компоненты и версии Hadoop, доступные в HDInsight?](../hdinsight-component-versioning.md)

> [!NOTE]
> Настоятельно рекомендуем изучить матрицу совместимости версий в [руководстве по HBase](https://hbase.apache.org/book.html#upgrading).

Ниже приведен пример матрицы совместимости версий ("Да" — совместимость, "Нет" — возможная несовместимость).

| Тип совместимости | Основной номер версии| Дополнительный номер версии | Исправление |
| --- | --- | --- | --- |
| Совместимость клиента и сервера на уровне передачи данных | Нет | Да | Да |
| Совместимость серверов | Нет | Да | Да |
| Совместимость форматов файлов | Нет | Да | Да |
| Совместимость API клиента | Нет | Да | Да |
| Совместимость клиента на уровне двоичного кода | Нет | Нет | Да |
| **Ограниченная совместимость API на стороне сервера** |  |  |  |
| Stable | Нет | Да | Да |
| Развитие | Нет | Нет | Да |
| Работает неустойчиво | Нет | Нет | Нет |
| Совместимость зависимостей | Нет | Да | Да |
| Совместимость операций | Нет | Нет | Да |

> [!NOTE]
> Все критические несовместимости должны быть описаны в заметках о выпуске версии HBase.

## <a name="upgrade-with-same-hbase-major-version"></a>Обновление с тем же основным номером версии HBase

Ниже описан сценарий обновления HDInsight 3.4 до 3.6 (обе версии предоставляются с Apache HBase 1.1.2) с той же основной версией HBase. Другие обновления версий схожи при условии, что между исходной версией и версией назначения нет никаких проблем совместимости.

1. Убедитесь, что приложение совместимо с новой версией, обратившись к матрице совместимости HBase и заметкам о выпуске. Протестируйте приложение в кластере, запустив целевые версии HDInsight и HBase.

2. [Настройте новый кластер назначения HDInsight](../hdinsight-hadoop-provision-linux-clusters.md), используя ту же учетную запись хранения, но другое имя контейнера:

    ![Используйте ту же учетную запись хранения, но создайте другой контейнер](./media/apache-hbase-migrate-new-version/same-storage-different-container.png)

3. Запишите данные исходного кластера HBase на диск. Это кластер, для которого выполняется обновление. HBase записывает входящие данные в хранилище в памяти, которое называется _memstore_. Когда memstore достигает определенного размера, оно переносится на диск для долговременного хранения в учетной записи хранения кластера. При удалении старого кластера memstore очищается, что может привести к потере данных. Чтобы вручную перенести данные memstore для каждой таблицы на диск, выполните следующий скрипт. Последнюю версию этого скрипта можно найти на [GitHub](https://raw.githubusercontent.com/Azure/hbase-utils/master/scripts/flush_all_tables.sh) для Azure.

    ```bash
    #!/bin/bash
    
    #-------------------------------------------------------------------------------#
    # SCRIPT TO FLUSH ALL HBASE TABLES.
    #-------------------------------------------------------------------------------#
    
    LIST_OF_TABLES=/tmp/tables.txt
    HBASE_SCRIPT=/tmp/hbase_script.txt
    TARGET_HOST=$1
    
    usage ()
    {
        if [[ "$1" == "-h" ]] || [[ "$1" == "--help" ]]
        then
            cat << ...
    
    Usage: 
    
    $0 [hostname]
    
    Providing hostname is optional and not required when the script is executed within HDInsight cluster with access to 'hbase shell'.
    
    However hostname should be provided when executing the script as a script-action from HDInsight portal.
    
    For Example:
    
        1.  Executing script inside HDInsight cluster (where 'hbase shell' is 
            accessible):
    
            $0 
    
            [No need to provide hostname]
    
        2.  Executing script from HDinsight Azure portal:
    
            Provide Script URL.
    
            Provide hostname as a parameter (i.e. hn0, hn1 or wn2 etc.).
    ...
            exit
        fi
    }
    
    validate_machine ()
    {
        THIS_HOST=`hostname`
    
        if [[ ! -z "$TARGET_HOST" ]] && [[ $THIS_HOST  != $TARGET_HOST* ]]
        then
            echo "[INFO] This machine '$THIS_HOST' is not the right machine ($TARGET_HOST) to execute the script."
            exit 0
        fi
    }
    
    get_tables_list ()
    {
    hbase shell << ... > $LIST_OF_TABLES 2> /dev/null
        list
        exit
    ...
    }
    
    add_table_for_flush ()
    {
        TABLE_NAME=$1
        echo "[INFO] Adding table '$TABLE_NAME' to flush list..."
        cat << ... >> $HBASE_SCRIPT
            flush '$TABLE_NAME'
    ...
    }
    
    clean_up ()
    {
        rm -f $LIST_OF_TABLES
        rm -f $HBASE_SCRIPT
    }
    
    ########
    # MAIN #
    ########
    
    usage $1
    
    validate_machine
    
    clean_up
    
    get_tables_list
    
    START=false
    
    while read LINE 
    do 
        if [[ $LINE == TABLE ]] 
        then
            START=true
            continue
        elif [[ $LINE == *row*in*seconds ]]
        then
            break
        elif [[ $START == true ]]
        then
            add_table_for_flush $LINE
        fi
    
    done < $LIST_OF_TABLES
    
    cat $HBASE_SCRIPT
    
    hbase shell $HBASE_SCRIPT << ... 2> /dev/null
    exit
    ...
    
    ```
    
4. Остановите прием данных в старом кластере HBase.
5. Чтобы убедиться, что все последние данные в хранилище memstore записаны на диск, еще раз выполните предыдущий скрипт.
6. Войдите в Ambari на старом кластере (https://OLDCLUSTERNAME.azurehdidnsight.net) и остановите работу служб HBase. При появлении запроса подтвердите, что вы хотите остановить службы, и установите флажок, чтобы включить режим обслуживания для HBase. Подробные сведения о подключении к платформе Ambari и ее использовании см. в статье [Управление кластерами HDInsight с помощью веб-интерфейса Ambari](../hdinsight-hadoop-manage-ambari.md).

    ![В Ambari перейдите на вкладку Services (Службы), выберите "HBase" в меню слева, а затем в разделе Service Actions (Действия службы) выберите Stop (Остановить).](./media/apache-hbase-migrate-new-version/stop-hbase-services.png)

    ![Проверьте, установлен ли флажок Turn On Maintenance Mode for HBase (Включить режим обслуживания HBase), а затем подтвердите остановку.](./media/apache-hbase-migrate-new-version/turn-on-maintenance-mode.png)

7. Войдите в Ambari на новом кластере HDInsight. Измените параметр HDFS `fs.defaultFS`, чтобы указать имя контейнера, используемого в исходном кластере. Этот параметр находится в разделе **HDFS > Configs (Конфигурации) > Advanced (Расширенные) > Advanced core-site (Расширенный основной сайт)**.

    ![В Ambari выберите вкладку Services (Службы), в меню слева выберите HDFS, перейдите на вкладку Configs (Конфигурации) и выберите вкладку Advanced (Расширенные) в нижней области.](./media/apache-hbase-migrate-new-version/hdfs-advanced-settings.png)

    ![Изменение имени контейнера в Ambari](./media/apache-hbase-migrate-new-version/change-container-name.png)

8. Сохраните изменения.
9. Перезапустите все необходимые службы, как указано в Ambari.
10. Направьте приложение в новый кластер.

    > [!NOTE]
    > Статическое DNS-имя вашего приложения изменится при обновлении. Вместо того чтобы жестко запрограммировать это DNS-имя, можно настроить запись CNAME в параметрах DNS доменного имени, которая указывает на имя кластера. Другой вариант — использовать файл конфигурации для приложения, которое можно обновить без повторного развертывания.

11. Запустите прием данных, чтобы убедиться, что все работает правильно.
12. Если новый кластер подходит, удалите исходный кластер.

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о HBase и обновлении кластеров HDInsight см. в следующих статьях:

* [Обновление кластера HDInsight до более новой версии](../hdinsight-upgrade-cluster.md)
* [Управление кластерами HDInsight с помощью веб-интерфейса Ambari](../hdinsight-hadoop-manage-ambari.md)
* [Что представляют собой компоненты и версии Hadoop, доступные в HDInsight?](../hdinsight-component-versioning.md)
* [Оптимизация конфигураций с использованием Ambari](../hdinsight-changing-configs-via-ambari.md#hbase-optimization-with-the-ambari-web-ui)
