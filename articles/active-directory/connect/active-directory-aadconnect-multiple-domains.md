---
title: "Поддержка нескольких доменов в Azure AD Connect"
description: "В этом документе описываются процедуры установки и настройки нескольких доменов верхнего уровня в Office&365; и Azure AD."
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: curtand
ms.assetid: 5595fb2f-2131-4304-8a31-c52559128ea4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: fa1c3d9cb07d417f5dbde41d6269fb1d157c3104
ms.openlocfilehash: a6a97cd187036222f5a47e55670da613117a2318


---
# <a name="multiple-domain-support-for-federating-with-azure-ad"></a>Поддержка нескольких доменов для федерации с Azure AD
В следующей документации представлено руководство по использованию нескольких доменов верхнего уровня и поддоменов в федерации с Office 365 или доменами Azure AD.

## <a name="multiple-top-level-domain-support"></a>Поддержка нескольких доменов верхнего уровня
Для создания федерации нескольких доменов верхнего уровня с Azure AD необходимо выполнить некоторые дополнительные настройки, которые не требуются при создании федерации с одним доменом верхнего уровня.

При добавлении домена в федерацию с Azure AD необходимо установить несколько свойств для этого домена в Azure.  Важным свойством является IssuerUri.  Это URI, который используется Azure AD для идентификации домена, с которым связан маркер.  URI не должен разрешаться, но обязан быть действительным.  По умолчанию Azure AD устанавливает для этого свойства значение идентификатора службы федерации в локальной конфигурации службы AD FS.

> [!NOTE]
> Идентификатор службы федерации — это URI, который уникально идентифицирует службу федерации.  Служба федерации — это экземпляр AD FS, который работает как служба токенов безопасности. 
> 
> 

Для просмотра IssuerUri можно воспользоваться следующей командой PowerShell `Get-MsolDomainFederationSettings -DomainName <your domain>`.

![Get-MsolDomainFederationSettings](./media/active-directory-multiple-domains/MsolDomainFederationSettings.png)

Проблема возникает, когда нам необходимо добавить несколько доменов верхнего уровня.  Например, предположим, что у вас настроена федерация между Azure AD и локальной средой.  В данном документе я использую домен bmcontoso.com.  Теперь я добавил второй домен верхнего уровня bmfabrikam.com.

![Домены](./media/active-directory-multiple-domains/domains.png)

При попытке преобразования нашего домена bmfabrikam.com домена в федеративный мы получим сообщение об ошибке.  Причина в том, что в Azure AD существует ограничение, при котором свойство IssuerUri не может иметь одно и то же значение для нескольких доменов.  

![Ошибка федерации](./media/active-directory-multiple-domains/error.png)

### <a name="supportmultipledomain-parameter"></a>Параметр SupportMultipleDomain
Чтобы обойти это, необходимо добавить другой IssuerUri. Это можно сделать с помощью параметра `-SupportMultipleDomain`.  Этот параметр используется со следующими командлетами:

* `New-MsolFederatedDomain`
* `Convert-MsolDomaintoFederated`
* `Update-MsolFederatedDomain`

Этот параметр заставляет Azure AD настроить IssuerUri таким образом, чтобы он был основан на имени домена.  Оно будет уникальным в различных каталогах в Azure AD.  Использование параметра позволяет успешно выполнить команду PowerShell.

![Ошибка федерации](./media/active-directory-multiple-domains/convert.png)

Просмотрев параметры нашего нового домена bmfabrikam.com, вы увидите следующее:

![Ошибка федерации](./media/active-directory-multiple-domains/settings.png)

Обратите внимание, что `-SupportMultipleDomain` не изменяет другие конечные точки, в которых адрес службы федерации по-прежнему задан как adfs.bmcontoso.com.

Параметр `-SupportMultipleDomain` также гарантирует, что система AD FS содержит правильное значение издателя для маркеров, выпущенных для Azure AD. Для этого он извлекает доменную часть UPN пользователей и устанавливает ее в свойстве IssuerURI домена, т. е. https://{суффикс upn}/adfs/services/trust. 

Таким образом, при проверке подлинности в Azure AD или Office 365 для обнаружения домена в Azure AD используется элемент IssuerUri в маркере пользователя.  Если совпадение не найдено, проверка подлинности завершится ошибкой. 

Например, если UPN пользователя имеет значение bsimon@bmcontoso.com,, то элементу IssuerUri в маркере, который выпускается AD FS, будет присвоено значение http://bmcontoso.com/adfs/services/trust. Это будет соответствовать конфигурации Azure AD, и проверка подлинности пройдет успешно.

Ниже приведено пользовательское правило утверждения, которое реализует эту логику.

    c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, ".+@(?<domain>.+)", "http://${domain}/adfs/services/trust/"));


> [!IMPORTANT]
> Чтобы использовать параметр -SupportMultipleDomain при добавлении новых или преобразовании существующих доменов, необходимо заранее включить федеративное отношение доверия для этих доменов.  
> 
> 

## <a name="how-to-update-the-trust-between-ad-fs-and-azure-ad"></a>Обновление отношения доверия между службами федерации Active Directory и Azure AD
Если вы не настроили федеративного отношения доверия между AD FS и своим экземпляром Azure AD, может потребоваться создать это отношение доверия снова.  Это вызвано тем, что при изначальной настройке без параметра `-SupportMultipleDomain` в IssuerUri записывается значение по умолчанию.  На приведенном ниже снимке экрана видно, что IssuerUri имеет значение https://adfs.bmcontoso.com/adfs/services/trust.

Поэтому если мы добавим новый домен на портале Azure AD, а затем попытаемся преобразовать его с помощью `Convert-MsolDomaintoFederated -DomainName <your domain>`, то получим следующую ошибку.

![Ошибка федерации](./media/active-directory-multiple-domains/trust1.png)

При попытке указать параметр `-SupportMultipleDomain` мы все равно получим следующую ошибку:

![Ошибка федерации](./media/active-directory-multiple-domains/trust2.png)

Попытка просто запустить `Update-MsolFederatedDomain -DomainName <your domain> -SupportMultipleDomain` в исходном домене также приведет к ошибке.

![Ошибка федерации](./media/active-directory-multiple-domains/trust3.png)

Выполните указанные ниже действия для добавления дополнительного домена верхнего уровня.  Если домен уже был добавлен и параметр `-SupportMultipleDomain` не использовался, начните с удаления и обновления исходного домена.  Если вы не еще добавили домен верхнего уровня, можете начать с добавления домена с помощью PowerShell или Azure AD Connect.

Выполните следующие действия для удаления доверия Microsoft Online и обновления исходного домена.

1. На сервере федерации AD FS откройте **Управление AD FS** 
2. Разверните **Отношения доверия** и **Отношения доверия проверяющей стороны** слева.
3. Удалите запись **Платформа идентификации Microsoft Office 365** справа.
   ![Удалить Microsoft Online](./media/active-directory-multiple-domains/trust4.png)
4. На компьютере, на котором установлен [модуль Azure Active Directory для Windows PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx), выполните следующую команду: `$cred=Get-Credential`.  
5. Введите имя пользователя и пароль глобального администратора домена Azure AD, для которого настраивается федерация.
6. В PowerShell введите `Connect-MsolService -Credential $cred`
7. В PowerShell введите `Update-MSOLFederatedDomain -DomainName <Federated Domain Name> -SupportMultipleDomain`.  Это исходный домен.  С приведенными выше доменами мы получим следующее: `Update-MsolFederatedDomain -DomainName bmcontoso.com -SupportMultipleDomain`.

Выполните следующие действия для добавления нового домена верхнего уровня с помощью PowerShell.

1. На компьютере, на котором установлен [модуль Azure Active Directory для Windows PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx), выполните следующую команду: `$cred=Get-Credential`.  
2. Введите имя пользователя и пароль глобального администратора домена Azure AD, для которого настраивается федерация.
3. В PowerShell введите `Connect-MsolService -Credential $cred`
4. В PowerShell введите `New-MsolFederatedDomain –SupportMultipleDomain –DomainName`

Выполните следующие действия для добавления нового домена верхнего уровня с помощью Azure AD Connect.

1. Запустите Azure AD Connect на рабочем столе или в меню "Пуск".
2. Выберите "Добавить дополнительный домен Azure AD" ![Добавить дополнительный домен Azure AD](./media/active-directory-multiple-domains/add1.png).
3. Введите учетные данные Azure AD и Active Directory.
4. Выберите второй домен, который необходимо настроить для федерации.
   ![Добавить дополнительный домен Azure AD](./media/active-directory-multiple-domains/add2.png)
5. Нажмите "Установить".

### <a name="verify-the-new-top-level-domain"></a>Проверка нового домена верхнего уровня
С помощью команды PowerShell `Get-MsolDomainFederationSettings -DomainName <your domain>`можно просмотреть обновленный IssuerUri.  На приведенном ниже снимке экрана показано, что настройки федерации для исходного домена http://bmcontoso.com/adfs/services/trust были обновлены.

![Get-MsolDomainFederationSettings](./media/active-directory-multiple-domains/MsolDomainFederationSettings.png)

И IssuerUri на нашем новом домене установлен в https://bmfabrikam.com/adfs/services/trust.

![Get-MsolDomainFederationSettings](./media/active-directory-multiple-domains/settings2.png)

## <a name="support-for-sub-domains"></a>Поддержка поддоменов
При добавлении поддомена он унаследует параметры родительского домена из-за особенностей обработки доменов Azure AD.  Это означает, что IssuerUri будет совпадать со значением аналогичного параметра у родительского элемента.

Давайте предположим, что у меня был домен bmcontoso.com, а затем я добавил поддомен corp.bmcontoso.com.  Это значит, что значение IssuerUri для пользователя из corp.bmcontoso.com должно быть **http://bmcontoso.com/adfs/services/trust**.  Однако реализованное выше стандартное правило для Azure AD создает маркер с издателем **http://corp.bmcontoso.com/adfs/services/trust**. , который не будет соответствовать необходимому значению для домена, и аутентификация завершится неудачно.

### <a name="how-to-enable-support-for-sub-domains"></a>Включение поддержки для поддоменов
Чтобы обойти эту проблему, необходимо обновить отношение доверия проверяющей стороны AD FS для Microsoft Online.  Для этого необходимо настроить пользовательское правило утверждения так, чтобы оно удаляло поддомены из суффикса UPN пользователя при создании настраиваемого значения элемента Issuer. 

Это можно сделать с помощью следующего утверждения:

    c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, "^.*@([^.]+\.)*?(?<domain>([^.]+\.?){2})$", "http://${domain}/adfs/services/trust/"));

[!NOTE]
Последнее число в регулярном выражении задает количество родительских доменов в корневом домене. Так как здесь используется bmcontoso.com, необходимы два родительских домена. Если бы нужно было сохранить три родительских домена (т. е. corp.bmcontoso.com), использовалось бы число три. Впоследствии может быть задан целый диапазон. Совпадение всегда будет выполняться для соответствия максимальному количеству доменов. "{2,3}" означает, что два домена будут сопоставлены с тремя (т. е. bmfabrikam.com и corp.bmcontoso.com).

Используйте следующие шаги для добавления пользовательского утверждения для поддержки поддоменов.

1. Откройте оснастку управления AD FS.
2. Щелкните правой кнопкой мыши по отношению доверия Microsoft Online RP и выберите "Изменить правила утверждения".
3. Выберите третье правило утверждения и замените ![Изменить правила утверждений](./media/active-directory-multiple-domains/sub1.png).
4. Замените текущее утверждение:
   
        c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, ".+@(?<domain>.+)","http://${domain}/adfs/services/trust/"));
   
       with
   
        c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, "^.*@([^.]+\.)*?(?<domain>([^.]+\.?){2})$", "http://${domain}/adfs/services/trust/"));

    ![Заменить утверждение](./media/active-directory-multiple-domains/sub2.png)

5. Нажмите кнопку "ОК".  Нажмите кнопку "Применить".  Нажмите кнопку "ОК".  Откройте оснастку управления AD FS.




<!--HONumber=Jan17_HO2-->


