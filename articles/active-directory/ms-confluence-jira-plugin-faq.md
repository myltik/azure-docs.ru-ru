---
title: Часто задаваемые вопросы по подключаемому модулю единого входа Microsoft Azure Active Directory | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и приложением единого входа Microsoft Azure Active Directory для JIRA.
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
ms.openlocfilehash: 571fbd5078f66375f6e81cba2a790121366f9d60
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/16/2018
---
# <a name="microsoft-azure-active-directory-single-sign-on-plugin-faq"></a>Часто задаваемые вопросы по подключаемому модулю единого входа Microsoft Azure Active Directory 

## <a name="1-whats-the-microsoft-sso-add-on"></a>1. Что представляет собой дополнение единого входа Microsoft?

Это дополнение предоставляет возможность единого входа в Atlassian JIRA (включая JIRA Core, JIRA Software и JIRA Service Deck) и в локальный программный пакет Confluence. Дополнение использует Azure AD в качестве поставщика удостоверений.

## <a name="2-add-on-works-with-which-atlassian-products"></a>2. Работает ли дополнение с продуктами компании Atlassian?

Сейчас дополнение работает с локальными версиями JIRA и Confluence.

## <a name="3-does-this-add-on-work-on-cloud-version"></a>3. Работает ли дополнение с облачной версией?

Нет. Поддерживаются только локальные версии JIRA и Confluence.

## <a name="4-which-versions-of-jira-and-confluence-are-supported"></a>4. Какие версии JIRA и Confluence поддерживаются?

Ниже приведен список поддерживаемых версий.

* JIRA Core и JIRA Software: 6.0–7.2.2; 
* JIRA Service Desk: 3.0–3.2. 
* Confluence: версии c 5.0 по 5.10

## <a name="5-is-this-add-on-free-or-paid"></a>5. Это дополнение предлагается бесплатно или за плату?

Это бесплатное дополнение и вы можете установить его через магазин Atlassian.

## <a name="6-do-i-need-to-restart-jiraconfluence-once-i-deploy-the-add-on"></a>6. Нужно ли перезапускать JIRA/Confluence после установки дополнения?

Перезагрузка после развертывания дополнения не требуется. Вы можете использовать дополнение немедленно после развертывания.

## <a name="7-how-do-i-get-support-for-the-add-on"></a>7. Как можно получить поддержку по этому дополнению?

Обратитесь к нам по адресу <email>. Мы ответим в течение <> часов. Можно также создать обращение в службу поддержки корпорации Майкрософт через портал Azure. Также в рабочие дни с <> по <> часов вы можете позвонить нам по телефону <Number>.

## <a name="8-would-this-add-on-work-on-mac-or-ubuntu-installation-of-jira-and-confluence"></a>8. Будет ли это дополнение работать с установкой JIRA и (или) Confluence на Mac или Ubuntu?

Мы тестировали дополнение только с установками JIRA и Confluence в 64-разрядных операционных системах Windows Server.

## <a name="9-does-this-add-on-work-with-other-idps-than-azure-ad"></a>9. Работает ли это дополнение с другими поставщиками удостоверений, кроме Azure AD?

Нет. Дополнение работает только с Azure AD.

## <a name="10-what-version-of-saml-does-the-add-on-work-with"></a>10. Какая версия SAML нужна для работы дополнения?

Дополнение работает с SAML 2.0.

## <a name="11-does-the-add-on-do-use-provisioning-as-well"></a>11. Использует ли это дополнение механизм подготовки?

Нет. На данный момент дополнение поддерживает единый вход только на основе SAML 2.0. Пользователь должен быть подготовлен в приложении заранее, до входа через этот механизм.

## <a name="12-are-cluster-versions-of-jira-and-confluence-supported-by-add-on"></a>12. Поддерживает ли это дополнение кластерные версии JIRA и (или) Confluence?

Нет. Сейчас дополнение работает только с локальными версиями JIRA и Confluence.

## <a name="13-would-this-plugin-work-with-http-version-of-jira-and-confluence"></a>13. Будет ли этот подключаемый модуль работать с HTTP-версией JIRA и (или) Confluence?

Нет. Это дополнение поддерживает для установок только протокол HTTPS.

## <a name="14-do-i-need-to-buy-license-of-the-add-on"></a>14. Нужно ли приобретать лицензию для использования дополнения?

Это бесплатное дополнение.