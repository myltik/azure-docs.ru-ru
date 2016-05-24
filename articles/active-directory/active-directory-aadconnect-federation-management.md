<properties
	pageTitle="Управление службами федерации Active Directory и их настройка с помощью Azure AD Connect | Microsoft Azure"
	description="Управление службами AD FS с помощью Azure AD Connect и настройка пользовательского входа в AD FS с помощью Azure AD Connect и Powershell."
	services="active-directory"
	documentationCenter=""
	authors="anandyadavmsft"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/04/2016"
	ms.author="anandy"/>

# Управление службами федерации Active Directory и их настройка с помощью Azure AD Connect

В этой статье подробно описаны различные задачи, связанные со службами федерации Active Directory (AD FS), которые можно выполнить с помощью Azure AD Connect, а также другие стандартные задачи AD FS, выполнение которых может потребоваться для полной настройки фермы AD FS.

## Управление AD FS

В Azure AD Connect можно выполнять разные связанные с AD FS задачи с помощью мастера Azure AD Connect с минимальным участием пользователя. Завершив установку Azure AD Connect с помощью мастера, вы можете снова запустить этот мастер, чтобы выполнить дополнительные задачи.

### Восстановление доверия

Azure AD Connect может проверить текущую работоспособность AD FS и Azure ADtrust и предпринять соответствующие действия для восстановления доверия. Выполните приведенные ниже действия для восстановления доверия Azure AD и AD FS.

Выберите в списке доступных задач пункт **Восстановить доверие AAD и AD FS**.

![](media\active-directory-aadconnect-federation-management\RepairADTrust1.PNG)

На странице **Подключение к Azure AD** укажите учетные данные глобального администратора для Azure AD и нажмите кнопку "Далее".

![](media\active-directory-aadconnect-federation-management\RepairADTrust2.PNG)

На странице **Учетные данные для удаленного доступа** введите учетные данные администратора домена.

![](media\active-directory-aadconnect-federation-management\RepairADTrust3.PNG)

Когда вы нажмете кнопку "Далее", Azure AD Connect проверит работоспособность сертификата и отобразит возможные проблемы, если они существуют.

![](media\active-directory-aadconnect-federation-management\RepairADTrust4.PNG)

На странице **Готово к настройке** отобразится список действий, которые будут выполнены для восстановления доверия.

![](media\active-directory-aadconnect-federation-management\RepairADTrust5.PNG)

Нажмите "Установить", чтобы продолжить восстанавливать доверие.

>[AZURE.NOTE] Azure AD Connect может выполнить восстановление или другие действия только для самозаверяющих сертификатов. Сертификаты третьих сторон нельзя восстановить с помощью Azure AD Connect.

### Добавление нового сервера AD FS

> [AZURE.NOTE] Для добавления сервера AD FS Azure AD Connect требует файл сертификата PFX. Таким образом, эту операцию можно выполнить, только если ферма AD FS настроена с помощью Azure AD Connect.

Выберите пункт **Развернуть дополнительный сервер федерации** и нажмите кнопку "Далее".

![](media\active-directory-aadconnect-federation-management\AddNewADFSServer1.PNG)

На странице **Подключение к Azure AD** укажите учетные данные глобального администратора для Azure AD и нажмите кнопку "Далее".

![](media\active-directory-aadconnect-federation-management\AddNewADFSServer2.PNG)

Введите учетные данные администратора домена на следующей странице.

![](media\active-directory-aadconnect-federation-management\AddNewADFSServer3.PNG)

На следующей странице Azure AD Connect запросит пароль PFX-файла, который был указан при настройке новой фермы AD FS с помощью Azure AD Connect. Щелкните "Ввести пароль", чтобы указать пароль для PFX-файла.

![](media\active-directory-aadconnect-federation-management\AddNewADFSServer4.PNG)

![](media\active-directory-aadconnect-federation-management\AddNewADFSServer5.PNG)

На следующей странице введите имя или IP-адрес одного или нескольких дополнительных серверов, которые будут добавлены в ферму AD FS.

![](media\active-directory-aadconnect-federation-management\AddNewADFSServer6.PNG)

Нажмите кнопку "Далее" и выполните инструкции на последней странице "Настройка". После того как Azure AD Connect завершит добавление серверов в ферму AD FS, у вас будет возможность проверить подключение.

![](media\active-directory-aadconnect-federation-management\AddNewADFSServer7.PNG)

![](media\active-directory-aadconnect-federation-management\AddNewADFSServer8.PNG)

### Добавление нового прокси-сервера веб-приложения AD FS

> [AZURE.NOTE] Чтобы добавить прокси-сервер веб-приложения для Azure AD Connect, требуется файл сертификата PFX. Таким образом, эту операцию можно выполнить, только если ферма AD FS настроена с помощью Azure AD Connect.

Выберите пункт **Развернуть прокси-сервер веб-приложений** в списке доступных задач.

![](media\active-directory-aadconnect-federation-management\WapServer1.PNG)

На следующей странице введите учетные данные глобального администратора Azure.

![](media\active-directory-aadconnect-federation-management\wapserver2.PNG)

Затем откроется страница **Укажите SSL-сертификат**, где необходимо предоставить пароль для PFX-файла, который использовался при настройке фермы AD FS с помощью Azure AD Connect.

![](media\active-directory-aadconnect-federation-management\WapServer3.PNG)

![](media\active-directory-aadconnect-federation-management\WapServer4.PNG)

На следующей странице добавьте сервер в качестве прокси-сервера веб-приложения. Так как прокси-сервер веб-приложения может быть присоединен или не присоединен к домену, мастер запросит учетные данные администратора для добавляемого сервера.

![](media\active-directory-aadconnect-federation-management\WapServer5.PNG)

На странице **Учетные данные доверия прокси-сервера** введите учетные данные администратора, чтобы настроить доверие прокси-сервера и доступ к основному серверу в ферме AD FS.

![](media\active-directory-aadconnect-federation-management\WapServer6.PNG)

В мастере на странице **Готово к настройке** отображается список действий, которые будут выполнены.

![](media\active-directory-aadconnect-federation-management\WapServer7.PNG)

Нажмите "Установить", чтобы завершить настройку. По завершении настройки мастер предоставляет возможность проверить подключение к серверам. Нажмите "Проверить" для проверки подключения.

![](media\active-directory-aadconnect-federation-management\WapServer8.PNG)

### Добавление нового федеративного домена

Вы можете быстро добавить новый домен в федерацию с Azure AD с помощью Azure AD Connect. Azure AD Connect не только добавляет новый домен для федерации, но также изменяет правила утверждения, чтобы правильно отражать издателя при наличии нескольких доменов в федерации с Azure AD.

Чтобы добавить новый федеративный домен, выберите задачу **Добавить дополнительный домен Azure AD**.

![](media\active-directory-aadconnect-federation-management\AdditionalDomain1.PNG)

На следующей странице мастера введите учетные данные глобального администратора для Azure AD.

![](media\active-directory-aadconnect-federation-management\AdditionalDomain2.PNG)

На странице "Учетные данные для удаленного доступа" введите учетные данные администратора домена.

![](media\active-directory-aadconnect-federation-management\additionaldomain3.PNG)

На следующей странице мастера будет предоставлен список доменов Azure AD, которые следует использовать для федерации вашего локального каталога. Выберите домен из списка.

![](media\active-directory-aadconnect-federation-management\AdditionalDomain4.PNG)

Когда вы выберете домен, мастер предоставит соответствующие сведения о дальнейших действиях, которые будут выполнены мастером, а также о результатах настроек. В некоторых случаях при выборе домена, еще не проверенного в Azure AD, мастер предоставит сведения, которые помогут проверить домен. Дополнительные сведения см. в статье, посвященной [добавлению пользовательских доменных имен в Azure Active Directory](active-directory-add-domain.md).

Нажмите кнопку "Далее", и на странице **Готово к настройке** отобразится список действий, которые выполнит Azure AD Connect. Нажмите "Установить", чтобы завершить настройку.

![](media\active-directory-aadconnect-federation-management\AdditionalDomain5.PNG)

## Пользовательская настройка AD FS

Ниже приведены сведения о том, как выполнять некоторые распространенные задачи, связанные с пользовательской настройкой страницы входа AD FS.

### Добавление настраиваемого логотипа компании или иллюстрации

Чтобы изменить логотип компании, который отображается на странице входа, используйте следующий командлет Windows PowerShell и синтаксис.

> [AZURE.NOTE] Рекомендуемые размеры логотипа — 260x35, 96 точек на дюйм. Размер файла не должен превышать 10 КБ.

    Set-AdfsWebTheme -TargetName default -Logo @{path="c:\Contoso\logo.PNG"}

> [AZURE.NOTE] Параметр TargetName является обязательным. Стандартная тема для AD FS называется темой по умолчанию.
 

### Добавление описания входа в систему

Чтобы добавить на страницу входа описание страницы входа, используйте следующий командлет Windows PowerShell и синтаксис.

    Set-AdfsGlobalWebContent -SignInPageDescriptionText "<p>Sign-in to Contoso requires device registration. Click <A href='http://fs1.contoso.com/deviceregistration/'>here</A> for more information.</p>"

### Изменение правил утверждений служб федерации Active Directory

Службы федерации Active Directory позволяют указывать пользовательские правила для выдачи утверждений. Они поддерживают обширный язык утверждений, с помощью которого можно создавать настраиваемые правила утверждений. Дополнительные сведения см. в [этой статье](https://technet.microsoft.com/library/dd807118.aspx).

Далее подробно рассматривается, как можно создавать пользовательские правила для некоторых сценариев, связанных с использованием Azure AD и федерации AD FS.

#### Неизменяемый идентификатор, зависящий от наличия значения в атрибуте

Azure AD Connect позволяет указывать атрибут, используемый в качестве привязки к источнику, когда объекты будут синхронизированы с Azure AD. Возможно, вы захотите выдавать утверждение неизменяемого идентификатора в зависимости от условия, если значение в настраиваемом атрибуте не является пустым. Например, предположим, что в качестве привязки к источнику выбран атрибут ms-ds-consistencyguid, и нужно выдать ImmutableID как ms-ds-consistencyguid в случае наличия у атрибута значения. В противном случае в качестве неизменяемого идентификатора следует выдать objectGuid. Можно создать набор пользовательских правил утверждения, как описано ниже.

**Правило 1 (атрибуты запросов)**

    c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname"]
    => add(store = "Active Directory", types = ("http://contoso.com/ws/2016/02/identity/claims/objectguid", "http://contoso.com/ws/2016/02/identity/claims/msdsconcistencyguid"), query = "; objectGuid,ms-ds-consistencyguid;{0}", param = c.Value);

В этом правиле просто запрашиваются значения ms-ds-consistencyguid и objectguid для пользователя из Active Directory. Измените имя хранилища на имя соответствующего хранилища, доступного в развернутой системе ADFS, а тип утверждений — на соответствующий тип утверждений, определенный в федерации для objectGUID и ms-ds-consistencyguid. Мы определили пользовательские типы утверждений в тестовой среде.

Кроме того, используя параметр add вместо issue, мы предотвратим добавление исходящей выдачи для сущности и просто будем использовать значения в качестве промежуточных значений. Утверждение мы выдадим в последующем правиле после того, как установим, какое значение следует использовать в качестве неизменяемого идентификатора.

**Правило 2. (Проверка существования ms-ds-consistencyguid для пользователя)**

    NOT EXISTS([Type == "http://contoso.com/ws/2016/02/identity/claims/msdsconcistencyguid"])
    => add(Type = "urn:anandmsft:tmp/idflag", Value = "useguid");

Это правило просто определяет временный флаг idflag, который получает значение useguid, если ms-ds-concistencyguid для пользователя не заполнен. Логика такого решения в том, что службы AD FS запрещают пустые утверждения. Поэтому при добавлении утверждений http://contoso.com/ws/2016/02/identity/claims/objectguid и http://contoso.com/ws/2016/02/identity/claims/msdsconcistencyguid в правиле 1 результатом будет утверждение msdsconsistencyguid ТОЛЬКО в том случае, если значение для пользователя заполнено. В случае, если оно не заполнено, службы AD FS замечают, что значение окажется пустым, и опускают его. ObjectGuid, как известно, имеют все объекты, поэтому такое утверждение всегда будет присутствовать после выполнения правила 1.

**Правило 3. Выдача ms-ds-consistencyguid в качестве неизменяемого идентификатора, если он присутствует**

    c:[Type == "http://contoso.com/ws/2016/02/identity/claims/msdsconcistencyguid"]
    => issue(Type = "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier", Value = c.Value);

Это неявная проверка EXIST. Если значение для утверждения существует, выполняется его выдача в качестве неизменяемого идентификатора. Обратите внимание, что мы выдаем утверждение nameidentifier. Его потребуется заменить соответствующим типом утверждения для неизменяемого идентификатора в конкретной среде.

**Правило 4. Выдача Guid объекта в качестве неизменяемого идентификатора при отсутствии ms-ds-consistencyGuid**

    c1:[Type == "urn:anandmsft:tmp/idflag", Value =~ "useguid"]
    && c2:[Type == "http://contoso.com/ws/2016/02/identity/claims/objectguid"]
    => issue(Type = "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier", Value = c2.Value);

В этом правиле просто выполняется проверка временного флага idflag; на основе значения определяется, выдавать или не выдавать утверждение.

> [AZURE.NOTE] Последовательность этих правил важна.

#### Единый вход с помощью UPN поддомена

Вы можете включить несколько доменов в федерацию с помощью Azure AD Connect (см. [Добавление нового федеративного домена](active-directory-aadconnect-federation-management.md#add-a-new-federated-domain)). Следует изменить утверждение UPN так, чтобы идентификатор издателя соответствовал корневому домену, а не поддомену, так как федеративный корневой домен включает и дочерние домены.

По умолчанию для идентификатора издателя установлено следующее правило утверждения:

	c:[Type 
	== “http://schemas.xmlsoap.org/claims/UPN“]

	=> issue(Type = “http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid“, Value = regexreplace(c.Value, “.+@(?<domain>.+)“, “http://${domain}/adfs/services/trust/“));

![Утверждение идентификатора издателя по умолчанию](media\active-directory-aadconnect-federation-management\issuer_id_default.png)

Правило по умолчанию принимает суффикс UPN и использует его в утверждении идентификатора издателя. Предположим, Джон является пользователем в домене sub.contoso.com, а contoso.com включен в федерацию с Azure AD. При входе в Azure AD Джон вводит имя пользователя john@sub.contoso.com, и правило по умолчанию для утверждения идентификатора поставщика в AD FS обрабатывает его следующим образом:

c:[Type == “http://schemas.xmlsoap.org/claims/UPN“]

=> issue(Type = “http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid“, Value = regexreplace(john@sub.contoso.com, “.+@(?<domain>.+)“, “http://${domain}/adfs/services/trust/“));

**Значение утверждения:** http://sub.contoso.com/adfs/services/trust/

Чтобы просто включить корневой домен в значение утверждения поставщика, измените правило утверждения так:

	c:[Type == “http://schemas.xmlsoap.org/claims/UPN“]

	=> issue(Type = “http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid“, Value = regexreplace(c.Value, “^((.*)([.|@]))?(?<domain>[^.]*[.].*)$”, “http://${domain}/adfs/services/trust/“));

## Дальнейшие действия

См. дополнительные сведения о [параметрах входа пользователя](active-directory-aadconnect-user-signin.md).

<!---HONumber=AcomDC_0511_2016-->