<properties
   pageTitle="Библиотека документов для управления приложениями | Microsoft Azure"
   description="Список статей по управлению приложениями в Azure Active Directory со ссылками на технические руководства, инструкции, рекомендации по устранению неполадок и часто задаваемые вопросы"
   services="active-directory"
   documentationCenter=""
   authors="kgremban"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="12/03/2015"
   ms.author="kgremban"/>

# Библиотека документов для управления приложениями

Эта страница содержит ссылки на полезные ресурсы, посвященные основным функциям единого входа на основе Azure Active Directory. Ссылки отсортированы по нескольким категориям.

- **Обзор:** обзор функций с примерами использования и общим описанием темы.
- **Инструкции:** подробные указания по началу работы с определенными сценариями.
- **Устранение неполадок:** советы по диагностике распространенных проблем.
- **Часто задаваемые вопросы:** ответы на распространенные вопросы.  

## Cloud App Discovery

| | |
| ------ | ------ |
| Обзор | [Как обнаруживать несанкционированные облачные приложения, которые используются в моей организации](active-directory-cloudappdiscovery-whatis.md) |
| Инструкции | [Начало работы с Cloud App Discovery](http://social.technet.microsoft.com/wiki/contents/articles/30962.getting-started-with-cloud-app-discovery.aspx) <br><br> [Вопросы безопасности и конфиденциальности Cloud App Discovery](active-directory-cloudappdiscovery-security-and-privacy-considerations.md) <br><br> [Руководство по развертыванию групповой политики Cloud App Discovery ](http://social.technet.microsoft.com/wiki/contents/articles/30965.cloud-app-discovery-group-policy-deployment-guide.aspx) <br><br> [Руководство по развертыванию Cloud App Discovery System Center](http://social.technet.microsoft.com/wiki/contents/articles/30968.cloud-app-discovery-system-center-deployment-guide.aspx) <br><br> [Параметры реестра Cloud App Discovery для прокси-служб](active-directory-cloudappdiscovery-registry-settings-for-proxy-services.md) |
| Часто задаваемые вопросы | [Часто задаваемые вопросы по Cloud App Discovery](http://social.technet.microsoft.com/wiki/contents/articles/24037.cloud-app-discovery-frequently-asked-questions.aspx) |

## Назначение приложений

| | |
| ------ | ------ |
| Обзор | [Управление доступом к ресурсам с помощью групп Azure Active Directory](active-directory-manage-groups.md) |
| Инструкции | [Создание простого правила для настройки динамического членства для группы](active-directory-accessmanagement-simplerulegroup.md) <br><br> [Использование группы для управления доступом к приложениям SaaS](active-directory-accessmanagement-group-saasapps.md) <br><br> [Настройка Azure Active Directory для управления самостоятельным доступом к приложениям](active-directory-accessmanagement-self-service-group-management.md) <br><br> [Управление владельцами группы](active-directory-accessmanagement-managing-group-owners.md) <br><br> [Управление группами безопасности в Azure Active Directory](active-directory-accessmanagement-manage-groups.md) <br><br> [Выделенные группы в Azure Active Directory](active-directory-accessmanagement-dedicated-groups.md) <br><br> [Использование атрибутов для создания расширенных правил](active-directory-accessmanagement-groups-with-advanced-rules.md) <br><br> [Автоматическая подготовка пользователей и ее отзыв для приложений SaaS в Azure Active Directory ](active-directory-saas-app-provisioning.md) <br><br> [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md) |

## Федеративный единый вход

| | |
| ------ | ------ |
| Обзор |[Настройка федерации с AD FS](active-directory-aadconnect-get-started-custom.md)
| Инструкции | [DirSync с единым входом](https://msdn.microsoft.com/library/azure/dn441213.aspx) <br><br> [Microsoft Azure теперь поддерживает федерацию с Windows Server Active Directory](https://azure.microsoft.com/blog/windows-azure-now-supports-federation-with-windows-server-active-directory/) <br><br> [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md) <br><br> [Приложения Azure Active Directory](https://azure.microsoft.com/marketplace/active-directory/) |
| Устранение неполадок | [Устранение неполадок в мастере настройки средства Azure AD DirSync](http://social.technet.microsoft.com/wiki/contents/articles/19100.troubleshooting-azure-ad-dirsync-tool-configuration-wizard-failed-to-get-address-for-method-createidentityhandle2.aspx) <br><br> [Устранение неполадок при установке средства синхронизации Azure Active Directory и ошибок мастера настройки](https://support.microsoft.com/kb/2684395) <br><br> [Устранение неполадок при синхронизации каталогов](https://msdn.microsoft.com/library/azure/jj151787.aspx) <br><br> [Устранение неполадок при выполнении единого входа](https://msdn.microsoft.com/library/azure/jj151834.aspx) |
| Часто задаваемые вопросы | [Управление сертификатами для федеративного единого входа в Azure Active Directory](active-directory-sso-certs.md) <br><br> [Azure Active Directory Connect: вопросы и ответы](active-directory-aadconnect-faq.md) |

## Единый вход по паролю

| | |
| ------ | ------ |
| Инструкции | [Интеграция локальных удостоверений с Azure Active Directory](active-directory-aadconnect.md) <br><br> [DirSync с синхронизацией паролей](https://msdn.microsoft.com/library/azure/dn441214.aspx) |
| Устранение неполадок | [Устранение неполадок в мастере настройки средства Azure AD DirSync](http://social.technet.microsoft.com/wiki/contents/articles/19100.troubleshooting-azure-ad-dirsync-tool-configuration-wizard-failed-to-get-address-for-method-createidentityhandle2.aspx)<br><br>[Устранение неполадок при установке средства синхронизации Azure Active Directory и ошибок мастера настройки](https://support.microsoft.com/kb/2684395) <br><br> [Устранение неполадок при синхронизации каталогов](https://msdn.microsoft.com/library/azure/jj151787.aspx) |
| Часто задаваемые вопросы | [Azure Active Directory Connect: вопросы и ответы](active-directory-aadconnect-faq.md) |

## Подготовка полнофункциональных приложений

| | |
| ------ | ------ |
| Обзор | [Автоматическая подготовка пользователей и ее отзыв для приложений SaaS в Azure Active Directory](active-directory-saas-app-provisioning.md) |
| Инструкции | [Руководство. Интеграция Azure Active Directory с Concur](active-directory-saas-concur-tutorial.md) <br><br> [Учебник. Интеграция Azure Active Directory с DocuSign](active-directory-saas-docussign-tutorial.md) <br><br> [Руководство. Интеграция Azure Active Directory с Dropbox for Business](active-directory-saas-dropboxforbusiness-tutorial.md) <br><br> [Учебник. Интеграция Google Apps с Azure Active Directory](active-directory-saas-google-apps-tutorial.md) <br><br> [Учебник. Как интегрировать Salesforce с Azure Active Directory](active-directory-saas-salesforce-tutorial.md) <br><br> [Руководство: интеграция Azure Active Directory с ServiceNow](active-directory-saas-servicenow-tutorial.md) <br><br> [Руководство. Настройка Workday для входящей синхронизации](active-directory-saas-workday-inbound-tutorial.md) |

## Коллекция приложений Azure AD

| | |
| ------ | ------ |
| Инструкции | [Добавление приложения в коллекцию приложений Azure Active Directory](active-directory-app-gallery-listing.md) <br><br> [Использование собственных приложений с конфигурацией SAML самообслуживания Azure AD](http://blogs.technet.com/b/ad/archive/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-gt-now-in-preview.aspx) |

## Пользовательские приложения

| | |
| ------ | ------ |
| Обзор | [Использование собственных приложений с конфигурацией SAML самообслуживания Azure AD](http://blogs.technet.com/b/ad/archive/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-gt-now-in-preview.aspx). |
| Инструкции | [Учебник. Как интегрировать Salesforce с Azure Active Directory](active-directory-saas-salesforce-tutorial.md) |

## Прокси приложения

| | |
| ------ | ------ |
| Обзор | [Как обеспечить безопасный удаленный доступ к локальным приложениям](active-directory-application-proxy-get-started.md) |
| Инструкции | [Включение прокси приложения Azure AD](active-directory-application-proxy-enable.md) <br><br> [Публикация приложений с помощью прокси приложения Azure AD](active-directory-application-proxy-publish.md) <br><br> [Работа с условным доступом](active-directory-application-proxy-conditional-access.md) <br><br> [Работа с пользовательскими доменами в прокси приложения Azure AD](active-directory-application-proxy-custom-domains.md) <br><br> [Работа с приложениями, поддерживающими утверждения, в прокси приложения](active-directory-application-proxy-claims-aware-apps.md) <br><br> [Единый вход с помощью прокси приложения](active-directory-application-proxy-sso-using-kcd.md) |
| Устранение неполадок | [Устранение неполадок прокси-сервера приложений](active-directory-application-proxy-troubleshoot.md) |

## Запуск приложений

| | |
| ------ | ------ |
| Обзор | [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md) |
| Инструкции | [Панель доступа Azure Active Directory — EMS](http://blogs.msdn.com/b/haddy_el-haggan_blog/archive/2015/04/02/azure-active-directory-access-panel-ems.aspx) <br><br> [Интеграция с Azure Active Directory](active-directory-how-to-integrate.md) |

<!---HONumber=AcomDC_1210_2015-->