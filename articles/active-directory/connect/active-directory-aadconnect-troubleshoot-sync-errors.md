---
title: 'Azure AD Connect: устранение ошибок синхронизации | Документация Майкрософт'
description: В этой статье объясняется, как устранить ошибки, возникшие во время синхронизации с Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: curtand
ms.assetid: 2209d5ce-0a64-447b-be3a-6f06d47995f8
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.author: billmath
ms.openlocfilehash: aaa374d5a11ef5b5860f83a87386ff981319189f
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/05/2018
---
# <a name="troubleshooting-errors-during-synchronization"></a>Устранение ошибок синхронизации
При синхронизации данных удостоверений Windows Server Active Directory (AD DS) с Azure Active Directory (Azure AD) могут возникать ошибки. В этой статье предоставляются общие сведения о различных типах ошибок синхронизации, некоторые возможные сценарии возникновения этих ошибок, а также возможные способы их устранения. Здесь содержатся сведения о распространенных типах ошибок, возможно, рассматриваются не все возможные ошибки.

 В этой статье предполагается, что читатель знаком с базовыми [принципами архитектуры Azure AD и Azure AD Connect](active-directory-aadconnect-design-concepts.md).

В последней версии Azure AD Connect \(от августа 2016 г. и более новых версиях\) добавлена возможность просмотра отчета об ошибках синхронизации на [портале Azure](https://aka.ms/aadconnecthealth) в качестве части отчета Azure AD Connect Health для синхронизации.

С 1 сентября 2016 года функция [устойчивости повторяющихся атрибутов Azure AD](active-directory-aadconnectsyncservice-duplicate-attribute-resiliency.md) будет включена по умолчанию для всех *новых* клиентов Azure AD. В ближайшие месяцы эта функция будет автоматически включена для имеющихся клиентов.

Во время синхронизации Azure AD Connect выполняет в каталогах 3 типа операций: импорт, синхронизацию и экспорт. В ходе всех операций могут возникать ошибки. Эта статья главным образом посвящена ошибкам во время экспорта данных в Azure AD.

## <a name="errors-during-export-to-azure-ad"></a>Ошибки во время экспорта данных в Azure AD
В следующем разделе описываются разные типы ошибок синхронизации, которые могут возникнуть при экспорте данных в Azure AD с помощью соединителя Azure AD. Этот соединитель можно определить по формату имени, например contoso.*onmicrosoft.com*.
Ошибки во время экспорта данных в Azure AD указывают, что при попытке \(модуля синхронизации\) Azure AD Connect выполнить \(добавление, удаление, обновление и т. п.\) в Azure Active Directory произошел сбой.

![Обзор ошибок экспорта](./media/active-directory-aadconnect-troubleshoot-sync-errors/Export_Errors_Overview_01.png)

## <a name="data-mismatch-errors"></a>Ошибки несовпадения данных
### <a name="invalidsoftmatch"></a>InvalidSoftMatch
#### <a name="description"></a>ОПИСАНИЕ
* Когда \(модуль синхронизации\) Azure AD Connect указывает Azure AD добавить или обновить объекты, Azure AD сопоставляет входящий объект, используя атрибут **sourceAnchor**, с атрибутом **immutableId** объектов в Azure AD. Это сопоставление называется **жестким**.
* Если перед подготовкой нового объекта **не удалось найти** объект, соответствующий атрибуту **immutableId** и атрибуту **sourceAnchor** входящего объекта, для поиска соответствия Azure AD использует атрибуты proxyAddresses и userPrincipalName. Такое сопоставление называется **мягким**. Мягкое сопоставление предназначено для имеющихся в Azure AD объектов (происходящих их Azure AD) с новыми объектами, добавленными или обновленными при синхронизации, которые представляют ту же сущность (пользователей, группы) в локальной среде.
* Ошибка **InvalidSoftMatch** возникает, если при жестком сопоставлении не удалось найти соответствующий объект, **А** при мягком сопоставлении он найден, но при этом значение *immutableId* этого объекта и *sourceAnchor* входящего объекта отличаются, предполагая что совпадающий объект синхронизирован с другим объектом из локального каталога AD.

Другими словами, чтобы выполнить мягкое сопоставление, для атрибута *immutableId* объекта, с которым выполняется сопоставление, не должно быть указано значение. Если для объекта с атрибутом *immutableId* задать значение, которое не соответствует условиям жесткого сопоставления, но соответствует условиям мягкого, при синхронизации возникнет ошибка InvalidSoftMatch.

В схеме Azure Active Directory запрещено использовать для нескольких объектов одинаковые значения следующих атрибутов. \(Это неполный список.\)

* ProxyAddresses
* UserPrincipalName
* onPremisesSecurityIdentifier;
* ObjectId

> [!NOTE]
> Функция [устойчивости повторяющихся атрибутов](active-directory-aadconnectsyncservice-duplicate-attribute-resiliency.md) также внедряется в Azure Active Directory в рамках поведения по умолчанию.  Это позволит снизить количество ошибок синхронизации Azure AD Connect (и других клиентов синхронизации), за счет чего обработка повторяющихся в локальных средах Azure AD атрибутов proxyAddresses и userPrincipalName станет более устойчивой. Эта функция не исправляет ошибки дублирования, поэтому данные все равно необходимо исправлять. Но используя эту функцию, можно подготавливать новые объекты, подготовка которых запрещена из-за повторяющихся значений в Azure AD. Это также позволит снизить количество ошибок синхронизации, возвращаемых клиенту синхронизации.
> После включения в клиенте функции устойчивости повторяющихся атрибутов при подготовке новых объектов не будет отображаться ошибка синхронизации InvalidSoftMatch.
>
>

#### <a name="example-scenarios-for-invalidsoftmatch"></a>Примеры сценариев для InvalidSoftMatch
1. В локальном каталоге AD есть несколько объектов с одинаковым значением атрибута proxyAddresses. Azure AD подготовит только один объект.
2. В локальном каталоге AD есть несколько объектов с одинаковым значением атрибута userPrincipalName. Azure AD подготовит только один объект.
3. В локальный каталог AD добавлен объект с таким же значением атрибута proxyAddresses, что и у имеющегося объекта в Azure AD. Объект, добавленный в локальный каталог, не будет подготовлен в Azure Active Directory.
4. В локальный каталог AD добавлен объект с таким же значением атрибута userPrincipalName, что и у учетной записи в Azure AD. Объект не будет подготовлен в Azure Active Directory.
5. Синхронизированная учетная запись перемещена из леса А в лес Б. Azure AD Connect (модуль синхронизации) использовал атрибут ObjectGUID для вычисления sourceAnchor. После перемещения леса значение sourceAnchor изменилось. Новый объект (из леса Б) не удалось синхронизировать с имеющимся объектом в Azure AD.
6. Синхронизированный объект случайно удален из локального каталога AD. В AD для той же сущности (такой как пользователь) создан новый объект. При этом учетная запись в Azure AD не удалена. При синхронизации новой учетной записи с имеющимся объектом Azure AD произойдет сбой.
7. Azure AD Connect удалили и переустановили. При повторной установке вместо атрибута sourceAnchor выбран другой атрибут. Синхронизация всех синхронизированных ранее объектов будет остановлена. Отобразится ошибка InvalidSoftMatch.

#### <a name="example-case"></a>Пример:
1. Пользователь **Григорий Авдеев** синхронизирован в Azure AD из локального каталога AD в домене *contoso.com*.
2. Для атрибута **userPrincipalName** Григория задано значение **bobs@contoso.com**.
3. **abcdefghijklmnopqrstuv==** — это атрибут **sourceAnchor**, вычисленный Azure AD Connect на основе атрибута **objectGUID** из локального каталога Active Directory, который является атрибутом **immutableId** Григория в Azure AD.
4. Для атрибута **proxyAddresses** Григория используются следующие значения.
   * smtp:bobs@contoso.com
   * smtp:bob.smith@contoso.com
   * **smtp:bob@contoso.com**
5. В локальный каталог AD добавлен новый пользователь **Артем Кузнецов**.
6. Для атрибута **userPrincipalName** Артема задано значение **bobt@contoso.com**.
7. **abcdefghijkl0123456789==** — это атрибут **sourceAnchor**, вычисленный Azure AD Connect с использованием атрибута **objectGUID** Артема из локального каталога AD. Объект Артема Кузнецова пока не синхронизирован с Azure AD.
8. Для атрибута proxyAddresses Артема используются следующие значения:
   * smtp:bobt@contoso.com
   * smtp:bob.taylor@contoso.com
   * **smtp:bob@contoso.com**
9. Во время синхронизации Azure AD Connect определит добавление Артема Кузнецова в локальный каталог AD и отправит запрос на изменение аналогичных настроек в Azure AD.
10. Сначала Azure AD выполнит жесткое сопоставление. Другими словами, выполнит поиск объектов, у которых для атрибута immutableId задано значение abcdefghijkl0123456789==. Так как в Azure AD отсутствуют объекты с таким атрибутом immutableId, жесткое сопоставление завершится ошибкой.
11. Затем Azure AD выполнит мягкое сопоставление объекта Артема Кузнецова. То есть Azure AD выполнит поиск объектов, у которых для атрибута proxyAddresses задано три значения, в том числе smtp:bob@contoso.com.
12. Azure AD найдет объект Григория Авдеева, который соответствует условиям поиска. Но для атрибута immutableId этого объекта задано значение abcdefghijklmnopqrstuv==. Это значит, что этот объект синхронизирован из другого объекта локального каталога AD. Поэтому Azure AD не может выполнить нестрогое сопоставление, что приведет к ошибке синхронизации **InvalidSoftMatch**.

#### <a name="how-to-fix-invalidsoftmatch-error"></a>Как устранить ошибку InvalidSoftMatch
Чаще всего причина ошибки InvalidSoftMatch — наличие двух объектов с разными атрибутами sourceAnchor \(immutableId\), но одинаковым значением атрибутов proxyAddresses и/или userPrincipalName, которые используются при мягком сопоставлении в Azure AD. Чтобы устранить ошибку InvalidSoftMatch, выполните следующее:

1. Определите повторяющееся значение атрибута proxyAddresses, userPrincipalName или другого атрибута, вызвавшее ошибку. Кроме того, определите два \(или несколько\) объектов, участвующих в конфликте. Эти объекты можно определить с помощью отчета, созданного [Azure AD Connect Health для синхронизации](https://aka.ms/aadchsyncerrors).
2. Определите объекты, для которых требуется повторяющееся значение.
3. Удалите из объекта ненужное повторяющееся значение. Обратите внимание, что эти изменения необходимо вносить в каталоге, из которого происходит объект. В некоторых случаях может потребоваться удалить один из объектов, участвующих в конфликте.
4. Если изменения внесены в локальном каталоге AD, выполните синхронизацию этих изменений с Azure AD Connect.

Обратите внимание, что отчет об ошибках Azure AD Connect Health для синхронизации обновляется каждые 30 минут. В нем отображаются ошибки, возникшие при последних попытках синхронизации.

> [!NOTE]
> По сути атрибут immutableId не должен изменяться в течение времени существования объекта. Если при настройке Azure AD Connect не учтены некоторые из сценариев выше, может возникнуть ситуация, когда Azure AD Connect вычисляет разное значение атрибута sourceAnchor, представляющего ту же сущность (пользователя, группу, контакт и т. п.), что и в имеющемся объекте Azure AD, который необходимо продолжать использовать.
>
>

#### <a name="related-articles"></a>Связанные статьи
* [Duplicate or invalid attributes prevent directory synchronization in Office 365](https://support.microsoft.com/en-us/kb/2647098) (Запрет синхронизации службы каталогов в Office 365 из-за повторяющихся или недопустимых атрибутов)

### <a name="objecttypemismatch"></a>ObjectTypeMismatch
#### <a name="description"></a>ОПИСАНИЕ
При попытке Azure AD мягко сопоставить два объекта может возникнуть ситуация, когда у двух объектов разных типов (пользователя, группы, контакта и т. п.) одинаковые значения атрибутов, используемых в этом процессе. Так как повторение этих атрибутов не допускается в Azure AD, операция может завершиться ошибкой синхронизации ObjectTypeMismatch.

#### <a name="example-scenarios-for-objecttypemismatch-error"></a>Примеры сценариев ошибки ObjectTypeMismatch
* Администратор создал в Office 365 группу безопасности, поддерживающую почту. Он добавил в локальный каталог AD (пока не синхронизированный с Azure AD) нового пользователя или контакт с тем же значением атрибута proxyAddresses, что и у группы Office 365.

#### <a name="example-case"></a>Примеры
1. Администратор создал для налогового департамента в Office 365 группу безопасности, поддерживающую почту, а в качестве адреса электронной почты указал tax@contoso.com. Таким образом он назначил этой группе атрибут ProxyAddresses со значением **smtp:tax@contoso.com**.
2. К домену contoso.com присоединился новый пользователь, для которого в локальном каталоге создана учетная запись с атрибутом proxyAddress. Значение этого атрибута — **smtp:tax@contoso.com**.
3. Когда Azure AD Connect синхронизирует новую учетную запись пользователя, произойдет ошибка ObjectTypeMismatch.

#### <a name="how-to-fix-objecttypemismatch-error"></a>Как устранить ошибку ObjectTypeMismatch
Чаще всего причина ошибки ObjectTypeMismatch заключается в наличии двух объектов разных типов (пользователь, группа, контакт и т. д.), у которых одинаковое значение атрибута proxyAddresses. Чтобы устранить ошибку ObjectTypeMismatch, выполните следующее:

1. Определите повторяющееся значение атрибута proxyAddresses (или другого атрибута), вызвавшее ошибку. Кроме того, определите два \(или несколько\) объектов, участвующих в конфликте. Эти объекты можно определить с помощью отчета, созданного [Azure AD Connect Health для синхронизации](https://aka.ms/aadchsyncerrors).
2. Определите объекты, для которых требуется повторяющееся значение.
3. Удалите из объекта ненужное повторяющееся значение. Обратите внимание, что эти изменения необходимо вносить в каталоге, из которого происходит объект. В некоторых случаях может потребоваться удалить один из объектов, участвующих в конфликте.
4. Если изменения внесены в локальном каталоге AD, выполните синхронизацию этих изменений с Azure AD Connect. Отчет об ошибках Azure AD Connect Health для синхронизации обновляется каждые 30 минут. В нем отображаются ошибки, возникшие при последних попытках синхронизации.

## <a name="duplicate-attributes"></a>Повторяющиеся атрибуты
### <a name="attributevaluemustbeunique"></a>AttributeValueMustBeUnique
#### <a name="description"></a>ОПИСАНИЕ
В схеме Azure Active Directory запрещено использовать для нескольких объектов одинаковые значения следующих атрибутов. Это значит, что у каждого объекта в Azure AD должно быть уникальное значение для этих атрибутов в заданном экземпляре.

* ProxyAddresses
* UserPrincipalName

Если Azure AD Connect пытается добавить новый объект или обновить указанные выше атрибуты имеющегося объекта, добавив для них значения, назначенные другому объекту в Azure Active Directory, то операция завершится ошибкой синхронизации AttributeValueMustBeUnique.

#### <a name="possible-scenarios"></a>Возможные сценарии
1. Повторяющееся значение назначено синхронизированному объекту, конфликтующему с другим синхронизированным объектом.

#### <a name="example-case"></a>Пример
1. Пользователь **Григорий Авдеев** синхронизирован в Azure AD из локального каталога AD в домене contoso.com.
2. Для атрибута **userPrincipalName** Григория в локальном каталоге задано значение **bobs@contoso.com**.
3. Для атрибута **proxyAddresses** Григория используются следующие значения.
   * smtp:bobs@contoso.com
   * smtp:bob.smith@contoso.com
   * **smtp:bob@contoso.com**
4. В локальный каталог AD добавлен новый пользователь **Артем Кузнецов**.
5. Для атрибута **userPrincipalName** Артема задано значение **bobt@contoso.com**.
6. Для атрибута **proxyAddresses** **Артема Кузнецова** используются следующие значения: smtp:bobt@contoso.com. smtp:bob.taylor@contoso.com
7. Синхронизация объекта Артема Кузнецова выполнена успешно.
8. Администратор решил заменить значение атрибута **proxyAddresses** Артема на следующее: **smtp:bob@contoso.com**
9. Azure AD выполнит попытку обновить объект Артема в Azure AD, добавив в него значение выше, но эта операция завершится ошибкой, потому что такое значение уже назначено Григорию Авдееву. В результате отобразится ошибка AttributeValueMustBeUnique.

#### <a name="how-to-fix-attributevaluemustbeunique-error"></a>Как устранить ошибку AttributeValueMustBeUnique
Чаще всего причина ошибки AttributeValueMustBeUnique заключается в наличии двух объектов с разными атрибутами sourceAnchor \(immutableId\), но одинаковым значением атрибутов proxyAddresses и/или userPrincipalName. Чтобы устранить ошибку AttributeValueMustBeUnique, выполните следующее:

1. Определите повторяющееся значение атрибута proxyAddresses, userPrincipalName или другого атрибута, вызвавшее ошибку. Кроме того, определите два \(или несколько\) объектов, участвующих в конфликте. Эти объекты можно определить с помощью отчета, созданного [Azure AD Connect Health для синхронизации](https://aka.ms/aadchsyncerrors).
2. Определите объекты, для которых требуется повторяющееся значение.
3. Удалите из объекта ненужное повторяющееся значение. Обратите внимание, что эти изменения необходимо вносить в каталоге, из которого происходит объект. В некоторых случаях может потребоваться удалить один из объектов, участвующих в конфликте.
4. Если изменения внесены в локальном каталоге AD, выполните их синхронизацию c Azure AD Connect. Это позволит устранить ошибку.

#### <a name="related-articles"></a>Связанные статьи
-[Duplicate or invalid attributes prevent directory synchronization in Office 365](https://support.microsoft.com/en-us/kb/2647098) (Запрет синхронизации службы каталогов в Office 365 из-за повторяющихся или недопустимых атрибутов)

## <a name="data-validation-failures"></a>Сбой проверки данных
### <a name="identitydatavalidationfailed"></a>IdentityDataValidationFailed
#### <a name="description"></a>ОПИСАНИЕ
Перед записью данных в каталог Azure AD применяет к ним разные ограничения. Это улучшает работу пользователей с приложениями, которые зависят от этих данных.

#### <a name="scenarios"></a>Сценарии
a. Значение атрибута userPrincipalName содержит недопустимые или неподдерживаемые символы.
Б. Атрибут userPrincipalName не соответствует требуемому формату.

#### <a name="how-to-fix-identitydatavalidationfailed-error"></a>Как устранить ошибку IdentityDataValidationFailed
a. Убедитесь, что для значения атрибута userPrincipalName указаны поддерживаемые символы и значение соответствует требуемому формату.

#### <a name="related-articles"></a>Связанные статьи
* [Подготовка пользователей к работе путем синхронизации каталогов с Office 365](https://support.office.com/en-us/article/Prepare-to-provision-users-through-directory-synchronization-to-Office-365-01920974-9e6f-4331-a370-13aea4e82b3e)

### <a name="federateddomainchangeerror"></a>Ошибка FederatedDomainChangeError
#### <a name="description"></a>ОПИСАНИЕ
Ошибка синхронизации **FederatedDomainChangeError** возникает в конкретном случае, когда суффикс атрибута userPrincipalName пользователя изменяется при переходе из одного федеративного домена в другой.

#### <a name="scenarios"></a>Сценарии
Суффикс атрибута userPrincipalName синхронизированного пользователя изменился при переходе из одного федеративного домена в другой локальный федеративный домен. Например, *userPrincipalName = bob@contoso.com* изменился на *userPrincipalName = bob@fabrikam.com*.

#### <a name="example"></a>Пример
1. Григорий Авдеев (учетная запись для contoso.com) добавлен в AD как новый пользователь. Для атрибута userPrincipalName учетной записи задано значение bob@contoso.com.
2. Григорий перешел в другой домен contoso.com, fabrikam.com. Значение атрибута userPrincipalName изменилось на bob@fabrikam.com.
3. Домены contoso.com и fabrikam.com — это федеративные домены Azure AD.
4. Атрибут userPrincipalName Григория не обновляется, поэтому возникла ошибка синхронизации FederatedDomainChangeError.

#### <a name="how-to-fix"></a>Как устранить
Если при переходе из домена bob@**contoso.com** в домен bob@**fabrikam.com** (домены **contoso.com** и **fabrikam.com** **федеративные**) суффикс атрибута userPrincipalName изменился, выполните приведенные ниже шаги, чтобы устранить ошибку синхронизации.

1. Измените UserPrincipalName пользователя в Azure AD с bob@contoso.com на bob@contoso.onmicrosoft.com. Вы можете использовать следующую команду PowerShell в модуле Azure AD PowerShell: `Set-MsolUserPrincipalName -UserPrincipalName bob@contoso.com -NewUserPrincipalName bob@contoso.onmicrosoft.com`
2. Разрешите выполнить следующий цикл синхронизации. В этот раз синхронизация пройдет успешно, а для атрибута userPrincipalName Григория будет задано значение bob@fabrikam.com (как и ожидалось).

#### <a name="related-articles"></a>Связанные статьи
* [Изменения не синхронизируются с помощью инструмента синхронизации Azure Active Directory после изменения имени участника-пользователя или учетной записи пользователя для использования другого федеративного домена](https://support.microsoft.com/en-us/help/2669550/changes-aren-t-synced-by-the-azure-active-directory-sync-tool-after-you-change-the-upn-of-a-user-account-to-use-a-different-federated-domain)

## <a name="largeobject"></a>LargeObject
### <a name="description"></a>ОПИСАНИЕ
Если атрибут превышает установленное в схеме Azure AD значение размера, длины и количества, то во время синхронизации возникнет ошибка **LargeObject** или **ExceededAllowedLength**. Как правило, эта ошибка возникает для следующих атрибутов:

* userCertificate
* userSMIMECertificate
* thumbnailPhoto;
* proxyAddresses

### <a name="possible-scenarios"></a>Возможные сценарии
1. Атрибут userCertificate хранит большое количество назначенных Григорию сертификатов. К ним также относятся недействительные и старые сертификаты. Жесткий предел — 15 сертификатов. Дополнительные сведения о том, как обрабатывать ошибки LargeObject с атрибутом userCertificate, см. в статье, посвященной [обработке ошибок LargeObject, вызванных атрибутом userCertificate](active-directory-aadconnectsync-largeobjecterror-usercertificate.md).
2. Атрибут userSMIMECertificat хранит большое количество назначенных Григорию сертификатов. К ним также относятся недействительные и старые сертификаты. Жесткий предел — 15 сертификатов.
3. Атрибут thmubnailPhoto, заданный в Active Directory, слишком большой для синхронизации в Azure AD.
4. При автоматическом заполнении в Active Directory объекту назначено слишком много атрибутов ProxyAddresses.

### <a name="how-to-fix"></a>Как устранить
1. Убедитесь, что атрибут, повлекший ошибку, не превысил установленное ограничение.

## <a name="related-links"></a>Связанные ссылки
* [Locate Active Directory Objects in Active Directory Administrative Center](https://technet.microsoft.com/library/dd560661.aspx) (Поиск объектов Active Directory в центре администрирования Active Directory)
* [How to query Azure Active Directory for an object using Azure Active Directory PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx) (Как запросить объект с помощью модуля Azure Active Directory для PowerShell)
