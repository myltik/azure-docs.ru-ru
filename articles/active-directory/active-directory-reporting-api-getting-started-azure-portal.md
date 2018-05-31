---
title: Начало работы с API отчетов Azure AD | Документация Майкрософт
description: Как начать работу с API отчетов Azure Active Directory
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 8813b911-a4ec-4234-8474-2eef9afea11e
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2018
ms.author: dhanyahk;markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: a818c9b0e2c9097f45d2fdd39676ef6807d06ca5
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/10/2018
ms.locfileid: "33929092"
---
# <a name="get-started-with-the-azure-active-directory-reporting-api"></a>Начало работы с API отчетов Azure Active Directory

Azure Active Directory предоставляет разнообразные [отчеты](active-directory-reporting-azure-portal.md). Данные этих отчетов могут быть очень полезными для приложений, например систем SIEM, а также инструментов аудита и бизнес-аналитики. 

С помощью интерфейсов API отчетов Azure AD предоставляют программный доступ к данным с помощью набора интерфейсов API на базе REST. Эти интерфейсы API можно вызвать, используя различные языки и инструменты программирования.

Эта статья предоставляет инструкции по получению доступа к данным отчетов с использованием соответствующих API.

При возникновении проблем ознакомьтесь со статьей [Как получить поддержку для Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-troubleshooting-support-howto).


## <a name="prerequisites"></a>предварительным требованиям

Чтобы получить доступ к API отчетов, даже если вы планируете получить доступ к API, используя сценарий, необходимо следующее:

1. Назначение ролей ("Читатель безопасности", "Администратор безопасности", "Глобальный администратор").
2. Регистрация приложения
3. предоставьте разрешения;
4. Сбор параметров конфигурации


 
Подробные инструкции см. в [предварительных требованиях для доступа к API отчетов Azure Active Directory](active-directory-reporting-api-prerequisites-azure-portal.md).


## <a name="recommendation"></a>Рекомендации 

Если вы планируете извлечь данные об отчетах без вмешательства пользователя, следует использовать API отчетов Azure AD с сертификатами.

Подробные инструкции см. в статье о [получении данных с помощью API отчетов Azure AD с сертификатами](active-directory-reporting-api-with-certificates.md).


## <a name="explore"></a>Изучить

Ознакомьтесь с API отчетов:
   
   - [Использование примеров для API аудита](active-directory-reporting-api-audit-samples.md) 
 
   - [Использование примеров для API отчетов о действиях при входе](active-directory-reporting-api-sign-in-activity-samples.md)


## <a name="customize"></a>Настроить  

Создайте собственное решение: 
   
   - [Справочник по использованию API аудита](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) 

   - [Справочник по использованию API отчетов о действиях при входе](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)



