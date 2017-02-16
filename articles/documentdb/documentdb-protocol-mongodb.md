---
title: "Что такое поддержка протокола DocumentDB для MongoDB? | Документация Майкрософт"
description: "Что такое поддержка протокола DocumentDB для MongoDB? Этот протокол позволяет использовать управляемую облачную службу Azure DocumentDB в качестве хранилища данных для приложений, написанных для MongoDB."
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
ms.date: 11/23/2016
ms.author: anhoh
translationtype: Human Translation
ms.sourcegitcommit: 0703fa4f4d3eb9f23aa9b3c0b90267fac9557f13
ms.openlocfilehash: 876fff6a63ccf1a3c10a6852927c60b454436954


---
# <a name="what-is-documentdb-protocol-support-for-mongodb"></a>Что такое поддержка протокола DocumentDB для MongoDB?

Теперь базы данных DocumentDB можно использовать как хранилище данных для приложений, написанных для MongoDB. Это означает, что используя имеющиеся [драйверы](https://docs.mongodb.org/ecosystem/drivers/) для баз данных MongoDB, приложение, написанное для MongoDB, теперь может взаимодействовать с DocumentDB и использовать базы данных DocumentDB вместо баз данных MongoDB. Чаще всего, чтобы перейти с использования MongoDB к DocumentDB, нужно просто изменить строку подключения. Эта возможность позволяет пользователям легко создавать и запускать приложения базы данных MongoDB в облаке Azure, используя полностью управляемые и масштабируемые базы данных NoSQL DocumentDB и продолжая применять знакомые навыки и средства для MongoDB.

## <a name="what-is-the-benefit-of-using-documentdb-protocol-support-for-mongodb"></a>Преимущества поддержки протокола DocumentDB для MongoDB
**Отсутствие сервера управления.** DocumentDB — это полностью управляемая служба. Это означает, что вам не нужно самостоятельно управлять какой-либо инфраструктурой или виртуальными машинами. DocumentDB доступна в более 20 [регионах Azure](https://azure.microsoft.com/regions/services/).

**Безграничные возможности масштабирования.** DocumentDB позволяет выполнять эластическое масштабирование пропускной способности и хранилища независимо друг от друга. Вы можете без труда добавить емкость, чтобы обрабатывать миллионы запросов в секунду.

**Корпоративный класс.** DocumentDB поддерживает несколько локальных реплик, что гарантирует высокий уровень доступности (99,99 %) и защиту данных в случае локальных и региональных сбоев. Эта служба имеет [сертификаты соответствия требованиям](https://www.microsoft.com/trustcenter) и функции обеспечения безопасности корпоративного класса. 

**Совместимость с MongoDB.** Поддержка протокола DocumentDB для MongoDB обеспечивает совместимость с MongoDB. Это позволяет использовать имеющийся код, приложения, драйверы и средства для работы с DocumentDB. 

## <a name="how-to-get-started"></a>Как приступить к работе
Создайте учетную запись DocumentDB с поддержкой протокола для MongoDB на [портале Azure](https://portal.azure.com) и подключитесь к новой учетной записи. 

*Вот и все!*

Подробные инструкции см. в документации по [созданию учетной записи](documentdb-create-mongodb-account.md) и [подключению к ней](documentdb-connect-mongodb-account.md).

## <a name="next-steps"></a>Дальнейшие действия
* Сведения о создании учетной записи DocumentDB с поддержкой протокола MongoDB см. в [этом руководстве](documentdb-create-mongodb-account.md).
* Инструкции по получению сведений о строке подключения для учетной записи DocumentDB с поддержкой протокола MongoDB см. в [этом руководстве](documentdb-connect-mongodb-account.md).
* Сведения о создании подключения между базой данных DocumentDB и приложением MongoDB в MongoChef см. в [этом руководстве](documentdb-mongodb-mongochef.md).
* Изучите [примеры](documentdb-mongodb-samples.md)использования DocumentDB с поддержкой протокола MongoDB.




<!--HONumber=Nov16_HO4-->


