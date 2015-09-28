<properties
	pageTitle="Предварительная версия Azure Active Directory B2C: создание каталога Azure Active Directory B2C | Microsoft Azure"
	description="Раздел о том, как создать каталог Azure Active Directory B2C."
	services="active-directory-b2c"
	documentationCenter=""
	authors="swkrish"
	manager="msmbaldwin"
	editor="curtand"/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/15/2015"
	ms.author="swkrish"/>

# Предварительная версия Azure Active Directory B2C: как создать каталог Azure AD B2C

Чтобы начать использовать Azure Active Directory (AD) B2C, выполните 3 шага, описанные ниже.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Шаг 1. Оформление подписки Azure

При наличии подписки Azure перейти к следующему шагу. Если у вас ее нет, зарегистрируйтесь для оформления [подписки Azure](sign-up-organization.md) и получите доступ к Azure AD B2C.

> [AZURE.NOTE]Сейчас предварительная версия Azure AD B2C предоставляется бесплатно, но с ограничениями (до 50 000 пользователей на каталог). Подписка Azure необходима для доступа к [порталу Azure](http://manage.windowsazure.com/).

## Шаг 2. Создание каталога Azure AD B2C

Чтобы создать новый каталог Azure AD B2C, выполните следующее. Сейчас функции B2C нельзя включить в существующих каталогах, если они у вас есть.

1. Войдите на [портал Azure](https://manage.windowsazure.com/) от имени администратора подписки. Это та же рабочая или учебная учетная запись либо учетная запись Майкрософт, которая использовалась для регистрации в Azure.
2. Щелкните **Создать** > **Службы приложений** > **Active Directory** > **Каталог** > **Настраиваемое создание**.

    ![Создание каталога](./media/active-directory-b2c-get-started/new-directory.png)

3. Выберите **имя**, **доменное имя** и **страну или регион** для каталога.
4. Установите флажок с надписью **Это каталог B2C**.
5. Щелкните флажок, чтобы завершить действие.

    ![Создание каталога B2C](./media/active-directory-b2c-get-started/create-b2c-directory.png)

6. Ваш каталог будет создан, и он появится в расширении Active Directory. Кроме того, вы стали глобальным администратором каталога. При необходимости можно добавить других глобальных администраторов.

    > [AZURE.IMPORTANT]Создание каталога может занять до двух минут. Если вы столкнулись с проблемами во время создания каталога, ознакомьтесь с рекомендациями в этой [статье](active-directory-b2c-support-create-directory.md).

## Шаг 3. Переход к колонке функций B2C на портале предварительной версии Azure

1. Перейдите к расширению Active Directory в области навигации слева.
2. Найдите свой каталог на вкладке **Каталог** и щелкните его.
3. Откройте вкладку **Настройка**.
4. Щелкните ссылку **Параметры управления B2C** в разделе **Администрирование B2C**.

    ![Создание каталога B2C](./media/active-directory-b2c-get-started/b2c-directory-configure-tab.png)

4. В новом окне или на вкладке браузера откроется [предварительная версия портала Azure](https://portal.azure.com/) с колонкой функций B2C.
5. Закрепите эту колонку (см. правый верхний угол) на начальной панели для удобного доступа.

    ![Колонка функций B2C](./media/active-directory-b2c-get-started/b2c-features-blade.png)

    > [AZURE.NOTE]На [портале Azure](https://manage.windowsazure.com/) можно управлять пользователями и группами, настраивать самостоятельный сброс пароля и фирменную символику компании для каталога.

## Дальнейшие действия

Перейдите к [регистрации приложения для использования Azure AD B2C и созданию приложения быстрого запуска](active-directory-b2c-app-registration.md).

<!---HONumber=Sept15_HO3-->