---
title: "Создание кластеров Azure HDInsight, использующих Data Lake Store, с помощью портала Azure | Документация Майкрософт"
description: "Создание и использование кластеров HDInsight для работы с Azure Data Lake Store с помощью портала Azure."
services: data-lake-store,hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: a8c45a83-a8e3-4227-8b02-1bc1e1de6767
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/04/2017
ms.author: nitinme
ms.translationtype: Human Translation
ms.sourcegitcommit: e72275ffc91559a30720a2b125fbd3d7703484f0
ms.openlocfilehash: 37211b961644fdc5567147cde946f86afb9b1aa9
ms.contentlocale: ru-ru
ms.lasthandoff: 05/05/2017


---
# <a name="create-hdinsight-clusters-with-data-lake-store-by-using-the-azure-portal"></a>Создание кластеров HDInsight, использующих Data Lake Store, с помощью портала Azure
> [!div class="op_single_selector"]
> * [Использование портала Azure](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [Использование PowerShell (для хранилища по умолчанию)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [Использование PowerShell (для дополнительного хранилища)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Использование Resource Manager](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
>
>

В этой статье описывается, как с помощью портала Azure создать кластер HDInsight с доступом к Azure Data Lake Store. В поддерживаемых типах кластеров можно использовать Data Lake Store в качестве учетной записи хранения по умолчанию или дополнительной учетной записи хранения.

Если Data Lake Store используется в качестве дополнительного хранилища, то учетной записью хранения по умолчанию для кластеров остается хранилище BLOB-объектов Azure, а файлы кластера (например, журналы) по-прежнему записываются в хранилище по умолчанию. Однако данные, требующие обработки, могут храниться в учетной записи Data Lake Store. Использование Data Lake Store в качестве дополнительной учетной записи хранения не влияет на производительность либо возможность выполнять чтение или запись в хранилище из кластера.

Ниже приведены некоторые важные сведения об использовании HDInsight с Data Lake Store.

* Возможность создавать кластеры HDInsight, использующие Data Lake Store в качестве хранилища по умолчанию, поддерживается для версии HDInsight 3.5.

* Возможность создавать кластеры HDInsight, использующие Data Lake Store в качестве хранилища по умолчанию, *не поддерживается* для кластеров HDInsight уровня "Премиум".

* Возможность создавать кластеры HDInsight, использующие Data Lake Store в качестве дополнительного хранилища, поддерживается для версий HDInsight 3.2, 3.4 и 3.5.

* В кластерах HBase (Windows и Linux) Data Lake Store *невозможно* использовать ни как хранилище по умолчанию, ни как дополнительное хранилище.

* Data Lake Store могут использовать кластеры Storm (Windows и Linux) для записи данных из топологии Storm. Data Lake Store также может использоваться для хранения эталонных данных, которые затем можно будет считать с помощью топологии Storm. Дополнительные сведения см. в разделе [Использование хранилища озера данных в топологии Storm](#use-data-lake-store-in-a-storm-topology).


## <a name="prerequisites"></a>Предварительные требования
Прежде чем приступить к изучению этого руководства, убедитесь, что выполнены следующие требования.

* **Подписка Azure**. Перейдите на сайт [бесплатной пробной версии Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Учетная запись Azure Data Lake Store.** Следуйте инструкциям в разделе [Начало работы с хранилищем озера данных Azure с помощью портала Azure](data-lake-store-get-started-portal.md).

* **Субъект-служба Azure Active Directory**. В этом руководстве приведены инструкции по созданию субъекта-службы в Azure Active Directory (Azure AD). Чтобы создать субъект-службу, необходимо быть администратором Azure AD. Если вы являетесь администратором, то можете пропустить это предварительное требование и продолжить работу с руководством.

    >[!NOTE]
    >Создать субъект-службу может только администратор Azure AD. Администратор Azure AD должен сначала создать субъект-службу, после чего вы сможете создать кластер HDInsight, использующий Data Lake Store. При создании субъекта-службы также необходимо использовать сертификат, как описано в разделе [Создание субъекта-службы с использованием сертификата](../azure-resource-manager/resource-group-authenticate-service-principal.md#create-service-principal-with-certificate-from-certificate-authority).
    >

## <a name="create-an-hdinsight-cluster-with-access-to-a-data-lake-store"></a>Создание кластера HDInsight с доступом к Data Lake Store
В этом разделе вы создадите кластер Hadoop в HDInsight, использующий Data Lake Store в качестве хранилища по умолчанию или дополнительного хранилища.

### <a name="create-a-cluster-with-data-lake-store-as-default-storage"></a>Создание кластера HDInsight, использующего Data Lake Store в качестве хранилища по умолчанию

>[!NOTE]
>Этот параметр можно использовать только с кластерами версии HDInsight 3.5 (выпуск "Стандартный"). В кластерах HDInsight 3.5 этот параметр недоступен для типа кластеров HBase.

1. Войдите на [портал Azure](https://portal.azure.com).

2. Выполните действия, описанные в разделе [Создание кластеров под управлением Linux в HDInsight с помощью портала Azure](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md), чтобы начать подготовку кластера HDInsight.

3. В колонке **Хранилище** в разделе **Тип первичного хранилища** выберите **Data Lake Store**.

4. Выберите существующую учетную запись Data Lake Store и укажите путь к корневой папке, в которой будут храниться связанные с кластером файлы.

    На снимке экрана выше показан путь к корневой папке /clusters/myhdiadlcluster, где *myhdiadlcluster* — это имя создаваемого кластера. Убедитесь, что папка */clusters* существует в учетной записи Data Lake Store. Папка *myhdiadlcluster* создается вместе с кластером. Аналогично, если корневая папка — это */hdinsight/clusters/data/myhdiadlcluster*, убедитесь, что папка */hdinsight/clusters/data/* существует в учетной записи Data Lake Store.

    ![Добавление субъекта-службы в кластер HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.adls.storage.png "Добавление субъекта-службы в кластер HDInsight")

5. Щелкните **Доступ к Data Lake Store** для настройки доступа между учетной записью Data Lake Store и кластером HDInsight. Инструкции доступны в разделе [Создание кластеров HDInsight, использующих Data Lake Store, с помощью портала Azure](#configure-access-between-hdinsight-cluster-and-data-lake-store).


### <a name="create-a-cluster-with-data-lake-store-as-additional-storage"></a>Создание кластера HDInsight, использующего Data Lake Store в качестве дополнительного хранилища

1. Войдите на [портал Azure](https://portal.azure.com).

2. Выполните действия, описанные в разделе [Создание кластеров под управлением Linux в HDInsight с помощью портала Azure](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md), чтобы начать подготовку кластера HDInsight.

3. В колонке **Хранилище** в разделе **Тип первичного хранилища** выберите **Служба хранилища Azure**.

4. В разделе **Метод выбора** выполните одно из следующих действий.

    * Выберите **Мои подписки**, чтобы указать учетную запись хранения, которая является частью вашей подписки Azure, а затем выберите учетную запись хранения.

    * Чтобы указать учетную запись хранения, не входящую в вашу подписку Azure, выберите **Ключ доступа**, а затем введите данные внешней учетной записи хранения.

5. В поле **Контейнер по умолчанию** можно оставить имя контейнера по умолчанию, предлагаемое порталом, или указать собственное значение.

    ![Добавление субъекта-службы в кластер HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.png "Добавление субъекта-службы в кластер HDInsight")

6. Если в качестве хранилища по умолчанию используется хранилище BLOB-объектов, то Data Lake Store можно использовать как дополнительное хранилище кластера. Для этого щелкните **Доступ к Data Lake Store**, чтобы настроить доступ между учетной записью Data Lake Store и кластером HDInsight. Инструкции доступны в разделе [Создание кластеров HDInsight, использующих Data Lake Store, с помощью портала Azure](#configure-access-between-hdinsight-cluster-and-data-lake-store).

## <a name="configure-access-between-hdinsight-cluster-and-data-lake-store"></a>Настройка доступа между кластером HDInsight и Data Lake Store

В этом разделе вы настроите доступ между кластерами HDInsight и Data Lake Store с помощью субъекта-службы Azure Active Directory.

1. В колонке **Доступ к Data Lake Store** укажите, следует ли использовать новый или существующий субъект-службу. Это действие создает субъект-службу. Чтобы использовать существующий субъект-службу, сразу переходите к следующему шагу.

    а. В колонке **Доступ к Data Lake Store** щелкните **Создать** и щелкните **Субъект-служба**.

    b. В колонке **Создание субъекта-службы** введите необходимые значения.  

    В. Щелкните **Создать**. Сертификат и приложение Azure AD создаются автоматически.

    ![Добавление субъекта-службы в кластер HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.2.png "Добавление субъекта-службы в кластер HDInsight")

    Вы также можете **скачать сертификат**, связанный с созданным субъектом-службой. Скачать сертификат удобно, если вы холите использовать этот же субъект-службу для создания дополнительных кластеров HDInsight. Нажмите кнопку **Выбрать**.

2. Чтобы использовать существующий субъект-службу, выполните действия, описанные ниже.

    а. В колонке **Доступ к Data Lake Store** щелкните **Использовать существующий** и щелкните **Субъект-служба**.

    b. В колонке **Выберите субъект-службу** найдите существующий субъект-службу. Щелкните нужный субъект-службу и нажмите кнопку **Выбрать**.

    c. В колонке **Доступ к Data Lake Store** передайте связанный с выбранным субъектом-службой сертификат (PFX-файл) и введите пароль этого сертификата.

    ![Добавление субъекта-службы в кластер HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.5.png "Добавление субъекта-службы в кластер HDInsight")

7. В колонке **Доступ к Data Lake Store** щелкните **Доступ**. По умолчанию открывается колонка **Выбор разрешений для файла**. В ней отображены все учетные записи Data Lake Store в вашей подписке.

8. Выберите учетную запись Data Lake Store, которую требуется связать с кластером.

    **При использовании Data Lake Store в качестве хранилища по умолчанию** следует назначить разрешения на двух уровнях.

    а. Во-первых, следует назначить разрешение на уровне корня учетной записи Data Lake Store. Для этого наведите указатель мыши (но не щелкайте) на имя учетной записи Data Lake Store, чтобы отобразился флажок. Установите флажок.

    ![Добавление субъекта-службы в кластер HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3.png "Добавление субъекта-службы в кластер HDInsight")

    b. Во-вторых, следует назначить разрешение для корня хранилища кластера HDInsight. В соответствии с предыдущим снимком экрана, корнем хранилища кластера является папка **/clusters**, указанная при выборе Data Lake Store в качестве хранилища по умолчанию.

    ![Добавление субъекта-службы в кластер HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3.add.png "Добавление субъекта-службы в кластер HDInsight")

    **При использовании Data Lake Store в качестве дополнительного хранилища** необходимо назначить разрешение только для папки, к которой нужен доступ из кластера HDInsight. Например, на снимке экрана ниже предоставляется доступ только к папке **hdiaddonstorage** в учетной записи Data Lake Store.

    ![Назначение разрешений субъекта-службы для кластера HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3-1.png "Назначение разрешений субъекта-службы для кластера HDInsight")

9. Для всех выбранных учетных записей и путей выберите разрешения (на чтение, запись или выполнение), а также укажите, будут ли эти разрешения применяться рекурсивно к дочерним элементам. Затем нажмите кнопку **Выбрать**.

11. В окне **Назначение выбранных разрешений** щелкните **Запустить**, чтобы назначить субъекту-службе Azure Active Directory разрешения для выбранной учетной записи, файлов или папок.

    ![Назначение разрешений субъекта-службы для кластера HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3-2.png "Назначение разрешений субъекта-службы для кластера HDInsight")

12. Назначив разрешения, нажимайте кнопку **Готово** в каждой открытой колонке, чтобы вернуться в колонку **Доступ к Data Lake Store**.

13. В колонке **Доступ к Data Lake Store** нажмите кнопку **Выбрать** и продолжайте создание кластера, как описано в статье [Создание кластеров под управлением Linux в HDInsight с помощью портала Azure](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="verify-cluster-set-up"></a>Проверка настроек кластера

После завершения настройки кластера проверьте результат в колонке кластера, выполнив одно или оба следующих действия.

* Чтобы проверить, является ли указанная вами учетная запись Data Lake Store связанным хранилищем для кластера, щелкните **Учетные записи хранения** в левой области.

    ![Добавление субъекта-службы в кластер HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.6-1.png "Добавление субъекта-службы в кластер HDInsight")

* Чтобы проверить, правильно ли связан субъект-служба с кластером HDInsight, щелкните **Доступ к Data Lake Store** в левой области.

    ![Добавление субъекта-службы в кластер HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.6.png "Добавление субъекта-службы в кластер HDInsight")


## <a name="examples"></a>Примеры

Настроив кластер для работы с Data Lake Store в качестве хранилища, вы можете изучить следующие примеры использования кластера HDInsight для анализа данных, хранимых в Data Lake Store.

### <a name="run-a-hive-query-against-data-in-a-data-lake-store-as-primary-storage"></a>Выполнение запроса Hive к данным, хранящимся в Data Lake Store (основное хранилище)

Чтобы выполнить запрос Hive, используйте интерфейс представлений Hive, доступный на портале Ambari. Инструкции по использованию представлений Ambari Hive см. в статье [Использование представления Hive с Hadoop в HDInsight](../hdinsight/hdinsight-hadoop-use-hive-ambari-view.md).

При работе с данными в Data Lake Store изменить можно лишь несколько строк.

Например, если вы используете созданный кластер, в котором основным хранилищем является Data Lake Store, то путь к данным будет следующим: *adl://<учентая_запись_Data_Lake_Store>/azuredatalakestore.net/<путь_к_файлу>*. Запрос Hive для создания таблицы из демонстрационных данных, хранимых в учетной записи Data Lake Store, будет выглядеть следующим образом.

    CREATE EXTERNAL TABLE websitelog (str string) LOCATION 'adl://hdiadlsstorage.azuredatalakestore.net/clusters/myhdiadlcluster/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/'

Описание
* `adl://hdiadlstorage.azuredatalakestore.net/` — корневой элемент учетной записи Data Lake Store;
* `/clusters/myhdiadlcluster` — корень данных кластера, указанный при создании кластера;
* `/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/` — расположение примера файла, который используется в запросе.

### <a name="run-a-hive-query-against-data-in-a-data-lake-store-as-additional-storage"></a>Выполнение запроса Hive к данным, хранящимся в Data Lake Store (дополнительное хранилище)

Если созданный кластер в качестве хранилища по умолчанию использует хранилище BLOB-объектов, то демонстрационные данные не будут находиться в учетной записи Azure Data Lake Store, используемой в качестве дополнительного хранилища. В этом случае сначала передайте данные из хранилища BLOB-объектов в Data Lake Store, а затем выполните запросы, как показано в предыдущем примере.

Сведения о том, как скопировать данные из хранилища BLOB-объектов в Azure Data Lake Store, см. в следующих статьях:

* [Использование Distcp для копирования данных между BLOB-объектами и хранилищем озера данных](data-lake-store-copy-data-wasb-distcp.md)
* [Копирование данных из больших двоичных объектов хранилища Azure в хранилище озера данных](data-lake-store-copy-data-azure-storage-blob.md)

### <a name="use-data-lake-store-with-a-spark-cluster"></a>Использование Data Lake Store с кластером Spark
Кластер Spark можно использовать для выполнения заданий Spark с данными, хранящимися в Data Lake Store. Дополнительные сведения см. в статье [Использование кластера HDInsight Spark для анализа данных в Data Lake Store](../hdinsight/hdinsight-apache-spark-use-with-data-lake-store.md).


### <a name="use-data-lake-store-in-a-storm-topology"></a>Использование хранилища озера данных в топологии Storm
Хранилище озера данных можно использовать для записи данных из топологии Storm. Инструкции по реализации этого сценария см. в статье [Использование хранилища озера данных Azure с помощью Apache Storm в HDInsight](../hdinsight/hdinsight-storm-write-data-lake-store.md).

## <a name="see-also"></a>Дополнительные материалы
* [PowerShell: создание кластера HDInsight для работы с хранилищем озера данных](data-lake-store-hdinsight-hadoop-use-powershell.md)

[makecert]: https://msdn.microsoft.com/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx

