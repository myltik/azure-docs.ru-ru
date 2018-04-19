---
title: Вопросы и ответы по подключаемому модулю единого входа Azure Active Directory | Документация Майкрософт
description: Ответы на часто задаваемые вопросы по настройке единого входа между Azure Active Directory и Jira или Confluence.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 4b663047-7f88-443b-97bd-54224b232815
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/06/2018
ms.author: jeedes
ms.openlocfilehash: b642d6de9fd9265d7f7ee11b827a36d20e5bcce7
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/05/2018
---
# <a name="faq-for-the-azure-active-directory-sso-plug-in"></a>Вопросы и ответы по подключаемому модулю единого входа Azure Active Directory 

## <a name="what-does-the-plug-in-do"></a>Для чего нужен этот подключаемый модуль?

Этот подключаемый модуль предоставляет возможность единого входа для Atlassian Jira (включая Jira Core, Jira Software и Jira Service Desk) и для локального программного пакета Confluence. Подключаемый модуль использует Azure Active Directory (Azure AD) в качестве поставщика удостоверений (IdP).

## <a name="which-atlassian-products-does-the-plug-in-work-with"></a>С какими продуктами Atlassian работает этот подключаемый модуль?

Подключаемый модуль работает только с локальными версиями Jira и Confluence.

## <a name="does-the-plug-in-work-on-cloud-versions"></a>Работает ли подключаемый модуль с облачной версией?

Нет. Подключаемый модуль поддерживает только локальные версии Jira и Confluence.

## <a name="which-versions-of-jira-and-confluence-does-the-plug-in-support"></a>Какие версии Jira и Confluence поддерживает этот подключаемый модуль?

Подключаемый модуль поддерживает следующие версии:

* Jira Core и Jira Software: версии с 6.0 по 7.2.2; 
* Jira Service Desk: версии с 3.0 по 3.2; 
* Confluence: версии c 5.0 по 5.10

## <a name="is-the-plug-in-free-or-paid"></a>Этот подключаемый модуль предоставляется бесплатно или на платной основе?

Это бесплатное дополнение. Его можно установить из магазина компании Atlassian.

## <a name="do-i-need-to-restart-jira-or-confluence-after-i-deploy-the-plug-in"></a>Нужно ли перезапускать Jira или Confluence после развертывания подключаемого модуля?

Перезапуск не требуется. Можно сразу начать работу с подключаемым модулем.

## <a name="how-do-i-get-support-for-the-plug-in"></a>Как мне получить поддержку по этому подключаемому модулю?

Обратитесь к [команде интеграции единого входа Azure AD](<mailto:SaaSApplicationIntegrations@service.microsoft.com>). Вам ответят в течение 24–48 рабочих часов. 

Можно также создать обращение в службу поддержки корпорации Майкрософт через портал Azure.

## <a name="would-the-plug-in-work-on-a-mac-or-ubuntu-installation-of-jira-and-confluence"></a>Будет ли этот подключаемый модуль работать с установкой Jira и Confluence на Mac или Ubuntu?

Мы тестировали подключаемый модуль только с установками Jira и Confluence на 64-разрядных операционных системах Windows Server.

## <a name="does-the-plug-in-work-with-idps-other-than-azure-ad"></a>Работает ли этот подключаемый модуль с другими поставщиками удостоверений, кроме Azure AD?

Нет. Он работает только с Azure AD.

## <a name="what-version-of-saml-does-the-plug-in-work-with"></a>Какая версия SAML нужна для работы подключаемого модуля?

Он работает с SAML 2.0.

## <a name="does-the-plug-in-do-user-provisioning"></a>Поддерживает ли этот подключаемый модуль подготовку пользователей?

Нет. Сейчас подключаемый модуль поддерживает только единый вход на основе SAML 2.0. Прежде чем использовать единый вход, необходимо подготовить пользователя в приложении.

## <a name="does-the-plug-in-support-cluster-versions-of-jira-and-confluence"></a>Поддерживает ли этот подключаемый модуль кластерные версии Jira и Confluence?

Нет. Подключаемый модуль работает только с локальными версиями Jira и Confluence.

## <a name="does-the-plug-in-work-with-http-versions-of-jira-and-confluence"></a>Поддерживает ли этот подключаемый модуль HTTP-версии Jira и Confluence?

Нет. Этот подключаемый модуль работает только с системами, поддерживающими протокол HTTPS.