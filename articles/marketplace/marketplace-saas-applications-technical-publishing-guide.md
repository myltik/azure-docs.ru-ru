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
ms.date: 02/28/2018
ms.author: pabutler
ms.openlocfilehash: eb6db45ca0fcb6879aeaeaaf70715691cac438b0
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
---
# <a name="saas-applications-technical-publishing-guide"></a>Техническое руководство по публикации SaaS-приложений

Добро пожаловать в техническое руководство по публикации SaaS-приложений в Azure Marketplace! Это руководство предназначено для помощи кандидатам и существующим издателям в публикации своих приложений и служб в Azure Marketplace с использованием предложения "SaaS-приложения".  
Предложение "SaaS-приложения" можно использовать, если решение будет развернуто в вашей подписке Azure и пользователи будут входить в систему через интерфейс, который вы разработали и которым вы управляете, для тестирования приложения. При этом для тестирования существующей пробной среды используется [Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/active-directory-whatis). Другими словами, это партнерская бесплатная пробная версия, используемая клиентами. Очень важно разместить такое решение, чтобы покупатели облака могли испытать его независимо, без каких-либо затрат и чтобы этот тип предложения предоставлял бесплатную версию, которая соответствует пользовательским запросам на облачные решения.  

Общие сведения о других предложениях Marketplace см. в статье [Руководство по публикации в Azure Marketplace и AppSource](https://aka.ms/sellerguide).

## <a name="saas-application-technical-guidance"></a>Техническое руководство по приложению SaaS
Технические требования для приложений SaaS просты. Для публикации приложение издателей только должно быть интегрировано с Azure AD.  Интеграция Azure AD с приложениями описана во многих документах, и корпорация Майкрософт предоставляет множество ресурсов и пакетов SDK для выполнения этой задачи.  

Для начала вам нужна подписка, предназначенная для публикации в Azure Marketplace, чтобы изолировать этот сценарий от других инициатив. Кроме того, в среде разработки необходимо установить следующие компоненты (если вы еще этого не сделали): 
- [интерфейс командной строки Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)  
- [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-5.0.0)  
- [Средства для разработчиков Azure (обзор доступных средств)](https://azure.microsoft.com/tools/)  
- [Visual Studio Code](https://code.visualstudio.com/)  

### <a name="resources"></a>Ресурсы
Ниже приведены ссылки на ресурсы Azure AD, которые помогут приступить к работе. 

**Документация**

- [Руководство разработчика по Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide)

- [Интеграция с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-to-integrate)

- [Интеграция приложений с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)

- [Безопасность и идентификация на странице стратегии развития Azure](https://azure.microsoft.com/roadmap/?category=security-identity)

**Видеоролики**

- [Видео о проверке подлинности Azure Active Directory с участием Витторио Берточчи (Vittorio Bertocci)](https://channel9.msdn.com/Shows/XamarinShow/Episode-27-Azure-Active-Directory-Authentication-with-Vittorio-Bertocci?term=azure%20active%20directory%20integration)

- [Технический брифинг. Удостоверение Azure Active Directory. Часть 1 из 2](https://channel9.msdn.com/Blogs/MVP-Enterprise-Mobility/Azure-Active-Directory-Identity-Technical-Briefing-Part-1-of-2?term=azure%20active%20directory%20integration)

- [Технический брифинг. Удостоверение Azure Active Directory. Часть 2 из 2](https://channel9.msdn.com/Blogs/MVP-Azure/Azure-Active-Directory-Identity-Technical-Briefing-Part-2-of-2?term=azure%20active%20directory%20integration)

- [Создание приложений с использованием Microsoft Azure Active Directory](https://channel9.msdn.com/Blogs/Windows-Development-for-the-Enterprise/Building-Apps-with-Microsoft-Azure-Active-Directory?term=azure%20active%20directory%20integration)

- [Видеоролики Microsoft Azure, посвященные Azure Active Directory](https://azure.microsoft.com/resources/videos/index/?services=active-directory)

**Обучение**  
- [Серия материалов "Microsoft Azure для ИТ-специалистов": Azure Active Directory](https://mva.microsoft.com/en-US/training-courses/microsoft-azure-for-it-pros-content-series-azure-active-directory-16754?l=N0e23wtxC_2106218965)

**Обновления службы Azure Active Directory**  
- [Обновления службы Azure AD](https://azure.microsoft.com/updates/?product=active-directory)

Для получения помощи можно воспользоваться следующими ресурсы:
- [Форумы MSDN](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD)
- [Stackoverflow](https://stackoverflow.com/questions/tagged/azure-active-directory)

## <a name="the-azure-active-directory-gallery"></a>Коллекция Azure Active Directory
Помимо публикации приложения в Azure Marketplace или AppSource, его также можно разместить в коллекции приложений Azure AD, которая является частью AppStore и Azure Marketplace. Клиенты, которые используют Azure AD в качестве поставщика удостоверений, могут находить опубликованные здесь различные соединители приложений SaaS. Администраторы ИТ-систем могут добавлять соединители из коллекции приложений, а затем настраивать и использовать эти соединители для единого входа (SSO) и подготовки. Azure AD поддерживает для функции SSO все основные протоколы федерации, в том числе SAML 2.0, OpenID Connect, OAuth и WS-Fed.  

Протестировав интеграцию приложения с Azure AD, отправьте запрос на доступ через наш портал сети приложений. Если у вас есть учетная запись Office 365, используйте ее для входа на портал. Если у вас нет учетной записи Office 365, то для входа можно использовать учетную запись Майкрософт (например, Outlook или Hotmail).

## <a name="program-benefits"></a>Преимущества программы
- Клиенты получают самый удобный способ единого входа.
- Конфигурация приложения будет простой и минимально необходимой.
- Клиенты смогут искать приложения и находить их в коллекции.
- Эта интеграция доступна абсолютно всем клиентам Azure AD, независимо от используемого уровня SKU: "Бесплатный", "Базовый" или "Премиум".
- Нашим общим клиентам предлагаются пошаговые руководства по настройке.
- В случае использования SCIM вы сможете подготавливать пользователей для размещенного приложения.

## <a name="prerequisites"></a>предварительным требованиям
Чтобы включить приложение в коллекцию Azure AD, необходимо сначала реализовать один из протоколов федерации, поддерживаемых Azure AD. Ознакомьтесь с условиями использования коллекции приложений Azure AD, которые можно найти на странице [Юридическая информация Службы Microsoft Azure](https://azure.microsoft.com/support/legal/).  

Ниже приведены дополнительные предварительные требования, зависящие от используемого протокола.

**Подключение OpenID**. Создайте многопользовательское приложение в Azure AD и реализуйте в нем инфраструктуру согласия. Направляйте запрос на вход к общедоступной конечной точке, чтобы любой клиент мог предоставить согласие для приложения. Доступом пользователей клиента можно управлять на основе идентификатора клиента и имени участника-пользователя, которые передаются в маркере.  
**SAML 2.0 или WS-Fed**. Приложение должно поддерживать интеграцию единого входа SAML или WS-Fed в любом из режимов (инициируемом поставщиком услуг или поставщиком удостоверений).
