---
title: "Включение записи концентраторов событий с помощью портала | Документация Microsoft"
description: "Включите функцию записи концентраторов событий с помощью портала Azure."
services: event-hubs
documentationcenter: 
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/28/2017
ms.author: sethm
ms.translationtype: HT
ms.sourcegitcommit: 8351217a29af20a10c64feba8ccd015702ff1b4e
ms.openlocfilehash: 5420c1cdefa99ff30320dd757e04aa0cafc792bc
ms.contentlocale: ru-ru
ms.lasthandoff: 08/29/2017

---

# <a name="enable-event-hubs-capture-using-the-azure-portal"></a>Включение записи концентраторов событий с помощью портала Azure

Функция [записи концентраторов событий][capture-overview] Azure позволяет автоматически доставлять данные потоковой передачи из концентраторов событий в выбранную учетную запись [хранилища BLOB-объектов Azure](https://azure.microsoft.com/services/storage/blobs/) или [Azure Data Lake Store](https://azure.microsoft.com/services/data-lake-store/).

Запись можно настроить при создании концентратора событий с помощью [портала Azure](https://portal.azure.com). Данные можно записывать в контейнер [хранилища BLOB-объектов Azure](https://azure.microsoft.com/services/storage/blobs/) или в учетную запись [Azure Data Lake Store](https://azure.microsoft.com/services/data-lake-store/).

Дополнительные сведения см. в статье [Запись концентраторов событий Azure][capture-overview].

## <a name="capture-data-to-an-azure-storage-account"></a>Запись данных в учетную запись службы хранилища Azure  

Запись можно включить при создании концентратора событий. Для этого на экране **Создание концентратора событий** на портале нажмите кнопку **Вкл**. Затем укажите учетную запись хранения и контейнер, выбрав **Служба хранилища Azure** в поле **поставщика записи**. Так как для функции записи концентраторов событий и хранилища используется проверка подлинности с взаимодействием между службами, указывать строку подключения к хранилищу не нужно. Средство выбора ресурсов автоматически выбирает универсальный код ресурса (URI) для вашей учетной записи хранения. При использовании Azure Resource Manager этот универсальный код ресурса необходимо явным образом указать как строку.

Продолжительность времени окна по умолчанию составляет 5 минут. Минимальное значение равно 1, а максимальное — 15. Диапазон **окна размера** составляет 10–500 МБ.

![][1]

## <a name="capture-data-to-an-azure-data-lake-store-account"></a>Запись данных в учетную запись Azure Data Lake Store

Для записи данных в Azure Data Lake Store создайте учетную запись Data Lake Store и концентратор событий.

### <a name="create-an-azure-data-lake-store-account-and-folders"></a>Создание учетной записи и папок Azure Data Lake Store

1. Создайте учетную запись Data Lake Store, следуя инструкциям в статье [Начало работы с Azure Data Lake Store с помощью портала Azure](../data-lake-store/data-lake-store-get-started-portal.md). 
2. Создайте папку в этой учетной записи, следуя инструкциям в разделе [Создание папок в учетной записи хранения озера данных Azure](../data-lake-store/data-lake-store-get-started-portal.md#createfolder).
3. На странице учетной записи Data Lake Store щелкните **Обозреватель данных**.
4. Щелкните **Доступ**.
5. Щелкните **Добавить**.
6. В поле **Поиск по имени или электронной почте** введите **Microsoft.EventHubs** и выберите этот вариант. 
7. Появится вкладка **Разрешения**. Задайте разрешения, как показано на следующем снимке экрана:

    ![][6]

8. Нажмите кнопку **ОК**.
9. Теперь создайте папку в корневой папке, выбрав целевую папку и щелкнув ее имя.
10. Щелкните **Доступ**.
11. Щелкните **Добавить**.
12. В поле **Поиск по имени или электронной почте** введите **Microsoft.EventHubs** и выберите этот вариант.
13. Снова появится вкладка **Разрешения**. Задайте разрешения, как показано на следующем снимке экрана:

    ![][5]

### <a name="create-an-event-hub"></a>Создание концентратора событий

1. Обратите внимание, что концентратор событий должен находиться в той же подписке Azure, что и только что созданная учетная запись Azure Data Lake Store. Создайте концентратор событий, нажав кнопку **Вкл.** в разделе **Запись** на странице портала **Создание концентратора событий**. 
2. На странице портала **Создание концентратора событий** в поле **поставщика записи** выберите **Azure Data Lake Store**.
3. В поле **Выберите Data Lake Store** укажите учетную запись Data Lake Store, созданную ранее, а в поле **Data Lake Path** (Путь к Data Lake) введите путь к созданной папке данных.

    ![][3]

## <a name="add-or-configure-capture-on-an-existing-event-hub"></a>Добавление или настройка функции записи для существующего концентратора событий

Функцию записи можно настроить в существующих концентраторах событий, расположенных в соответствующих пространствах имен. Чтобы включить запись в существующем концентраторе событий или изменить параметры записи, щелкните пространство имен. Появится экран **Основные компоненты**. Затем щелкните концентратор событий, для которого необходимо включить запись или изменить ее параметры. Наконец, на открывшейся странице щелкните раздел **Свойства** и измените параметры записи, как показано на следующих снимках экрана.

### <a name="azure-blob-storage"></a>Хранилище больших двоичных объектов Azure

![][2]

### <a name="azure-data-lake-store"></a>Хранилище озера данных Azure

![][4]

[1]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture1.png
[2]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture2.png
[3]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture3.png
[4]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture4.png
[5]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture5.png
[6]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture6.png

## <a name="next-steps"></a>Дальнейшие действия

- Дополнительные сведения о записи концентраторов событий см. в статье [Запись концентраторов событий Azure][capture-overview].
- Запись концентраторов событий можно также настроить с помощью шаблонов Azure Resource Manager. См. дополнительные сведения [о включении записи с помощью шаблона Azure Resource Manager](event-hubs-resource-manager-namespace-event-hub-enable-capture.md).

[capture-overview]: event-hubs-capture-overview.md
