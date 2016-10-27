<properties
   pageTitle="Аутентификация и авторизация в Power BI Embedded"
   description="Аутентификация и авторизация в Power BI Embedded"
   services="power-bi-embedded"
   documentationCenter=""
   authors="guyinacube"
   manager="erikre"
   editor=""
   tags=""/>
<tags
   ms.service="power-bi-embedded"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="powerbi"
   ms.date="10/04/2016"
   ms.author="asaxton"/>


# <a name="authenticating-and-authorizing-with-power-bi-embedded"></a>Аутентификация и авторизация в Power BI Embedded

Вместо явной проверки подлинности пользователей служба Power BI Embedded использует для проверки подлинности и авторизации **ключи** и **маркеры приложений**. В этой модели ваше приложение управляет проверкой подлинности и авторизацией пользователей. При необходимости приложение создает и отправляет маркеры приложений, которые сообщают службе о том, что необходимо подготовить запрошенный отчет. Этот подход не требует использования Azure Active Directory для проверки подлинности и авторизации пользователей, хотя вы можете это сделать.

## <a name="two-ways-to-authenticate"></a>Два метода проверки подлинности

**Ключи** можно использовать для всех вызовов REST API в Power BI Embedded. Их можно найти на **портале Azure**, щелкнув **Все параметры**, а затем выбрав **Ключи доступа**. С ключом следует обращаться как с паролем. Эти ключи предоставляют разрешение на выполнение любого вызова REST API в определенной коллекции рабочих областей.

Чтобы использовать ключ для вызова REST, добавьте следующий заголовок авторизации:            

    Authorization: AppKey {your key}

**Маркеры приложений** используются для всех запросов внедрения. Они предназначены для выполнения на стороне клиента и, следовательно, ограничены одним отчетом. Для них рекомендуется задать время окончания срока действия.

Маркеры приложения представляют собой веб-маркеры JWT, подписанные одним из ключей.

Маркер приложения может содержать следующие утверждения:

| Утверждение      | Описание        |
|--------------|------------|
| **ver**      | Версия маркера приложения. Текущая версия — 0.2.0.       |
| **aud**      | Целевой получатель маркера. Для Power BI Embedded используйте адрес https://analysis.windows.net/powerbi/api  |
| **iss**      |  Строка, указывающая приложение, которое выдает маркер    |
| **type**     | Тип маркера приложения, который создается. Сейчас поддерживается только тип **embed**   |
| **wcn**      | Имя коллекции рабочих областей, для которой выдается маркер  |
| **wid**      | Идентификатор рабочей области, для которой выдается маркер  |
| **rid**      | Идентификатор отчета, для которого выдается маркер     |
| **username** (необязательно) |  Используется с RLS. Это строка, которая помогает идентифицировать пользователя при применении правил RLS |
| **roles** (необязательно)   |   Строка, содержащая роли, которые выбираются при применении правил безопасности на уровне строк. При выборе нескольких ролей их нужно передавать в виде массива строк.    |
| **exp** (необязательно)    |   Указывает время истечения срока действия маркера. Его следует передавать в качестве метки времени Unix   |
| **nbf** (необязательно)    |   Указывает, когда маркер станет допустимым. Его следует передавать в качестве метки времени Unix   |

Пример маркера приложения будет выглядеть следующим образом:

![](media\power-bi-embedded-app-token-flow\power-bi-embedded-app-token-flow-sample-coded.png)


Когда он будет декодирован, то станет выглядеть следующим образом:

![](media\power-bi-embedded-app-token-flow\power-bi-embedded-app-token-flow-sample-decoded.png)


## <a name="here's-how-the-flow-works"></a>Вот как выглядит процедура

1. Скопируйте ключи API для своего приложения. Их можно получить на **портале Azure**.

    ![](media\powerbi-embedded-get-started-sample\azure-portal.png)

2. Маркер декларирует утверждение и имеет срок действия.

    ![](media\powerbi-embedded-get-started-sample\power-bi-embedded-token-2.png)

3. Маркер подписывается с помощью ключей доступа API.

    ![](media\powerbi-embedded-get-started-sample\power-bi-embedded-token-3.png)

4. Пользователь запрашивает просмотр отчета.

    ![](media\powerbi-embedded-get-started-sample\power-bi-embedded-token-4.png)

5.  Маркер проверяется с помощью ключей доступа API.

    ![](media\powerbi-embedded-get-started-sample\power-bi-embedded-token-5.png)

6.  Power BI Embedded отправляет отчет пользователю.

    ![](media\powerbi-embedded-get-started-sample\power-bi-embedded-token-6.png)

После того как **Power BI Embedded** отправляет отчет пользователю, пользователь может просмотреть его в своем приложении. Например, если вы импортировали [PBIX-файл с примером анализа данных продаж](http://download.microsoft.com/download/1/4/E/14EDED28-6C58-4055-A65C-23B4DA81C4DE/Analyzing_Sales_Data.pbix), пример веб-приложения будет выглядеть следующим образом:

![](media\powerbi-embedded-get-started-sample\sample-web-app.png)

## <a name="see-also"></a>См. также
- [Приступая к работе с примером Microsoft Power BI Embedded](power-bi-embedded-get-started-sample.md)
- [Типичные сценарии использования Microsoft Power BI Embedded](power-bi-embedded-scenarios.md)
- [Начало работы с Microsoft Power BI Embedded (предварительная версия)](power-bi-embedded-get-started.md)



<!--HONumber=Oct16_HO2-->


