---
title: "Что такое DocumentDB: API для MongoDB? | Документация Майкрософт"
description: "Сведения о DocumentDB: API для MongoDB и о том, как можно легко запускать существующие приложения MongoDB в облаке Azure."
keywords: "что такое MongoDB"
services: documentdb
author: AndrewHoh
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: 4afaf40d-c560-42e0-83b4-a64d94671f0a
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/06/2017
ms.author: anhoh
translationtype: Human Translation
ms.sourcegitcommit: 72b2d9142479f9ba0380c5bd2dd82734e370dee7
ms.openlocfilehash: 6c143a18883c99a24264d3174df7ec214d696fb5
ms.lasthandoff: 03/08/2017


---
# <a name="what-is-documentdb-api-for-mongodb"></a>Что такое DocumentDB: API для MongoDB?

Теперь базы данных DocumentDB можно использовать как хранилище данных для приложений, написанных для MongoDB. Это означает, что используя имеющиеся [драйверы](https://docs.mongodb.org/ecosystem/drivers/) для баз данных MongoDB, приложение, написанное для MongoDB, теперь может взаимодействовать с DocumentDB и использовать базы данных DocumentDB вместо баз данных MongoDB. Чаще всего, чтобы перейти с использования MongoDB к DocumentDB, нужно просто изменить строку подключения. Эта возможность позволяет пользователям легко создавать и запускать приложения базы данных MongoDB в облаке Azure, используя полностью управляемые и масштабируемые базы данных NoSQL DocumentDB и продолжая применять знакомые навыки и средства для MongoDB.

## <a name="what-is-the-benefit-of-using-documentdb-api-for-mongodb"></a>Преимущества использования DocumentDB: API для MongoDB
**Отсутствие сервера управления.** DocumentDB — это полностью управляемая служба. Это означает, что вам не нужно самостоятельно управлять какой-либо инфраструктурой или виртуальными машинами. DocumentDB доступна в более 20 [регионах Azure](https://azure.microsoft.com/regions/services/).

**Безграничные возможности масштабирования.** DocumentDB позволяет выполнять эластическое масштабирование пропускной способности и хранилища независимо друг от друга. Вы можете без труда добавить емкость, чтобы обрабатывать миллионы запросов в секунду.

**Корпоративный класс.** DocumentDB поддерживает несколько локальных реплик, что гарантирует высокий уровень доступности (99,99 %) и защиту данных в случае локальных и региональных сбоев. Эта служба имеет [сертификаты соответствия требованиям](https://www.microsoft.com/trustcenter) и функции обеспечения безопасности корпоративного класса. 

**Совместимость с MongoDB**. DocumentDB: API для MongoDB обеспечивает совместимость с MongoDB. Это позволяет использовать имеющийся код, приложения, драйверы и средства для работы с DocumentDB. 

Узнайте больше в этом видео из цикла "Azure, пятница" от Скотта Хансельмана (Scott Hanselman) и главного технического руководителя DocumentDB, Кирилла Гаврилюка.

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/DocumentDB-Database-as-a-Service-for-MongoDB-Developers/player]
> 


## <a name="how-to-get-started"></a>Как приступить к работе
Создайте учетную запись DocumentDB: API для MongoDB на [портале Azure](https://portal.azure.com) и подключитесь к ней. 

*Вот и все!*

Подробные инструкции см. в документации по [созданию учетной записи](documentdb-create-mongodb-account.md) и [подключению к ней](documentdb-connect-mongodb-account.md).

## <a name="next-steps"></a>Дальнейшие действия
* Инструкции по получению сведений о строке подключения для учетной записи MongoDB см. в [этом руководстве](documentdb-connect-mongodb-account.md).
* Сведения о создании подключения между базой данных DocumentDB и приложением MongoDB в MongoChef см. в [этом руководстве](documentdb-mongodb-mongochef.md).
* Ознакомьтесь с [примерами](documentdb-mongodb-samples.md) DocumentDB: API для MongoDB.


