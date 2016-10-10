<properties
    pageTitle="Приступая к работе с API отчетов Azure AD | Microsoft Azure"
    description="Как начать работу с API отчетов Azure Active Directory"
    services="active-directory"
    documentationCenter=""
    authors="dhanyahk"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="09/25/2016"
    ms.author="dhanyahk;markvi"/>

# Приступая к работе с API отчетов Azure Active Directory

* является частью [руководства по отчетам Azure Active Directory](active-directory-reporting-guide.md).*

Azure Active Directory предоставляет разнообразные отчеты. Данные этих отчетов могут быть очень полезными для приложений, например систем SIEM, а также инструментов аудита и бизнес-аналитики. Интерфейсы API отчетов Azure AD предоставляют программный доступ к данным с помощью набора интерфейсов API на базе REST. Эти интерфейсы API можно вызвать, используя различные языки и инструменты программирования.

В этой статье содержатся сведения, необходимые для начала работы с интерфейсами API отчетов Azure AD. В следующем разделе представлены дополнительные сведения об использовании интерфейсов API аудита и входа. Дополнительные сведения о других интерфейсах API см. в статье [Отчеты и события Azure AD (предварительная версия)](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-reports-and-events-preview).


Чтобы задать вопросы, обговорить проблемы или предоставить отзыв, обратитесь в [службу поддержки по инструментам создания отчетов AAD](mailto:aadreportinghelp@microsoft.com).


##Карта обучения

1. **Подготовка**. Перед тестированием примеров API нужно выполнить [предварительные требования для доступа к API отчетов Azure AD](active-directory-reporting-api-prerequisites.md).

2. **Изучение**. Ознакомьтесь с интерфейсами API отчетов:

  - [Использование примеров для API аудита](active-directory-reporting-api-audit-samples.md)
  - [Использование примеров для API отчетов о действиях при входе](active-directory-reporting-api-sign-in-activity-samples.md)

3. **Настройка**. Создайте свое решение:

  - [Справочник по использованию API аудита](active-directory-reporting-api-audit-reference.md)
  - [Справочник по использованию API отчетов о действиях при входе](active-directory-reporting-api-sign-in-activity-reference.md)





## Дальнейшие действия

Если вы хотите просмотреть все доступные конечные точки API Graph Azure AD, перейдите по адресу [https://graph.windows.net/tenant-name/reports/$metadata?api-version=beta](https://graph.windows.net/tenant-name/reports/$metadata?api-version=beta).

<!---HONumber=AcomDC_0928_2016-->