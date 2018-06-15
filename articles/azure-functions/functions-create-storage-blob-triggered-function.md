---
title: Создание функции, активируемой хранилищем BLOB-объектов, в Azure | Документация Майкрософт
description: Создавайте независимые от сервера функции, активируемые элементами, добавляемыми в хранилище BLOB-объектов Azure.
services: azure-functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: ''
tags: ''
ms.assetid: d6bff41c-a624-40c1-bbc7-80590df29ded
ms.service: functions
ms.devlang: multiple
ms.topic: quickstart
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 03/27/2018
ms.author: glenga
ms.custom: mvc, cc996988-fb4f-47
ms.openlocfilehash: 928ea1dbb68206e128f0593ba15cb48935ab1ccf
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/30/2018
ms.locfileid: "30282119"
---
# <a name="create-a-function-triggered-by-azure-blob-storage"></a>Создание функции, активируемой хранилищем BLOB-объектов Azure

Узнайте, как создавать функцию, активируемую при добавлении файлов или их обновлении в хранилище BLOB-объектов Azure.

![Просмотр сообщения в журналах](./media/functions-create-storage-blob-triggered-function/function-app-in-portal-editor.png)

## <a name="prerequisites"></a>предварительным требованиям

+ Скачайте и установите [обозреватель хранилищ Microsoft Azure](http://storageexplorer.com/).
+ Подписка Azure. Если у вас еще нет подписки Azure, создайте [бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начать работу.

## <a name="create-an-azure-function-app"></a>Создание приложения-функции Azure

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

![Приложение-функция успешно создана.](./media/functions-create-first-azure-function/function-app-create-success.png)

Затем создайте функцию в новом приложении-функции.

<a name="create-function"></a>

## <a name="create-a-blob-storage-triggered-function"></a>Создание функции, активируемой хранилищем BLOB-объектов

1. Разверните приложение-функцию и нажмите кнопку **+** рядом с элементом **Функции**. Если это первая функция в приложении-функции, выберите **Пользовательская функция**. Откроется полный набор шаблонов функций.

    ![Страница быстрого начала работы с функциями на портале Azure](./media/functions-create-storage-blob-triggered-function/add-first-function.png)

2. В поле поиска введите `blob` и выберите нужный язык для шаблона триггера для хранилища BLOB-объектов.

    ![Выбор шаблона триггера для хранилища BLOB-объектов](./media/functions-create-storage-blob-triggered-function/functions-create-blob-storage-trigger-portal.png)
 
3. Затем используйте настройки, указанные в таблице под изображением.

    ![Создание функции, активируемой хранилищем BLOB-объектов.](./media/functions-create-storage-blob-triggered-function/functions-create-blob-storage-trigger-portal-2.png)

    | Параметр | Рекомендуемое значение | Описание |
    |---|---|---|
    | **Имя** | Уникальное для вашего приложения-функции | Имя функции, активируемой большим двоичным объектом. |
    | **Путь**   | samples-workitems/{name}    | Расположение в хранилище BLOB-объектов отслеживается. Имя файла большого двоичного объекта передается в привязке как параметр _name_.  |
    | **Подключение к учетной записи хранения** | AzureWebJobsStorage | Вы можете использовать подключение к учетной записи хранения, которое уже используется вашим приложением-функцией, или создать его.  |

3. Щелкните **Создать**, чтобы создать функцию.

Затем необходимо подключиться к своей учетной записи хранения Azure и создать контейнер **samples-workitems**.

## <a name="create-the-container"></a>Создание контейнера

1. Щелкните **Интегрировать** в своей функции, затем разверните узел **Документация** и скопируйте **имя учетной записи** и **ключ учетной записи**. Эти учетные данные используются для подключения к учетной записи хранения. Если вы уже подключились к учетной записи хранения, перейдите к шагу 4.

    ![Получение учетных данных для подключения к учетной записи хранения.](./media/functions-create-storage-blob-triggered-function/functions-storage-account-connection.png)

1. Запустите инструмент [Обозреватель службы хранилища Microsoft Azure](http://storageexplorer.com/), щелкните значок подключения слева, выберите **Use a storage account name and key** (Использовать имя и ключ учетной записи хранения), а затем щелкните **Далее**.

    ![Запуск инструмента "Обозреватель учетной записи хранения"](./media/functions-create-storage-blob-triggered-function/functions-storage-manager-connect-1.png)

1. Введите **имя учетной записи** и **ключ учетной записи** из шага 1, щелкните **Далее**, а затем — **Подключить**. 

    ![Введите учетные данные хранилища и подключитесь.](./media/functions-create-storage-blob-triggered-function/functions-storage-manager-connect-2.png)

1. Разверните подключенные учетную запись хранения, щелкните правой кнопкой мыши **Blob containers** (Контейнеры больших двоичных объектов), а затем щелкните **Create blob container** (Создать контейнер больших двоичных объектов), введите `samples-workitems` и нажмите клавишу ВВОД.

    ![Создание очереди службы хранилища](./media/functions-create-storage-blob-triggered-function/functions-storage-manager-create-blob-container.png)

Теперь, когда у вас есть контейнер больших двоичных объектов, вы можете проверить функцию, отправив файл в контейнер.

## <a name="test-the-function"></a>Проверка функции

1. На портале Azure перейдите к вашей функции, в нижней части страницы разверните **Журналы** и убедитесь, что потоковая передача журналов не остановлена.

1. В обозревателе службы хранилища разверните свою учетную запись хранения, **контейнеры больших двоичных объектов** и **samples-workitems**. Щелкните **Отправка**, а затем **Отправка файлов**.

    ![Отправьте файл в контейнер больших двоичных объектов.](./media/functions-create-storage-blob-triggered-function/functions-storage-manager-upload-file-blob.png)

1. В диалоговом окне **Отправка файлов** щелкните поле **Файлы**. Перейдите к файлу на вашем локальном компьютере, например к файлу изображения, выберите его и щелкните **Открыть**, а затем **Отправить**.

1. Вернитесь к журналам функции и убедитесь, что большой двоичный объект был считан.

   ![Просмотр сообщения в журналах](./media/functions-create-storage-blob-triggered-function/functions-blob-storage-trigger-view-logs.png)

    >[!NOTE]
    > Если ваше приложение-функция выполняется в рамках плана потребления по умолчанию, между добавлением или обновлением большого двоичного объекта и активацией функции могут возникнуть задержки до нескольких минут. Выполняйте свое приложение-функцию в рамках плана службы приложений, если требуется малая задержка в функции, активируемой большим двоичным объектом.

## <a name="clean-up-resources"></a>Очистка ресурсов

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Дополнительная информация

Вы создали функцию, которая выполняется при добавлении или обновлении большого двоичного объекта в хранилище BLOB-объектов. 

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

Дополнительные сведения о триггерах хранилища BLOB-объектов см.в статье [Привязки больших двоичных объектов службы хранилища для Функций Azure](functions-bindings-storage-blob.md).
