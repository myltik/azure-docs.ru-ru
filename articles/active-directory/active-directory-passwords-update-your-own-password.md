.<properties
	pageTitle="Руководство по регистрации для сброса пароля Azure AD | Microsoft Azure"
	description="Узнайте, как зарегистрироваться для сброса пароля в Azure Active Directory."
	services="active-directory"
	documentationCenter=""
	authors="asteen"
	manager="femila"
	editor="curtand"/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/21/2016"
	ms.author="asteen"/>

# Как пройти регистрацию для сброса пароля

> [AZURE.IMPORTANT]
**Почему я это вижу?** Если вы попали сюда, переходя по ссылке, скорее всего, ваш администратор настроил регистрацию пользователей для сброса пароля, используемого для доступа к приложению. Вам может понадобиться предоставить номер телефона или адрес электронной почты, либо ввести контрольный вопрос. Не волнуйтесь — мы не будем использовать эти сведения для рассылки спама; это нужно для защиты вашей учетной записи. Приведенные ниже инструкции помогут вам выполнить все необходимые действия.

Самый быстрый способ регистрации для сброса пароля — переход по адресу [http://aka.ms/ssprsetup](http://aka.ms/ssprsetup).

 1. Откройте страницу [http://aka.ms/ssprsetup](http://aka.ms/ssprsetup).
 2. Введите имя пользователя и пароль.
 3. Выберите нужный вариант, щелкнув **настроить сейчас**. В этом примере я продемонстрирую регистрацию **номера телефона для проверки подлинности**.

    ![][011]

 4. Выберите в раскрывающемся списке код страны и введите **полный номер телефона с кодом города**.

    ![][012] ![][013]

 5. Выберите один из вариантов: **Отправить SMS** или **Позвонить**. В нашем примере я выберу **Отправить SMS**. На мой телефон будет прислан код из 6 цифр. Подождите, пока на телефон придет сообщение с кодом.

    ![][014]

 6. Когда вы получите код, введите его в поле ввода и нажмите кнопку "Проверить".
 7. Вы увидите надпись **Спасибо**. Все готово! Теперь вы в любой момент сможете использовать зарегистрированный способ сброса пароля, открыв страницу [https://passwordreset.microsoftonline.com](https://passwordreset.microsoftonline.com).

    ![][015]

 >[AZURE.IMPORTANT] Если администратор разрешает зарегистрировать несколько методов, мы настоятельно рекомендуем зарегистрировать резервный метод на тот случай, если телефон или доступ к электронной почте будут утрачены.

## Другие способы обновления пароля
В этой статье описано, как выполнять такие стандартные действия, как смена и сброс пароля.

* [**Как изменить пароль в Office 365**](active-directory-passwords-update-methods.md#how-to-change-your-password-from-o365)
* [**Как изменить пароль на панели доступа**](active-directory-passwords-update-methods.md#how-to-change-your-password-from-the-access-panel)
* [**Как сбросить свой пароль**](active-directory-passwords-update-methods.md#how-to-reset-your-password)
* [**Как разблокировать учетную запись**](active-directory-passwords-update-methods.md#how-to-unlock-your-account)
* [**Распространенные проблемы и способы их устранения**](active-directory-passwords-update-methods.md#common-problems-and-their-solutions)

## Ссылки на документацию по сбросу паролей
Ниже приведены ссылки на все страницы документации по управлению паролями Azure AD.

* [**Как работает служба**](active-directory-passwords-how-it-works.md) — узнайте, из каких шести компонентов состоит служба и за что отвечает каждый из них.
* [**Приступая к работе**](active-directory-passwords-getting-started.md) — узнайте, как предоставить пользователям возможность сбрасывать и менять свои облачные и локальные пароли.
* [**Настройка**](active-directory-passwords-customize.md) — узнайте, как настроить оформление и функциональность службы в соответствии с потребностями организации.
* [**Рекомендации**](active-directory-passwords-best-practices.md) — узнайте, как быстро развернуть службу и эффективно управлять паролями в организации.
* [**Аналитика**](active-directory-passwords-get-insights.md) — узнайте об интегрированных функциях отчетности.
* [**Часто задаваемые вопросы**](active-directory-passwords-faq.md) — ознакомьтесь с ответами на часто задаваемые вопросы.
* [**Устранение неполадок**](active-directory-passwords-troubleshoot.md) — узнайте, как быстро устранять проблемы, связанные со службой.
* [**Дополнительные сведения**](active-directory-passwords-learn-more.md) — ознакомьтесь с технической стороной работы службы.



[001]: ./media/active-directory-passwords-update-your-own-password/001.jpg "Image_001.jpg"
[002]: ./media/active-directory-passwords-update-your-own-password/002.jpg "Image_002.jpg"
[003]: ./media/active-directory-passwords-update-your-own-password/003.jpg "Image_003.jpg"
[004]: ./media/active-directory-passwords-update-your-own-password/004.jpg "Image_004.jpg"
[005]: ./media/active-directory-passwords-update-your-own-password/005.jpg "Image_005.jpg"
[006]: ./media/active-directory-passwords-update-your-own-password/006.jpg "Image_006.jpg"
[007]: ./media/active-directory-passwords-update-your-own-password/007.jpg "Image_007.jpg"
[008]: ./media/active-directory-passwords-update-your-own-password/008.jpg "Image_008.jpg"
[009]: ./media/active-directory-passwords-update-your-own-password/009.jpg "Image_009.jpg"
[010]: ./media/active-directory-passwords-update-your-own-password/010.jpg "Image_010.jpg"
[011]: ./media/active-directory-passwords-update-your-own-password/011.jpg "Image_011.jpg"
[012]: ./media/active-directory-passwords-update-your-own-password/012.jpg "Image_012.jpg"
[013]: ./media/active-directory-passwords-update-your-own-password/013.jpg "Image_013.jpg"
[014]: ./media/active-directory-passwords-update-your-own-password/014.jpg "Image_014.jpg"
[015]: ./media/active-directory-passwords-update-your-own-password/015.jpg "Image_015.jpg"

<!---HONumber=AcomDC_0824_2016-->