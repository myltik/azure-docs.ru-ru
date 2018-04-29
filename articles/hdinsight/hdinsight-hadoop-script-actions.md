---
title: Разработка действий скриптов с помощью HDInsight — Azure | Документы Майкрософт
description: Дополнительные сведения о настройке кластеров Hadoop с помощью действия скрипта. Действие сценария можно использовать для установки дополнительного программного обеспечения, работающего в кластере Hadoop, или для изменения конфигурации приложений, установленных в кластере.
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 836d68a8-8b21-4d69-8b61-281a7fe67f21
ms.service: hdinsight
ms.devlang: na
ms.topic: conceptual
ms.date: 05/25/2017
ms.author: jgao
ROBOTS: NOINDEX
ms.openlocfilehash: 98040f10eb15245f36eb0b365dcdf0f5ba7f107a
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2018
---
# <a name="develop-script-action-scripts-for-hdinsight-windows-based-clusters"></a>Разработка скриптов c действиями сценария для кластеров HDInsight под управлением Windows
Узнайте, как разрабатывать скрипты действия сценария для HDInsight. Дополнительную информацию о скриптах с действиями сценария см. в статье [Настройка кластеров HDInsight под управлением Windows с помощью действия сценария](hdinsight-hadoop-customize-cluster.md). Аналогичные сведения для кластеров HDInsight под управлением Linux см. в статье [Разработка действий сценариев с помощью HDInsight](hdinsight-hadoop-script-actions-linux.md).



> [!IMPORTANT]
> Шаги, описанные в этом документе, можно применять только к кластерам HDInsight под управлением Windows. Для версий ниже HDInsight 3.4 кластер HDInsight доступен только в Windows. Linux — это единственная операционная система, используемая для работы с HDInsight 3.4 или более поздних версий. Дополнительные сведения см. в разделе [Приближается дата прекращения сопровождения HDI версии 3.3](hdinsight-component-versioning.md#hdinsight-windows-retirement). Сведения об использовании действий скриптов в кластерах на платформе Linux см. в статье, посвященной [разработке действий скриптов с помощью HDInsight в Linux](hdinsight-hadoop-script-actions-linux.md).
>
>



Действие сценария можно использовать для установки дополнительного программного обеспечения, работающего в кластере Hadoop, или для изменения конфигурации приложений, установленных в кластере. Действия сценариев — это сценарии, выполняемые на узлах кластера во время развертывания кластеров HDInsight. Они будут выполнены, как только узлы в кластере завершат конфигурацию HDInsight. Действие сценария выполняется из учетной записи с правами системного администратора и предоставляет права полного доступа к узлам кластера. Для каждого кластера можно задать ряд действий сценария, которые будут выполнены в указанном порядке.

> [!NOTE]
> Если появляется следующее сообщение об ошибке:
>
> System.Management.Automation.CommandNotFoundException; ExceptionMessage: Термин Save-HDIFile не распознан как имя командлета, функции, файла скрипта или выполняемой программы. Проверьте правильность написания имени, а также наличие и правильность пути, после чего повторите попытку.
> Это означает, что вы не включили вспомогательные методы.  См. раздел [Вспомогательные методы для пользовательских скриптов](hdinsight-hadoop-script-actions.md#helper-methods-for-custom-scripts).
>
>

## <a name="sample-scripts"></a>Примеры сценариев
Действие скрипта для создания кластеров HDInsight в операционной системе Windows представляет собой скрипт Azure PowerShell. Ниже приведен пример скрипта для настройки файлов конфигурации сайта.

[!INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

    param (
        [parameter(Mandatory)][string] $ConfigFileName,
        [parameter(Mandatory)][string] $Name,
        [parameter(Mandatory)][string] $Value,
        [parameter()][string] $Description
    )

    if (!$Description) {
        $Description = ""
    }

    $hdiConfigFiles = @{
        "hive-site.xml" = "$env:HIVE_HOME\conf\hive-site.xml";
        "core-site.xml" = "$env:HADOOP_HOME\etc\hadoop\core-site.xml";
        "hdfs-site.xml" = "$env:HADOOP_HOME\etc\hadoop\hdfs-site.xml";
        "mapred-site.xml" = "$env:HADOOP_HOME\etc\hadoop\mapred-site.xml";
        "yarn-site.xml" = "$env:HADOOP_HOME\etc\hadoop\yarn-site.xml"
    }

    if (!($hdiConfigFiles[$ConfigFileName])) {
        Write-HDILog "Unable to configure $ConfigFileName because it is not part of the HDI configuration files."
        return
    }

    [xml]$configFile = Get-Content $hdiConfigFiles[$ConfigFileName]

    $existingproperty = $configFile.configuration.property | where {$_.Name -eq $Name}

    if ($existingproperty) {
        $existingproperty.Value = $Value
        $existingproperty.Description = $Description
    } else {
        $newproperty = @($configFile.configuration.property)[0].Clone()
        $newproperty.Name = $Name
        $newproperty.Value = $Value
        $newproperty.Description = $Description
        $configFile.configuration.AppendChild($newproperty)
    }

    $configFile.Save($hdiConfigFiles[$ConfigFileName])

    Write-HDILog "$configFileName has been configured."

Сценарий принимает четыре параметра: имя файла конфигурации, свойство, которое вы хотите изменить, значение, которое вы хотите установить, и описание. Например: 

    hive-site.xml hive.metastore.client.socket.timeout 90

Эти параметры устанавливают для hive.metastore.client.socket.timeout значение 90 в файле hive-site.xml.  Значение по умолчанию составляет 60 секунд.

Этот пример скрипта также можно найти по адресу [https://hditutorialdata.blob.core.windows.net/customizecluster/editSiteConfig.ps1](https://hditutorialdata.blob.core.windows.net/customizecluster/editSiteConfig.ps1).

HDInsight предоставляет несколько скриптов для установки дополнительных компонентов в кластерах HDInsight:

| ИМЯ | Скрипт |
| --- | --- |
| **Установка Spark** |https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1. Ознакомьтесь со статьей [Установка и использование Spark в кластерах HDInsight Hadoop с помощью действия сценария][hdinsight-install-spark]. |
| **Установка R** |https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1. Ознакомьтесь со статьей [Установка и использование R на кластерах HDInsight Hadoop][hdinsight-r-scripts]. |
| **Установка Solr** |https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1. Ознакомьтесь со статьей [Установка и использование Solr в кластерах HDInsight](hdinsight-hadoop-solr-install.md). |
| - **Установка Giraph** |https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1. Ознакомьтесь со статьей [Установка и использование Giraph в кластерах HDInsight](hdinsight-hadoop-giraph-install.md). |

Действие сценария можно развернуть из портала Azure, из пакета SDK для HDInsight .NET или Azure PowerShell.  Дополнительные сведения см. в статье [Настройка кластеров HDInsight под управлением Windows с помощью действия сценария][hdinsight-cluster-customize].

> [!NOTE]
> Примеры сценариев работают только с кластером HDInsight версии 3.1 или более поздней. Дополнительную информацию о версиях кластера HDInsight см. в статье [Что представляют собой различные компоненты Hadoop, доступные в HDInsight](hdinsight-component-versioning.md).
>
>

## <a name="helper-methods-for-custom-scripts"></a>Вспомогательные методы для пользовательских скриптов
Вспомогательные методы действий скриптов представляют собой служебные программы, которые можно использовать при создании пользовательских скриптов. Эти методы определяются в [https://hdiconfigactions.blob.core.windows.net/configactionmodulev05/HDInsightUtilities-v05.psm1](https://hdiconfigactions.blob.core.windows.net/configactionmodulev05/HDInsightUtilities-v05.psm1). Их можно включить в скрипты при помощи следующего примера:

    # Download config action module from a well-known directory.
    $CONFIGACTIONURI = "https://hdiconfigactions.blob.core.windows.net/configactionmodulev05/HDInsightUtilities-v05.psm1";
    $CONFIGACTIONMODULE = "C:\apps\dist\HDInsightUtilities.psm1";
    $webclient = New-Object System.Net.WebClient;
    $webclient.DownloadFile($CONFIGACTIONURI, $CONFIGACTIONMODULE);

    # (TIP) Import config action helper method module to make writing config action easy.
    if (Test-Path ($CONFIGACTIONMODULE))
    {
        Import-Module $CONFIGACTIONMODULE;
    }
    else
    {
        Write-Output "Failed to load HDInsightUtilities module, exiting ...";
        exit;
    }

Ниже приведены вспомогательные методы, предоставляемые этим скриптом.

| Вспомогательный метод | ОПИСАНИЕ |
| --- | --- |
| **Save-HDIFile** |Скачивает файл с указанным универсальным идентификатором ресурса в расположение на локальном диске, который сопоставлен с узлом виртуальной машины Azure, назначенным данному кластеру. |
| **Expand-HDIZippedFile** |Распаковывает ZIP-файл. |
| **Invoke-HDICmdScript** |Запускает сценарий из cmd.exe. |
| **Write-HDILog** |Записывает выходные данные пользовательского сценария, используемого для действия сценария. |
| **Get-Services** |Получает список служб, запущенных на том компьютере, где выполняется сценарий. |
| **Get-Service** |Используя имя определенной службы в качестве входных данных, получает подробную информацию об этой службе (имя службы, идентификатор процесса, состояние и т. п.) на том компьютере, где выполняется сценарий. |
| **Get-HDIServices** |Получает список служб HDInsight, запущенных на том компьютере, где выполняется сценарий. |
| **Get-HDIService** |Используя имя определенной службы HDInsight в качестве входных данных, получает подробную информацию об этой службе (имя службы, идентификатор процесса, состояние и т. п.) на том компьютере, где выполняется сценарий. |
| **Get-ServicesRunning** |Получает список служб, запущенных на том компьютере, где выполняется сценарий. |
| **Get-ServiceRunning** |Проверяет, запущена ли служба с определенным именем на том компьютере, где выполняется сценарий. |
| **Get-HDIServicesRunning** |Получает список служб HDInsight, запущенных на том компьютере, где выполняется сценарий. |
| **Get-HDIServiceRunning** |Проверяет, запущена ли служба HDInsight с определенным именем на том компьютере, где выполняется сценарий. |
| **Get-HDIHadoopVersion** |Получает версию Hadoop, установленную на том компьютере, где выполняется сценарий. |
| **Test-IsHDIHeadNode** |Проверяет, является ли тот компьютер, где выполняется сценарий, головным узлом. |
| **Test-IsActiveHDIHeadNode** |Проверяет, является ли тот компьютер, где выполняется сценарий, активным головным узлом. |
| **Test-IsHDIDataNode** |Проверяет, является ли тот компьютер, где выполняется сценарий, узлом данных. |
| **Edit-HDIConfigFile** |Изменяет файлы конфигурации hive-site.xml, core-site.xml, hdfs-site.xml, mapred-site.xml и yarn-site.xml. |

## <a name="best-practices-for-script-development"></a>Рекомендации по разработке скриптов
При разработке пользовательского скрипта для кластера HDInsight следует иметь в виду некоторые рекомендации.

* Проверка версии Hadoop

    Только HDInsight версии 3.1 (Hadoop 2.4) и выше поддерживает использование действия скрипта для установки пользовательских компонентов в кластере. В пользовательском сценарии необходимо использовать вспомогательный метод **Get-HDIHadoopVersion** для проверки версии Hadoop, и только затем продолжить выполнение других задач в сценарии.
* Стабильные ссылки на ресурсы скрипта

    Пользователям следует убедиться в том, что все скрипты и другие артефакты, используемые при настройке кластера, доступны в течение времени существования кластера и версии этих файлов не изменялись на протяжении всего периода. Эти ресурсы необходимы, если требуется восстановить узлы в кластере из образа. Мы советуем скачивать и архивировать все материалы в учетную запись хранения, управляемую пользователем. Это может быть учетная запись хранения по умолчанию или любые дополнительные учетные записи хранения, указанные во время развертывания настроенного кластера.
    К примеру, в приведенных в документации примерах настроенного кластера Spark и R есть локальная копия ресурсов в этой учетной записи хранения: https://hdiconfigactions.blob.core.windows.net/.
* Обеспечение идемпотентности сценария настройки кластера

    Следует иметь в виду, что узлы кластера HDInsight могут восстанавливаться из образа в течение времени существования кластера. Скрипт настройки кластера выполняется при каждом восстановлении кластера из образа. Этот скрипт должен создаваться как идемпотентный. Имеется в виду, что при восстановлении из образа скрипт должен обеспечивать возврат кластера к тем настройкам, которые были заданы для него во время первого запуска скрипта после изначального создания этого кластера. Например, если пользовательский скрипт во время первого запуска установил приложение в папку D:\AppLocation, то при каждом последующем запуске и восстановлении из образа скрипт должен проверить, существует ли приложение в папке D:\AppLocation, и только после этого переходить к следующим шагам.
* Установка пользовательских компонентов в оптимальное расположение

    Если узлы кластера восстанавливаются из образа, диск ресурсов C:\ и системный диск D:\ могут быть переформатированы, что приведет к потере находящихся на них данных и приложений. Такая потеря данных также может произойти, если узел виртуальной машины Azure, являющийся частью кластера, выходит из строя и заменяется новым узлом. Компоненты можно установить на диске D:\ или в папке C:\apps на кластере. Все расположения на диске C:\ зарезервированы. Укажите расположение, где должны устанавливаться приложения или библиотеки в рамках скрипта настройки кластера.
* Обеспечение высокого уровня доступности кластера

    В целях обеспечения высокой доступности HDInsight имеет активно-пассивную архитектуру, в которой один головной узел находится в активном режиме (где выполняются службы HDInsight), а другой головной узел находится в режиме ожидания (на нем службы HDInsight не запущены). Узлы переключаются между активным и пассивным режимами в случае сбоев в службах службы HDInsight. Если с помощью действия скрипта на обоих головных узлах устанавливаются службы для обеспечения высокой доступности, следует помнить о том, что механизм отработки отказа HDInsight не может автоматически выполнить отработку отказа для этих установленных пользователем служб. Поэтому службы HDInsight, установленные пользователем на головных узлах, к которым предъявляется требование высокой доступности, должны иметь собственный механизм отработки отказа в режиме "активный — пассивный" или работать в режиме "активный — активный".

    Команда действия сценария HDInsight выполняется на обоих головных узлах, после того как в качестве значения параметра *ClusterRoleCollection* будет указана роль головного узла. Поэтому при разработке пользовательского скрипта убедитесь, что в скрипте учтены эти особенности установки. Не следует создавать проблемы, устанавливая и запуская на обоих головных узлах одинаковые службы, которые в итоге будут конкурировать друг с другом. Обратите внимание, что при восстановлении из образа данные теряются, поэтому программное обеспечение, установленное посредством действий скрипта, должно быть устойчивым к таким событиям. Приложения должны быть рассчитаны на работу с высокодоступными данными, распределенными на множестве узлов. Как минимум пятая часть узлов в кластере может быть восстановлена из образа одновременно.
* Настройка пользовательских компонентов для использования хранилища больших двоичных объектов Azure

    Конфигурация по умолчанию для пользовательских компонентов, устанавливаемых на узлах кластера, может предполагать использование хранилища распределенной файловой системы Hadoop (HDFS). Следует изменить конфигурацию, чтобы в ней использовалось хранилище больших двоичных объектов Azure. При восстановлении кластера из образа файловая система HDFS форматируется, в результате чего теряются все хранящиеся в ней данные. В случае использования хранилища BLOB-объектов Azure эти данные сохраняются.

## <a name="common-usage-patterns"></a>Общие варианты использования
В этом разделе содержится руководство по реализации некоторых общих вариантов использования, которые могут понадобиться при написании пользовательского сценария.

### <a name="configure-environment-variables"></a>Настройка переменных среды
Часто при разработке действий скрипта необходимо задавать переменные среды. Например, наиболее вероятный сценарий — скачивание двоичного файла с внешнего сайта, установка этого файла в кластер и добавление его расположения в переменную среду PATH. В следующем фрагменте кода показано, как задавать переменные среды в пользовательском сценарии.

    Write-HDILog "Starting environment variable setting at: $(Get-Date)";
    [Environment]::SetEnvironmentVariable('MDS_RUNNER_CUSTOM_CLUSTER', 'true', 'Machine');

Этот оператор задает для переменной среды **MDS_RUNNER_CUSTOM_CLUSTER** значение true, а также задает область действия этой переменной в рамках виртуальной машины. Крайне важно, чтобы переменные среды были заданы в соответствующей области — для компьютера или пользователя. Дополнительные сведения о настройке переменных среды см. [здесь][1].

### <a name="access-to-locations-where-the-custom-scripts-are-stored"></a>Доступ к расположениям, в которых хранятся пользовательские сценарии
Сценарии для настройки кластера должны находиться в учетной записи хранения по умолчанию для кластера или в доступном только для чтения контейнере другой учетной записи хранения. Если скрипт получает доступ к ресурсам, расположенным в другом месте, эти ресурсы должны быть открыты для чтения. Например, вам может потребоваться получить доступ к файлу и сохранить его с помощью команды SaveFile-HDI.

    Save-HDIFile -SrcUri 'https://somestorageaccount.blob.core.windows.net/somecontainer/some-file.jar' -DestFile 'C:\apps\dist\hadoop-2.4.0.2.1.9.0-2196\share\hadoop\mapreduce\some-file.jar'

В этом примере необходимо убедиться, что контейнер `somecontainer` в учетной записи хранения `somestorageaccount` является общедоступным. В противном случае скрипт выдаст исключение "Не найдено" и прекратит работу.

### <a name="pass-parameters-to-the-add-azurermhdinsightscriptaction-cmdlet"></a>Передача параметров командлету Add-AzureRmHDInsightScriptAction
Чтобы передать несколько параметров командлету Add-AzureRmHDInsightScriptAction, необходимо отформатировать строковое значение, которое содержит все параметры для данного сценария. Например: 

    "-CertifcateUri wasb:///abc.pfx -CertificatePassword 123456 -InstallFolderName MyFolder"

или

    $parameters = '-Parameters "{0};{1};{2}"' -f $CertificateName,$certUriWithSasToken,$CertificatePassword


### <a name="throw-exception-for-failed-cluster-deployment"></a>Вызов исключения при сбое развертывания кластера
Если вы хотите получать точные уведомления о том, что настройки кластера не сработали надлежащим образом, необходимо вызвать исключение и отменить создание кластера. Например, может потребоваться обработка файла, если он существует, или обработка ошибки, если файл не существует. Это обеспечит корректное завершение сценария и точное оповещение о состоянии кластера. В следующем фрагменте кода показано, как этого достичь:

    If(Test-Path($SomePath)) {
        #Process file in some way
    } else {
        # File does not exist; handle error case
        # Print error message
    exit
    }

В этом фрагменте кода, если файл не существовал, это приведет к состоянию, когда скрипт фактически завершает свою работу после выдачи сообщения об ошибке, а кластер переходит в рабочее состояние при условии, что процесс настройки кластера был завершен "успешно". Если вы хотите получать уведомления о том, что настройка кластера завершилась неудачно из-за отсутствия файла, то более правильным действием будет выдача исключения и остановка этапа настройки кластера. Для этого необходимо использовать следующий образец фрагмента кода.

    If(Test-Path($SomePath)) {
        #Process file in some way
    } else {
        # File does not exist; handle error case
        # Print error message
    throw
    }


## <a name="checklist-for-deploying-a-script-action"></a>Контрольный список для развертывания действия сценария
Ниже приведены шаги, которые использовались при подготовке к развертыванию этих скриптов.

1. Поместите файлы, содержащие пользовательские скрипты, в месте, доступном из узлов кластера во время развертывания. Это может быть любая из используемых по умолчанию или дополнительных учетных записей хранения, указанных во время развертывания кластера, или другой общедоступный контейнер хранилища.
2. Добавьте проверки в скрипты, чтобы гарантировать, что они выполняются идемпотентно, а значит, скрипт сможет многократно выполняться на одном и том же узле.
3. Используйте командлет Azure PowerShell `Write-Output` для вывода в STDOUT и STDERR. Не используйте `Write-Host`.
4. Используйте папку для временных файлов, например `$env:TEMP`, чтобы сохранить скачанный файл, используемый скриптами. После выполнения скриптов очистите эту папку.
5. Устанавливайте пользовательское программное обеспечения только на диске D:\ или в папке C:\apps. Не следует производить установку в другие расположения на диске C:, так как они зарезервированы. Установка файлов на диске C: вне папки C:\apps может привести к сбою установки во время восстановления узла из образа.
6. В случае изменений параметров на уровне операционной системы или файлов конфигурации службы Hadoop может потребоваться перезапуск служб HDInsight. За счет этого службы HDInsight смогут автоматически установить любые параметры уровня операционной системы, например заданные в сценариях переменные среды.

## <a name="debug-custom-scripts"></a>Отладка пользовательских сценариев
Журналы ошибок сценария хранятся вместе с другими выходными данными в учетной записи хранения по умолчанию, заданной для кластера при его создании. Журналы хранятся в таблице с именем *u<фрагмент-имени-кластера><\метка-времени>setuplog*. Это сводные журналы, содержащие записи из всех узлов (рабочих и главного), на которых выполняется сценарий в кластере.

Для проверки журналов удобно воспользоваться инструментами HDInsight для Visual Studio. Сведения об установке инструментов см. в статье [Приступая к работе с инструментами Hadoop в Visual Studio для HDInsight для выполнения запроса Hive](hadoop/apache-hadoop-visual-studio-tools-get-started.md#install-or-update-data-lake-tools-for-visual-studio).

**Для проверки журнала с помощью Visual Studio**

1. Откройте Visual Studio.
2. Последовательно щелкните **Представление** и **Обозреватель сервера**.
3. Щелкните правой кнопкой мыши "Azure", выберите "Подключение к **подписке Microsoft Azure**", а затем введите учетные данные.
4. Разверните **Хранилище**, затем разверните узлы учетной записи хранения Azure, используемой в качестве файловой системы по умолчанию, разверните **Таблицы**, а затем дважды щелкните имя таблицы.

Вы также можете осуществить удаленный доступ к узлам кластера для просмотра STDOUT и STDERR для пользовательских скриптов. Журналы на каждом узле относятся только к соответствующему узлу и хранятся в расположении **C:\HDInsightLogs\DeploymentAgent.log**. В эти файлы журналов записываются все выходные данные пользовательского скрипта. Фрагмент журнала для действия сценария Spark выглядит следующим образом:

    Microsoft.Hadoop.Deployment.Engine.CustomPowershellScriptCommand; Details : BEGIN: Invoking powershell script https://configactions.blob.core.windows.net/sparkconfigactions/spark-installer.ps1.;
    Version : 2.1.0.0;
    ActivityId : 739e61f5-aa22-4254-aafc-9faf56fc2692;
    AzureVMName : HEADNODE0;
    IsException : False;
    ExceptionType : ;
    ExceptionMessage : ;
    InnerExceptionType : ;
    InnerExceptionMessage : ;
    Exception : ;
    ...

    Starting Spark installation at: 09/04/2014 21:46:02 Done with Spark installation at: 09/04/2014 21:46:38;

    Version : 2.1.0.0;
    ActivityId : 739e61f5-aa22-4254-aafc-9faf56fc2692;
    AzureVMName : HEADNODE0;
    IsException : False;
    ExceptionType : ;
    ExceptionMessage : ;
    InnerExceptionType : ;
    InnerExceptionMessage : ;
    Exception : ;
    ...

    Microsoft.Hadoop.Deployment.Engine.CustomPowershellScriptCommand;
    Details : END: Invoking powershell script https://configactions.blob.core.windows.net/sparkconfigactions/spark-installer.ps1.;
    Version : 2.1.0.0;
    ActivityId : 739e61f5-aa22-4254-aafc-9faf56fc2692;
    AzureVMName : HEADNODE0;
    IsException : False;
    ExceptionType : ;
    ExceptionMessage : ;
    InnerExceptionType : ;
    InnerExceptionMessage : ;
    Exception : ;


Из данных журнала следует, что действие скрипта Spark было выполнено на виртуальной машине с именем HEADNODE0 и во время выполнения исключения не возникали.

В случае сбоя при выполнении в файле журнала будут содержаться выходные данные со сведениями о нем. Информация в этих журналах может быть полезной при отладке сценария.

## <a name="see-also"></a>См. также
* [Настройка кластеров HDInsight под управлением Windows с помощью действия сценария][hdinsight-cluster-customize]
* [Установка и использование Spark в кластерах HDInsight Hadoop с помощью действия сценария][hdinsight-install-spark]
* [Установка и использование R на кластерах HDInsight Hadoop][hdinsight-r-scripts]
* [Установка и использование Solr в кластерах HDInsight](hdinsight-hadoop-solr-install.md).
* [Установка и использование Giraph в кластерах HDInsight](hdinsight-hadoop-giraph-install.md).

[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster.md
[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-r-scripts]: hdinsight-hadoop-r-scripts.md
[powershell-install-configure]: install-configure-powershell.md

<!--Reference links in article-->
[1]: https://msdn.microsoft.com/library/96xafkes(v=vs.110).aspx
