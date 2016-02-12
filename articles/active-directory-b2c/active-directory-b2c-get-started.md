<properties
	pageTitle="Предварительная версия Azure Active Directory B2C: создание клиента Azure Active Directory B2C | Microsoft Azure"
	description="Раздел о том, как создать клиент Azure Active Directory B2C"
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
	ms.date="01/28/2016"
	ms.author="swkrish"/>

# Предварительная версия Azure Active Directory B2C: создание клиента Azure AD B2C

Чтобы начать использовать Azure Active Directory (AD) B2C, выполните 3 шага, описанные ниже.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Шаг 1. Оформление подписки Azure

При наличии подписки Azure перейти к следующему шагу. Если у вас ее нет, зарегистрируйтесь для оформления [подписки Azure](../active-directory/sign-up-organization.md) и получите доступ к Azure AD B2C.

> [AZURE.NOTE]
Сейчас предварительную версию Azure AD B2C можно использовать бесплатно, но с ограничениями (до 50 000 пользователей на один клиент). Подписка Azure необходима для доступа к [классическому порталу Azure](http://manage.windowsazure.com/).

## Шаг 2. Создание клиента Azure AD B2C

Чтобы создать клиент Azure AD B2C, выполните указанные ниже действия. Сейчас функции B2C нельзя включить в существующих каталогах, если они у вас есть.

1. Войдите на [классический портал Azure](https://manage.windowsazure.com/) под именем администратора подписки. Это та же рабочая или учебная учетная запись либо учетная запись Майкрософт, которая использовалась для регистрации в Azure.
2. Щелкните **Создать** > **Службы приложений** > **Active Directory** > **Каталог** > **Настраиваемое создание**.

    ![Создание клиента](./media/active-directory-b2c-get-started/new-directory.png)

3. Выберите **имя**, **доменное имя** и **страну или регион** для клиента.
4. Установите флажок с надписью **Это каталог B2C**.
5. Щелкните флажок, чтобы завершить действие.

    ![Создание клиента B2C](./media/active-directory-b2c-get-started/create-b2c-directory.png)

6. Ваш клиент создан и должен отобразиться в расширении Active Directory. Кроме того, теперь вы — глобальный администратор клиента. При необходимости можно добавить других глобальных администраторов.

    > [AZURE.IMPORTANT]
    Создание клиента может занять до двух минут. Если во время создания клиента вы столкнулись с проблемами, ознакомьтесь с рекомендациями в этой [статье](active-directory-b2c-support-create-directory.md).

## Шаг 3. Переход к колонке функций B2C на портале Azure

1. Перейдите к расширению Active Directory в области навигации слева.
2. Найдите свой клиент на вкладке **Каталог** и щелкните его.
3. Откройте вкладку **Настройка**.
4. Щелкните ссылку **Параметры управления B2C** в разделе **Администрирование B2C**.

    ![Создание клиента B2C](./media/active-directory-b2c-get-started/b2c-directory-configure-tab.png)

4. В новом окне или на новой вкладке браузера откроется портал Azure с колонкой функций B2C.

    > [AZURE.IMPORTANT]
    Известна проблема, из-за которой эта страница загружается неправильно (при небольшом количестве клиентов). Чтобы устранить ее, попробуйте обновить окно браузера. Если это не поможет, обратитесь в службу поддержки.

5. Закрепите эту колонку (см. правый верхний угол) на начальной панели для удобного доступа.

    ![Колонка функций B2C](./media/active-directory-b2c-get-started/b2c-features-blade.png)

    > [AZURE.NOTE]
    На [классическом портале Azure](https://manage.windowsazure.com/) можно управлять пользователями, группами, конфигурацией самостоятельного сброса пароля и фирменной символикой компании для клиента.

## Дальнейшие действия

Перейдите к этапу [регистрации приложения с помощью Azure AD B2C и создания приложения быстрого запуска](active-directory-b2c-app-registration.md).

<!---HONumber=AcomDC_0204_2016-->