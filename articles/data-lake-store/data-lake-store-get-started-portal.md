---
title: "Начало работы с Data Lake Store с помощью портала Azure | Документация Майкрософт"
description: "Использование портала Azure для создания учетной записи Data Lake Store и выполнения базовых операций в Data Lake Store"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: fea324d0-ad1a-4150-81f0-8682ddb4591c
ms.service: data-lake-store
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 08/28/2017
ms.author: nitinme
ms.translationtype: HT
ms.sourcegitcommit: 1dbb1d5aae55a4c926b9d8632b416a740a375684
ms.openlocfilehash: fa13266993017374ba49709f8e22fbe6b03a28c7
ms.contentlocale: ru-ru
ms.lasthandoff: 08/07/2017

---
# <a name="get-started-with-azure-data-lake-store-using-the-azure-portal"></a>Начало работы с Azure Data Lake Store с помощью портала Azure
> [!div class="op_single_selector"]
> * [Портал](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [Пакет SDK для .NET](data-lake-store-get-started-net-sdk.md)
> * [Пакет SDK для Java](data-lake-store-get-started-java-sdk.md)
> * [REST API](data-lake-store-get-started-rest-api.md)
> * [Azure CLI 2.0](data-lake-store-get-started-cli-2.0.md)
> * [Node.js](data-lake-store-manage-use-nodejs.md)
> * [Python](data-lake-store-get-started-python.md)
>
> 

Узнайте, как с помощью портала Azure создать учетную запись Azure Data Lake Store и выполнять базовые операции, такие как создание папок, передача и загрузка файлов данных, удаление учетной записи и т. д. Дополнительные сведения см. в [обзоре Azure Data Lake Store](data-lake-store-overview.md).

Следующие два видео содержат те же сведения, которые описаны в этой статье:

* [Создание учетной записи хранения озера данных](https://mix.office.com/watch/1k1cycy4l4gen)
* [Управление данными в хранилище озера данных с помощью обозревателя данных](https://mix.office.com/watch/icletrxrh6pc)

## <a name="prerequisites"></a>Предварительные требования
Перед началом работы с этим руководством необходимо иметь следующее:

* **Подписка Azure**. Ознакомьтесь с [бесплатной пробной версией Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="create-an-azure-data-lake-store-account"></a>Создание учетной записи хранения озера данных Azure

1. Войдите на новый [портал Azure](https://portal.azure.com).
2. Щелкните **СОЗДАТЬ**, щелкните **Данные + хранилище**, а затем — **Azure Data Lake Store**. Ознакомьтесь со сведениями в колонке **Azure Data Lake Store** и нажмите кнопку **Создать** в левом нижнем углу колонки.
3. В колонке **Создать Data Lake Store** задайте значения, как показано на следующем снимке экрана:
   
    ![Создание учетной записи Azure Data Lake Store](./media/data-lake-store-get-started-portal/ADL.Create.New.Account.png "Создание учетной записи Azure Data Lake")
   
   * **Имя**. Введите уникальное имя учетной записи Data Lake Store.
   * **Подписка**. выберите подписку, в которой нужно создать учетную запись Data Lake Store.
   * **Группа ресурсов**: выберите существующую группу ресурсов Azure или создайте новую группу. Выберите существующую группу ресурсов или щелкните **Создать**, чтобы создать новую. Группа ресурсов представляет собой контейнер, содержащий связанные ресурсы для приложения. Дополнительные сведения см. в разделе [Группы ресурсов](../azure-resource-manager/resource-group-overview.md#resource-groups).
   * **Расположение**: выберите расположение, в котором нужно создать учетную запись хранения озера данных.
   * **Параметры шифрования**. Доступны три параметра:
     
     * **Не включать шифрование**.
     * **Использовать ключи, управляемые Azure Data Lake**  (если вы хотите, чтобы хранилище Azure Data Lake Store управляло ключами шифрования).
     * **Выбрать ключи из Azure Key Vault**. Вы можете выбрать существующее хранилище Azure Key Vault или создать новое. Чтобы использовать ключи из хранилища Key Vault, учетной записи Azure Data Lake Store необходимо назначить разрешения на доступ к хранилищу Azure Key Vault. Инструкции см. в разделе, посвященном [назначению разрешений для Azure Key Vault](#assign-permissions-to-azure-key-vault).
       
        ![Шифрование Data Lake Store](./media/data-lake-store-get-started-portal/adls-encryption-2.png "Шифрование Data Lake Store")
       
        Нажмите кнопку **ОК** в колонке **Параметры шифрования**.

        Дополнительные сведения см. в статье [Шифрование данных в Azure Data Lake Store](./data-lake-store-encryption.md).

4. Щелкните **Создать**. Если вы закрепили учетную запись на панели мониторинга, вы вернетесь на панель мониторинга, где сможете следить за ходом подготовки учетной записи Data Lake Store. После подготовки учетной хранения озера данных появится колонка учетной записи.

Вы можете также создать учетную запись Data Lake Store, используя шаблоны Azure Resource Manager. Эти шаблоны доступны на сайте [шаблонов быстрого запуска Azure](https://azure.microsoft.com/resources/templates/?term=data+lake+store).

- Шаблон без шифрования: [Deploy Azure Data Lake Store account with no data encryption](https://azure.microsoft.com/en-us/resources/templates/101-data-lake-store-no-encryption/) (Развертывание учетной записи Azure Data Lake Store без шифрования данных).
- С шифрованием данных с помощью Data Lake Store: [Deploy Data Lake Store account with encryption (Data Lake)](https://azure.microsoft.com/resources/templates/101-data-lake-store-encryption-adls/) (Развертывание учетной записи Data Lake Store с шифрованием данных (Data Lake)).
- С шифрованием данных с помощью Azure Key Vault: [Deploy Data Lake Store account with encryption (Key Vault)](https://azure.microsoft.com/resources/templates/101-data-lake-store-encryption-key-vault/) (Развертывание учетной записи Data Lake Store с шифрованием данных (Key Vault)).

### <a name="assign-permissions-to-azure-key-vault"></a> Назначение разрешений для Azure Key Vault
Если для настройки шифрования в учетной записи Data Lake Store использовались ключи из хранилища ключей Azure, учетной записи Data Lake Store необходимо назначить разрешения на доступ к хранилищу ключей Azure. Для этого выполните следующие действия.

1. Если вы использовали ключи из хранилища ключей Azure, вверху колонки учетной записи Data Lake Store отображается предупреждение. Щелкните его, чтобы открыть колонку **Настройка разрешений хранилища ключей**.
   
    ![Шифрование Data Lake Store](./media/data-lake-store-get-started-portal/adls-encryption-3.png "Шифрование Data Lake Store")
2. В колонке доступно два варианта настройки доступа.
   
   * В первом случае, чтобы настроить доступ, щелкните **Предоставить разрешение**. Этот вариант возможен, только если пользователь, который создал учетную запись Data Lake Store, также является администратором хранилища ключей Azure.
   * Другой вариант — выполнить командлет PowerShell, отображаемый в колонке. В этом случае нужно быть владельцем хранилища ключей Azure или иметь возможность предоставлять разрешения на него. Выполнив командлет, вернитесь в колонку и нажмите кнопку **Включить** для настройки доступа.

## <a name="createfolder"></a>Создание папок в учетной записи хранения озера данных Azure
Чтобы хранить данные и управлять ими, вы можете создать папки в своей учетной записи хранения озера данных.

1. Откройте созданную учетную запись Data Lake Store. В левой панели щелкните **Обзор**, щелкните **Data Lake Store** и затем в колонке Data Lake Store щелкните имя учетной записи, в которой нужно создать папки. Если учетная запись была закреплена на начальной панели, щелкните элемент этой учетной записи.
2. В колонке учетной записи «Хранилище озера данных» щелкните **Обозреватель данных**.
   
    ![Создание папок в учетной записи Data Lake Store](./media/data-lake-store-get-started-portal/ADL.Create.Folder.png "Создание папок в учетной записи Data Lake Store")
3. В колонке своей учетной записи Data Lake Store щелкните **Создать папку**, введите имя новой папки и нажмите кнопку **ОК**.
   
    ![Создание папок в учетной записи Data Lake Store](./media/data-lake-store-get-started-portal/ADL.Folder.Name.png "Создание папок в учетной записи Data Lake Store")
   
    Созданная папка появится в колонке **Обозреватель данных**. Вы можете создавать вложенные папки любого уровня.
   
    ![Создание папок в учетной записи Data Lake](./media/data-lake-store-get-started-portal/ADL.New.Directory.png "Создание папок в учетной записи Data Lake")

## <a name="uploaddata"></a>Передача данных в учетную запись хранилища озера данных Azure
Данные можно передавать в учетную запись хранения озера данных Azure непосредственно на корневой уровень или в папку, созданную в учетной записи. На следующем снимке экрана выполните необходимые действия, чтобы передать файл во вложенную папку из колонки **Обозреватель данных**. На этом снимке экрана файл передается во вложенную папку, показанную в адресной строке (выделена красным прямоугольником).

Если у вас нет под рукой подходящих для этих целей данных, передайте папку **Ambulance Data** из [репозитория Git для озера данных Azure](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData).

![Отправка данных](./media/data-lake-store-get-started-portal/ADL.New.Upload.File.png "Отправка данных")

## <a name="properties"></a>Свойства и действия, доступные на сохраненных данных
Щелкните добавленный файл, чтобы открыть колонку **Свойства** . В этой колонке показываются свойства, связанные с этим файлом, и действия, которые можно с ним выполнять. Также можно скопировать полный путь к файлу в вашей учетной записи Azure Data Lake Store, выделенный красным прямоугольником на снимке экрана ниже:

![Свойства данных](./media/data-lake-store-get-started-portal/ADL.File.Properties.png "Свойства данных")

* Щелкните **Просмотр**, чтобы просмотреть файл непосредственно в браузере. Вы также можете указать формат просмотра. Щелкните **Просмотр**, в колонке **Просмотр файла** щелкните **Формат** и в колонке **Формат просмотра файла** укажите нужные параметры, такие как количество отображаемых строк, используемая кодировка, разделитель и т. д.
  
  ![Формат просмотра файла](./media/data-lake-store-get-started-portal/ADL.File.Preview.png "Формат просмотра файла")
* Щелкните **Загрузить** , чтобы загрузить файл на компьютер.
* Щелкните **Переименовать файл** , чтобы переименовать файл.
* Щелкните **Удалить файл** , чтобы удалить файл.

## <a name="secure-your-data"></a>Защита данных
Защитить данные, хранящиеся в вашей учетной записи хранения озера данных Azure, можно с помощью Azure Active Directory и контроля доступа (ACL). Соответствующие инструкции см. в статье, посвященной [защите данных в Azure Data Lake Store](data-lake-store-secure-data.md).

## <a name="delete-azure-data-lake-store-account"></a>Удаление учетной записи хранения озера данных Azure
Чтобы удалить учетную запись хранения озера данных Azure, в колонке хранилища озера данных щелкните **Удалить**. Чтобы подтвердить действие, вам будет предложено ввести имя учетной записи, которую вы хотите удалить. Введите имя учетной записи и нажмите кнопку **Удалить**.

![Удаление учетной записи Data Lake](./media/data-lake-store-get-started-portal/ADL.Delete.Account.png "Удаление учетной записи Data Lake")

## <a name="next-steps"></a>Дальнейшие действия
* [Защита данных в хранилище озера данных](data-lake-store-secure-data.md)
* [Использование аналитики озера данных Azure с хранилищем озера данных](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Использование Azure HDInsight с хранилищем озера данных](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Доступ к журналам диагностики Azure Data Lake Store](data-lake-store-diagnostic-logs.md)


