---
title: Создание концентраторов событий с поддержкой Kafka | Документация Майкрософт
description: Сведения о создании пространства имен концентраторов событий Azure с поддержкой Kafka с помощью портала Azure.
services: event-hubs
documentationcenter: .net
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: dotnet
ms.topic: article
ms.date: 05/07/2018
ms.author: shvija
ms.openlocfilehash: 4f1d21be3c19dfbc764485fea47b6d4cb2171b3c
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/10/2018
ms.locfileid: "33941232"
---
# <a name="create-kafka-enabled-event-hubs"></a>Создание концентраторов событий с поддержкой Kafka

Концентраторы событий Azure представляют собой платформу как услугу (PaaS) для потока больших данных, которая принимает миллионы событий в секунду и предоставляет небольшую задержку и высокий уровень пропускной способности для аналитики и визуализации в режиме реального времени.

Концентраторы событий Azure для экосистемы Kafka предоставляют конечную точку. Эта конечная точка позволяет вашему пространству имен концентраторов событий распознавать протокол [Apache Kafka](https://kafka.apache.org/intro) и API в собственном коде. Эта возможность позволяет подключиться к концентраторам событий, как к темам Kafka, не изменяя клиентов протокола или запустив собственные кластеры. Концентраторы событий для экосистемы Kafka поддерживают [Apache Kafka версии 1.0.](https://kafka.apache.org/10/documentation.html) и более поздние версии.

В этой статье описывается создание пространства имен концентраторов событий и получение строки подключения, необходимой для подключения приложения Kafka к концентраторам событий с поддержкой Kafka.

## <a name="prerequisites"></a>предварительным требованиям

Если у вас еще нет подписки Azure, создайте [бесплатную учетную запись Azure](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio), прежде чем начинать работу.

## <a name="create-a-kafka-enabled-event-hubs-namespace"></a>Создание пространства имен концентраторов событий с поддержкой Kafka

1. Войдите на [портал Azure][Azure portal] и щелкните **Создать ресурс** в левой верхней части экрана.

2. Найдите концентраторы событий и выберите отображаемые параметры:
    
    ![Поиск концентраторов событий на портале](./media/event-hubs-create-kafka-enabled/event-hubs-create-event-hubs.png)
 
3. **Создайте пространство имен**, предоставив уникальное имя и включив Kafka в пространстве имен. Нажмите кнопку **Создать**.
    
    ![Создание пространства имен](./media/event-hubs-create-kafka-enabled/create-kafka-namespace.png)
 
4. После создания пространства имен, на вкладке **Параметры** щелкните **Политики общего доступа** для получения строки подключения.

    ![Выбор политик общего доступа](./media/event-hubs-create/create-event-hub7.png)

5. Вы можете выбрать значение по умолчанию **RootManageSharedAccessKey** или добавить новую политику. Щелкните имя политики и скопируйте строку подключения. 
    
    ![Выбор политики](./media/event-hubs-create/create-event-hub8.png)
 
6. Добавьте эту строку подключения в конфигурацию приложения Kafka.

Теперь можно выполнять потоковую передачу событий из приложений, использующих протокол Kafka, в концентраторы событий.

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о концентраторах событий см. в следующих статьях:

* [Потоковая передача данных в концентраторы событий для экосистемы Kafka](event-hubs-quickstart-kafka-enabled-event-hubs.md)
* [Event Hubs for Kafka ecosystems](event-hubs-for-kafka-ecosystem-overview.md) (Концентраторы событий для экосистемы Kafka)
* [Что такое концентраторы событий?](event-hubs-what-is-event-hubs.md)


[Azure portal]: https://portal.azure.com/
