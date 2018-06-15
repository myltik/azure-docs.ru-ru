---
title: Техническое руководство по публикации SaaS-приложений в Azure Marketplace
description: Пошаговое руководство и контрольный список по публикации SaaS-приложений в Azure Marketplace
services: Marketplace, Compute, Storage, Networking, Blockchain, Security, SaaS
documentationcenter: ''
author: BrentL-Collabera
manager: ''
editor: BrentL-Collabera
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 05/09/2018
ms.author: pabutler
ms.openlocfilehash: 2ac8119e36843e38e334fb5772ea4ade9962b4f9
ms.sourcegitcommit: 6cf20e87414dedd0d4f0ae644696151e728633b6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/06/2018
ms.locfileid: "34809477"
---
# <a name="saas-applications-technical-publishing-guide"></a>Техническое руководство по публикации SaaS-приложений

Добро пожаловать в техническое руководство по публикации SaaS-приложений в Azure Marketplace! Это руководство предназначено для помощи кандидатам и существующим издателям в публикации своих приложений и служб в Azure Marketplace с использованием предложения "SaaS-приложения". 

Для удобства мы разбили руководство по публикации предложения SaaS на несколько разделов:
* обзор предложения;
* бизнес-требования;
* технические требования;
* Процесс публикации
* Активация пробных версий с помощью Azure Active Directory
* сертификация интеграции Azure AD для Marketplace.

## <a name="offer-overview"></a>Обзор предложения  

Приложения SaaS доступны в обоих онлайн-магазинах Azure. В следующей таблице описаны текущие доступные варианты:

| Вариант онлайн-магазина | Вывод списка | Пробная версия или транзакция |  
| --- | --- | --- |  
| AppSource | Да (связаться со мной) | Да (PowerBI, Dynamics) |
| Azure Marketplace | Нет  | Да (приложения SaaS) |   

**Список.** Вариант публикации "Список" включает тип предложения "Связаться со мной" и используется, когда реализовать пробную версию или транзакцию нецелесообразно. Преимущество такого подхода заключается в том, что он позволяет издателям, решения которых уже опубликованы, немедленно получать сведения о потенциальных клиентах, с которыми можно заключить сделки, стимулирующие рост бизнеса.  
**Пробная версия или транзакция.** Заказчик имеет возможность непосредственно купить или запросить пробную версию вашего решения. Предоставление пробной версии положительно влияет на взаимодействие с клиентами и позволяет им изучить решение до покупки. Благодаря этому у вас будет больше шансов на продвижение в онлайн-магазинах, что позволит получать больше сведений о потенциальных клиентах. Для бесплатных пробных версий должна предоставляться бесплатная поддержка, по крайней мере на протяжении пробного периода.  

| Предложение приложений SaaS | Бизнес-требования | Технические требования |  
| --- | --- | --- |  
| **Связаться с нами** | Yes | Нет  |  
| **PowerBI, Dynamics** | Yes | Да (интеграция с Azure AD) |  
| **Приложения SaaS**| Yes | Да (интеграция с Azure AD) |     

Дополнительные сведения об онлайн-магазинах Marketplace и описание каждого из вариантов публикации см. в [руководстве издателя Marketplace](https://aka.ms/sellerguide) и разделе [Варианты публикации](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide#select-a-publishing-option).

## <a name="business-requirements"></a>Бизнес-требования
Соответствие бизнес-требованиям к предложению SaaS можно обеспечить параллельно с выполнением технических требований. Большинство бизнес-требований и сведений собираются при создании предложения SaaS на Портале Cloud Partner. Ниже перечислены бизнес-требования: 
* принятие политик участия;
* интеграция с Майкрософт; 
* определение аудитории предложения;
* определение способа для управления взаимодействием с потенциальными клиентами;
* установка политики конфиденциальности и условий использования;
* определение контактов для поддержке.  

Дополнительные сведения можно найти в разделе [Необходимые условия для публикации в Marketplace](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide#prerequisites-for-marketplace-publishing).

## <a name="technical-requirements"></a>Технические требования

Технические требования для приложений SaaS просты. Для публикации издателям требуется только интеграция с Azure Active Directory (Azure AD). Интеграция Azure AD с приложениями описана во многих документах, и корпорация Майкрософт предоставляет множество ресурсов и пакетов SDK для выполнения этой задачи.  

Для начала вам нужна подписка, предназначенная для публикации в Azure Marketplace, чтобы изолировать этот сценарий от других инициатив. После этого можно приступать к развертыванию приложения SaaS в этой подписке и начинать разработку.  

Полезную документацию по Azure Active Directory, примеры и руководства см. последующим ссылкам: 

* [Руководство разработчика по Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide)

* [Интеграция с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-to-integrate)

* [Интеграция приложений с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)

* [Безопасность и идентификация на странице стратегии развития Azure](https://azure.microsoft.com/roadmap/?category=security-identity)

Видеоруководства см. по следующим ссылкам:

* [Видео о проверке подлинности Azure Active Directory с участием Витторио Берточчи (Vittorio Bertocci)](https://channel9.msdn.com/Shows/XamarinShow/Episode-27-Azure-Active-Directory-Authentication-with-Vittorio-Bertocci?term=azure%20active%20directory%20integration)

* [Технический брифинг. Удостоверение Azure Active Directory. Часть 1 из 2](https://channel9.msdn.com/Blogs/MVP-Enterprise-Mobility/Azure-Active-Directory-Identity-Technical-Briefing-Part-1-of-2?term=azure%20active%20directory%20integration)

* [Технический брифинг. Удостоверение Azure Active Directory. Часть 2 из 2](https://channel9.msdn.com/Blogs/MVP-Azure/Azure-Active-Directory-Identity-Technical-Briefing-Part-2-of-2?term=azure%20active%20directory%20integration)

* [Создание приложений с использованием Microsoft Azure Active Directory](https://channel9.msdn.com/Blogs/Windows-Development-for-the-Enterprise/Building-Apps-with-Microsoft-Azure-Active-Directory?term=azure%20active%20directory%20integration)

* [Видеоролики Microsoft Azure, посвященные Azure Active Directory](https://azure.microsoft.com/resources/videos/index/?services=active-directory)

Бесплатный курс обучения по Azure Active Directory см. по следующей ссылке:  
* [Серия материалов "Microsoft Azure для ИТ-специалистов": Azure Active Directory](https://mva.microsoft.com/en-US/training-courses/microsoft-azure-for-it-pros-content-series-azure-active-directory-16754?l=N0e23wtxC_2106218965)

Проверить наличие обновлений Azure Active Directory можно по следующей ссылке:   
* [Обновления службы Azure AD](https://azure.microsoft.com/updates/?product=active-directory)

Для получения помощи можно воспользоваться следующими ресурсы:
* [Форумы MSDN](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD)
* [Stackoverflow](https://stackoverflow.com/questions/tagged/azure-active-directory)

## <a name="publishing-process"></a>Процесс публикации

При публикации SaaS есть нужно обеспечить соответствие техническим требованиям и бизнес-требованиям.  Большую часть процесса разработки и интеграции Azure Active Directory можно осуществлять параллельно с выполнением бизнес-требований предложения. Значительное число бизнес-требований установлено в конфигурации предложения приложений SaaS на Портале Cloud Partner.  
На следующей схеме показаны основные этапы публикации предложения пробной версии или транзакции:  

![Шаги публикации SaaS](./media/marketplace-saas-applications-technical-publishing-guide/saaspublishingsteps.png)  

В следующей таблице описан каждый из основных шагов публикации:  

| Шаг публикации | ОПИСАНИЕ |   
| --- | --- |  
| **Создание приложения SaaS** | Войдите на Портал Cloud Partner и выберите **Создать**. Затем выберите предложение **Приложения SaaS**. |  
| **Создание интеграции с Azure AD** | Выполните технические требования, указанные в предыдущем разделе, для интеграции предложения SaaS с Azure AD. |  
| **Указание параметров предложения**| Введите все начальные сведения о предложении SaaS. Укажите идентификатор и имя предложения, которые следует использовать. |     
| **Указание технических сведений** | Введите технические сведения о предложении. Для приложений SaaS обязательно укажите URI решения и тип кнопки для приобретения предложения (бесплатная версия, пробная версия или "Связаться со мной"). |  
| **Тестовый выпуск (необязательно)** | Это необязательный тип пробной версии, необходимый в основном для других типов предложений Marketplace. Он позволяет развернуть пробную версию в подписках издателя вместо пользователя. |  
| **Предоставление материалов предложения в онлайн-магазине**| В этом разделе издатель предоставляет ссылки и отправляет эмблемы, маркетинговые материалы, юридические документы, а также настраивает систему управления взаимодействием с потенциальными клиентами. |
| **Указание контактов для предложения** | Введите контактные данные для связи по вопросам проектирования и получения поддержки по предложению SaaS. |  
| **Проверка интеграции приложения SaaS с Azure AD** | Перед отправкой приложения SaaS на публикацию необходимо убедиться, что приложение интегрировано с Azure AD. |  
| **Публикация предложения**| Оформив предложение и выполнив технические требования, вы можете отправить предложение на публикацию. После этого шаблон решения тестируется, проверяется, сертифицируется и утверждается для публикации. |

## <a name="using-azure-active-directory-to-enable-trials"></a>Активация пробных версий с помощью Azure Active Directory  

Корпорация Майкрософт выполняет аутентификацию всех пользователей Marketplace с помощью Azure AD. Когда пользователь, прошедший аутентификацию, щелкает ваш список пробных версий в Marketplace и перенаправляется в вашу пробную среду, вы можете выполнить подготовку для пользователя непосредственно в пробной среде без необходимости дополнительного входа. Токен, получаемый вашим приложением от Azure AD во время аутентификации, содержит ценные сведения о пользователе, которые помогут вам создать его учетную запись в своем приложении, чтобы автоматизировать процесс подготовки и повысить вероятность перехода. Дополнительные сведения о токене см. в статье [Справочник по токенам в Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims).

С помощью Azure AD для приложения или пробной версии можно включить аутентификацию одним щелчком. Она предоставляет следующие преимущества:  
* упрощается взаимодействие клиентов с пробной версией в Marketplace;  
* сохраняется ощущение использования продукта, даже когда пользователь перенаправляется с Marketplace на ваш домен или в пробную среду;  
* сокращается вероятность отказа от перенаправления, так как отсутствует дополнительный шаг выполнения входа.  
* Уменьшаются ограничения развертывания для большого числа пользователей Azure AD.  

## <a name="certifying-your-azure-ad-integration-for-marketplace"></a>сертификация интеграции Azure AD для Marketplace.  

Интеграцию Azure AD можно сертифицировать несколькими способами. Это зависит от типа приложения (однотенантное или мультитенантное), а также от того, впервые ли вы используете федеративный единый вход (SSO) в Azure AD.  

**Мультитенантные приложения**  

Если вы уже используете Azure AD, сделайте следующее:
1.  Зарегистрируйте приложение на портале Azure.
2.  Включите функцию поддержки мультитенантности в Azure AD, чтобы получить пробную версию одним щелчком. Дополнительные сведения можно найти [здесь](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications).  

Если вы впервые используете федеративный единый вход в Azure AD, сделайте следующее: 
1.  Зарегистрируйте приложение на портале Azure.
2.  Настройте единый вход в Azure AD с помощью [OpenID Connect](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code) или [OAuth 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code).
3.  Включите функцию поддержки мультитенантности в AAD, чтобы получить пробную версию одним щелчком. Дополнительные сведения можно найти [здесь](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified).  

**Для однотенантного приложения используйте любой из следующих вариантов:**  
* Добавьте пользователей в свой каталог в качестве гостевых пользователей, используя [Azure B2B](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)
* Вручную подготовьте пробную версию для клиентов с использованием функции "Связаться со мной".
* Разработайте тестовый выпуск для одного клиента.
* Создайте демонстрационное мультитенантное приложение с SSO.

