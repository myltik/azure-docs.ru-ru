---
title: "Приступая к работе с Azure Active Directory Premium"
description: "Раздел, где объясняется, как подписаться на выпуск Azure Active Directory Premium на веб-сайте корпоративного лицензирования."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: 391764e5-c4eb-4ae6-b8a9-3d09f0de04a6
ms.service: active-directory
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/24/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 73cbbce9ae3d727632b8cac2a29c2b1b92332ae7


---
# <a name="getting-started-with-azure-active-directory-premium"></a>Приступая к работе с Azure Active Directory Premium
Есть несколько способов регистрации в Active Directory Premium: 

**Azure или Office 365** — каждый подписчик Azure или Office 365 может приобрести Active Directory Premium через Интернет. Подробные инструкции см. в видео [How to Purchase Azure Active Directory Premium - Existing Customers](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/How-to-Purchase-Azure-Active-Directory-Premium-Existing-Customer) (Как существующие клиенты могут приобрести Azure Active Directory Premium) и [How to Purchase Azure Active Directory Premium - New Customers](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/How-to-Purchase-Azure-Active-Directory-Premium-New-Customers) (Как новые клиенты могут приобрести Azure Active Directory Premium).  

**Enterprise Mobility + Security** (предыдущее название — Enterprise Mobility Suite) — это эффективное средство, с помощью которого организации могут совместно использовать службы Active Directory Premium, Azure Rights Management и Microsoft Intune в рамках одного плана лицензирования. Дополнительные сведения см. на веб-сайте [Enterprise Mobility + Security](https://www.microsoft.com/en-us/server-cloud/enterprise-mobility/overview.aspx). Чтобы получить бесплатную 30-дневную пробную версию, щелкните [здесь](https://portal.office.com/Signup/Signup.aspx?OfferId=2E63A04D-BE0B-4A0F-A8CF-407C1C299221&dl=EMS&ali=1#0).

**Корпоративное лицензирование Microsoft** — служба Azure Active Directory Premium доступна по программам [Microsoft Enterprise](https://www.microsoft.com/en-us/licensing/licensing-programs/enterprise.aspx) (250 и более лицензий) и [Open Volume](https://www.microsoft.com/en-us/licensing/licensing-programs/open-license.aspx) (5–250 лицензий).

В этой статье показано, как приступить к работе со службой Azure Active Directory Premium, приобретенной по программе корпоративного лицензирования. Дополнительную информацию о разных выпусках Azure Active Directory см. в статье [Выпуски Azure Active Directory](active-directory-editions.md).  

> [!NOTE]
> Выпуски Azure Active Directory Premium и Basic доступны для клиентов в Китае, использующих доступный по всему миру экземпляр Azure Active Directory. Эти выпуски в настоящее время не поддерживает служба Microsoft Azure под управлением 21Vianet в Китае. Чтобы получить дополнительную информацию, свяжитесь с нами на [форуме Azure Active Directory](https://feedback.azure.com/forums/169401-azure-active-directory/).
> 
> 

## <a name="step-1-sign-up-for-active-directory-premium"></a>Шаг 1. Регистрация на Active Directory Premium
Сведения о регистрации см. в статье [How to purchase through Volume Licensing](http://www.microsoft.com/en-us/licensing/how-to-buy/how-to-buy.aspx) (Покупка по программе корпоративного лицензирования).

## <a name="step-2-activate-your-license-plan"></a>Шаг 2. Активирование плана лицензирования
Это ваша первая покупка плана лицензирования по программе корпоративного лицензирования от Майкрософт?
В таком случае после завершения покупки вы получите сообщение электронной почты с подтверждением.
Это нужно для активации первого плана лицензирования.

При последующих покупках для этого каталога лицензии будут автоматически активироваться в том же каталоге.

**Чтобы активировать план лицензирования, выполните одно из следующих действий:**

1. Чтобы начать активацию, щелкните **Вход** или **Регистрация**.
   
    ![входа][1]

    - Если у вас есть существующий клиент, щелкните **Вход** и выполните вход с существующей учетной записью администратора. Для входа необходимо использовать учетные данные глобального администратора из каталога, в котором должны быть активированы лицензии.

    - Если вы хотите создать новый клиент Azure Active Directory для использования с планом лицензирования, щелкните **Регистрация**. Откроется диалоговое окно **Создание профиля учетной записи**.

        ![Создание учетной записи][2]

Когда вы закончите, откроется следующее диалоговое окно, подтверждающее активацию плана лицензирования для вашего клиента.

![Подтверждение][3]

## <a name="step-3-activate-your-azure-active-directory-access"></a>Шаг 3. Активирование доступа к Azure Active Directory
Если вы уже используете Microsoft Azure, переходите к [шагу 4](#step-4-assign-license-to-user-accounts). 

При подготовке лицензий для своего каталога вы получите **приветственное сообщение электронной почты** . Это сообщение подтвердит, что вы можете приступать к управлению лицензиями и функциями Azure Active Directory Premium или Enterprise Mobility Suite. 

Если вы попытаетесь активировать доступ к Azure Active Directory до получения приветственного сообщения, может появиться следующее сообщение об ошибке: 

![Доступ не предоставляется][9]

Повторите попытку через несколько минут после получения сообщения.

Новые администраторы в вашей подписке также могут активировать доступ к классическому порталу Azure по этой ссылке.

**Чтобы активировать доступ к Azure Active Directory, выполните следующие действия:**

1. В **приветственном сообщении** щелкните **Вход**. 
   
    ![приветственное сообщение электронной почты][4]
2. Когда вы войдете, следует выполнить двухфакторную проверку подлинности с использованием номера мобильного телефона:
   
    ![Подтверждение по мобильному телефону][5]

Активация может занять несколько минут. Как только доступ станет возможным, коричневая панель исчезнет. Теперь можно щелкнуть **Портал**.

![Подождите, выполняется настройка][6]

В нашем примере доступ к Azure ограничен средой Azure Active Directory.

![Возможности Azure][7]

Вы уже могли ранее получать доступ к Azure. Кроме того, доступ к Azure Active Directory можно обновить до полного доступа к Azure, активировав дополнительные подписки Azure. В этом случае на классическом портале Azure у вас будет больше возможностей.

![Возможности Azure][8]

## <a name="step-4-assign-license-to-user-accounts"></a>Шаг 4. Назначение лицензии учетным записям пользователей
Прежде чем использовать приобретенный план, вам следует вручную назначить лицензии учетным записям корпоративных пользователей, чтобы они могли использовать все возможности выпуска Premium. Для назначения лицензий пользователям, чтобы они могли использовать возможности выпуска Azure Active Directory Premium, выполните следующие действия.

**Чтобы назначить лицензии пользователям, выполните следующие действия:**

1. Войдите на классический портал Azure с учетными данными администратора того каталога, который нужно настроить.
2. Щелкните **Active Directory**, а затем выберите каталог, где требуется назначить лицензии.
3. Перейдите на вкладку **Лицензии**, выберите **Active Directory Premium** или **Enterprise Mobility Suite**, а затем нажмите кнопку **Назначить**.
   
    ![Планы лицензирования][10]
4. В диалоговом окне выберите пользователей, которым требуется назначить лицензии, и щелкните значок галочки, чтобы сохранить изменения.
   
    ![Назначение лицензий][11]

### <a name="license-restrictions"></a>Лицензионные ограничения
Некоторые планы лицензирования являются подмножествами или супермножествами других планов лицензирования. В большинстве случаев пользователю нельзя назначить план лицензирования, который уже был ему назначен. Если вы хотите назначить план лицензирования, который является расширенным набором, сначала следует удалить входящий в него план лицензирования.

### <a name="license-requirements"></a>Требования лицензий
Назначая пользователю лицензию, вы можете указать в свойствах его учетной записи основное расположение. Если место использования не указано, местоположение клиента будет автоматически назначено пользователю.

![Местонахождение пользователей][12]

Доступность служб и компонентов для облачной службы Майкрософт зависит от страны или региона. Служба, например голосовая связь через Интернет (VoIP), может быть доступна в одной стране или регионе и недоступна в другой. В некоторых странах или регионах компоненты в службе могут быть ограничены по юридическим причинам. Чтобы проверить доступность службы или компонента (то есть наличие ограничений на использование), найдите нужную страну или регион на странице с лицензионными ограничениями для службы.

## <a name="whats-next"></a>Что дальше?
* [Добавление фирменной символики компании на страницах входа и панели доступа](active-directory-add-company-branding.md)
* [Просмотр отчетов о доступе и использовании](active-directory-view-access-usage-reports.md)

<!--Image references-->
[1]: ./media/active-directory-get-started-premium/MOLSEmail.png
[2]: ./media/active-directory-get-started-premium/MOLSAccountProfile.png
[3]: ./media/active-directory-get-started-premium/MOLSThankYou.png
[4]: ./media/active-directory-get-started-premium/AADEmail.png
[5]: ./media/active-directory-get-started-premium/SignUppage.png
[6]: ./media/active-directory-get-started-premium/Subscriptionspage.png
[7]: ./media/active-directory-get-started-premium/Premiuminportal.png
[8]: ./media/active-directory-get-started-premium/Premiuminportal_large.png
[9]: ./media/active-directory-get-started-premium/Signuppage_oops.png
[10]: ./media/active-directory-get-started-premium/contosolicenseplan.png
[11]: ./media/active-directory-get-started-premium/Assignlicensespicker.png
[12]: ./media/active-directory-get-started-premium/Usagelocation.png



<!--HONumber=Dec16_HO2-->


