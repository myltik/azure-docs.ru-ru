<properties
	pageTitle="Azure Active Directory: справочный раздел ";Создание клиента"; | Microsoft Azure"
	description="Создание клиента Azure Active Directory или клиента Azure Active Directory B2C: проблемы и решения"
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
	ms.date="09/24/2015"
	ms.author="swkrish"/>

# Создание клиента Azure AD или клиента Azure AD B2C: проблемы и их решение

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Создание клиента Azure AD

Если вам не удается создать клиент Azure AD с первого раза, попробуйте сделать это еще раз. Если проблема не исчезнет, обратитесь в службу поддержки.

## Создание клиента Azure AD B2C (предварительная версия)

При [создании клиента Azure AD B2C](active-directory-b2c-get-started.md) вы можете столкнуться с рядом известных проблем.

- Если клиент Azure AD B2C не отображается в списке клиентов, повторите попытку.
- Клиент Azure AD B2C не отображается в списке клиентов, но появляется сообщение об ошибке "Не удалось завершить создание клиента B2C contosob2c". Дополнительные сведения см. по этой [ссылке](http://go.microsoft.com/fwlink/?LinkID=624192&clcid=0x409)", выполните одно из следующих действий:
    - Перейдите к колонке функций B2C на портале предварительной версии Azure по ссылке [https://portal.azure.com/{клиент}.onmicrosoft.com/?Microsoft\_AAD\_B2CAdmin=true#blade/Microsoft\_AAD\_B2CAdmin/TenantManagementBlade/id/{клиент}.onmicrosoft.com](https://portal.azure.com/{directory}.onmicrosoft.com/?Microsoft_AAD_B2CAdmin=true#blade/Microsoft_AAD_B2CAdmin/TenantManagementBlade/id/{tenant}.onmicrosoft.com). Замените **{клиент}** именем, которое использовалось при создании клиента (например, contosob2c), и войдите в качестве глобального администратора. Выберите команду **Включить функции B2C** в верхней части колонки и нажмите кнопку **ОК**. Теперь клиент Azure AD B2C должен быть готов к использованию.
	- Удалите только что созданный клиент и создайте его еще раз.
- Если ни одно из приведенных выше решений не помогло, обратитесь в службу поддержки. Инструкции по отправке запросов на техническую поддержку Azure AD B2C читайте в [этой статье](active-directory-b2c-support.md).

<!---HONumber=Oct15_HO3-->