---
title: "Неправильное отображение страницы приложения для приложения прокси приложения | Документы Майкрософт"
description: "Указания на случай, когда страница отображается неправильно в приложении прокси приложения, интегрированном с Azure AD."
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
ms.openlocfilehash: d0b0ddba5ad9f8c584cd35c6f20edf76883b425d
ms.contentlocale: ru-ru
ms.lasthandoff: 04/17/2017

---

<a id="application-page-does-not-display-correctly-for-an-application-proxy-application" class="xliff"></a>

# Неправильное отображение страницы приложения для приложения прокси приложения

Эта статья помогает устранить проблемы с приложениями прокси приложения Azure Active Directory, когда вы переходите на страницу и на ней что-то работает неправильно.

<a id="overview" class="xliff"></a>

## Обзор
При публикации приложения прокси приложения доступны только страницы из корневого каталога. Если страница отображается неправильно, возможно, в корневом внутреннем URL-адресе для этого приложения отсутствуют некоторые ресурсы страницы. Чтобы решить проблему, убедитесь, что вы опубликовали *все* ресурсы для страницы в составе своего приложения.

Убедиться в наличии проблемы можно, открыв средство отслеживания сети (например, Fiddler либо инструменты F12 в Internet Explorer или Edge), загрузив страницу и выполнив поиск ошибок 404. Это позволяет выявить страницы, которые сейчас не удается найти и, возможно, все еще требуется опубликовать.

В качестве примера этой ситуации предположим, что вы опубликовали приложение учета расходов с помощью внутреннего URL-адреса <http://myapps/expenses>, но приложение использует таблицу стилей <http://myapps/style.css>. В этом случае таблица стилей не публикуется в вашем приложении, поэтому при запуске приложения выдается ошибка 404 из-за попытки загрузить style.css. В этом примере проблему можно было бы решить, опубликовав приложение с помощью внутреннего URL-адреса <http://myapp/>.

<a id="problems-with-publishing-as-one-application" class="xliff"></a>

## Проблемы при публикации в качестве одного приложения

Если невозможно опубликовать все ресурсы в одном приложении, нужно опубликовать несколько приложений и включить ссылки между ними.

Для этого рекомендуется использовать решение на базе [личных доменов](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-custom-domains). Однако это решение требует, чтобы вы владели сертификатом для своего домена, а ваши приложения использовали полные доменные имена (FQDN). Другие варианты описаны в [документации по устранению неполадок с неработающими ссылками](https://microsoft-my.sharepoint.com/personal/harshja_microsoft_com/_layouts/15/guestaccess.aspx?guestaccesstoken=IxuG3mFVbnPWI3Yn4Qi7wCNi8VIfHS5mwPt5quh8DMw%3d&docid=2_14558cd6ddea34c1c9887dc640feb5831&rev=1).

<a id="next-steps" class="xliff"></a>

## Дальнейшие действия
[Публикация приложений с помощью прокси приложения Azure AD](application-proxy-publish-azure-portal.md)

