---
title: "Azure AD Connect в Microsoft Cloud для Германии"
description: "Azure AD Connect интегрирует локальные каталоги с Azure Active Directory. Таким образом вы сможете предоставить пользователям возможность получать доступ с использованием одного удостоверения для Office 365, Azure и SaaS, интегрированных с Azure AD."
keywords: "введение в Azure AD Connect, обзор Azure AD Connect, что такое Azure AD Connect, установка Аctive Directory, Германия, Шварцвальд"
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: 
ms.assetid: 2bcb0caf-5d97-46cb-8c32-bda66cc22dad
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/12/2017
ms.author: billmath
ms.translationtype: Human Translation
ms.sourcegitcommit: 6dbb88577733d5ec0dc17acf7243b2ba7b829b38
ms.openlocfilehash: 780728950199bac6a317767ef1db4462b3fe6ffd
ms.contentlocale: ru-ru
ms.lasthandoff: 07/04/2017

---
# Общедоступная предварительная версия Azure AD Connect в Microsoft Cloud для Германии
<a id="azure-ad-connect-in-microsoft-cloud-germany---public-preview" class="xliff"></a>
## Введение
<a id="introduction" class="xliff"></a>
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
 
## Загрузить
<a id="download" class="xliff"></a>
Вы можете загрузить Azure AD Connect из колонки Azure AD Connect на портале.  Чтобы найти колонку Azure AD Connect, следуйте указанным ниже инструкциям.

### Колонка Azure AD Connect
<a id="the-azure-ad-connect-blade" class="xliff"></a>
Войдите на портал Azure и выполните следующие действия:

1. Щелкните "Обзор".
2. Выберите Azure Active Directory.
3. Затем выберите Azure AD Connect.

Вы увидите следующее:

![Колонка Azure AD Connect](media/active-directory-aadconnect-germany/germany1.png)

Следующая таблица содержит описание функций, представленных в колонке.

| Название | Описание |
| --- | --- |
| Состояние синхронизации |Сообщает, включена ли синхронизация. |
| Последняя синхронизация |Показывает, когда в последний раз была успешно выполнена синхронизация. |
| Федеративные домены |Показывает текущее количество настроенных федеративных доменов. |

## Установка
<a id="installation" class="xliff"></a>
Чтобы установить Azure AD Connect, воспользуйтесь [этой документацией](active-directory-aadconnect.md#install-azure-ad-connect).

## Дополнительные функции и дополнительные сведения
<a id="advanced-features-and-additional-information" class="xliff"></a>
Для начала ознакомьтесь со статьей [Интеграция локальных удостоверений с Azure Active Directory](active-directory-aadconnect.md), чтобы получить подробные сведения и рекомендации по пользовательским и расширенным настройкам.  На этой странице представлена необходимая информация и ссылки на дополнительные инструкции.


