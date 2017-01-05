---
title: "Приступая к работе с многофакторной идентификацией Azure в облаке | Документация Майкрософт"
description: "Эта страница посвящена службе Microsoft Azure Multi-Factor Authentication. Она содержит сведения по началу работы с Azure MFA в облаке."
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: yossib
ms.assetid: 6b2e6549-1a26-4666-9c4a-cbe5d64c4e66
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/04/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: c6fe00b72d95a3eb40d91f6f7989b7163518c46f


---
# <a name="getting-started-with-azure-multi-factor-authentication-in-the-cloud"></a>Приступая к работе с Azure Multi-Factor Authentication в облаке
В этой статье вы узнаете, как приступить к работе с многофакторной идентификацией Azure в облаке.

> [!NOTE]
> Здесь рассматривается использование **классического портала Azure**. Сведения о настройке многофакторной идентификации Azure для пользователей Office 365 см. в статье [Настройка многофакторной идентификации для Office 365](https://support.office.com/article/Set-up-multi-factor-authentication-for-Office-365-users-8f0454b2-f51a-4d9c-bcde-2c48e41621c6?ui=en-US&rs=en-US&ad=US).

![MFA в облаке](./media/multi-factor-authentication-get-started-cloud/mfa_in_cloud.png)

## <a name="prerequisites"></a>Предварительные требования
Чтобы включить Azure Multi-Factor Authentication для пользователей, необходимо выполнить следующие предварительные требования.

1. [Зарегистрируйтесь, чтобы оформить подписку Azure](https://azure.microsoft.com/pricing/free-trial/). Если у вас еще нет подписки Azure, вам необходимо зарегистрироваться для ее оформления. Если вы только начинаете работать с Azure Multi-Factor Authentication (MFA), используйте пробную подписку.
2. [Создайте поставщик многофакторной идентификации Azure](multi-factor-authentication-get-started-auth-provider.md) и назначьте его для своего каталога либо [назначьте лицензии пользователям](multi-factor-authentication-get-started-assign-licenses.md).

> [!NOTE]
> Лицензии доступны пользователям, у которых есть Azure MFA, Azure AD Premium или Enterprise Mobility Suite (EMS).  MFA входит в состав Azure AD Premium и EMS. Если у вас есть необходимые лицензии, вам не нужно создавать поставщик проверки подлинности.

## <a name="turn-on-two-step-verification-for-users"></a>Включение двухфакторной проверки подлинности для пользователей
Чтобы включить двухфакторную проверку подлинности для пользователей, измените состояние пользователя с "Отключено" на "Включено".  Дополнительные сведения о состояниях пользователей см. в статье [Состояния пользователей в многофакторной идентификации Azure](multi-factor-authentication-get-started-user-states.md).

Чтобы включить MFA для пользователей, выполните процедуру ниже.

### <a name="to-turn-on-multi-factor-authentication"></a>Включение многофакторной проверки подлинности
1. Войдите на [классический портал Azure](https://manage.windowsazure.com) с учетной записью администратора.
2. В левой части щелкните **Active Directory**.
3. В разделе "Каталог" выберите каталог пользователя, которого нужно включить.
   ![Щелкните каталог](./media/multi-factor-authentication-get-started-cloud/directory1.png)
4. В верхней части щелкните **Пользователи**.
5. В нижней части страницы щелкните **Управление Multi-Factor Auth**. Откроется новая вкладка браузера.
   ![Щелкните каталог](./media/multi-factor-authentication-get-started-cloud/manage1.png)
6. Найдите пользователя, для которого нужно включить двухфакторную проверку подлинности. Возможно, потребуется изменить представление в верхней части страницы. Для состояния должно быть установлено значение **Отключено**.
   ![Включение пользователя](./media/multi-factor-authentication-get-started-cloud/enable1.png)
7. Установите **флажок** рядом с именем пользователя.
8. В правой части окна щелкните **Включить**.
   ![Включение пользователя](./media/multi-factor-authentication-get-started-cloud/user1.png)
9. Щелкните **Включить проверку Multi-Factor Auth**.
   ![Включение пользователя](./media/multi-factor-authentication-get-started-cloud/enable2.png)
10. Обратите внимание, что состояние пользователя изменилось с **Отключено** на **Включено**.
    ![Включение пользователей](./media/multi-factor-authentication-get-started-cloud/user.png)

После включения рекомендуется уведомить пользователей по электронной почте. В следующий раз при попытке входа в систему им нужно будет зарегистрировать свою учетную запись для прохождения двухфакторной проверки. Им также потребуется задать пароли для приложений, чтобы они не были заблокированы при входе в приложения, не использующие браузер.

## <a name="use-powershell-to-automate-turning-on-two-step-verification"></a>Автоматизация включения двухфакторной проверки подлинности с помощью PowerShell
Чтобы изменить [состояние](multi-factor-authentication-whats-next.md) с помощью [Azure AD PowerShell](/powershell/azureps-cmdlets-docs), можно использовать команды, приведенные ниже.  Для параметра `$st.State` можно задать одно из следующих состояний:

* Включено
* Принудительно
* Отключено  

> [!IMPORTANT]
> Не рекомендуется менять состояние пользователя непосредственно с "Отключено" на "Принудительно". Приложения, не использующие браузер, перестанут работать, так как пользователь не прошел регистрацию многофакторной идентификации и не получил [пароль приложения](multi-factor-authentication-whats-next.md#app-passwords). Если вы используете приложения, работающие вне браузера, и вам требуются пароли приложения, рекомендуется переключаться с состояния "Отключено" на состояние "Включено". Так пользователи могут пройти регистрацию и получить пароли. После этого вы можете задать для них состояние "Принудительно".

Для массового включения пользователей можно использовать PowerShell. В настоящее время на портале Azure невозможно массово включать пользователей и необходимо выбирать каждого пользователя отдельно. Это может быть сложной задачей, если у вас много пользователей. Создав скрипт PowerShell с помощью кода ниже, можно перебрать список пользователей и включить их.

        $st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
        $st.RelyingParty = "*"
        $st.State = “Enabled”
        $sta = @($st)
        Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements $sta

Пример:

    $users = "bsimon@contoso.com","jsmith@contoso.com","ljacobson@contoso.com"
    foreach ($user in $users)
    {
        $st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
        $st.RelyingParty = "*"
        $st.State = “Enabled”
        $sta = @($st)
        Set-MsolUser -UserPrincipalName $user -StrongAuthenticationRequirements $sta
    }


Дополнительные сведения см. в статье [Состояния пользователей в многофакторной идентификации Azure](multi-factor-authentication-get-started-user-states.md).

## <a name="next-steps"></a>Дальнейшие действия
Теперь, когда многофакторная идентификация в облаке настроена, можно настроить и выполнить развертывание. Дополнительные сведения см. в статье [Настройка многофакторной идентификации Azure](multi-factor-authentication-whats-next.md).




<!--HONumber=Dec16_HO2-->


