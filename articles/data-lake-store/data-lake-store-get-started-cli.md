<properties 
   pageTitle="Начало работы с хранилищем озера данных с помощью кроссплатформенного интерфейса командной строки | Microsoft Azure"
   description="Использование кроссплатформенного интерфейса командной строки Azure для создания учетной записи хранения озера данных и выполнения базовых операций" 
   services="data-lake-store" 
   documentationCenter="" 
   authors="nitinme" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="11/05/2015"
   ms.author="nitinme"/>

# Начало работы с хранилищем озера данных Azure с помощью командной строки Azure

> [AZURE.SELECTOR]
- [Using Portal](data-lake-store-get-started-portal.md)
- [Using PowerShell](data-lake-store-get-started-powershell.md)
- [Using .NET SDK](data-lake-store-get-started-net-sdk.md)
- [Using Azure CLI](data-lake-store-get-started-cli.md)

Узнайте, как с помощью интерфейса командной строки Azure создать учетную запись хранения озера данных Azure и выполнять базовые операции, такие как создание папок, передача и скачивание файлов данных, удаление учетной записи и т. д. Дополнительные сведения о хранилище озера данных см. в статье [Обзор хранилища озера данных](data-lake-store-overview.md).

Интерфейс командной строки (CLI) Azure реализован в Node.js. Его можно использовать на любой платформе, которая поддерживает Node.js, включая Windows, Mac и Linux. Azure CLI имеет открытый исходный код. Исходный код управляется с помощью GitHub по адресу <a href= "https://github.com/azure/azure-xplat-cli">https://github.com/azure/azure-xplat-cli</a>. В этой статье описывается только использование CLI Azure с хранилищем озера данных. Общее руководство по использованию CLI Azure см. в статье [Использование CLI Azure][azure-command-line-tools].


##Предварительные требования

Перед началом работы с этой статьей необходимо иметь следующее:

- **Подписка Azure.**. См. [Бесплатная пробная версия Azure](https://azure.microsoft.com/ru-RU/pricing/free-trial/).
- **Включите свою подписку Azure** для общедоступной предварительной версии хранилища озера данных. См. [инструкции](data-lake-store-get-started-portal.md#signup). 
- **CLI Azure** — Сведения об установке и настройке CLI Azure см. в разделе [Установка и настройка CLI Azure](../xplat-cli-install.md) . Обязательно перезагрузите компьютер после установки интерфейса командной строки.

##Вход в подписку Azure

Выполните действия, описанные в статье [Подключение к среде Azure с использованием интерфейса командной строки Azure (Azure CLI)](xplat-cli-connect.md), и подключитесь к подписке с помощью метода __login__.


## Создание учетной записи хранения озера данных Azure

Откройте командную строку, оболочку или сеанс терминала и выполните следующие команды.

1. Выполните вход в свою подписку Azure:

		azure login

	Вам будет предложено открыть веб-страницу и ввести код проверки подлинности. Следуйте инструкциям на странице для входа в свою подписку Azure.

2. Переключитесь в режим диспетчера ресурсов Azure с помощью следующей команды:

		azure config mode arm


3. Отобразите список подписок Azure для своей учетной записи.

		azure account list


4. Если подписок Azure несколько, воспользуйтесь следующей командой, чтобы указать, какую подписку должны использовать команды Azure CLI:

		azure account set <subscriptionname>

5. Создайте новую группу ресурсов. В следующей команде укажите значения параметров, которые требуется использовать.

		azure group create <resourceGroup> <location>

	Если имя расположения содержит пробелы, заключите его в кавычки. Например, "East US 2".

5. Создайте учетную запись хранилища озера данных.

		azure datalake store account create <dataLakeStoreAccountName> <location> <resourceGroup>

## Создание папок в хранилище озера данных

Чтобы хранить данные и управлять ими, вы можете создать папки в своей учетной записи хранения озера данных Azure. Используйте следующую команду, чтобы создать папку с именем "mynewfolder" в корневом каталоге хранилища озера данных.

	azure datalake store filesystem create <dataLakeStoreAccountName> <path> --folder

Например:

	azure datalake store filesystem create mynewdatalakestore /mynewfolder --folder

## Передача данных в хранилище озера данных

Данные можно передавать в хранилище озера данных Azure непосредственно на корневой уровень или в папку, созданную в учетной записи. Фрагменты кода ниже показывают, как передать некоторые образцы данных в папку (**mynewfolder**), которая была создана в предыдущем шаге.

Если у вас нет под рукой подходящих для этих целей данных, загрузите папку **Ambulance Data** из [репозитория Git для озера данных Azure](https://github.com/MicrosoftBigData/ProjectKona/tree/master/SQLIPSamples/SampleData/AmbulanceData). Загрузите файл и сохраните его в локальном каталоге на компьютере, например C:\\sampledata.

	azure datalake store filesystem import <dataLakeStoreAccountName> "<source path>" "<destination path>"

Например:

	azure datalake store filesystem import mynewdatalakestore "C:\SampleData\AmbulanceData\vehicle1_09142014.csv" "/mynewfolder/vehicle1_09142014.csv"


## Вывод списка файлов в хранилище озера данных

Чтобы вывести список файлов в учетной записи хранения озера данных, используйте следующую команду.

	azure datalake store filesystem list <dataLakeStoreAccountName> <path>

Например:

	azure datalake store filesystem list mynewdatalakestore /mynewfolder

Результат этой команды должен выглядеть примерно следующим образом:

	info:    Executing command datalake store filesystem list
	data:    accessTime: 1446245025257
	data:    blockSize: 268435456
	data:    group: NotSupportYet
	data:    length: 1589881
	data:    modificationTime: 1446245105763
	data:    owner: NotSupportYet
	data:    pathSuffix: vehicle1_09142014.csv
	data:    permission: 777
	data:    replication: 0
	data:    type: FILE
	data:    ------------------------------------------------------------------------------------
	info:    datalake store filesystem list command OK

## Переименование, загрузка и удаление данных из хранилища озера данных

* **Чтобы переименовать файл**, используйте следующую команду:

    	azure datalake store filesystem move <dataLakeStoreAccountName> <path/old_file_name> <path/new_file_name>

	Например:

		azure datalake store filesystem move mynewdatalakestore /mynewfolder/vehicle1_09142014.csv /mynewfolder/vehicle1_09142014_copy.csv

* **Чтобы скачать файл**, используйте следующую команду: Убедитесь, что указанный конечный путь уже существует.

		azure datalake store filesystem export <dataLakeStoreAccountName> <source_path> <destination_path>

	Например:

		azure datalake store filesystem export mynewdatalakestore /mynewfolder/vehicle1_09142014_copy.csv "C:\mysampledata\vehicle1_09142014_copy.csv"

* **Чтобы удалить файл**, используйте следующую команду:

		azure datalake store filesystem delete <dataLakeStoreAccountName> <path> 

	Например:

		azure datalake store filesystem delete mynewdatalakestore /mynewfolder/vehicle1_09142014_copy.csv
	
	При появлении запроса введите **Y**, чтобы удалить элемент.

## Просмотр списка управления доступом для папки в хранилище озера данных

Используйте следующую команду для просмотра списков ACL для папки хранилища озера данных. В текущем выпуске списки ACL можно задать только в корневом каталоге хранилища озера данных. Таким образом, приведенный ниже параметр пути всегда будет указывать на корень (/).

	azure datalake store permissions show <dataLakeStoreName> <path>

Например:

	azure datalake store permissions show mynewdatalakestore /


## Удаление учетной записи хранения озера данных

Чтобы удалить учетную запись хранения озера данных, используйте следующую команду.

	azure datalake store account delete <dataLakeStoreAccountName>

Например:

	azure datalake store account delete mynewdatalakestore

При появлении запроса введите **Y**, чтобы удалить учетную запись.

## Другие способы создания учетной записи хранения озера данных

- [Начало работы с хранилищем озера данных с помощью портала](data-lake-store-get-started-portal.md)
- [Начало работы с хранилищем озера данных с помощью пакета SDK .NET](data-lake-store-get-started-net-sdk.md)
- [Начало работы с хранилищем озера данных с помощью PowerShell](data-lake-store-get-started-powershell.md)


## Дальнейшие действия

- [Защита данных в хранилище озера данных](data-lake-store-secure-data.md)
- [Использование аналитики озера данных Azure с хранилищем озера данных](data-lake-analytics-get-started-portal.md)
- [Использование Azure HDInsight с хранилищем озера данных](data-lake-store-hdinsight-hadoop-use-portal.md)


[azure-command-line-tools]: ../xplat-cli-install.md

<!---HONumber=Nov15_HO3-->