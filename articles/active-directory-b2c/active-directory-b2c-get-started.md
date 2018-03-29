---
title: Создание клиента Azure Active Directory B2C | Документация Майкрософт
description: Инструкции по созданию клиента Azure Active Directory B2C
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 06/07/2017
ms.author: davidmu
ms.openlocfilehash: 56e0ae7454e86911c894da88b5aa8ccc03a08af3
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/23/2018
---
# <a name="create-an-azure-active-directory-b2c-tenant-in-the-azure-portal"></a>Создание клиента Azure Active Directory B2C на портале Azure

Из этого краткого руководства вы узнаете, как создать клиент Microsoft Azure Active Directory (Azure AD) B2C за несколько минут. По завершении у вас будет клиент B2C (каталог) для регистрации приложений B2C.

## <a name="prerequisites"></a>предварительным требованиям

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="log-in-to-azure"></a>Вход в Azure

Войдите на [портал Azure](https://portal.azure.com/).

## <a name="create-an-azure-ad-b2c-tenant"></a>Создание клиента Azure AD B2C

Возможности B2C невозможно включить в имеющихся клиентах. Нужно создать клиент Azure AD B2C.

[!INCLUDE [active-directory-b2c-create-tenant](../../includes/active-directory-b2c-create-tenant.md)]

Поздравляем! Вы создали клиент Azure Active Directory B2C. Теперь вы — глобальный администратор клиента. При необходимости можно добавить других глобальных администраторов. Чтобы переключиться к новому клиенту, щелкните *ссылку для управления новым клиентом*.

![Ссылка для управления новым клиентом](./media/active-directory-b2c-get-started/manage-new-b2c-tenant-link.png)

> [!IMPORTANT]
> Если вы планируете использовать B2C клиент для рабочего приложения, см. статью, в которой [сравниваются рабочая версия и предварительная версия клиента B2C](active-directory-b2c-reference-tenant-type.md). При удалении существующего клиента B2C и его повторном создании с тем же доменным именем могут возникнуть известные проблемы. Создавайте клиент B2C с другим доменным именем.
>
>

## <a name="link-your-tenant-to-your-subscription"></a>Привязка клиента к подписке

Клиент Azure AD B2C нужно связать с подпиской Azure, чтобы включить все возможности B2C и вносить плату за использование. Дополнительные сведения см. в [этой статье](active-directory-b2c-how-to-enable-billing.md). Если не связать клиент Azure AD B2C с подпиской Azure, некоторые возможности будут заблокированы, а в колонке параметров B2C отобразится предупреждение No Subscription linked to this B2C tenant or the Subscription needs your attention (С этим клиентом B2C не связана ни одна подписка, или подписка требует вашего внимания). Важно выполнить этот шаг, прежде чем переводить приложения в рабочую среду.

## <a name="easy-access-to-settings"></a>Простой доступ к параметрам

[!INCLUDE [active-directory-b2c-find-service-settings](../../includes/active-directory-b2c-find-service-settings.md)]

Чтобы получить доступ к колонке, можно также ввести `Azure AD B2C` в поле **Поиск ресурсов** в верхней части портала. В списке результатов выберите **Azure AD B2C** для доступа к колонке параметров B2C.

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Azure Active Directory B2C: регистрация приложения](active-directory-b2c-app-registration.md)