---
title: "Политики доступа к данным в Azure Time Series Insights | Документация Майкрософт"
description: "Это руководство содержит сведения об управлении политиками доступа к данным в Time Series Insights"
keywords: 
services: time-series-insights
documentationcenter: 
author: op-ravi
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: time-series-insights
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/01/2017
ms.author: omravi
ms.translationtype: Human Translation
ms.sourcegitcommit: 300958a69fc854cb8db02120a383a4cbbfcacd7b
ms.openlocfilehash: e975c6d8f217bc73948c0c046204b16b1a742bc7
ms.contentlocale: ru-ru
ms.lasthandoff: 05/20/2017

---

# <a name="grant-data-access-to-a-time-series-insights-environment-using-azure-portal"></a>Предоставление доступа к данным для среды Time Series Insights с помощью портала Azure

Среда Time Series Insights содержит 2 типа независимых политик доступа:

* политики управления доступом;
* политики доступа к данным.

Оба типа политик предоставляют субъектам Azure Active Directory (пользователям или приложениям) различные разрешения для конкретной среды. Субъекты (пользователи и приложения) должны принадлежать к службе Active Directory (или клиенту Azure), связанной с подпиской, содержащей среду.

Политики управления доступом предоставляют разрешения, связанные с конфигурацией среды, например:
*    создание и удаление среды, источников событий и справочных наборов данных;
*    управление политиками доступа к данным.

Политики доступа к данным предоставляют разрешения для отправки запросов данных, обработки ссылочных данных в среде, а также предоставляют другим пользователям среды доступ к сохраненным запросам и перспективам.

Эти политики позволяют четко разделить доступ к управлению среды и доступ к данным в среде. Например, можно установить среду таким образом, что владелец или создатель среды не будет иметь доступа к данным. Пользователи и службы, которые могут считывать данные из среды, также могут не иметь доступа к конфигурации среды.

## <a name="grant-data-access"></a>Предоставление доступа к данным
Ниже показано, как предоставить доступ к данным для участника-пользователя.

1.    Выполните вход на [портал Azure](https://portal.azure.com).
2.    Щелкните "Все ресурсы" в меню слева на портале Azure.
3.    Выберите среду Time Series Insights.

  ![Управление исходной средой Time Series Insights](media/data-access/getstarted-grant-data-access1.png)

4.    Выберите Data Plane Access (Доступ к плоскости данных) и щелкните "Добавить".

  ![Управление источником Time Series Insights — "Добавить"](media/data-access/getstarted-grant-data-access2.png)

5.    Щелкните "Выбор пользователя".
6.    Найдите пользователя по электронной почте и выберите его.
7.    В колонке "Выбор пользователя" нажмите кнопку "Выбрать".

  ![Управление источником Time Series Insights — "Выбор пользователя"](media/data-access/getstarted-grant-data-access3.png)

8.    Щелкните "Выбор ролей".
9.    Выберите "Участник", если вы хотите разрешить пользователю изменять ссылочные данные и предоставить другим пользователям среды доступ к сохраненным запросам и перспективам. В противном случае выберите "Читатель", чтобы разрешить пользователю запрашивать данные в среде и сохранять в ней личные (не общие) запросы.
10.    В колонке "Выбор ролей" нажмите кнопку "ОК".

  ![Управление источником Time Series Insights — "Выбор ролей"](media/data-access/getstarted-grant-data-access4.png)

11.    В колонке Select User Role (Выбор роли пользователя) нажмите кнопку "ОК".
12.    Вы должны увидеть следующее:

  ![Управление источником Time Series Insights — результаты](media/data-access/getstarted-grant-data-access5.png)

## <a name="next-steps"></a>Дальнейшие действия

* [Создайте источник событий](time-series-insights-add-event-source.md).
* [Отправьте события](time-series-insights-send-events.md) в источник событий.
* Просмотрите свою среду на [портале Time Series Insights](https://insights.timeseries.azure.com).

