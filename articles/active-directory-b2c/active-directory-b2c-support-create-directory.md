<properties
	pageTitle="Azure Active Directory: справочная статья ";Создание каталога"; | Microsoft Azure"
	description="Создание каталога Azure Active Directory или Azure Active Directory B2C: проблемы и решения"
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

# Создание каталога Azure AD или Azure AD B2C: проблемы и решения

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Создание каталога Azure AD

Если с первого раза создать каталог Azure AD не получится, повторите попытку. Если проблема не исчезнет, обратитесь в службу поддержки.

## Создание каталога Azure AD B2C (предварительная версия)

Во время [создания каталога Azure AD B2C](active-directory-b2c-get-started) могут возникнуть проблемы, о которых нам известно.

- Если каталог Azure AD B2C не отображается в списке каталогов, повторите попытку.
- Если каталог Azure AD B2C не отображается в списке каталогов, но появляется сообщение об ошибке "Не удалось создать каталог B2C contosob2c. Дополнительные сведения см. по этой [ссылке](http://go.microsoft.com/fwlink/?LinkID=624192&clcid=0x409)", выполните одно из следующих действий:
    - Перейдите к колонке функций B2C на портале предварительной версии Azure по этой ссылке [https://portal.azure.com/{directory}.onmicrosoft.com/?Microsoft\_AAD\_B2CAdmin=true#blade/Microsoft\_AAD\_B2CAdmin/TenantManagementBlade/id/{directory}.onmicrosoft.com](https://portal.azure.com/{directory}.onmicrosoft.com/?Microsoft_AAD_B2CAdmin=true#blade/Microsoft_AAD_B2CAdmin/TenantManagementBlade/id/{directory}.onmicrosoft.com). **{directory}** замените именем, которое использовалось при создании каталога (например, contosob2c), и войдите в учетную запись глобального администратора. Выберите команду **Включить функции B2C** в верхней части колонки и нажмите кнопку **ОК**. Теперь каталог Azure AD B2C должен быть готов к использованию.
	- Удалите только что созданный каталог и повторите попытку.
- Если ни одно из приведенных выше решений не помогло, обратитесь в службу поддержки. Инструкции по отправке запросов на техническую поддержку Azure AD B2C читайте в [этой статье](active-directory-b2c-support.md).

<!---HONumber=Sept15_HO3-->