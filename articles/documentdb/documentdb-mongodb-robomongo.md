---
title: "Использование Robomongo с учетной записью Azure DocumentDB с поддержкой протокола MongoDB | Документация Майкрософт"
description: "Узнайте, как использовать Robomongo с учетной записью DocumentDB с поддержкой протокола MongoDB, доступной в предварительной версии."
keywords: Robomongo
services: documentdb
author: AndrewHoh
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: 352c5fb9-8772-4c5f-87ac-74885e63ecac
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2016
ms.author: anhoh
translationtype: Human Translation
ms.sourcegitcommit: a9d78597f708020eaec3fc03d8c8fc2652bddb50
ms.openlocfilehash: 6a7fb7052700bdeed5a3ca43da85304bba4230d8


---
# <a name="use-robomongo-with-a-documentdb-account-with-protocol-support-for-mongodb"></a>Использование Robomongo с учетной записью DocumentDB с поддержкой протокола MongoDB
Для подключения с помощью Robomongo к учетной записи Azure DocumentDB с поддержкой протокола MongoDB вам потребуется:

* скачать и установить [Robomongo](https://robomongo.org/);
* подготовить сведения о [строке подключения](documentdb-connect-mongodb-account.md) для учетной записи DocumentDB с поддержкой протокола MongoDB;

## <a name="connect-using-robomongo"></a>Подключение с использованием Robomongo
Чтобы добавить к подключениям Robomongo MongoDB учетную запись DocumentDB с поддержкой протокола MongoDB, сделайте следующее.

1. Извлеките сведения о подключении DocumentDB с поддержкой протокола MongoDB. Ознакомьтесь с инструкциями [здесь](documentdb-connect-mongodb-account.md).

    ![Снимок экрана, колонка строки подключения](./media/documentdb-mongodb-robomongo/connectionstringblade.png)
2. Запустите *Robomongo.exe*.

3. Нажмите кнопку подключения под меню **File** (Файл) для управления подключениями. Щелкните **Create** (Создать) в окне **MongoDB Connections** (Подключения MongoDB). Откроется окно **Connection Settings** (Параметры подключения).

4. В окне **Connection Settings** (Параметры подключения) выберите имя. Затем найдите **узел** и **порт**, указанные в сведениях о подключении, полученных на шаге 1, и введите их в полях **Address** (Адрес) и **Port** (Порт) соответственно.

    ![Снимок экрана окна управления подключениями Robomongo](./media/documentdb-mongodb-robomongo/manageconnections.png)
5. На вкладке **Authentication** (Аутентификация) установите флажок **Perform authentication** (Выполнять аутентификацию). Затем введите базу данных (по умолчанию — *Admin*), **имя пользователя** и **пароль**.
**Имя пользователя** и **пароль** можно найти в данных подключения, полученных на шаге 1.

    ![Снимок экрана вкладки аутентификации Robomongo](./media/documentdb-mongodb-robomongo/authentication.png)
6. На вкладке **SSL** установите флажок **Use SSL protocol** (Использовать протокол SSL), затем измените значение параметра **Authentication Method** (Метод аутентификации) на **Self-signed Certificate** (Самозаверяющий сертификат).

    ![Снимок экрана вкладки SSL Robomongo](./media/documentdb-mongodb-robomongo/SSL.png)
7. Наконец, нажмите кнопку **Test** (Проверить), чтобы проверить возможность подключения, затем нажмите кнопку **Save** (Сохранить).

## <a name="next-steps"></a>Дальнейшие действия
* Изучите [примеры](documentdb-mongodb-samples.md)использования DocumentDB с поддержкой протокола MongoDB.



<!--HONumber=Dec16_HO1-->


