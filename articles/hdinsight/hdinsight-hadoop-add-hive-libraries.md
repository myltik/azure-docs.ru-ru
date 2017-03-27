---
title: "Добавление библиотек Hive во время создания кластера HDInsight | Документация Майкрософт"
description: "Узнайте, как добавлять библиотеки Hive (JAR-файлы) в кластер HDInsight во время создания кластера."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 2fd74b8d-c006-45c6-a9e2-72ff5d2d978a
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/27/2017
ms.author: larryfr
ms.custom: H1Hack27Feb2017,hdinsightactive
translationtype: Human Translation
ms.sourcegitcommit: cfaade8249a643b77f3d7fdf466eb5ba38143f18
ms.openlocfilehash: a17a57c0eb7f35b1fba0d5b7e48fa8db249cd0f0
ms.lasthandoff: 03/01/2017

---
# <a name="add-custom-hive-libraries-when-creating-your-hdinsight-cluster"></a>Добавление пользовательских библиотек Hive при создании кластера HDInsight

Если у вас есть библиотеки, которые часто используются с Hive в HDInsight, ознакомьтесь с этим документом, содержащим сведения об использовании действия сценария для предварительной загрузки библиотек во время создания кластера. Библиотеки, добавляемые с помощью инструкций в этом документе, глобально доступны в Hive. Для их загрузки нет необходимости использовать [ADD JAR](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Cli).

## <a name="how-it-works"></a>Принцип работы

При создании кластера можно указать действие сценария, которое запускает сценарий на узлах кластера во время их создания. Сценарий в этом документе принимает один параметр, которым является расположение WASB с библиотеками (сохраненными как JAR-файлы), которые будут предварительно загружены.

Во время создания кластера сценарий перечисляет файлы, копирует их в каталог `/usr/lib/customhivelibs/` на головных и рабочих узлах, а затем добавляет их в свойство `hive.aux.jars.path` в файле `core-site.xml`. Для кластеров под управлением Linux он также обновляет файл `hive-env.sh` расположением файлов.

> [!NOTE]
> В результате использования действий сценариев, приведенных в этой статье, библиотеки будут доступны в следующих случаях.
>
> * **HDInsight под управлением Linux** — при использовании **командной строки Hive**, **WebHCat** и **HiveServer2**.
> * **HDInsight под управлением Windows** — при использовании **командной строки Hive** и **WebHCat**.

## <a name="the-script"></a>Сценарий

**Расположение скрипта**

Для **кластеров под управлением Linux** — [https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh](https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh).

Для **кластеров под управлением Windows** — [https://hdiconfigactions.blob.core.windows.net/setupcustomhivelibsv01/setup-customhivelibs-v01.ps1](https://hdiconfigactions.blob.core.windows.net/setupcustomhivelibsv01/setup-customhivelibs-v01.ps1).

> [!IMPORTANT]
> Linux — единственная операционная система, используемая для работы с HDInsight 3.4 или более поздней версии. См. дополнительные сведения о [нерекомендуемых версиях HDInsight в Windows](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date).

**Требования**

* Скрипты необходимо применять к **головным узлам** и **рабочим узлам**.

* JAR-файлы, которые нужно установить, должны храниться в хранилище BLOB-объектов Azure в **одном контейнере**.

* Учетная запись хранилища, содержащая библиотеку JAR-файлов, **должна** быть связана с кластером HDInsight во время создания. Это должна быть учетная запись хранения по умолчанию или учетная запись, добавленная с помощью колонки __Необязательная настройка__.

* Путь WASB к контейнеру необходимо указать в виде параметра для действия сценария. Например, если JAR-файлы хранятся в контейнере **libs** в учетной записи хранения **mystorage**, то параметром будет **wasbs://libs@mystorage.blob.core.windows.net/**.

  > [!NOTE]
  > В этом документе предполагается, что вы уже создали учетную запись хранения и контейнер BLOB-объектов и отправили в него файлы.
  >
  > Если учетная запись хранения не создана, создайте ее на [портале Azure](https://portal.azure.com). Затем с помощью служебной программы, например [Azure Storage Explorer](http://storageexplorer.com/), в учетной записи можно создать контейнер и передать в него файлы.

## <a name="create-a-cluster-using-the-script"></a>Создание кластера с помощью сценария

> [!NOTE]
> Ниже приведены инструкции по созданию кластера HDInsight под управлением Linux. Чтобы создать кластер под управлением Windows, выберите **Windows** в качестве ОС кластера и вместо сценария bash используйте сценарий Windows (PowerShell).
>
> Для создания кластера с помощью этого сценария также можно использовать Azure PowerShell или пакет SDK HDInsight для .NET. Дополнительные сведения об использовании этих методов см. в статье [Настройка кластеров HDInsight под управлением Linux с помощью действия сценария](hdinsight-hadoop-customize-cluster-linux.md).

1. Начните подготовку кластера с помощью действий, описанных в статье [Создание кластеров Hadoop под управлением Linux в HDInsight](hdinsight-hadoop-provision-linux-clusters.md), но не завершайте ее.

2. В колонке **Необязательная настройка** выберите **Действия сценария** и введите следующие сведения.

   * **ИМЯ**: введите понятное имя для действия сценария.

   * **Универсальный код ресурса скрипта** — https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh;

   * **Головной** — установите флажок.

   * **Рабочая роль** — установите флажок;

   * **Zookeeper** — оставьте это поле пустым;

   * **Параметры** — введите адрес WASB к контейнеру и учетной записи хранения, содержащий JAR-файлы (например, **wasbs://libs@mystorage.blob.core.windows.net/**).

3. В нижней части раздела **Действия скрипта** нажмите кнопку **Выбрать**, чтобы сохранить конфигурацию.

4. В колонке **Необязательная настройка** выберите **Связанные учетные записи хранения** и щелкните ссылку **Добавить ключ хранилища**. Выберите учетную запись, содержащую JAR-файлы, а затем с помощью кнопок **Выбрать** сохраните настройки и вернитесь к колонке **Необязательная настройка**.

5. Нажмите кнопку **Выбрать** в нижней части колонки **Необязательная настройка**, чтобы сохранить необязательную конфигурацию.

6. Продолжите подготовку кластера, как описано в статье [Создание кластеров Hadoop под управлением Linux в HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

После создания кластера вы сможете использовать JAR-файлы, добавленные с помощью этого сценария из Hive, без инструкции `ADD JAR`.

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о работе с Hive см. в статье [Использование Hive и HiveQL с Hadoop в HDInsight для анализа примера файла Apache log4j](hdinsight-use-hive.md).

