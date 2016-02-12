<properties
   pageTitle="Формат внешнего пользовательского токена для предварительной версии службы Azure Active Directory B2B Collaboration | Microsoft Azure"
   description="Служба Azure Active Directory B2B поддерживает взаимодействие между компаниями, позволяя предоставлять бизнес-партнерам выборочный доступ к вашим корпоративным приложениям."
   services="active-directory"
   authors="viv-liu"
   manager="cliffdi"
   editor=""
   tags=""/>

<tags
   ms.service="active-directory"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="na"
   ms.date="02/03/2016"
   ms.author="viviali"/>

# Формат внешнего пользовательского токена для предварительной версии службы Azure Active Directory B2B Collaboration

Утверждения для стандартного токена Azure AD рассматриваются в статье [Поддерживаемые типы токенов и утверждений](active-directory-token-and-claims.md).

Следующие утверждения отличаются от авторизованного внешнего пользователя B2B Collaboration:<br/> ― **OID** ― идентификатор объекта из клиента источника<br/> ― **TID** ― идентификатор клиента из клиента источника<br/> ― **Issuer** ― клиент источника<br/> ― **IDP** ― главный клиент пользователя<br/> ― **AltSecId** ― альтернативный идентификатор безопасности, который вам не виден<br/>

## Связанные статьи
Ознакомьтесь с другими статьями о службе Azure B2B Collaboration.

- [Обзор службы Azure AD B2B Collaboration](active-directory-b2b-what-is-azure-ad-b2b.md)
- [Принцип работы](active-directory-b2b-how-it-works.md)
- [Подробное пошаговое руководство](active-directory-b2b-detailed-walkthrough.md)
- [Справка в формате CSV-файла](active-directory-b2b-references-csv-file-format.md)
- [Изменение атрибутов объекта внешнего пользователя](active-directory-b2b-references-external-user-object-attribute-changes.md)
- [Текущие ограничения предварительной версии](active-directory-b2b-current-preview-limitations.md)

<!---HONumber=AcomDC_0204_2016-->