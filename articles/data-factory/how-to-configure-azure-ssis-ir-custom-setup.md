---
title: Пользовательская установка для среды выполнения интеграции Azure SSIS | Документация Майкрософт
description: В этой статье описано, как использовать интерфейс пользовательской установки для среды выполнения интеграции Azure SSIS.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/03/2018
ms.author: douglasl
ms.openlocfilehash: b377b5ca9d46d66fe99a8f60383076920b098a7d
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33769721"
---
# <a name="custom-setup-for-the-azure-ssis-integration-runtime"></a>Пользовательская установка для среды выполнения интеграции Azure SSIS

Интерфейс пользовательской установки для среды выполнения интеграции Azure SSIS позволяет изменить стандартную рабочую конфигурацию либо среду (например, запустить дополнительные службы Windows) или установить дополнительные компоненты (например, сборки, драйверы или расширения) на каждом узле среды выполнения интеграции (IR) Azure SSIS. В общем с помощью этого интерфейса можно добавить собственные шаги установки во время подготовки или перенастройки среды выполнения интеграции Azure SSIS.

Для настройки пользовательской установки вы подготавливаете скрипт и связанные файлы, а затем отправляете их в контейнер больших двоичных объектов в учетной записи хранения Azure. При подготовке или перенастройке среды выполнения интеграции Azure SSIS необходимо предоставить универсальный код ресурса (URI) SAS для контейнера. Затем каждый узел Azure SSIS IR скачивает скрипт и связанные с ним файлы из контейнера и выполняет пользовательскую установку с более высоким уровнем привилегий. После завершения пользовательской установки каждый узел передает стандартный поток вывода выполнения и другие журналы в контейнер.

Можно установить как бесплатные, так и нелицензированные компоненты, а также платные или лицензированные компоненты. Если вы являетесь независимым поставщиком ПО, ознакомьтесь со статьей [Develop paid or licensed custom components for the Azure SSIS integration runtime](how-to-develop-azure-ssis-ir-licensed-components.md) (Разработка платных или лицензионных пользовательских компонентов для среды выполнения интеграции Azure SSIS).


## <a name="current-limitations"></a>Текущие ограничения

-   Если вы хотите использовать файл `gacutil.exe`, чтобы установить сборки в глобальном кэше сборок, необходимо предоставить его как часть пользовательской установки или воспользоваться его копией, доступной в контейнере общедоступной предварительной версии.

-   Если Azure SSIS IR с пользовательской установкой необходимо соединить с виртуальной сетью, это можно сделать только с виртуальной сетью Azure Resource Manager. Классическая виртуальная сеть не поддерживается.

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить Azure SSIS IR, необходимо следующее:

-   [Подписка Azure.](https://azure.microsoft.com/)

-   [База данных SQL Azure или сервер управляемого экземпляра.](https://ms.portal.azure.com/#create/Microsoft.SQLServer)

-   [Подготовка Azure SSIS IR.](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure)

-   [Учетная запись хранения Azure.](https://azure.microsoft.com/services/storage/) Чтобы выполнить пользовательскую установку, отправьте и сохраните свой скрипт пользовательской установки и связанные с ним файлы в контейнер больших двоичных объектов. Процесс пользовательской установки отправляет свои журналы выполнения в тот же контейнер больших двоичных объектов.

## <a name="instructions"></a>Указания

2.  Загрузите и установите [Azure PowerShell](https://github.com/Azure/azure-powershell/releases/tag/v5.5.0-March2018) версии 5.4 или более поздней.

3.  Подготовьте свой собственный скрипт пользовательской установки и связанные с ним файлы (например, файлы BAT, CMD, EXE, DLL, MSI или PS1).

    1.  Необходимо иметь файл скрипта с именем `main.cmd`, который является точкой входа в пользовательской установке.

    2.  Если необходимо, чтобы в контейнер отправлялись дополнительные журналы, генерируемые другими инструментами (например, `msiexec.exe`), укажите предопределенную переменную среды `CUSTOM_SETUP_SCRIPT_LOG_DIR` в качестве папки журнала в ваших скриптах (например, `msiexec /i xxx.msi /quiet
        /lv %CUSTOM_SETUP_SCRIPT_LOG_DIR%\install.log`).

4.  Загрузите, установите и запустите [Обозреватель службы хранилища Azure](http://storageexplorer.com/).

    1.  В узле **(Local and Attached)** (Локальные и присоединенные) щелкните правой кнопкой мыши **Учетные записи хранения**, а затем — **Connect to Azure storage** (Подключиться к службе хранилища Azure).

       ![Подключение к службе хранилища Azure](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image1.png)

    2.  Выберите **Use a storage account name and key** (Использовать имя и ключ учетной записи хранения), а затем нажмите кнопку **Далее**.

       ![Использование имени и ключа учетной записи хранения](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image2.png)

    3.  Введите имя и ключ учетной записи хранения Azure, нажмите кнопку **Далее**, а затем — **Подключить**.

       ![Укажите имя и ключ учетной записи хранения](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image3.png)

    4.  В разделе подключенной учетной записи хранения Azure щелкните правой кнопкой мыши **Контейнеры больших двоичных объектов**, выберите **Создать контейнер BLOB-объектов** и присвойте имя новому контейнеру.

       ![Создание контейнера BLOB-объектов](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image4.png)

    5.  Выберите новый контейнер и передайте скрипт пользовательской установки и связанные с ним файлы. Убедитесь, что `main.cmd` отправлен в верхний уровень контейнера, а не в любую другую папку. 

       ![Отправка файлов в контейнер больших двоичных объектов](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image5.png)

    6.  Щелкните правой кнопкой мыши контейнер и выберите **Get Shared Access Signature** (Получить подписанный URL-адрес).

       ![Получение подписанного URL-адреса для контейнера](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image6.png)

    7.  Создайте URI SAS для своего контейнера с достаточно длительным сроком действия, а также разрешениями чтения, записи и вывода списка. URI SAS нужен для загрузки и выполнения скрипта пользовательской установки и связанных с ним файлов всякий раз, когда пересоздается образ какого-либо узла Azure SSIS IR. Разрешение на запись необходимо для отправки журналов выполнения программы установки.

       ![Создание подписанного URL-адреса для контейнера](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image7.png)

    8.  Скопируйте и сохраните URI SAS контейнера.

       ![Копирование и сохранение подписанного URL-адреса](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image8.png)

    9.  При подготовке или перенастройке Azure SSIS IR с помощью PowerShell, прежде чем запустить Azure SSIS IR, выполните командлет `Set-AzureRmDataFactoryV2IntegrationRuntime` с URI SAS контейнера в качестве значения нового параметра `SetupScriptContainerSasUri`. Например: 

       ```powershell
       Set-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $MyDataFactoryName `
                                                  -Name $MyAzureSsisIrName `
                                                  -ResourceGroupName $MyResourceGroupName `
                                                  -SetupScriptContainerSasUri $MySetupScriptContainerSasUri

       Start-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $MyDataFactoryName `
                                                    -Name $MyAzureSsisIrName `
                                                    -ResourceGroupName $MyResourceGroupName
       ```

    10.  После завершения пользовательской установки и запуска Azure SSIS IR можно найти стандартные выходные данные `main.cmd` и другие журналы выполнения в папке `main.cmd.log` контейнера хранилища.

2.  Чтобы просмотреть другие примеры пользовательской установки, подключитесь к контейнеру общедоступной предварительной версии с помощью Обозревателя службы хранилища Azure.

    a.  В узле **(Local and Attached)** (Локальные и присоединенные) щелкните правой кнопкой мыши **Учетные записи хранения**, выберите пункт **Connect to Azure storage** (Подключиться к службе хранилища Azure), а затем — **Use a storage account name and key** (Использовать имя и ключ учетной записи хранения) и нажмите кнопку **Далее**.

       ![Подключение к хранилищу Azure с помощью подписанного URL-адреса](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image9.png)

    Б.  Выберите **Use a SAS URI** (Использовать URI SAS) и введите следующий URI SAS для контейнера общедоступной предварительной версии. Нажмите кнопку **Далее**, а затем — **Подключить**.

       `https://ssisazurefileshare.blob.core.windows.net/publicpreview?sp=rl&st=2018-04-08T14%3A10%3A00Z&se=2020-04-10T14%3A10%3A00Z&sv=2017-04-17&sig=mFxBSnaYoIlMmWfxu9iMlgKIvydn85moOnOch6%2F%2BheE%3D&sr=c`

       ![Предоставление подписанного URL-адреса для контейнера](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image10.png)

    c. Выберите подключенный контейнер общедоступной предварительной версии и дважды щелкните папку `CustomSetupScript`. Внутри этой папки находятся следующие элементы:

       1. Папка `Sample`, где содержится файл пользовательской установки для установки базовой задачи на каждом узле Azure SSIS IR. Задача не делает ничего, только несколько секунд находится в спящем режиме. Папка также содержит папку `gacutil`, в которой расположен файл `gacutil.exe`.

       2. Папка `UserScenarios`, содержащая несколько конфигураций пользовательской установки для реальных сценариев.

    ![Содержимое контейнера общедоступной предварительной версии](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image11.png)

    d. Дважды щелкните папку `UserScenarios`. Внутри этой папки находятся следующие элементы:

       1. Папка `.NET FRAMEWORK 3.5`, в которой содержится конфигурация пользовательской установки для установки более ранней версии .NET Framework, которая может потребоваться для пользовательских компонентов на каждом узле вашей Azure SSIS IR.

       2. Папка `BCP`, которая содержит файл пользовательской установки для установки служебных программ командной строки SQL Server (`MsSqlCmdLnUtils.msi`), включая программу массового копирования (`bcp`), на каждом узле Azure SSIS IR.

       3. Папка `EXCEL`, где содержится файл пользовательской установки для установки сборок с открытым исходным кодом (`DocumentFormat.OpenXml.dll`, `ExcelDataReader.DataSet.dll` и `ExcelDataReader.dll`) на каждом узле Azure SSIS IR.

       4. Папка `MSDTC`, содержащая пользовательскую установку для изменения конфигураций сети и безопасности для экземпляра координатора распределенных транзакций Майкрософт (MSDTC) на каждом узле вашей Azure SSIS IR.

       5. Папка `ORACLE ENTERPRISE`, которая содержит скрипт пользовательской установки (`main.cmd`) и файл конфигурации автоматической установки (`client.rsp`), позволяющие установить драйвер Oracle OCI на каждом узле среды выполнения интеграции Azure SSIS Enterprise Edition (закрытая предварительная версия). Эта установка предоставляет возможность использовать диспетчер подключений, источник и назначение Oracle. Сначала загрузите `winx64_12102_client.zip` из [Oracle](http://www.oracle.com/technetwork/database/enterprise-edition/downloads/database12c-win64-download-2297732.html), а затем отправьте его вместе с `main.cmd` и `client.rsp` в контейнер. Если TNS используется для подключения к Oracle, необходимо загрузить файл `tnsnames.ora`, изменить его и отправить в контейнер, чтобы его можно было скопировать в папку установки Oracle во время установки.

       6. Папка `ORACLE STANDARD`, где содержится скрипт пользовательской установки (`main.cmd`) для установки драйвера Oracle ODP.NET на каждом узле Azure SSIS IR. Эта установка дает возможность использовать диспетчер подключений, источник и назначение ADO.NET. Сначала загрузите файл `ODP.NET_Managed_ODAC122cR1.zip` из [Oracle](http://www.oracle.com/technetwork/database/windows/downloads/index-090165.html), а затем отправьте его вместе с `main.cmd` в свой контейнер.

       7. Папка `SAP BW`, которая содержит скрипт пользовательской установки (`main.cmd`), чтобы установить сборку соединителя SAP .NET (`librfc32.dll`) на каждом узле среды выполнения интеграции Azure SSIS Enterprise Edition (закрытая предварительная версия). Эта установка предоставляет возможность использовать диспетчер подключений, источник и назначение SAP BW. Сначала передайте в контейнер 64- или 32-разрядную версию `librfc32.dll` из папки установки SAP вместе с `main.cmd`. Затем скрипт скопирует сборку SAP в папку `%windir%\SysWow64` или `%windir%\System32` во время установки.

       8. Папка `STORAGE`, где содержится файл пользовательской установки для установки Azure PowerShell на каждом узле Azure SSIS IR. Эта установка позволяет развертывать и запускать пакеты SSIS, которые запускают [скрипты PowerShell для работы с учетной записью хранения Azure](https://docs.microsoft.com/azure/storage/blobs/storage-how-to-use-blobs-powershell). Скопируйте `main.cmd`, пример `AzurePowerShell.msi` (или установите последнюю версию) и `storage.ps1` в контейнер. Используйте PowerShell.dtsx в качестве шаблона для пакетов. Шаблон пакета объединяет [задачу загрузки большого двоичного объекта Azure](https://docs.microsoft.com/sql/integration-services/control-flow/azure-blob-download-task), которая загружает `storage.ps1` в качестве изменяемого скрипта PowerShell, и [задачу обработки выполнения](https://blogs.msdn.microsoft.com/ssis/2017/01/26/run-powershell-scripts-in-ssis/), которая выполняет скрипт на каждом узле.

       9. Папка `TERADATA`, которая содержит скрипт пользовательской установки (`main.cmd)`), связанный файл (`install.cmd`) и пакеты установки (`.msi`). Эти файлы устанавливают соединители Teradata, API TPT и драйвер ODBC на каждый узел среды выполнения интеграции Azure SSIS Enterprise Edition (закрытая предварительная версия). Эта установка предоставляет возможность использовать диспетчер подключений, источник и назначение Teradata. Сначала загрузите ZIP-файл со средствами и служебными программами Teradata 15.х (например, `TeradataToolsAndUtilitiesBase__windows_indep.15.10.22.00.zip`) из [Teradata](http://partnerintelligence.teradata.com), а затем отправьте их вместе с файлами `.cmd` и `.msi` в свой контейнер.

    ![Папки в папке сценариев пользователя](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image12.png)

    д. Чтобы попробовать эти примеры пользовательской установки, скопируйте, а затем вставьте содержимое из выбранной папки в свой контейнер. При подготовке или перенастройке Azure SSIS IR с помощью PowerShell выполните командлет `Set-AzureRmDataFactoryV2IntegrationRuntime` с URI SAS контейнера в качестве значения нового параметра `SetupScriptContainerSasUri`.

## <a name="next-steps"></a>Дополнительная информация

-   [Выпуск Enterprise Edition среды выполнения интеграции Azure SSIS](how-to-configure-azure-ssis-ir-enterprise-edition.md)

-   [Разработка платных или лицензионных пользовательских компонентов для среды выполнения интеграции Azure SSIS](how-to-develop-azure-ssis-ir-licensed-components.md)