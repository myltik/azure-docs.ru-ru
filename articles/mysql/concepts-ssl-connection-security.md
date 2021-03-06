---
title: "Возможность подключения SSL для службы \"База данных Azure для MySQL\""
description: "Сведения о настройке базы данных Azure для MySQL и связанных приложений для правильного использования SSL-соединений."
services: mysql
author: JasonMAnderson
ms.author: janders
editor: jasonwhowell
manager: kfile
ms.service: mysql-database
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: f59d5eab9772515a3c59f887a48d597d27bab135
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/28/2018
---
# <a name="ssl-connectivity-in-azure-database-for-mysql"></a>SSL-соединения в базе данных Azure для MySQL
База данных Azure для MySQL поддерживает подключение сервера базы данных к клиентским приложениям с помощью SSL (Secure Sockets Layer). Применение SSL-соединений между сервером базы данных и клиентскими приложениями обеспечивает защиту от атак "злоумышленник в середине" за счет шифрования потока данных между сервером и приложением.

## <a name="default-settings"></a>Параметры по умолчанию
По умолчанию в службе базы данных должно быть настроено обязательное использование SSL-соединений при подключении к MySQL.  Мы рекомендуем не отключать параметр SSL без необходимости. 

При подготовке нового сервера базы данных Azure для MySQL с помощью портала Azure и интерфейса командной строки применение SSL-соединений включается по умолчанию. 

Строки подключения для различных языков программирования отображаются на портале Azure. Они включают необходимые параметры SSL для подключения к базе данных. На портале Azure выберите свой сервер. В разделе **Параметры** выберите **Строки подключения**. Значение параметра SSL зависит от соединителя. Например, может использоваться "ssl=true", "sslmode=require", "sslmode=required" или другой вариант.

Чтобы узнать, как включить или отключить SSL-соединение при разработке приложения, ознакомьтесь со [статьей, посвященной настройке SSL](howto-configure-ssl.md). 

## <a name="next-steps"></a>Дополнительная информация
[Библиотеки подключений для базы данных Azure для MySQL](concepts-connection-libraries.md)
