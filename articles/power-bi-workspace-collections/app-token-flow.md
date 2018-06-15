---
title: Аутентификация и авторизация в коллекциях рабочих областей Power BI | Документация Майкрософт
description: Аутентификация и авторизация в коллекциях рабочих областей Power BI.
services: power-bi-embedded
documentationcenter: ''
author: markingmyname
manager: kfile
editor: ''
tags: ''
ROBOTS: NOINDEX
ms.assetid: 1c1369ea-7dfd-4b6e-978b-8f78908fd6f6
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 09/20/2017
ms.author: maghan
ms.openlocfilehash: 74d34e708fb74daa295642d50643b78af8f6cb7a
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2018
ms.locfileid: "31412191"
---
# <a name="authenticating-and-authorizing-with-power-bi-workspace-collections"></a>Аутентификация и авторизация в коллекциях рабочих областей Power BI

Вместо явной проверки подлинности пользователей служба коллекции рабочих областей Power BI использует для аутентификации и авторизации **ключи** и **маркеры приложений**. В этой модели ваше приложение управляет аутентификацией и авторизацией пользователей. При необходимости приложение создает и отправляет маркеры приложений, которые сообщают службе о том, что необходимо подготовить запрошенный отчет. Этот подход не требует использования Azure Active Directory для проверки подлинности и авторизации пользователей, хотя вы можете это сделать.

> [!IMPORTANT]
> Использовать службу "Коллекции рабочих областей Power BI" не рекомендуется. Она будет доступна до июня 2018 года или до даты, указанной в договоре. Мы советуем организовать перенос приложения в Power BI Embedded, чтобы избежать прерываний в его работе. Сведения о том, как перенести данные из коллекций рабочих областей Power BI в Power BI Embedded, см. в [этой статье](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

## <a name="two-ways-to-authenticate"></a>Два метода проверки подлинности

**Ключи** можно использовать для всех вызовов REST API в службе коллекций рабочих областей Power BI. Их можно найти на **портале Microsoft Azure**, щелкнув **Все параметры**, а затем выбрав **Ключи доступа**. С ключом следует обращаться как с паролем. Эти ключи предоставляют разрешение на выполнение любого вызова REST API в определенной коллекции рабочих областей.

Чтобы использовать ключ для вызова REST, добавьте следующий заголовок авторизации:

    Authorization: AppKey {your key}

**Маркеры приложений** используются для всех запросов внедрения. Они предназначены для выполнения на стороне клиента и, следовательно, ограничены одним отчетом. Для них рекомендуется задать время окончания срока действия.

Маркеры приложения представляют собой веб-маркеры JWT, подписанные одним из ключей.

Маркер приложения может содержать следующие утверждения:

| Утверждение | ОПИСАНИЕ |
| --- | --- |
| **ver** |Версия маркера приложения. Текущая версия — 0.2.0. |
| **aud** |Целевой получатель маркера. Для коллекций рабочих областей Power BI используйте https://analysis.windows.net/powerbi/api. |
| **iss** |Строка, указывающая приложение, которое выдало маркер. |
| **type** |Тип маркера приложения, который создается. Сейчас поддерживается только тип **embed** |
| **wcn** |Имя коллекции рабочих областей, для которой выдается маркер |
| **wid** |Идентификатор рабочей области, для которой выдается маркер |
| **rid** |Идентификатор отчета, для которого выдается маркер |
| **username** (необязательно) |Используется с RLS. Это строка, которая помогает идентифицировать пользователя при применении правил RLS. |
| **roles** (необязательно) |Строка, содержащая роли, которые выбираются при применении правил безопасности на уровне строк. При выборе нескольких ролей их нужно передавать в виде массива строк. |
| **scp** (необязательно) |Строка, содержащая области разрешений. При выборе нескольких ролей их нужно передавать в виде массива строк. |
| **exp** (необязательно) |Указывает время истечения срока действия маркера. Это значение следует передавать в качестве меток времени Unix. |
| **nbf** (необязательно) |Указывает, когда маркер станет допустимым. Это значение следует передавать в качестве меток времени Unix. |

Пример маркера приложения выглядит следующим образом.

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ2ZXIiOiIwLjIuMCIsInR5cGUiOiJlbWJlZCIsIndjbiI6Ikd1eUluQUN1YmUiLCJ3aWQiOiJkNGZlMWViMS0yNzEwLTRhNDctODQ3Yy0xNzZhOTU0NWRhZDgiLCJyaWQiOiIyNWMwZDQwYi1kZTY1LTQxZDItOTMyYy0wZjE2ODc2ZTNiOWQiLCJzY3AiOiJSZXBvcnQuUmVhZCIsImlzcyI6IlBvd2VyQklTREsiLCJhdWQiOiJodHRwczovL2FuYWx5c2lzLndpbmRvd3MubmV0L3Bvd2VyYmkvYXBpIiwiZXhwIjoxNDg4NTAyNDM2LCJuYmYiOjE0ODg0OTg4MzZ9.v1znUaXMrD1AdMz6YjywhJQGY7MWjdCR3SmUSwWwIiI
```

После декодирования он примет следующий вид.

```
Header

{
    typ: "JWT",
    alg: "HS256:
}

Body

{
  "ver": "0.2.0",
  "wcn": "SupportDemo",
  "wid": "ca675b19-6c3c-4003-8808-1c7ddc6bd809",
  "rid": "96241f0f-abae-4ea9-a065-93b428eddb17",
  "iss": "PowerBISDK",
  "aud": "https://analysis.windows.net/powerbi/api",
  "exp": 1360047056,
  "nbf": 1360043456
}

```

В пакетах SDK доступны методы, которые упрощают создание маркеров приложений. Например, для .NET это класс [Microsoft.PowerBI.Security.PowerBIToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken) и методы [CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN#methods_).

Методы для пакета SDK для .NET см. в статье [Класс Scopes](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.scopes).

## <a name="scopes"></a>Области действия

При использовании маркеров внедрения может потребоваться ограничить использование ресурсов, к которым предоставляется доступ. Для этого можно создать маркер с заданной областью разрешений.

Ниже приведены доступные области применения коллекций рабочих областей Power BI.

|Область|ОПИСАНИЕ|
|---|---|
|Dataset.Read|Предоставляет разрешение на чтение указанного набора данных.|
|Dataset.Write|Предоставляет разрешение на запись в указанном наборе данных.|
|Dataset.ReadWrite|Предоставляет разрешение на чтение и запись в указанном наборе данных.|
|Report.Read|Предоставляет разрешение на просмотр указанного отчета.|
|Report.ReadWrite|Предоставляет разрешение на просмотр и редактирование указанного отчета.|
|Workspace.Report.Create|Предоставляет разрешение на создание нового отчета в заданной рабочей области.|
|Workspace.Report.Copy|Предоставляет разрешение на клонирование существующего отчета в заданной рабочей области.|

Вы можете указать несколько областей, разделяя их пробелами, как показано ниже.

```
string scopes = "Dataset.Read Workspace.Report.Create";
```

**Обязательные утверждения — области**

scp: {scopesClaim} scopesClaim может быть строкой или массивом строк, в котором указаны допустимые разрешения для ресурсов в рабочей области (отчет, набор данных и т. д.).

Декодированный маркер с определенными областями будет выглядеть следующим образом.

```
Header

{
    typ: "JWT",
    alg: "HS256:
}

Body

{
  "ver": "0.2.0",
  "wcn": "SupportDemo",
  "wid": "ca675b19-6c3c-4003-8808-1c7ddc6bd809",
  "rid": "96241f0f-abae-4ea9-a065-93b428eddb17",
  "scp": "Report.Read",
  "iss": "PowerBISDK",
  "aud": "https://analysis.windows.net/powerbi/api",
  "exp": 1360047056,
  "nbf": 1360043456
}

```

### <a name="operations-and-scopes"></a>Операции и области

|Операция|Целевой ресурс|Разрешения для маркеров|
|---|---|---|
|Создайте (в памяти) новый отчет на основе набора данных.|Выборка|Dataset.Read|
|Создайте (в памяти) новый отчет на основе набора данных и сохраните этот отчет.|Выборка|* Dataset.Read<br>* Workspace.Report.Create|
|Просмотр и изменение (в памяти) существующего отчета. Report.Read подразумевает наличие Dataset.Read. Report.Read не допускает сохранение изменений.|Отчет|Report.Read|
|Изменение и сохранение существующего отчета.|Отчет|Report.ReadWrite|
|Сохранение копии отчета ("Сохранить как").|Отчет|* Report.Read<br>* Workspace.Report.Copy|

## <a name="heres-how-the-flow-works"></a>Вот как выглядит процедура
1. Скопируйте ключи API для своего приложения. Их можно получить на **портале Azure**.
   
    ![Где найти ключи API на портале Azure](media/get-started-sample/azure-portal.png)
1. Маркер декларирует утверждение и имеет срок действия.
   
    ![Блок-схема маркеров приложения: маркер подтверждает утверждение](media/get-started-sample/token-2.png)
1. Маркер подписывается с помощью ключей доступа API.
   
    ![Блок-схема маркеров приложения: маркер подписывается](media/get-started-sample/token-3.png)
1. Пользователь запрашивает просмотр отчета.
   
    ![Блок-схема маркеров приложения: пользователь запрашивает просмотр отчета](media/get-started-sample/token-4.png)
1. Маркер проверяется с помощью ключей доступа API.
   
   ![Блок-схема маркеров приложения: маркер проверяется](media/get-started-sample/token-5.png)
1. Служба коллекций рабочих областей Power BI отправляет отчет пользователю.
   
   ![Блок-схема маркеров приложения: служба отправляет отчет пользователю](media/get-started-sample/token-6.png)

После того как служба **коллекций рабочих областей Power BI** отправит отчет пользователю, он может просмотреть этот отчет в своем приложении. Например, если вы импортировали [PBIX-файл с примером анализа данных продаж](http://download.microsoft.com/download/1/4/E/14EDED28-6C58-4055-A65C-23B4DA81C4DE/Analyzing_Sales_Data.pbix), пример веб-приложения будет выглядеть следующим образом.

![Пример отчета, внедренного в приложение](media/get-started-sample/sample-web-app.png)

## <a name="see-also"></a>См. также

[CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN#methods_)  
[Пример для начала работы с коллекциями рабочих областей Power BI](get-started-sample.md)  
[Распространенные сценарии работы с коллекциями рабочих областей Microsoft Power BI](scenarios.md)  
[Начало работы с коллекциями рабочих областей Microsoft Power BI](get-started.md)  
[Репозиторий PowerBI-CSharp на сайте GitHub](https://github.com/Microsoft/PowerBI-CSharp)

У вас имеются и другие вопросы? [Попробуйте задать их в сообществе Power BI](http://community.powerbi.com/)