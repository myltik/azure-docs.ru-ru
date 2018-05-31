---
title: Azure Active Directory Application Proxy и Tableau | Документация Майкрософт
description: Сведения об использовании Azure Active Directory Application Proxy (Azure AD) для предоставления удаленного доступа к развертыванию Tableau.  .
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/02/2018
ms.author: barbkess
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 7fa8654e413ac337994d35afdce7bd3478127f64
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/11/2018
ms.locfileid: "34070878"
---
# <a name="azure-active-directory-application-proxy-and-tableau"></a>Azure Active Directory Application Proxy и Tableau 

Использование Azure Active Directory Application Proxy и Tableau упрощает использование прокси приложения для предоставления удаленного доступа к развертыванию Tableau. В этой статье описывается настройка сценария.  

## <a name="prerequisites"></a>предварительным требованиям 

Сценарий в этой статье предполагает, что:

- [Tableau](https://onlinehelp.tableau.com/current/server/en-us/proxy.htm#azure) настроен; 

- [соединитель прокси приложения](manage-apps/application-proxy-enable.md) установлен. 

 

## <a name="enabling-application-proxy-for-tableau"></a>Включение прокси приложения для Tableau 

Если вы хотите использовать Application Proxy для Tableau, отправьте электронное письмо на адрес [aadapfeedback@microsoft.com](mailto:aadapfeedback@microsoft.com), чтобы включить этот сценарий.
В вашем электронном письме:

-   В поле темы укажите "Включение прокси приложения для Tableau".
-   В тексте письма укажите свой ИД клиента.    

Вы получите подтверждение, когда будете готовы использовать приложение. Обычно это занимает примерно одну неделю. Однако вы можете завершить настройки во время ожидания подтверждения.


 

## <a name="publish-your-applications-in-azure"></a>Публикация приложений в Azure 

Чтобы опубликовать Tableau, вам необходимо опубликовать приложение на портале Azure.

Сведения:

- Подробные пошаговые инструкции по шагам 1–8 см. в статье [Публикация приложений с помощью прокси приложения Azure AD](manage-apps/application-proxy-publish-azure-portal.md). 
- Сведения о том, как найти значения Tableau для полей App Proxy, см. в документации по Tableau.  

**Чтобы опубликовать приложение, сделайте следующее**. 


1. Войдите на [портал Azure](https://portal.azure.com) как глобальный администратор. 

2. Выберите **Azure Active Directory > Корпоративные приложения**. 

3. Щелкните **Добавить** в верхней части колонки. 

4. Выберите **Локальное приложение**. 

5. Введите в обязательные поля сведения о новом приложении. Вам нужно настроить следующие параметры. 

    - **Внутренний URL-адрес**: это приложение должно использовать внутренний URL-адрес, которым является сам URL-адрес Tableau. Например, `https://adventure-works.tableau.com`. 

    - **Метод предварительной аутентификации**: Azure Active Directory (рекомендуется, но не обязательно). 

6. Щелкните **Добавить** в верхней части колонки. Когда приложение будет добавлено, откроется меню быстрого запуска. 

7. В меню быстрого запуска выберите **Назначить пользователя для тестирования**, а затем назначьте для приложения хотя бы одного пользователя. Убедитесь, что у тестовой учетной записи есть доступ к локальному приложению. 

8. Щелкните **Назначить**, чтобы сохранить назначение тестового пользователя. 

9. (Необязательно.) На странице управления приложением щелкните **Единый вход**. Выберите **Встроенная проверка подлинности Windows** в раскрывающемся меню и заполните обязательные поля на основе конфигурации Tableau. Щелкните **Сохранить**. 

 

## <a name="testing"></a>Тестирование 

Теперь приложение готово к тестированию. Перейдите по внешнему URL-адресу, который использовался для публикации Tableau, и войдите в систему как пользователь, назначенный для обоих приложений.



## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о прокси приложения Azure AD см. в статье [Как обеспечить безопасный удаленный доступ к локальным приложениям](manage-apps/application-proxy.md).

