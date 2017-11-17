---
title: "Управление службами федерации Active Directory и их настройка с помощью Azure AD Connect | Документация Майкрософт"
description: "Управление службами AD FS с помощью Azure AD Connect и настройка пользовательского входа в AD FS с помощью Azure AD Connect и PowerShell."
keywords: "AD FS, ADFS, управление AD FS, AAD Connect, Connect, вход, настройка AD FS, восстановление доверия, Office 365, федерация, проверяющая сторона"
services: active-directory
documentationcenter: 
author: anandyadavmsft
manager: femila
editor: 
ms.assetid: 2593b6c6-dc3f-46ef-8e02-a8e2dc4e9fb9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: billmath
ms.openlocfilehash: c9ff22c7b4793112da5211162ed9cd58ac86d238
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/14/2017
---
# <a name="manage-and-customize-active-directory-federation-services-by-using-azure-ad-connect"></a>Управление службами федерации Active Directory и их настройка с помощью Azure AD Connect
В этой статье описывается управление службами федерации Active Directory (AD FS) и их настройка с помощью Azure Active Directory (Azure AD) Connect, а также рассматриваются другие стандартные задачи AD FS, которые может потребоваться выполнить для полной настройки фермы AD FS.

| Раздел | Что описывает |
|:--- |:--- |
| **Управление AD FS** | |
| [Восстановление доверия](#repairthetrust) |Восстановление доверия федерации с Office 365. |
| [Федерация с Azure AD с помощью альтернативного имени пользователя](#alternateid) | Настройка федерации с использованием альтернативного имени пользователя.  |
| [Добавление сервера AD FS](#addadfsserver) |Расширение фермы AD FS с использованием дополнительного сервера AD FS. |
| [Добавление прокси-сервера веб-приложения AD FS](#addwapserver) |Добавление к ферме AD FS дополнительного сервера прокси-службы веб-приложения (WAP). |
| [Добавление федеративного домена](#addfeddomain) |Добавление федеративного домена. |
| [Обновление SSL-сертификата](active-directory-aadconnectfed-ssl-update.md)| Обновление SSL-сертификата для фермы AD FS. |
| **Настройка AD FS** | |
| [Добавление настраиваемого логотипа компании или иллюстрации](#customlogo) |Настройка логотипа компании и иллюстрации для страницы входа AD FS. |
| [Добавление описания входа в систему](#addsignindescription) |Добавление описания страницы входа в систему. |
| [Изменение правил утверждений служб федерации Active Directory](#modclaims) |Изменение утверждений AD FS для различных сценариев федерации. |

## <a name="manage-ad-fs"></a>Управление AD FS
В Azure AD Connect можно выполнять разные связанные с AD FS задачи с помощью мастера Azure AD Connect с минимальным участием пользователя. Завершив установку Azure AD Connect с помощью мастера, вы можете снова запустить этот мастер, чтобы выполнить дополнительные задачи.

## <a name="repairthetrust"></a>Восстановление доверия 
Azure AD Connect может проверить текущую работоспособность AD FS и доверие Azure AD и предпринять соответствующие действия для восстановления доверия. Выполните приведенные ниже действия для восстановления доверия Azure AD и AD FS.

1. Выберите в списке дополнительных задач пункт **Восстановление доверия AAD и ADFS** .
   ![Восстановление доверия AAD и ADFS](media/active-directory-aadconnect-federation-management/RepairADTrust1.PNG)

2. На странице **Подключение к Azure AD** укажите учетные данные глобального администратора для Azure AD и нажмите кнопку **Далее**.
   ![Подключение к Azure AD](media/active-directory-aadconnect-federation-management/RepairADTrust2.PNG)

3. На странице **Учетные данные для удаленного доступа** введите учетные данные администратора домена.

   ![Учетные данные удаленного доступа](media/active-directory-aadconnect-federation-management/RepairADTrust3.PNG)

    Когда вы нажмете кнопку **Далее**, Azure AD Connect проверит работоспособность сертификата и отобразит возможные проблемы.

    ![Состояние сертификатов](media/active-directory-aadconnect-federation-management/RepairADTrust4.PNG)

    На странице **Готово к настройке** отобразится список действий, которые будут выполнены для восстановления доверия.

    ![Готово к настройке](media/active-directory-aadconnect-federation-management/RepairADTrust5.PNG)

4. Чтобы восстановить доверие, нажмите кнопку **Установить** .

> [!NOTE]
> Azure AD Connect может выполнить восстановление или другие действия только для самозаверяющих сертификатов. С помощью Azure AD Connect нельзя восстановить сертификаты третьих сторон.

## <a name="alternateid"></a>Федерация с Azure AD с помощью альтернативного имени пользователя 
Рекомендуется, чтобы локальное имя участника-пользователя (UPN) и имя участника-пользователя в облаке были одинаковыми. Если в локальном имени участника-пользователя используется не поддерживающий маршрутизацию домен (например, Contoso.local), или если его невозможно изменить из-за зависимостей локального приложения, то рекомендуется настроить альтернативное имя пользователя. Альтернативное имя пользователя позволяет настроить процедуру входа таким образом, что пользователи смогут использовать для входа атрибут, отличный от имени участника-пользователя, например адрес электронной почты. По умолчанию для имени участника-пользователя в Azure AD Connect используется атрибут userPrincipalName в Active Directory. Если выбрать для имени участника-пользователя любой другой атрибут и выполнить федерацию с помощью AD FS, то Azure AD Connect настроит AD FS на использование альтернативного имени пользователя. В приведенном ниже примере показано, как выбрать другой атрибут для имени участника-пользователя.

![Выбор альтернативного атрибута имени пользователя](media/active-directory-aadconnect-federation-management/attributeselection.png)

Настройка альтернативного имени пользователя для AD FS состоит из двух основных этапов:
1. **Настройка правильного набора утверждений выдачи**. Изменяются правила утверждений выдачи в отношениях доверия с проверяющей стороной Azure AD, чтобы использовать выбранный атрибут UserPrincipalName в качестве альтернативного имени пользователя.
2. **Включение альтернативного имени пользователя в конфигурации AD FS**. Обновляется конфигурация AD FS, чтобы служба AD FS могла выполнять поиск пользователей в соответствующих лесах, используя альтернативное имя пользователя. Эта конфигурация поддерживается для службы AD FS, работающей под управлением Windows Server 2012 R2 (с обновлением KB2919355) и более поздних версий. Если серверы AD FS работают под управлением версии 2012 R2, то Azure AD Connect проверяет наличие необходимого обновления. Если обновление не удается обнаружить, то после выполнения настройки отображается предупреждение, как показано ниже.

    ![Предупреждение об отсутствии обновления в версии 2012 R2](media/active-directory-aadconnect-federation-management/kbwarning.png)

    При обнаружении такой проблемы необходимо исправить конфигурацию, установив обязательное обновление [KB2919355](http://go.microsoft.com/fwlink/?LinkID=396590) и восстановив доверие с помощью действия [Восстановление доверия AAD и ADFS](#repairthetrust).

> [!NOTE]
> Дополнительные сведения об альтернативном имени пользователя и о его настройке вручную см. в статье [Configuring Alternate Login ID](https://technet.microsoft.com/windows-server-docs/identity/ad-fs/operations/configuring-alternate-login-id) (Настройка альтернативного имени пользователя).

## <a name="addadfsserver"></a>Добавление сервера AD FS 

> [!NOTE]
> Для добавления сервера AD FS Azure AD Connect требует PFX-файл сертификата. Таким образом, эту операцию можно выполнить, только если ферма AD FS настроена с помощью Azure AD Connect.

1. Выберите пункт **Развернуть дополнительный сервер федерации** и нажмите кнопку **Далее**.

   ![Дополнительный сервер федерации](media/active-directory-aadconnect-federation-management/AddNewADFSServer1.PNG)

2. На странице **Подключение к Azure AD** укажите учетные данные глобального администратора для Azure AD и нажмите кнопку **Далее**.

   ![Подключение к Azure AD](media/active-directory-aadconnect-federation-management/AddNewADFSServer2.PNG)

3. Введите учетные данные администратора домена.

   ![Учетные данные администратора домена](media/active-directory-aadconnect-federation-management/AddNewADFSServer3.PNG)

4. Azure AD Connect запросит пароль PFX-файла, который был указан при настройке новой фермы AD FS с помощью Azure AD Connect. Щелкните **Введите пароль** , чтобы указать пароль для PFX-файла.

   ![Пароль сертификата](media/active-directory-aadconnect-federation-management/AddNewADFSServer4.PNG)

    ![Укажите SSL-сертификат](media/active-directory-aadconnect-federation-management/AddNewADFSServer5.PNG)

5. На странице **Серверы AD FS** введите имя сервера или IP-адрес, который нужно добавить к ферме AD FS.

   ![Серверы AD FS](media/active-directory-aadconnect-federation-management/AddNewADFSServer6.PNG)

6. Нажмите кнопку **Далее** и выполните инструкции на последней странице **Настройка**. После того как Azure AD Connect завершит добавление серверов в ферму AD FS, у вас будет возможность проверить подключение.

   ![Готово к настройке](media/active-directory-aadconnect-federation-management/AddNewADFSServer7.PNG)

    ![Установка завершена](media/active-directory-aadconnect-federation-management/AddNewADFSServer8.PNG)

## <a name="addwapserver"></a>Добавление WAP-сервера AD FS 

> [!NOTE]
> Для добавления WAP-сервера Azure AD Connect требует PFX-файл сертификата. Таким образом, эту операцию можно выполнить, только если ферма AD FS настроена с помощью Azure AD Connect.

1. Выберите пункт **Развертывание прокси-службы веб-приложения** в списке доступных задач.

   ![Развертывание прокси-службы веб-приложения](media/active-directory-aadconnect-federation-management/WapServer1.PNG)

2. Введите учетные данные глобального администратора Azure.

   ![Подключение к Azure AD](media/active-directory-aadconnect-federation-management/wapserver2.PNG)

3. На странице **Укажите SSL-сертификат** введите пароль для PFX-файла, который использовался при настройке фермы AD FS с помощью Azure AD Connect.
   ![Пароль сертификата](media/active-directory-aadconnect-federation-management/WapServer3.PNG)

    ![Укажите SSL-сертификат](media/active-directory-aadconnect-federation-management/WapServer4.PNG)

4. Добавьте сервер в качестве WAP-сервера. Так как WAP-сервер может быть не присоединен к домену, мастер запросит учетные данные администратора для добавляемого сервера.

   ![Учетные данные администратора сервера](media/active-directory-aadconnect-federation-management/WapServer5.PNG)

5. На странице **Учетные данные доверия прокси-сервера** введите учетные данные администратора, чтобы настроить доверие прокси-сервера и доступ к основному серверу в ферме AD FS.

   ![Учетные данные доверия прокси-сервера](media/active-directory-aadconnect-federation-management/WapServer6.PNG)

6. В мастере на странице **Готово к настройке** отображается список действий, которые будут выполнены.

   ![Готово к настройке](media/active-directory-aadconnect-federation-management/WapServer7.PNG)

7. Чтобы завершить настройку, нажмите кнопку **Установить** . По завершении настройки мастер предоставляет возможность проверить подключение к серверам. Чтобы проверить подключение, щелкните **Проверить** .

   ![Установка завершена](media/active-directory-aadconnect-federation-management/WapServer8.PNG)

## <a name="addfeddomain"></a>Добавление федеративного домена 

Вы можете быстро добавить домен в федерацию с Azure AD с помощью Azure AD Connect. Azure AD Connect добавляет домен для федерации, а также изменяет правила утверждения, чтобы правильно отражать издателя при наличии нескольких доменов в федерации с Azure AD.

1. Чтобы добавить федеративный домен, выберите задачу **Добавить дополнительный домен Azure AD**.

   ![Дополнительный домен Azure AD](media/active-directory-aadconnect-federation-management/AdditionalDomain1.PNG)

2. На следующей странице мастера введите учетные данные глобального администратора для Azure AD.

   ![Подключение к Azure AD](media/active-directory-aadconnect-federation-management/AdditionalDomain2.PNG)

3. На странице **Учетные данные для удаленного доступа** введите учетные данные администратора домена.

   ![Учетные данные удаленного доступа](media/active-directory-aadconnect-federation-management/additionaldomain3.PNG)

4. На следующей странице мастера будет предоставлен список доменов Azure AD, которые можно использовать для федерации вашего локального каталога. Выберите домен из списка.

   ![Домен Azure AD](media/active-directory-aadconnect-federation-management/AdditionalDomain4.PNG)

    Когда вы выберете домен, мастер предоставит соответствующие сведения о дальнейших действиях, которые будут выполнены мастером, а также о результатах настроек. В некоторых случаях при выборе домена, еще не проверенного в Azure AD, мастер предоставит сведения, которые помогут проверить домен. Дополнительные сведения см. в разделе [Добавление имени личного домена в Azure Active Directory](../active-directory-domains-add-azure-portal.md).

5. Щелкните **Далее**. На странице **Готово к настройке** отображается список действий, которые выполнит Azure AD Connect. Чтобы завершить настройку, нажмите кнопку **Установить** .

   ![Теперь все готово для настройки.](media/active-directory-aadconnect-federation-management/AdditionalDomain5.PNG)

> [!NOTE]
> Пользователи из добавленного федеративного домена должна быть синхронизированы, прежде чем они смогут войти в Azure AD.

## <a name="ad-fs-customization"></a>Пользовательская настройка AD FS
Ниже приведены сведения о некоторых стандартных задачах, связанных с пользовательской настройкой страницы входа AD FS.

## <a name="customlogo"></a>Добавление настраиваемого логотипа компании или иллюстрации 
Чтобы изменить логотип компании, который отображается на странице **входа**, используйте приведенный ниже командлет Windows PowerShell и синтаксис.

> [!NOTE]
> Рекомендуемые размеры логотипа — 260 x 35, 96 точек на дюйм. Размер файла не должен превышать 10 КБ.

    Set-AdfsWebTheme -TargetName default -Logo @{path="c:\Contoso\logo.PNG"}

> [!NOTE]
> Параметр *TargetName* является обязательным. Стандартная тема для AD FS называется темой по умолчанию.

## <a name="addsignindescription"></a>Добавление описания входа в систему 
Чтобы добавить на **страницу входа**описание страницы входа, используйте следующий командлет Windows PowerShell и синтаксис.

    Set-AdfsGlobalWebContent -SignInPageDescriptionText "<p>Sign-in to Contoso requires device registration. Click <A href='http://fs1.contoso.com/deviceregistration/'>here</A> for more information.</p>"

## <a name="modclaims"></a>Изменение правил утверждений служб федерации Active Directory 
Службы AD FS поддерживают обширный язык утверждений, с помощью которого можно создавать настраиваемые правила утверждений. Дополнительные сведения см. в разделе [Роль языка правил утверждений](https://technet.microsoft.com/library/dd807118.aspx).

Далее рассматривается, как можно создавать пользовательские правила для некоторых сценариев, связанных с использованием Azure AD и федерации AD FS.

### <a name="immutable-id-conditional-on-a-value-being-present-in-the-attribute"></a>Неизменяемый идентификатор, зависящий от наличия значения в атрибуте
Azure AD Connect позволяет указывать атрибут, используемый в качестве привязки к источнику, когда объекты синхронизируются с Azure AD. Если значение в настраиваемом атрибуте не пустое, можно выдать утверждение неизменяемого идентификатора.

Например, можно выбрать в качестве привязки к источнику атрибут **ms-ds-consistencyguid** и настроить выдачу **ImmutableID** как **ms-ds-consistencyguid** в случае наличия у атрибута значения. Если у атрибута нет значения, в качестве неизменяемого идентификатора следует выдать **objectGuid**. Можно создать набор пользовательских правил утверждения, как описано ниже.

**Правило 1. Атрибуты запросов**

    c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname"]
    => add(store = "Active Directory", types = ("http://contoso.com/ws/2016/02/identity/claims/objectguid", "http://contoso.com/ws/2016/02/identity/claims/msdsconsistencyguid"), query = "; objectGuid,ms-ds-consistencyguid;{0}", param = c.Value);

В этом правиле запрашиваются значения **ms-ds-consistencyguid** и **objectGuid** для пользователя из Active Directory. Измените имя хранилища на имя соответствующего хранилища в развертывании AD FS. Измените тип утверждений на определенный в федерации для **objectGuid** и **ms-ds-consistencyguid**.

Кроме того, используя параметр **add** вместо **issue**, можно предотвратить добавление исходящей выдачи для сущности и использовать значения в качестве промежуточных. Утверждение будет выдано в последующем правиле после того, как будет установлено, какое значение следует использовать в качестве неизменяемого идентификатора.

**Правило 2. Проверка наличия ms-ds-consistencyguid для пользователя**

    NOT EXISTS([Type == "http://contoso.com/ws/2016/02/identity/claims/msdsconsistencyguid"])
    => add(Type = "urn:anandmsft:tmp/idflag", Value = "useguid");

Это правило определяет временный флаг с именем **idflag**, который получает значение **useguid**, если **ms-ds-concistencyguid** для пользователя не заполнен. Логика такого решения в том, что службы AD FS запрещают пустые утверждения. Поэтому при добавлении утверждений http://contoso.com/ws/2016/02/identity/claims/objectguid и http://contoso.com/ws/2016/02/identity/claims/msdsconcistencyguid в правиле 1 результатом будет утверждение **msdsconsistencyguid** только в том случае, если значение для пользователя заполнено. Если оно не заполнено, службы AD FS определяют, что значение окажется пустым, и немедленно его опускают. **objectGuid**имеют все объекты, поэтому такое утверждение всегда будет присутствовать после выполнения правила 1.

**Правило 3. Выдача ms-ds-consistencyguid в качестве неизменяемого идентификатора, если он присутствует**

    c:[Type == "http://contoso.com/ws/2016/02/identity/claims/msdsconsistencyguid"]
    => issue(Type = "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier", Value = c.Value);

Это неявная проверка **Exist** . Если значение для утверждения существует, выполняется его выдача в качестве неизменяемого идентификатора. В предыдущем примере используется утверждение **nameidentifier** . Его потребуется заменить соответствующим типом утверждения для неизменяемого идентификатора в конкретной среде.

**Правило 4. Выдача objectGuid в качестве неизменяемого идентификатора при отсутствии ms-ds-consistencyGuid**

    c1:[Type == "urn:anandmsft:tmp/idflag", Value =~ "useguid"]
    && c2:[Type == "http://contoso.com/ws/2016/02/identity/claims/objectguid"]
    => issue(Type = "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier", Value = c2.Value);

В этом правиле просто выполняется проверка временного флага **idflag**. На основе его значения определяется, следует ли выдавать утверждение.

> [!NOTE]
> Последовательность этих правил важна.

### <a name="sso-with-a-subdomain-upn"></a>Единый вход с помощью UPN поддомена
Можно включить несколько доменов в федерацию с помощью Azure AD Connect, как описано в разделе [Добавление нового федеративного домена](active-directory-aadconnect-federation-management.md#addfeddomain). Следует изменить утверждение имени участника-пользователя (UPN) так, чтобы идентификатор издателя соответствовал корневому домену, а не поддомену, так как федеративный корневой домен включает и дочерние домены.

По умолчанию для идентификатора издателя установлено следующее правило утверждения:

    c:[Type
    == “http://schemas.xmlsoap.org/claims/UPN“]

    => issue(Type = “http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid“, Value = regexreplace(c.Value, “.+@(?<domain>.+)“, “http://${domain}/adfs/services/trust/“));

![Утверждение идентификатора издателя по умолчанию](media/active-directory-aadconnect-federation-management/issuer_id_default.png)

Правило по умолчанию принимает суффикс UPN и использует его в утверждении идентификатора издателя. Предположим, Григорий является пользователем в домене sub.contoso.com, а contoso.com включен в федерацию с Azure AD. При входе в Azure AD Григорий вводит имя пользователя john@sub.contoso.com, и правило по умолчанию для утверждения идентификатора издателя в AD FS обрабатывает его следующим образом:

    c:[Type
    == “http://schemas.xmlsoap.org/claims/UPN“]

    => issue(Type = “http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid“, Value = regexreplace(john@sub.contoso.com, “.+@(?<domain>.+)“, “http://${domain}/adfs/services/trust/“));

**Значение утверждения:** http://sub.contoso.com/adfs/services/trust/.

Чтобы включить в значение утверждения издателя только корневой домен, измените правило утверждения так:

    c:[Type == “http://schemas.xmlsoap.org/claims/UPN“]

    => issue(Type = “http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid“, Value = regexreplace(c.Value, “^((.*)([.|@]))?(?<domain>[^.]*[.].*)$”, “http://${domain}/adfs/services/trust/“));

## <a name="next-steps"></a>Дальнейшие действия
См. [дополнительные сведения о параметрах входа пользователя](active-directory-aadconnect-user-signin.md).
