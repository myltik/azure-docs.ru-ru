---
title: "Управление службами федерации Active Directory и их настройка с помощью Azure AD Connect | Документация Майкрософт"
description: "Управление службами AD FS с помощью Azure AD Connect и настройка пользовательского входа в AD FS с помощью Azure AD Connect и PowerShell."
keywords: "AD FS, ADFS, управление AD FS, AAD Connect, Connect, вход, настройка AD FS, восстановление доверия, Office 365, федерация, проверяющая сторона"
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
ms.date: 10/31/2016
ms.author: anandy
translationtype: Human Translation
ms.sourcegitcommit: 7db56a4c0efb208591bb15aa03a4c0dbf833d22e
ms.openlocfilehash: 4ed22092621949d53da5b8c961a080270dde1db7


---
# <a name="active-directory-federation-services-management-and-customization-with-azure-ad-connect"></a>Управление службами федерации Active Directory и их настройка с помощью Azure AD Connect
В этой статье подробно описаны задачи, связанные со службами федерации Active Directory (AD FS), которые можно выполнить с помощью Microsoft Azure Active Directory Connect, а также другие стандартные задачи AD FS, выполнение которых может потребоваться для полной настройки фермы AD FS.

| Раздел | Что описывает |
|:--- |:--- |
| **Управление AD FS** | |
| [Восстановление доверия](#repairthetrust) |Восстановление доверия федерации Office 365 |
| [Добавление сервера AD FS](#addadfsserver) |Развертывание фермы AD FS с использованием дополнительного сервера AD FS |
| [Добавление прокси-сервера веб-приложения AD FS](#addwapserver) |Развертывание фермы AD FS с использованием дополнительного WAP-сервера |
| [Добавление федеративного домена](#addfeddomain) |Добавление федеративного домена |
| **Пользовательская настройка AD FS** | |
| [Добавление настраиваемого логотипа компании или иллюстрации](#customlogo) |Настройка логотипа компании и иллюстрации для страницы входа AD FS |
| [Добавление описания входа в систему](#addsignindescription) |Добавление описания страницы входа в систему |
| [Изменение правил утверждений служб федерации Active Directory](#modclaims) |Изменение утверждений AD FS для различных сценариев федерации |

## <a name="ad-fs-management"></a>Управление AD FS
В Azure AD Connect можно выполнять разные связанные с AD FS задачи с помощью мастера Azure AD Connect при минимальном участии пользователя. Завершив установку Azure AD Connect с помощью мастера, вы можете снова запустить этот мастер, чтобы выполнить дополнительные задачи.

### <a name="repair-the-trust-a-namerepairthetrusta"></a>Восстановление доверия <a name=repairthetrust></a>
Azure AD Connect может проверить текущую работоспособность AD FS и Azure Active Directory и предпринять соответствующие действия для восстановления доверия. Выполните приведенные ниже действия для восстановления доверия Azure AD и AD FS.

1. Выберите в списке дополнительных задач пункт **Восстановление доверия AAD и ADFS** .
   ![Восстановление доверия AAD и ADFS](media/active-directory-aadconnect-federation-management/RepairADTrust1.PNG)
2. На странице **Подключение к Azure AD** укажите учетные данные глобального администратора для Azure AD и нажмите кнопку **Далее**.
   ![Подключение к Azure AD](media/active-directory-aadconnect-federation-management/RepairADTrust2.PNG)
3. На странице **Учетные данные для удаленного доступа** введите учетные данные администратора домена.
   ![Учетные данные для удаленного доступа](media/active-directory-aadconnect-federation-management/RepairADTrust3.PNG)

    Когда вы нажмете кнопку **Далее**, Azure AD Connect проверит работоспособность сертификата и отобразит возможные проблемы.

    ![Состояние сертификатов](media/active-directory-aadconnect-federation-management/RepairADTrust4.PNG)

    На странице **Готово к настройке** отобразится список действий, которые будут выполнены для восстановления доверия.

    ![Готово к настройке](media/active-directory-aadconnect-federation-management/RepairADTrust5.PNG)
4. Чтобы восстановить доверие, нажмите кнопку **Установить** .

> [!NOTE]
> Azure AD Connect может выполнить восстановление или другие действия только для самозаверяющих сертификатов. С помощью Azure AD Connect нельзя восстановить сертификаты третьих сторон.
>
>

### <a name="add-an-ad-fs-server-a-nameaddadfsservera"></a>Добавление сервера AD FS <a name=addadfsserver></a>
> [!NOTE]
> Для добавления сервера AD FS Azure AD Connect требует PFX-файл сертификата. Таким образом, эту операцию можно выполнить, только если ферма AD FS настроена с помощью Azure AD Connect.
>
>

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
   ![AD FS servers](media/active-directory-aadconnect-federation-management/AddNewADFSServer6.PNG)
6. Нажмите кнопку **Далее** и выполните инструкции на последней странице **Настройка**. После того как Azure AD Connect завершит добавление серверов в ферму AD FS, у вас будет возможность проверить подключение.
   ![Готово к настройке](media/active-directory-aadconnect-federation-management/AddNewADFSServer7.PNG)

    ![Установка завершена](media/active-directory-aadconnect-federation-management/AddNewADFSServer8.PNG)

### <a name="add-an-ad-fs-web-application-proxy-server-a-nameaddwapservera"></a>Добавление прокси-сервера веб-приложения AD FS <a name=addwapserver></a>
> [!NOTE]
> Чтобы добавить прокси-сервер веб-приложения для Azure AD Connect, требуется PFX-файл сертификата. Таким образом, эту операцию можно выполнить, только если ферма AD FS настроена с помощью Azure AD Connect.
>
>

1. Выберите пункт **Развертывание прокси-службы веб-приложения** в списке доступных задач.
   ![Deploy web application proxy](media/active-directory-aadconnect-federation-management/WapServer1.PNG)
2. Введите учетные данные глобального администратора Azure.
   ![Подключение к Azure AD](media/active-directory-aadconnect-federation-management/wapserver2.PNG)
3. На странице **Укажите SSL-сертификат** введите пароль для PFX-файла, который использовался при настройке фермы AD FS с помощью Azure AD Connect.
   ![Пароль сертификата](media/active-directory-aadconnect-federation-management/WapServer3.PNG)

    ![Укажите SSL-сертификат](media/active-directory-aadconnect-federation-management/WapServer4.PNG)
4. Добавьте сервер в качестве прокси-сервера веб-приложения. Так как прокси-сервер веб-приложения может быть не присоединен к домену, мастер запросит учетные данные администратора для добавляемого сервера.
   ![Учетные данные администратора сервера](media/active-directory-aadconnect-federation-management/WapServer5.PNG)
5. На странице **Учетные данные доверия прокси-сервера** введите учетные данные администратора, чтобы настроить доверие прокси-сервера и доступ к основному серверу в ферме AD FS.
   ![Учетные данные доверия прокси-сервера](media/active-directory-aadconnect-federation-management/WapServer6.PNG)
6. В мастере на странице **Готово к настройке** отображается список действий, которые будут выполнены.
   ![Готово к настройке](media/active-directory-aadconnect-federation-management/WapServer7.PNG)
7. Чтобы завершить настройку, нажмите кнопку **Установить** . По завершении настройки мастер предоставляет возможность проверить подключение к серверам. Чтобы проверить подключение, щелкните **Проверить** .
   ![Установка завершена](media/active-directory-aadconnect-federation-management/WapServer8.PNG)

### <a name="add-a-federated-domain-a-nameaddfeddomaina"></a>Добавление федеративного домена <a name=addfeddomain></a>
Вы можете быстро добавить домен в федерацию с Azure AD с помощью Azure AD Connect. Azure AD Connect добавляет домен для федерации, а также изменяет правила утверждения, чтобы правильно отражать издателя при наличии нескольких доменов в федерации с Azure AD.

1. Чтобы добавить федеративный домен, выберите задачу **Добавить дополнительный домен Azure AD**.
   ![Дополнительный домен Azure AD](media/active-directory-aadconnect-federation-management/AdditionalDomain1.PNG)
2. На следующей странице мастера введите учетные данные глобального администратора для Azure AD.
   ![Подключение к Azure AD](media/active-directory-aadconnect-federation-management/AdditionalDomain2.PNG)
3. На странице **Учетные данные для удаленного доступа** введите учетные данные администратора домена.
   ![Учетные данные для удаленного доступа](media/active-directory-aadconnect-federation-management/additionaldomain3.PNG)
4. На следующей странице мастера будет предоставлен список доменов Azure AD, которые можно использовать для федерации вашего локального каталога. Выберите домен из списка.
   ![Домен Azure AD](media/active-directory-aadconnect-federation-management/AdditionalDomain4.PNG)

    Когда вы выберете домен, мастер предоставит соответствующие сведения о дальнейших действиях, которые будут выполнены мастером, а также о результатах настроек. В некоторых случаях при выборе домена, еще не проверенного в Azure AD, мастер предоставит сведения, которые помогут проверить домен. Дополнительные сведения см. в разделе [Добавление имени личного домена в Azure Active Directory](../active-directory-add-domain.md).
5. Нажмите кнопку **Далее**, после чего на странице **Готово к настройке** отобразится список действий, которые выполнит Azure AD Connect. Чтобы завершить настройку, нажмите кнопку **Установить** .
   ![Готово к настройке](media/active-directory-aadconnect-federation-management/AdditionalDomain5.PNG)

## <a name="ad-fs-customization"></a>Пользовательская настройка AD FS
Ниже приведены сведения о некоторых стандартных задачах, связанных с пользовательской настройкой страницы входа AD FS.

### <a name="add-a-custom-company-logo-or-illustration-a-namecustomlogoa"></a>Добавление настраиваемого логотипа компании или иллюстрации <a name=customlogo></a>
Чтобы изменить логотип компании, который отображается на странице **входа** , используйте следующий командлет Windows PowerShell и синтаксис.

> [!NOTE]
> Рекомендуемые размеры логотипа — 260 x 35 @ 96 точек на дюйм. Размер файла не должен превышать 10 КБ.
>
>

    Set-AdfsWebTheme -TargetName default -Logo @{path="c:\Contoso\logo.PNG"}

> [!NOTE]
> Параметр *TargetName* является обязательным. Стандартная тема для AD FS называется темой по умолчанию.
>
>

### <a name="add-a-sign-in-description-a-nameaddsignindescriptiona"></a>Добавление описания входа в систему <a name=addsignindescription></a>
Чтобы добавить на **страницу входа**описание страницы входа, используйте следующий командлет Windows PowerShell и синтаксис.

    Set-AdfsGlobalWebContent -SignInPageDescriptionText "<p>Sign-in to Contoso requires device registration. Click <A href='http://fs1.contoso.com/deviceregistration/'>here</A> for more information.</p>"

### <a name="modify-ad-fs-claim-rules-a-namemodclaimsa"></a>Изменение правил утверждений служб федерации Active Directory <a name=modclaims></a>
Службы AD FS поддерживают обширный язык утверждений, с помощью которого можно создавать настраиваемые правила утверждений. Дополнительные сведения см. в разделе [Роль языка правил утверждений](https://technet.microsoft.com/library/dd807118.aspx).

Далее рассматривается, как можно создавать пользовательские правила для некоторых сценариев, связанных с использованием Azure AD и федерации AD FS.

#### <a name="immutable-id-conditional-on-a-value-being-present-in-the-attribute"></a>Неизменяемый идентификатор, зависящий от наличия значения в атрибуте
Azure AD Connect позволяет указывать атрибут, используемый в качестве привязки к источнику, когда объекты синхронизируются с Azure AD. Если значение в настраиваемом атрибуте не пустое, можно выдать утверждение неизменяемого идентификатора. Например, можно выбрать в качестве привязки к источнику атрибут **ms-ds-consistencyguid** и настроить выдачу **ImmutableID** как **ms-ds-consistencyguid** в случае наличия у атрибута значения. Если у атрибута нет значения, в качестве неизменяемого идентификатора следует выдать **objectGuid** .  Можно создать набор пользовательских правил утверждения, как описано ниже.

**Правило 1. Атрибуты запросов**

    c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname"]
    => add(store = "Active Directory", types = ("http://contoso.com/ws/2016/02/identity/claims/objectguid", "http://contoso.com/ws/2016/02/identity/claims/msdsconcistencyguid"), query = "; objectGuid,ms-ds-consistencyguid;{0}", param = c.Value);

В этом правиле запрашиваются значения **ms-ds-consistencyguid** и **objectGuid** для пользователя из Active Directory. Измените имя хранилища на имя соответствующего хранилища в развертывании AD FS. Измените тип утверждений на определенный в федерации для **objectGuid** и **ms-ds-consistencyguid**.

Кроме того, используя параметр **add** вместо **issue**, можно предотвратить добавление исходящей выдачи для сущности и использовать значения в качестве промежуточных. Утверждение будет выдано в последующем правиле после того, как будет установлено, какое значение следует использовать в качестве неизменяемого идентификатора.

**Правило 2. Проверка наличия ms-ds-consistencyguid для пользователя**

    NOT EXISTS([Type == "http://contoso.com/ws/2016/02/identity/claims/msdsconcistencyguid"])
    => add(Type = "urn:anandmsft:tmp/idflag", Value = "useguid");

Это правило определяет временный флаг с именем **idflag**, который получает значение **useguid**, если **ms-ds-concistencyguid** для пользователя не заполнен. Логика такого решения в том, что службы AD FS запрещают пустые утверждения. Поэтому при добавлении утверждений http://contoso.com/ws/2016/02/identity/claims/objectguid и http://contoso.com/ws/2016/02/identity/claims/msdsconcistencyguid в правиле 1 результатом будет утверждение **msdsconsistencyguid** только в том случае, если значение для пользователя заполнено. Если оно не заполнено, службы AD FS определяют, что значение окажется пустым, и немедленно его опускают. **objectGuid**имеют все объекты, поэтому такое утверждение всегда будет присутствовать после выполнения правила 1.

**Правило 3. Выдача ms-ds-consistencyguid в качестве неизменяемого идентификатора, если он присутствует**

    c:[Type == "http://contoso.com/ws/2016/02/identity/claims/msdsconcistencyguid"]
    => issue(Type = "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier", Value = c.Value);

Это неявная проверка **Exist** . Если значение для утверждения существует, выполняется его выдача в качестве неизменяемого идентификатора. В предыдущем примере используется утверждение **nameidentifier** . Его потребуется заменить соответствующим типом утверждения для неизменяемого идентификатора в конкретной среде.

**Правило 4. Выдача objectGuid в качестве неизменяемого идентификатора при отсутствии ms-ds-consistencyGuid**

    c1:[Type == "urn:anandmsft:tmp/idflag", Value =~ "useguid"]
    && c2:[Type == "http://contoso.com/ws/2016/02/identity/claims/objectguid"]
    => issue(Type = "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier", Value = c2.Value);

В этом правиле просто выполняется проверка временного флага **idflag**. На основе его значения определяется, следует ли выдавать утверждение.

> [!NOTE]
> Последовательность этих правил важна.
>
>

#### <a name="sso-with-a-subdomain-upn"></a>Единый вход с помощью UPN поддомена
Можно включить несколько доменов в федерацию с помощью Azure AD Connect, как описано в разделе [Добавление нового федеративного домена](active-directory-aadconnect-federation-management.md#addfeddomain). Следует изменить утверждение UPN так, чтобы идентификатор издателя соответствовал корневому домену, а не поддомену, так как федеративный корневой домен включает и дочерние домены.

По умолчанию для идентификатора издателя установлено следующее правило утверждения:

    c:[Type
    == “http://schemas.xmlsoap.org/claims/UPN“]

    => issue(Type = “http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid“, Value = regexreplace(c.Value, “.+@(?<domain>.+)“, “http://${domain}/adfs/services/trust/“));

![Утверждение идентификатора издателя по умолчанию](media/active-directory-aadconnect-federation-management/issuer_id_default.png)

Правило по умолчанию принимает суффикс UPN и использует его в утверждении идентификатора издателя. Предположим, Григорий является пользователем в домене sub.contoso.com, а contoso.com включен в федерацию с Azure AD. При входе в Azure AD Григорий вводит имя пользователя john@sub.contoso.com , и правило по умолчанию для утверждения идентификатора издателя в AD FS обрабатывает его следующим образом:

    c:[Type
    == “http://schemas.xmlsoap.org/claims/UPN“]

    => issue(Type = “http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid“, Value = regexreplace(john@sub.contoso.com, “.+@(?<domain>.+)“, “http://${domain}/adfs/services/trust/“));

**Значение утверждения:** http://sub.contoso.com/adfs/services/trust/.

Чтобы включить в значение утверждения издателя только корневой домен, измените правило утверждения так:

    c:[Type == “http://schemas.xmlsoap.org/claims/UPN“]

    => issue(Type = “http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid“, Value = regexreplace(c.Value, “^((.*)([.|@]))?(?<domain>[^.]*[.].*)$”, “http://${domain}/adfs/services/trust/“));

## <a name="next-steps"></a>Дальнейшие действия
См. [дополнительные сведения о параметрах входа пользователя](active-directory-aadconnect-user-signin.md).



<!--HONumber=Jan17_HO1-->


