---
title: "Запись данных из концентраторов событий в Azure Data Lake Store | Документация Майкрософт"
description: "Запись данных из концентраторов событий с помощью Azure Data Lake Store."
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 08/28/2017
ms.author: nitinme
ms.translationtype: HT
ms.sourcegitcommit: 7456da29aa07372156f2b9c08ab83626dab7cc45
ms.openlocfilehash: a9e69576958ae96d22a4eb03d0df429f0b307298
ms.contentlocale: ru-ru
ms.lasthandoff: 08/28/2017

---
# <a name="use-azure-data-lake-store-to-capture-data-from-event-hubs"></a>Запись данных из концентраторов событий с помощью Azure Data Lake Store

В этой статье приведены сведения о записи данных, полученных концентратором событий Azure с помощью Azure Data Lake Store.

## <a name="prerequisites"></a>Предварительные требования

* **Подписка Azure**. Ознакомьтесь с [бесплатной пробной версией Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Учетная запись Azure Data Lake Store.** Инструкции по созданию учетной записи см. в статье, посвященной [началу работы с Azure Data Lake Store с помощью портала Azure](data-lake-store-get-started-portal.md).

*  **Пространство имен концентраторов событий.** Дополнительные сведения см. в разделе [Создание пространства имен концентраторов событий](../event-hubs/event-hubs-create.md#create-an-event-hubs-namespace). Убедитесь, что учетная запись Data Lake Store и пространство имен концентраторов событий находятся в одной подписке Azure.


## <a name="assign-permissions-to-event-hubs"></a>Назначение разрешений концентраторам событий

В этом разделе вы создадите папку в учетной записи, в которой необходимо сохранить данные из концентраторов событий. Вы также можете назначить разрешения концентраторам событий. Это позволит им записывать данные в учетную запись Data Lake Store. 

1. Откройте учетную запись Data Lake Store, в которой необходимо сохранить данные из концентраторов событий, и щелкните **Обозреватель данных**.

    ![Обозреватель данных Data Lake Store](./media/data-lake-store-archive-eventhub-capture/data-lake-store-open-data-explorer.png "Data Lake Store data explorer")

2.  Выберите **Создать папку** и введите имя папки, в которую необходимо сохранять данные.

    ![Создание папки в Data Lake Store](./media/data-lake-store-archive-eventhub-capture/data-lake-store-create-new-folder.png "Create a new folder in Data Lake Store")

3. Назначьте разрешения в корневой папке Data Lake Store. 

    а. Щелкните **Обозреватель данных**, выберите корневую папку учетной записи Data Lake Store, а затем — **Доступ**.

    ![Назначение разрешений корневой папке Data Lake Store](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-permissions-to-root.png "Assign permissions for Data Lake Store root")

    b. В разделе **Доступ** выберите **Добавить**, щелкните **Выберите пользователя или группу**, а затем найдите `Microsoft.EventHubs`. 

    ![Назначение разрешений корневой папке Data Lake Store](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp.png "Assign permissions for Data Lake Store root")
    
    Нажмите кнопку **Выбрать**.

    c. В разделе **Назначение разрешений** выберите **Выбор разрешений**. Задайте для параметра **Разрешения** значение **Выполнить**. Задайте для параметра **Добавить к** значение **К этой папке и всем вложенным элементам**. Задайте для параметра **Add as** (Добавить как) значение **Запись разрешений доступа и запись разрешений по умолчанию**.

    ![Назначение разрешений корневой папке Data Lake Store](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp1.png "Assign permissions for Data Lake Store root")

    Нажмите кнопку **ОК**.

4. Назначьте разрешения папке в учетной записи Data Lake Store, в которой необходимо сохранить данные.

    а. Щелкните **Обозреватель данных**, выберите папку в учетной записи Data Lake Store, а затем — **Доступ**.

    ![Назначение разрешений в папке Data Lake Store](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-permissions-to-folder.png "Assign permissions for Data Lake Store folder")

    b. В разделе **Доступ** выберите **Добавить**, щелкните **Выберите пользователя или группу**, а затем найдите `Microsoft.EventHubs`. 

    ![Назначение разрешений в папке Data Lake Store](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp.png "Assign permissions for Data Lake Store folder")
    
    Нажмите кнопку **Выбрать**.

    c. В разделе **Назначение разрешений** выберите **Выбор разрешений**. Для параметра **Разрешения** установите флажки **Чтение, Запись** и **Выполнить**. Задайте для параметра **Добавить к** значение **К этой папке и всем вложенным элементам**. Наконец, задайте для параметра **Add as** (Добавить как) значение **Запись разрешений доступа и запись разрешений по умолчанию**.

    ![Назначение разрешений в папке Data Lake Store](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp-folder.png "Assign permissions for Data Lake Store folder")
    
    Нажмите кнопку **ОК**. 

## <a name="configure-event-hubs-to-capture-data-to-data-lake-store"></a>Настройка концентраторов событий для записи данных в Data Lake Store

В этом разделе вы создаете концентратор событий в пространстве имен концентраторов событий. Кроме того, вы также настроите концентратор событий для записи данных в учетную запись Azure Data Lake Store. В этом разделе предполагается, что вы уже создали пространство имен концентраторов событий.

2. В области **Обзор** пространства имен концентраторов событий выберите **Концентратор событий**.

    ![Создание концентратора событий](./media/data-lake-store-archive-eventhub-capture/data-lake-store-create-event-hub.png "Create Event Hub")

3. Чтобы настроить концентратор событий для записи данных в Data Lake Store, укажите приведенные ниже значения.

    ![Создание концентратора событий](./media/data-lake-store-archive-eventhub-capture/data-lake-store-configure-eventhub.png "Create Event Hub")

    а. Укажите имя концентратора событий.
    
    b. В этом руководстве задайте для параметров **Количество разделов** и **Хранение сообщений** значения по умолчанию.
    
    c. Установите для параметра **Запись** значение **Включено**. Задайте **окно времени** (частота выполнения записи) и **окно размера** (размер данных для записи). 
    
    d. Для параметра **Capture Provider** (Поставщик записи) задайте значение **Azure Data Lake Store** и выберите хранилище Data Lake Store, созданное ранее. В качестве значения параметра **Data Lake Path** (Путь к Data Lake) введите имя папки, созданной в учетной записи Data Lake Store. Необходимо только указать относительный путь к папке.

    д. Оставьте стандартное значение параметра **Воспользуйтесь поиском, чтобы отфильтровать репликации**. Этот параметр определяет структуру папки, созданной в папке записи.

    f. Щелкните **Создать**.

## <a name="test-the-setup"></a>Тестирование настройки

Теперь вы можете протестировать решение, отправив данные в концентратор событий Azure. Инструкции см. в статье [Отправка событий в концентраторы событий Azure с помощью платформы .NET Framework](../event-hubs/event-hubs-dotnet-framework-getstarted-send.md). Отправляемые данные отобразятся в Data Lake Store с использованием указанной структуры папки. Например, на снимке экрана ниже приведена структура папки, в которой отобразятся данные в Data Lake Store данные.

![Пример данных концентратора событий в Data Lake Store](./media/data-lake-store-archive-eventhub-capture/data-lake-store-eventhub-data-sample.png "Sample EventHub data in Data Lake Store")

> [!NOTE]
> Даже если в концентратор событий не поступают сообщения, он записывает пустые файлы лишь с заголовками в учетную запись Data Lake Store. Файлы записываются с интервалом времени, указанным при создании концентраторов событий.
> 
>

## <a name="analyze-data-in-data-lake-store"></a>Анализ данных в хранилище озера данных

Когда данные появятся в Data Lake Store, вы можете выполнить задания аналитики, чтобы обработать их. Сведения об использовании Azure Data Lake Analytics для выполнения этих действий см. в [примере USQL Avro](https://github.com/Azure/usql/tree/master/Examples/AvroExamples).
  

## <a name="see-also"></a>См. также
* [Защита данных в хранилище озера данных](data-lake-store-secure-data.md)
* [Копирование данных из больших двоичных объектов службы хранилища Azure в Data Lake Store](data-lake-store-copy-data-azure-storage-blob.md)

