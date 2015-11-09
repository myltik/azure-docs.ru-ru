<properties
   pageTitle="Изменения в атрибутах объекта внешнего пользователя в предварительной версии службы Azure Active Directory B2B Collaboration | Microsoft Azure"
   description="Служба Azure Active Directory B2B помогает поддерживать отношения между компаниями, предоставляя бизнес-партнерам выборочный доступ к вашим корпоративным приложениям"
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
   ms.date="10/27/2015"
   ms.author="viviali"/>

# Изменения в атрибутах объекта внешнего пользователя в предварительной версии службы Azure Active Directory (Azure AD) B2B Collaboration
Каждый пользователь в каталоге Azure AD представлен объектом пользователя. Атрибуты объекта пользователя в Azure AD претерпевают ряд изменений на разных этапах процесса приглашения и активации в службе B2B Collaboration. Объект пользователя, представляющий пользователя-партнера в каталоге, имеет атрибуты, которые изменяются при активации приглашения, т. е. когда пользователь- партнер переходит по ссылке в электронном письме с приглашением. В частности:

- Присваиваются значения атрибутам **SignInName** и **AltSecId**.
- Значение атрибута **DisplayName** изменяется: вместо имени участника-пользователя (user\_fabrikam.com#EXT#@contoso.com) присваивается учетное имя (user@fabrikam.com).

Вы можете проверить значения этих атрибутов в Azure AD, чтобы понять, активировал ли пользователь-партнер свое приглашение в службу B2B Collaboration.

## Связанные статьи
Ознакомьтесь с другими статьями о службе Azure B2B Collaboration.

- [Что такое служба Azure AD B2B Collaboration?](active-directory-b2b-what-is-azure-ad-b2b.md)
- [Принцип работы](active-directory-b2b-how-it-works.md)
- [Подробное пошаговое руководство](active-directory-b2b-detailed-walkthrough.md)
- [Справочник по формату файлов CSV](active-directory-b2b-references-csv-file-format.md)
- [Формат маркера внешнего пользователя](active-directory-b2b-references-external-user-token-format.md)
- [Текущие ограничения предварительной версии](active-directory-b2b-current-preview-limitations.md)

<!---HONumber=Nov15_HO1-->