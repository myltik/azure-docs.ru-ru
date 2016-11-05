---
title: Приступая к работе с Microsoft Azure Multi-Factor Authentication в облаке
description: Эта страница посвящена службе Microsoft Azure Multi-Factor Authentication. Она содержит сведения по началу работы с Azure MFA в облаке.
services: multi-factor-authentication
documentationcenter: ''
author: kgremban
manager: femila
editor: curtand

ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/15/2016
ms.author: kgremban

---
# Приступая к работе с Azure Multi-Factor Authentication в облаке
В этой статье вы узнаете, как приступить к работе с Azure Multi-Factor Authentication в облаке.

> [!NOTE]
> Приведенная здесь документация содержит сведения о том, как разрешить пользователям использовать **классический портал Azure**. Если вам нужны дополнительные сведения о настройке Azure Multi-Factor Authentication для пользователей Office 365, см. статью [Настройка многофакторной проверки подлинности для пользователей Office 365.](https://support.office.com/article/Set-up-multi-factor-authentication-for-Office-365-users-8f0454b2-f51a-4d9c-bcde-2c48e41621c6?ui=ru-RU&rs=ru-RU&ad=US)
> 
> 

![MFA в облаке](./media/multi-factor-authentication-get-started-cloud/mfa_in_cloud.png)

## Предварительные требования
Чтобы включить Azure Multi-Factor Authentication для пользователей, необходимо выполнить следующие предварительные требования.

* [Зарегистрируйтесь, чтобы оформить подписку Azure](https://azure.microsoft.com/pricing/free-trial/). Если у вас еще нет подписки Azure, вам необходимо зарегистрироваться для ее оформления. Если вы только начинаете работать с Azure Multi-Factor Authentication (MFA), используйте пробную подписку.
* [Создайте поставщик Azure Multi-Factor Authentication](multi-factor-authentication-get-started-auth-provider.md) и назначьте его своему каталогу или [пользователям лицензии](multi-factor-authentication-get-started-assign-licenses.md).

> [!NOTE]
> Лицензии доступны пользователям, у которых есть Azure MFA, Azure AD Premium или Enterprise Mobility Suite (EMS). MFA входит в состав Azure AD Premium и EMS. Если у вас есть необходимые лицензии, вам не нужно создавать поставщик проверки подлинности.
> 
> 

## Включение многофакторной проверки подлинности для пользователей
Чтобы включить многофакторную проверку подлинности для пользователя, достаточно просто изменить состояние пользователя с "Выключено" на "Включено". Дополнительные сведения о состояниях пользователей см. в статье [User States in Azure Multi-Factor Authentication](multi-factor-authentication-get-started-user-states.md) (Состояния пользователей в Azure Multi-Factor Authentication).

Чтобы включить MFA для пользователей, выполните процедуру ниже.

### Включение многофакторной проверки подлинности
- - -
1. Войдите на **классический портал Azure** с учетной записью администратора.
2. В левой части щелкните **Active Directory**.
3. В разделе **Каталог** щелкните каталог пользователя, которого нужно включить. ![Щелкните каталог](./media/multi-factor-authentication-get-started-cloud/directory1.png)
4. В верхней части щелкните **Пользователи**.
5. В нижней части страницы щелкните **Управление Multi-Factor Auth**. ![Щелкните каталог](./media/multi-factor-authentication-get-started-cloud/manage1.png)
6. Откроется новая вкладка браузера. Найдите пользователя, для которого нужно включить многофакторную проверку подлинности. Возможно, потребуется изменить представление в верхней части страницы. Для состояния должно быть установлено значение **Отключено.** ![Включение пользователя](./media/multi-factor-authentication-get-started-cloud/enable1.png)
7. Установите **флажок** рядом с именем пользователя.
8. В правой части окна щелкните **Включить**. ![Включение пользователя](./media/multi-factor-authentication-get-started-cloud/user1.png)
9. Щелкните **Включить проверку Multi-Factor Auth**. ![Включение пользователя](./media/multi-factor-authentication-get-started-cloud/enable2.png)
10. Следует отметить, что состояние пользователя изменилось с **Отключено** на **Включено**. ![Включение пользователей](./media/multi-factor-authentication-get-started-cloud/user.png)
11. После включения рекомендуется уведомить пользователей по электронной почте. В сообщении также следует рассказать, как использовать приложения, не использующие браузер, чтобы пользователя не заблокировали.

## Автоматизация включения многофакторной проверки подлинности с помощью PowerShell
Чтобы изменить [состояние](multi-factor-authentication-whats-next.md) с помощью [Azure AD PowerShell](../powershell-install-configure.md), можно использовать команды, приведенные ниже. Для параметра `$st.State` можно задать одно из следующих состояний:

* Включено
* Принудительно
* Отключено

> [!IMPORTANT]
> Имейте в виду, что при непосредственном изменении состояния с "Отключено" на "Принудительно" устаревшие клиенты проверки подлинности перестанут работать, так как пользователь не прошел регистрацию многофакторной проверки подлинности и не получил [пароль приложения](multi-factor-authentication-whats-next.md#app-passwords). Если вы используете устаревший клиент проверки подлинности и вам требуются пароли приложения, рекомендуется переключаться с состояния "Отключено" на состояние "Включено". Это позволит пользователям пройти регистрацию и получить пароли.
> 
> 

        $st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
        $st.RelyingParty = "*"
        $st.State = “Enabled”
        $sta = @($st)
        Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements $sta

Для массового включения пользователей можно использовать PowerShell. В настоящее время на портале Azure невозможно массово включать пользователей и необходимо выбирать каждого пользователя отдельно. Это может быть сложно, если у вас много пользователей. Создав сценарий PowerShell с помощью кода выше, можно перебрать список пользователей и включить их. Пример:

    $users = "bsimon@contoso.com","jsmith@contoso.com","ljacobson@contoso.com"
    foreach ($user in $users)
    {
        $st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
        $st.RelyingParty = "*"
        $st.State = “Enabled”
        $sta = @($st)
        Set-MsolUser -UserPrincipalName $user -StrongAuthenticationRequirements $sta
    }


Дополнительные сведения о состояниях пользователей см. в статье [User States in Azure Multi-Factor Authentication](multi-factor-authentication-get-started-user-states.md) (Состояния пользователей в Azure Multi-Factor Authentication).

## Дальнейшие действия
Теперь, когда многофакторная проверка подлинности в облаке настроена, можно настроить и выполнить развертывание. См. статью [Настройка Azure Multi-Factor Authentication.]

<!---HONumber=AcomDC_0921_2016-->