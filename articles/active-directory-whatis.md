<properties linkid="manage-services-identity-what-is-windows-azure-AD" urlDisplayName="What is Azure AD?" pageTitle="What is Azure Active Directory?" metaKeywords="" description="Use Azure Active Directory to extend your existing on-premises identities into the cloud for an improved admin and end user experience while Microsoft keeps Active Directory running in the cloud with high scale, high availability, and integrated disaster recovery. Or, develop Azure AD integrated applications for your organization or for other organizations." metaCanonical="" services="active-directory" documentationCenter="" title="What is Azure Active Directory?" authors="justinha" solutions="" manager="terrylan" editor="" />

<tags ms.service="active-directory" ms.workload="identity" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="justinha"></tags>

# Что такое Windows Azure Active Directory

Azure Active Directory — это служба, которая предоставляет возможности управления идентификацией и доступом в облаке. Аналогично тому, как служба каталогов Active Directory доступна клиентам операционной системы Windows Server для управления локальными удостоверениями, Azure Active Directory (Azure AD) является службой, доступной в Azure, для управления облачными удостоверениями. [Подробнее][Подробнее]

Поскольку это облачный каталог вашей организации, вы можете определять, кто будет пользователем каталога, какие сведения следует хранить в облаке, кто может пользоваться данными и управлять ими, а также какие приложения или службы могут получать доступ к этим сведениям.

При использовании Azure AD ответственность за работу Active Directory лежит на корпорации Майкрософт, которая обеспечивает высокую доступность, расширенные возможности масштабирования, а также интегрированное восстановление при сбоях, соблюдая при этом все требования к обеспечению конфиденциальности и безопасности данных вашей организации.

### Интеграция с локальной службой каталогов Active Directory

Службу Azure AD можно использовать как изолированный облачный каталог вашей организации, однако возможна и интеграция с существующей локальной службой каталогов Active Directory. Некоторые из функций интеграции включают в себя синхронизацию каталогов и единый вход, так что локальные удостоверения можно будет использовать для входа в облако, тем самым упрощая работу как пользователей, так и администраторов.
 [Подробнее][1]

### Интеграция с приложениями

Разработчики приложений могут интегрировать свои приложения с Azure AD, чтобы предоставить пользователям функциональные возможности единого входа. Это позволяет размещать корпоративные приложения в облаке и без труда проверять подлинность пользователей, использующих корпоративные учетные данные. Это также позволяет поставщикам SaaS (программного обеспечения как службы) упрощать аутентификацию тех пользователей, которые уже внесены в каталог Azure AD организации, при аутентификации для доступа к службам. Разработчики также могут использовать графический интерфейс API для запроса данных каталога в целях управления такими объектами, как пользователи и группы. [Подробнее][2]

**Дополнительные ресурсы**

-   [Подпишитесь на Azure как организация][Подпишитесь на Azure как организация]
-   [Удостоверение Azure][Удостоверение Azure]
-   [Библиотека по Azure AD на сайте MSDN][Библиотека по Azure AD на сайте MSDN]

  [Подробнее]: http://msdn.microsoft.com/library/hh967611.aspx
  [1]: http://msdn.microsoft.com/library/jj573653
  [2]: http://go.microsoft.com/fwlink/?LinkID=290817&clcid=0x409
  [Подпишитесь на Azure как организация]: /ru-ru/manage/services/identity/organizational-account/
  [Удостоверение Azure]: /ru-ru/manage/windows/fundamentals/identity/
  [Библиотека по Azure AD на сайте MSDN]: http://go.microsoft.com/fwlink/?LinkId=293425
