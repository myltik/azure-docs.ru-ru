---
title: Поддержка протокола DocumentDB для MongoDB | Microsoft Docs
description: Сведения о поддержке протокола DocumentDB для MongoDB, которая теперь общедоступна в предварительной версии.
keywords: MongoDB
services: documentdb
author: AndrewHoh
manager: jhubbard
editor: ''
documentationcenter: ''

ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/23/2016
ms.author: anhoh

---
# <a name="documentdb-protocol-support-for-mongodb"></a>Поддержка протокола DocumentDB для MongoDB
## <a name="what-is-azure-documentdb?"></a>Что такое Azure DocumentDB?
Azure DocumentDB — это полностью управляемая служба базы данных NoSQL, созданная для быстрой и прогнозируемой работы, высокой доступности, автоматического масштабирования и простоты разработки. Благодаря гибкой модели данных, согласованности и небольшой продолжительности задержек, а также широким возможностям запросов она прекрасно подходит для веб-приложений, мобильных приложений, игр, приложений IoT и многих других приложений, которым требуется эффективное масштабирование. Дополнительные сведения см. в статье [Введение DocumentDB: база данных NoSQL JSON](documentdb-introduction.md).

## <a name="what-is-documentdb-protocol-support-for-mongodb?"></a>Что такое поддержка протокола DocumentDB для MongoDB?
Теперь базы данных DocumentDB можно использовать как хранилище данных для приложений, написанных для MongoDB. Используя существующие [драйверы](https://docs.mongodb.org/ecosystem/drivers/) для MongoDB, приложения могут легко и прозрачно взаимодействовать с DocumentDB. Чаще всего это происходит при помощи простого изменения строки подключения.  С помощью функций предварительного просмотра пользователи могут легко создавать и запускать приложения в облаке Azure, используя полностью управляемые и масштабируемые базы данных NoSQL DocumentDB и продолжая применять знакомые навыки и средства для MongoDB.

Поддержка протокола DocumentDB для MongoDB включает основные функции API MongoDB, позволяя создавать, читать, обновлять и удалять данные (выполнять с ними действия CRUD), а также выполнять запросы к базе данных. Приоритеты среди текущих внедряемых возможностей расставляются согласно потребностям наиболее распространенных платформ и средств, а также потребностям крупных пользователей MongoDB, рассматривающих Azure в качестве своей облачной платформы.

## <a name="next-steps"></a>Дальнейшие действия
* Узнайте, как [создать](documentdb-create-mongodb-account.md) учетную запись DocumentDB с поддержкой протокола MongoDB.
* Узнайте, как [подключиться](documentdb-connect-mongodb-account.md) к учетной записи DocumentDB с поддержкой протокола MongoDB.
* Узнайте, как [использовать MongoChef](documentdb-mongodb-mongochef.md) с учетной записью DocumentDB с поддержкой протокола MongoDB.
* Изучите [примеры](documentdb-mongodb-samples.md)использования DocumentDB с поддержкой протокола MongoDB.

<!--HONumber=Oct16_HO2-->


