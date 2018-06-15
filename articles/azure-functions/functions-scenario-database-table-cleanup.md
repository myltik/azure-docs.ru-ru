---
title: Выполнение задачи очистки базы данных с помощью Функций Azure | Документы Майкрософт
description: С помощью Функций Azure можно запланировать задачу, которая периодически подключается к базе данных SQL Azure для очистки строк.
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: ''
tags: ''
ms.assetid: 076f5f95-f8d2-42c7-b7fd-6798856ba0bb
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/22/2017
ms.author: glenga
ms.openlocfilehash: 2947fc6da0c4559e81cf97255b8375b020e0b657
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2018
ms.locfileid: "30231282"
---
# <a name="use-azure-functions-to-connect-to-an-azure-sql-database"></a>Подключение к базе данных SQL Azure с помощью Функций Azure
В этой статье показано, как с помощью Функций Azure создать запланированное задание, которое очищает строки в таблице базы данных SQL Azure. Новая функция скрипта C# создается на основе стандартного шаблона триггера на портале Azure. Для выполнения этого сценария необходимо также задать строку подключения к базе данных как параметр приложений в приложении-функции. В этом сценарии к базе данных применяется массовая операция. 

Чтобы функция обрабатывала отдельные операции создания, чтения, обновления и удаления (CRUD) в таблице мобильных приложений, следует использовать [привязки мобильных приложений](functions-bindings-mobile-apps.md).

## <a name="prerequisites"></a>предварительным требованиям

+ В этой статье используется функция, активируемая по таймеру. Чтобы создать версию этой функции на языке C#, выполните инструкции в статье [Создание в Azure функции, активируемой по таймеру](functions-create-scheduled-function.md).   

+ В этом разделе демонстрируется команда Transact-SQL, которая выполняет операцию массовой очистки в таблице **SalesOrderHeader** образца базы данных AdventureWorksLT. Чтобы создать образец базы данных AdventureWorksLT, выполните инструкции в статье [Создание базы данных SQL Azure на портале Azure](../sql-database/sql-database-get-started-portal.md). 

## <a name="get-connection-information"></a>Получение сведений о подключении

Необходимо получить строку подключения к базе данных, созданную при выполнении инструкций в статье [Создание базы данных SQL Azure на портале Azure](../sql-database/sql-database-get-started-portal.md).

1. Войдите на [портал Azure](https://portal.azure.com/).
 
3. В меню слева выберите пункт **Базы данных SQL** и на странице **Базы данных SQL** выберите имя своей базы данных.

4. Щелкните **Показать строки подключения к базам данных** и полностью скопируйте строку подключения **ADO.NET**. 

    ![Скопируйте строку подключения ADO.NET.](./media/functions-scenario-database-table-cleanup/adonet-connection-string.png)

## <a name="set-the-connection-string"></a>Задание строки подключения 

Выполнение функций в Azure происходит в рамках приложений-функций. Рекомендуется хранить строки подключения и другие секретные данные в параметрах приложения-функции. Использование параметров приложения позволяет предотвратить случайное раскрытие строки подключения в коде. 

1. Перейдите к приложению-функции, созданному в результате выполнения инструкций в статье [Создание в Azure функции, активируемой по таймеру](functions-create-scheduled-function.md).

2. Выберите **Функции платформы** > **Параметры приложения**.
   
    ![Параметры приложения-функции](./media/functions-scenario-database-table-cleanup/functions-app-service-settings.png)

2. Прокрутите страницу вниз до раздела **Строки подключения** и добавьте строку подключения, используя параметры, указанные в таблице.
   
    ![Добавьте строку подключения в параметры приложения-функции.](./media/functions-scenario-database-table-cleanup/functions-app-service-settings-connection-strings.png)

    | Параметр       | Рекомендуемое значение | Описание             | 
    | ------------ | ------------------ | --------------------- | 
    | **Имя**  |  sqldb_connection  | Используется для доступа к сохраненной строке подключения в коде функции.    |
    | **Значение** | Скопированная строка  | Вставьте строку подключения, скопированную в предыдущем разделе, и замените заполнители `{your_username}` и `{your_password}` настоящими значениями. |
    | **Тип** | База данных SQL | Используйте подключение к базе данных SQL по умолчанию. |   

3. Выберите команду **Сохранить**.

Теперь можно добавить код функции C#, который подключается к базе данных SQL.

## <a name="update-your-function-code"></a>Обновление кода функции

1. В приложении-функции на портале выберите функцию, активируемую по таймеру.
 
3. Добавьте следующие ссылки на сборки в начало существующего кода функции скрипта C#:

    ```cs
    #r "System.Configuration"
    #r "System.Data"
    ```
    >[!NOTE]
    >Код в этих примерах представляет собой скрипт C# с портала. При локальной разработке предкомпилированной функции C# необходимо добавить ссылки на эти сборки в локальном проекте.  

3. Добавьте в функцию следующие операторы `using` :
    ```cs
    using System.Configuration;
    using System.Data.SqlClient;
    using System.Threading.Tasks;
    ```

4. Замените имеющуюся функцию `Run` следующим кодом:
    ```cs
    public static async Task Run(TimerInfo myTimer, TraceWriter log)
    {
        var str = ConfigurationManager.ConnectionStrings["sqldb_connection"].ConnectionString;
        using (SqlConnection conn = new SqlConnection(str))
        {
            conn.Open();
            var text = "UPDATE SalesLT.SalesOrderHeader " + 
                    "SET [Status] = 5  WHERE ShipDate < GetDate();";

            using (SqlCommand cmd = new SqlCommand(text, conn))
            {
                // Execute the command and log the # rows affected.
                var rows = await cmd.ExecuteNonQueryAsync();
                log.Info($"{rows} rows were updated");
            }
        }
    }
    ```

    Этот пример команды обновляет столбец `Status` в соответствии с датой отгрузки. Он должен обновить 32 строки данных.

5. Нажмите кнопку **Сохранить**. Выполнение следующей функции можно просмотреть в окне **Журналы**. Запишите число строк, обновленных в таблице **SalesOrderHeader**.

    ![Просмотрите журналы функции.](./media/functions-scenario-database-table-cleanup/functions-logs.png)

## <a name="next-steps"></a>Дополнительная информация

Узнайте, как использовать Функции с Logic Apps для интеграции с другими службами.

> [!div class="nextstepaction"] 
> [Создание функции, интегрируемой с Logic Apps](functions-twitter-email.md)

Дополнительные сведения о Функциях см. в следующих статьях:

* [Справочник разработчика по функциям Azure](functions-reference.md)  
  Справочник программиста по созданию функций, а также определению триггеров и привязок.
* [Testing Azure Functions](functions-test-a-function.md)  
  (Тестирование функций Azure) Описание различных средств и методов тестирования функций.  
