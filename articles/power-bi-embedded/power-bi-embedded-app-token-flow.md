---
title: "Аутентификация и авторизация в Power BI Embedded"
description: "Аутентификация и авторизация в Power BI Embedded"
services: power-bi-embedded
documentationcenter: 
author: guyinacube
manager: erikre
editor: 
tags: 
ms.assetid: 1c1369ea-7dfd-4b6e-978b-8f78908fd6f6
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 03/11/2017
ms.author: asaxton
translationtype: Human Translation
ms.sourcegitcommit: c1cd1450d5921cf51f720017b746ff9498e85537
ms.openlocfilehash: 93027f0f5467e0b21c47bbcbc84c67cdd50b26b8
ms.lasthandoff: 03/14/2017


---
# <a name="authenticating-and-authorizing-with-power-bi-embedded"></a>Аутентификация и авторизация в Power BI Embedded

Вместо явной проверки подлинности пользователей служба Power BI Embedded использует для проверки подлинности и авторизации **ключи** и **маркеры приложений**. В этой модели ваше приложение управляет проверкой подлинности и авторизацией пользователей. При необходимости приложение создает и отправляет маркеры приложений, которые сообщают службе о том, что необходимо подготовить запрошенный отчет. Этот подход не требует использования Azure Active Directory для проверки подлинности и авторизации пользователей, хотя вы можете это сделать.

## <a name="two-ways-to-authenticate"></a>Два метода проверки подлинности

**Ключи** можно использовать для всех вызовов REST API в Power BI Embedded. Их можно найти на **портале Azure**, щелкнув **Все параметры**, а затем выбрав **Ключи доступа**. С ключом следует обращаться как с паролем. Эти ключи предоставляют разрешение на выполнение любого вызова REST API в определенной коллекции рабочих областей.

Чтобы использовать ключ для вызова REST, добавьте следующий заголовок авторизации:            

    Authorization: AppKey {your key}

**Маркеры приложений** используются для всех запросов внедрения. Они предназначены для выполнения на стороне клиента и, следовательно, ограничены одним отчетом. Для них рекомендуется задать время окончания срока действия.

Маркеры приложения представляют собой веб-маркеры JWT, подписанные одним из ключей.

Маркер приложения может содержать следующие утверждения:

| Утверждение | Описание |
| --- | --- |
| **ver** |Версия маркера приложения. Текущая версия —&0;.2.0. |
| **aud** |Целевой получатель маркера. Для Power BI Embedded используйте адрес https://analysis.windows.net/powerbi/api |
| **iss** |Строка, указывающая приложение, которое выдает маркер |
| **type** |Тип маркера приложения, который создается. Сейчас поддерживается только тип **embed** |
| **wcn** |Имя коллекции рабочих областей, для которой выдается маркер |
| **wid** |Идентификатор рабочей области, для которой выдается маркер |
| **rid** |Идентификатор отчета, для которого выдается маркер |
| **username** (необязательно) |Используется с RLS. Это строка, которая помогает идентифицировать пользователя при применении правил RLS |
| **roles** (необязательно) |Строка, содержащая роли, которые выбираются при применении правил безопасности на уровне строк. При выборе нескольких ролей их нужно передавать в виде массива строк. |
| **scp** (необязательно) |Строка, содержащая области разрешений. При выборе нескольких ролей их нужно передавать в виде массива строк. |
| **exp** (необязательно) |Указывает время истечения срока действия маркера. Его следует передавать в качестве метки времени Unix |
| **nbf** (необязательно) |Указывает, когда маркер станет допустимым. Его следует передавать в качестве метки времени Unix |

Пример маркера приложения будет выглядеть следующим образом:

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ2ZXIiOiIwLjIuMCIsInR5cGUiOiJlbWJlZCIsIndjbiI6Ikd1eUluQUN1YmUiLCJ3aWQiOiJkNGZlMWViMS0yNzEwLTRhNDctODQ3Yy0xNzZhOTU0NWRhZDgiLCJyaWQiOiIyNWMwZDQwYi1kZTY1LTQxZDItOTMyYy0wZjE2ODc2ZTNiOWQiLCJzY3AiOiJSZXBvcnQuUmVhZCIsImlzcyI6IlBvd2VyQklTREsiLCJhdWQiOiJodHRwczovL2FuYWx5c2lzLndpbmRvd3MubmV0L3Bvd2VyYmkvYXBpIiwiZXhwIjoxNDg4NTAyNDM2LCJuYmYiOjE0ODg0OTg4MzZ9.v1znUaXMrD1AdMz6YjywhJQGY7MWjdCR3SmUSwWwIiI
```

После декодирования он станет выглядеть примерно следующим образом:

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

В пакете SDK доступны методы, которые упрощают создание маркеров приложений. Например, для .NET это класс [Microsoft.PowerBI.Security.PowerBIToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken) и методы [CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN#methods_).

Методы для пакета SDK для .NET см. в статье [Класс Scopes](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.scopes).

## <a name="scopes"></a>Области действия

При использовании маркеров внедрения может потребоваться ограничить использование ресурсов, к которым предоставляется доступ. Для этого можно создать маркер с заданной областью разрешений.

Ниже приведены доступные области для Power BI Embedded.

|Область|Описание|
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
|Создайте (в памяти) новый отчет на основе набора данных и сохраните этот отчет.|Выборка|*Dataset.Read<br>* Workspace.Report.Create|
|Просмотр и изменение (в памяти) существующего отчета. Report.Read подразумевает наличие Dataset.Read. Эта операция не предоставляет разрешения на сохранение изменений.|Отчет|Report.Read|
|Изменение и сохранение существующего отчета.|Отчет|Report.ReadWrite|
|Сохранение копии отчета ("Сохранить как").|Отчет|*Report.Read<br>* Workspace.Report.Copy|

## <a name="heres-how-the-flow-works"></a>Вот как выглядит процедура
1. Скопируйте ключи API для своего приложения. Их можно получить на **портале Azure**.
   
    ![](media/powerbi-embedded-get-started-sample/azure-portal.png)
2. Маркер декларирует утверждение и имеет срок действия.
   
    ![](media/powerbi-embedded-get-started-sample/power-bi-embedded-token-2.png)
3. Маркер подписывается с помощью ключей доступа API.
   
    ![](media/powerbi-embedded-get-started-sample/power-bi-embedded-token-3.png)
4. Пользователь запрашивает просмотр отчета.
   
    ![](media/powerbi-embedded-get-started-sample/power-bi-embedded-token-4.png)
5. Маркер проверяется с помощью ключей доступа API.
   
   ![](media/powerbi-embedded-get-started-sample/power-bi-embedded-token-5.png)
6. Power BI Embedded отправляет отчет пользователю.
   
   ![](media/powerbi-embedded-get-started-sample/power-bi-embedded-token-6.png)

После того как **Power BI Embedded** отправляет отчет пользователю, пользователь может просмотреть его в своем приложении. Например, если вы импортировали [PBIX-файл с примером анализа данных продаж](http://download.microsoft.com/download/1/4/E/14EDED28-6C58-4055-A65C-23B4DA81C4DE/Analyzing_Sales_Data.pbix), пример веб-приложения будет выглядеть следующим образом:

![](media/powerbi-embedded-get-started-sample/sample-web-app.png)

## <a name="see-also"></a>См. также

[CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN#methods_)  
[Приступая к работе с примером Microsoft Power BI Embedded](power-bi-embedded-get-started-sample.md)  
[Типичные сценарии использования Microsoft Power BI Embedded](power-bi-embedded-scenarios.md)  
[Начало работы с Microsoft Power BI Embedded (предварительная версия)](power-bi-embedded-get-started.md)  
[Репозиторий PowerBI-CSharp на сайте GitHub](https://github.com/Microsoft/PowerBI-CSharp)  
У вас имеются и другие вопросы? [Попробуйте задать их в сообществе Power BI](http://community.powerbi.com/)


