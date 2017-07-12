---
title: "Проблемы при входе в специально разработанное приложение | Документы Майкрософт"
description: "Распространенные ошибки, из-за которых может не работать вход в приложение, разработанное вами с помощью Azure AD"
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.translationtype: Human Translation
ms.sourcegitcommit: 0d6f6fb24f1f01d703104f925dcd03ee1ff46062
ms.openlocfilehash: 51fac8adc2fe11aac005b4c0c0e9b67bbca8fa6e
ms.contentlocale: ru-ru
ms.lasthandoff: 04/17/2017

---

<a id="problems-signing-in-to-an-custom-developed-application" class="xliff"></a>

# Проблемы при входе в специально разработанное приложение

Существует несколько ошибок, которые могут препятствовать входу в приложение. Основной причиной является неправильная настройка приложений.

<a id="errors-related-to--misconfigured-apps" class="xliff"></a>

## Ошибки, связанные с неправильной настройкой приложений

* Убедитесь, что конфигурации на портале соответствуют значениям в приложении. В частности, сравните идентификатор клиента и приложения, URL-адреса ответа, ключи и секреты клиентов, а также URI идентификатора приложения.

* Сравните ресурсы, к которым вы обращаетесь из кода с помощью настроенных на вкладке **Необходимые ресурсы** разрешений, чтобы запрашивались только настроенные ресурсы.

* Сведения об аналогичных ошибках или проблемах см. в разделе [StackOverflow в Azure AD](http://stackoverflow.com/questions/tagged/azure-active-directory).

<a id="next-steps" class="xliff"></a>

## Дальнейшие действия

[Руководство разработчика по Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide)<br>

[Согласие и интеграция приложений с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications>)<br>

[Согласие и разрешения для конвергированных приложений в Azure AD версии 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes)<br>

[StackOverflow в Azure AD](http://stackoverflow.com/questions/tagged/azure-active-directory>)

