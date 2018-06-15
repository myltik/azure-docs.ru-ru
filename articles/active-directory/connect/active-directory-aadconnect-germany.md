---
title: Azure AD Connect в Microsoft Cloud для Германии
description: Azure AD Connect интегрирует локальные каталоги с Azure Active Directory. Таким образом вы сможете предоставить пользователям возможность получать доступ с использованием одного удостоверения для Office 365, Azure и SaaS, интегрированных с Azure AD.
keywords: введение в Azure AD Connect, обзор Azure AD Connect, что такое Azure AD Connect, установка Аctive Directory, Германия, Шварцвальд
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: 2bcb0caf-5d97-46cb-8c32-bda66cc22dad
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/12/2017
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 5771d3319194ff4952f4c93140594db2d93bb7f8
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34593560"
---
# <a name="azure-ad-connect-in-microsoft-cloud-germany---public-preview"></a>Общедоступная предварительная версия Azure AD Connect в Microsoft Cloud для Германии
## <a name="introduction"></a>Введение
Azure AD Connect обеспечивает синхронизацию локальных каталогов Active Directory с каталогами Azure Active Directory.
В настоящее время многие сценарии в [Microsoft Cloud для Германии](https://www.microsoft.com/de-de/cloud/deutschland/default.aspx) должны выполняться оператором. При использовании Microsoft Cloud для Германии важно придерживаться следующих рекомендаций.

* Для успешного выполнения синхронизации указанные ниже URL-адреса должны быть открыты через прокси-сервер:
  
  * *.microsoftonline.de
  * *.windows.net
  * * списки отзыва сертификатов.
* При входе в каталог Azure AD необходимо использовать учетную запись в домене onmicrosoft.de.
* Следующие функции сейчас не поддерживаются:
  * Azure AD Connect Health,
  * Автоматическое обновление
 
## <a name="download"></a>Загрузка
Вы можете загрузить Azure AD Connect из колонки Azure AD Connect на портале.  Чтобы найти колонку Azure AD Connect, следуйте указанным ниже инструкциям.

### <a name="the-azure-ad-connect-blade"></a>Колонка Azure AD Connect
Войдите на портал Azure и выполните следующие действия:

1. Щелкните "Обзор".
2. Выберите Azure Active Directory.
3. Затем выберите Azure AD Connect.

Вы увидите следующее:

![Колонка Azure AD Connect](media/active-directory-aadconnect-germany/germany1.png)

Следующая таблица содержит описание функций, представленных в колонке.

| Название | ОПИСАНИЕ |
| --- | --- |
| Состояние синхронизации |Сообщает, включена ли синхронизация. |
| Последняя синхронизация |Показывает, когда в последний раз была успешно выполнена синхронизация. |
| Федеративные домены |Показывает текущее количество настроенных федеративных доменов. |

## <a name="installation"></a>Установка
Чтобы установить Azure AD Connect, воспользуйтесь [этой документацией](active-directory-aadconnect.md#install-azure-ad-connect).

## <a name="advanced-features-and-additional-information"></a>Дополнительные функции и дополнительные сведения
Для начала ознакомьтесь со статьей [Интеграция локальных удостоверений с Azure Active Directory](active-directory-aadconnect.md), чтобы получить подробные сведения и рекомендации по пользовательским и расширенным настройкам.  На этой странице представлена необходимая информация и ссылки на дополнительные инструкции.

