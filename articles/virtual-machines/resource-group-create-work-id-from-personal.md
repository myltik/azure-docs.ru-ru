<properties
   pageTitle="Создание рабочего или школьного удостоверения в Azure Active Directory"
	description="Узнайте, как создать рабочее или школьное удостоверение на основе личного удостоверения для использования с шаблонами групп ресурсов в соответствии с ролями, помимо других функций."
	services="virtual-machines"
	documentationCenter=""
	authors="squillace"
	manager="timlt"
	editor=""/>

<tags
   ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="infrastructure"
	ms.date="09/01/2015"
	ms.author="rasquill"/>

# Создание рабочего или школьного удостоверения в Azure Active Directory

Если вы создали личную учетную запись Azure или имеете подписку на MSDN и создали учетную запись Azure для получения преимуществ кредитов MSDN Azure — при ее создании вы использовали удостоверение *учетной записи Microsoft*. Для работы многих полезных функций Azure — одним из примеров являются [шаблоны групп ресурсов](../resource-group-overview.md) — требуется рабочая или школьная учетная запись (удостоверение, управляемое Azure Active Directory).

К счастью, одна из лучших особенностей личной учетной записи Azure в том, что она поставляется с доменом Azure Active Directory по умолчанию, который позволяет создать новую рабочую или школьную учетную запись и использовать ее с функциями Azure, которые ее требуют.

> [AZURE.NOTE]Если имя пользователя и пароль предоставлены вам администратором, велика вероятность, что у вас уже есть рабочий или учебный идентификатор (также иногда называемый *идентификатором организации*). В этом случае можно сразу начать использовать учетную запись Azure для доступа к ресурсам Azure, которым она требуется. Если вы обнаружили, что не можете воспользоваться этими ресурсами, возможно, вам понадобится вернуться к этой статье. Дополнительную информацию см. в разделах [Учетные записи, которые можно использовать для входа](https://msdn.microsoft.com/library/azure/dn629581.aspx#BKMK_SignInAccounts) и [Как подписка Azure связана с Azure AD](https://msdn.microsoft.com/library/azure/dn629581.aspx#BKMK_SubRelationToDir).

Необходимые действия просты. Вам нужно найти на портале Azure свое подписанное удостоверение, выяснить свой домен Azure Active Directory по умолчанию и добавить в него нового пользователя в качестве соадминистратора Azure.

## Найдите каталог по умолчанию на портале Azure.

Войдите на [портал Azure](https://manage.windowsazure.com) с помощью личного удостоверения учетной записи Майкрософт. После входа прокрутите синюю панель слева и щелкните **ACTIVE DIRECTORY**.

![Azure Active Directory](./media/resource-group-create-work-id-from-personal/azureactivedirectorywidget.png)

Давайте начнем с поиска некоторых сведений о вашем удостоверении в Azure. В главной области должны отображаться сведения, подобные указанным ниже, показывающие, что у вас есть один каталог по умолчанию.

![](./media/resource-group-create-work-id-from-personal/defaultaadlisting.png)

Давайте выясним некоторые дополнительные сведения о нем. Щелкните строку каталога по умолчанию, чтобы открыть свойства этого каталога.

![](./media/resource-group-create-work-id-from-personal/defaultdirectorypage.png)

Чтобы просмотреть имя домена по умолчанию, щелкните **ДОМЕНЫ**.

![](./media/resource-group-create-work-id-from-personal/domainclicktoseeyourdefaultdomain.png)

Здесь можно увидеть, что при создании учетной записи Azure служба Azure Active Directory создала личный домен по умолчанию, представляющий собой значение хэша (число, формируемое по текстовой строке) вашего личного идентификатора, используемого как поддомен onmicrosoft.com. Это домен, в который теперь мы добавим нового пользователя.

## Создание нового пользователя в домене по умолчанию

Щелкните **ПОЛЬЗОВАТЕЛИ** и поищите среди них личную учетную запись. В столбце **ИСТОЧНИК** можно увидеть, что это **учетная запись Майкрософт**. Мы хотим создать пользователя в используемом по умолчанию домене Azure Active Directory .onmicrosoft.com.

![](./media/resource-group-create-work-id-from-personal/defaultdirectoryuserslisting.png)

Дальше мы будем выполнять [эти указания](https://technet.microsoft.com/library/hh967632.aspx#BKMK_1), но с использованием конкретного примера.

Внизу страницы щелкните **+ДОБАВИТЬ ПОЛЬЗОВАТЕЛЯ**. На открывшейся странице введите имя нового пользователя, а в поле **Тип пользователя** выберите **Новый пользователь в вашей организации**. В этом примере новое имя пользователя — `ahmet`. Выберите домен по умолчанию, обнаруженный ранее, в качестве домена для адреса электронной почты пользователя Ahmet. После завершения щелкните стрелку «Далее».

![](./media/resource-group-create-work-id-from-personal/addingauserwithdirectorydropdown.png)

Введите дополнительные сведения о пользователе Ahmet и обязательно выберите соответствующее значение **РОЛЬ**. Для гарантированной работы используйте роль **Глобальный администратор**, но если есть возможность использовать роль с меньшими полномочиями, это прекрасно. В этом примере используется роль **Пользователь**. (Дополнительные сведения об этих ролях см. [здесь](https://msdn.microsoft.com/library/azure/dn468213.aspx#BKMK_1).) Не включайте многофакторную проверку подлинности, если только не хотите использовать ее для каждой операции входа. После завершения щелкните стрелку «Далее».

![](./media/resource-group-create-work-id-from-personal/userprofileuseradmin.png)

Нажмите кнопку **создать**, чтобы сгенерировать и отобразить временный пароль для пользователя Ahmet.

![](./media/resource-group-create-work-id-from-personal/gettemporarypasswordforuser.png)

Скопируйте электронный адрес пользователя имя или используйте функцию **ОТПРАВИТЬ ПАРОЛЬ ПО ЭЛЕКТРОННОЙ ПОЧТЕ**. Вскоре вам понадобятся данные для входа.

![](./media/resource-group-create-work-id-from-personal/receivedtemporarypassworddialog.png)

Теперь вы должны видеть нового пользователя, в этом случае **Ahmet the Developer**, источником которого является Azure Active Directory. Вы создали новое рабочее или школьное удостоверение с помощью Azure Active Directory. Но это удостоверение еще не имеет разрешений на использование ресурсов Azure.

![](./media/resource-group-create-work-id-from-personal/defaultdirectoryusersaftercreate.png)

При использовании функции **ОТПРАВИТЬ ПАРОЛЬ ПО ЭЛЕКТРОННОЙ ПОЧТЕ** отправляется электронное сообщение следующего вида.

![](./media/resource-group-create-work-id-from-personal/emailreceivedfromnewusercreation.png)

## Добавление прав соадминистратора Azure для подписок

Теперь необходимо добавить нового пользователя в качестве соадминистратора, чтобы новый пользователь мог войти на портал управления. Чтобы сделать это, на панели внизу слева щелкните **Параметры**.

![](./media/resource-group-create-work-id-from-personal/thesettingswidget.png)

В области основных параметров вверху щелкните **АДМИНИСТРАТОРЫ**, после чего должно отобразиться только ваше личное удостоверение учетной записи Майкрософт. Внизу страницы щелкните **+ДОБАВИТЬ**, чтобы указать соадминистратора. Здесь нужно ввести адрес электронной почты нового пользователя, которого вы создали, с доменом по умолчанию. Как показано на следующем снимке экрана, рядом с пользователем каталога по умолчанию отображается зеленая галочка. Не забудьте выбрать все подписки, возможность администрирования которых следует добавить этому пользователю.

![](./media/resource-group-create-work-id-from-personal/addingnewuserascoadmin.png)

После завершения будут отображаться уже два пользователя с новым удостоверением соадминистратора. Выйдите из портала.

![](./media/resource-group-create-work-id-from-personal/newuseraddedascoadministrator.png)

## Вход и изменение пароля нового пользователя

Войдите в систему в качестве созданного вами нового пользователя.

![](./media/resource-group-create-work-id-from-personal/signinginwithnewuser.png)

Вам будет немедленно предложено создать новый пароль.

![](./media/resource-group-create-work-id-from-personal/mustupdateyourpassword.png)

Вы получите вознаграждение за свои старания, которое выглядит следующим образом.

![](./media/resource-group-create-work-id-from-personal/successtourdialog.png)


## Дальнейшие действия

С помощью нового удостоверения Azure Active Directory можно использовать [шаблоны групп ресурсов Azure](xplat-cli-azure-resource-manager.md).

     azure login
    info:    Executing command login
    warn:    Please note that currently you can login only via Microsoft organizational account or service principal. For instructions on how to set them up, please read http://aka.ms/Dhf67j.
    Username: ahmet@aztrainpassxxxxxoutlook.onmicrosoft.com
    Password: *********
    /info:    Added subscription Azure Pass
    info:    Setting subscription Azure Pass as default
    +
    info:    login command OK
    ralph@local:~$ azure config mode arm
    info:    New mode is arm
    ralph@local:~$ azure group list
    info:    Executing command group list
    + Listing resource groups
    info:    No matched resource groups were found
    info:    group list command OK
    ralph@local:~$ azure group create newgroup westus
    info:    Executing command group create
    + Getting resource group newgroup
    + Creating resource group newgroup
    info:    Created resource group newgroup
    data:    Id:                  /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/newgroup
    data:    Name:                newgroup
    data:    Location:            westus
    data:    Provisioning State:  Succeeded
    data:    Tags:
    data:
    info:    group create command OK

<!---HONumber=September15_HO1-->