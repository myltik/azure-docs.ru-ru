<properties
   pageTitle="Сведения о потоке маркеров приложений в Power BI Embedded"
   description="Power BI Embedded, сведения о маркерах приложений для проверки подлинности и авторизации"
   services="power-bi-embedded"
   documentationCenter=""
   authors="minewiskan"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="power-bi-embedded"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="powerbi"
   ms.date="06/28/2016"
   ms.author="owend"/>

# Сведения о потоке маркеров приложений в Power BI Embedded

Служба **Power BI Embedded** использует **маркеры приложений** для проверки подлинности и авторизации вместо явной проверки подлинности пользователей. В модели с **маркерами приложений** проверкой подлинности и авторизацией конечных пользователей управляет ваше приложение. При необходимости ваше приложение создает и отправляет **маркеры приложений**, которые сообщают службе о том, что необходимо подготовить запрошенный отчет. Этот подход не требует использования **Azure Active Directory** для проверки подлинности и авторизации пользователей, хотя вы можете это сделать.

**Поток маркеров приложений выглядит следующим образом**

1. Скопируйте ключи API для своего приложения. Их можно получить на **портале Azure**.

    ![](media\powerbi-embedded-get-started-sample\azure-portal.png)

2. Маркер декларирует утверждение и имеет срок действия.

    ![](media\powerbi-embedded-get-started-sample\power-bi-embedded-token-2.png)

3. Маркер подписывается с помощью ключей доступа API.

    ![](media\powerbi-embedded-get-started-sample\power-bi-embedded-token-3.png)

4. Пользователь запрашивает просмотр отчета.

    ![](media\powerbi-embedded-get-started-sample\power-bi-embedded-token-4.png)

5.	Маркер проверяется с помощью ключей доступа API.

    ![](media\powerbi-embedded-get-started-sample\power-bi-embedded-token-5.png)

6.	Power BI Embedded отправляет отчет пользователю.

    ![](media\powerbi-embedded-get-started-sample\power-bi-embedded-token-6.png)

После того как **Power BI Embedded** отправляет отчет пользователю, пользователь может просмотреть его в своем приложении. Например, если вы импортировали [пример анализа данных продаж PBIX](http://download.microsoft.com/download/1/4/E/14EDED28-6C58-4055-A65C-23B4DA81C4DE/Analyzing_Sales_Data.pbix), пример веб-приложения будет выглядеть следующим образом:

![](media\powerbi-embedded-get-started-sample\sample-web-app.png)

## См. также
- [Приступая к работе с примером Microsoft Power BI Embedded](power-bi-embedded-get-started-sample.md)
- [Что такое Microsoft Power BI Embedded](power-bi-embedded-what-is-power-bi-embedded.md)
- [Типичные сценарии использования Microsoft Power BI Embedded](power-bi-embedded-scenarios.md)
- [Приступая к работе с предварительной версией Microsoft Power BI Embedded](power-bi-embedded-get-started.md)

<!---HONumber=AcomDC_0629_2016-->