<properties
	pageTitle="Службы синхронизации Azure AD Connect: общие сведений о синхронизации и ее настройка | Microsoft Azure"
	description="В этой статье описываются принципы работы и настройка служб синхронизации Azure AD Connect."
	services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/04/2016"
	ms.author="markusvi;andkjell"/>


# Службы синхронизации Azure AD Connect: общие сведений о синхронизации и ее настройка

Службы синхронизации Azure Active Directory Connect (службы синхронизации Azure AD Connect) — это основной компонент Azure AD Connect. Этот компонент отвечает за все операции, относящиеся к синхронизации данных удостоверения между локальной средой и Azure AD в облаке. С точки зрения синхронизации службы синхронизации Azure AD Connect — преемник таких решений, как DirSync, Azure AD Sync и Forefront Identity Manager с настроенным соединителем Azure Active Directory.

<center>![Azure AD Connect Sync](./media/active-directory-aadconnectsync-whatis/sync01.png) </center>


Эта статья содержит ссылки на статьи, посвященные службам синхронизации Azure AD Connect.

Что необходимо сделать дальше?

- Общие сведения о технических концепциях см. в статье [Технические концепции](active-directory-aadconnectsync-technical-concepts.md).
- Обзор топологий и сценариев, которые поддерживаются в службах синхронизации Azure AD Connect, см. в разделе [Топологии Azure AD Connect](active-directory-aadconnect-topologies.md).
- Если вы хотите узнать, как реализовать фильтр, см. статью [Настройка фильтров](active-directory-aadconnectsync-configure-filtering.md).
- Если вы хотите получить дополнительные сведения о синхронизации паролей, см. статью [Реализация синхронизации паролей](active-directory-aadconnectsync-implement-password-synchronization.md).
- Если вы хотите узнать о конфигурации по умолчанию в Azure AD Connect, см. статью [Общие сведения о конфигурации по умолчанию](active-directory-aadconnectsync-understanding-default-configuration.md).
- Дополнительные сведения о пользователях и контактах в службах синхронизации Azure AD Connect см. в статье [Общее представление о пользователях и контактах](active-directory-aadconnectsync-understanding-users-and-contacts.md).
- Сведения о том, как изменить конфигурацию по умолчанию, см. в статье [Рекомендации по изменению конфигурации по умолчанию](active-directory-aadconnectsync-best-practices-changing-default-configuration.md).
- Дополнительные сведения о работе сервера синхронизации приведены в статье [Рабочие задачи и рекомендации](active-directory-aadconnectsync-operations.md)
- Сведения об атрибутах, которые синхронизируются с Azure AD, приведены в статье [Атрибуты, синхронизируемые с Azure Active Directory](active-directory-aadconnectsync-attributes-synchronized.md).
- Больше о декларативной подготовке можно узнать в статье [Выражения декларативной подготовки](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md).
- Ознакомиться с функциями декларативной подготовки можно в [справочнике по функциям](active-directory-aadconnectsync-functions-reference.md).


## Дополнительные ресурсы

* [Интеграция локальных удостоверений с Azure Active Directory](active-directory-aadconnect.md)

<!---HONumber=AcomDC_0107_2016-->