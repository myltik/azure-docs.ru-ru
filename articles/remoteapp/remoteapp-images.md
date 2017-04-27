---
title: "Что входит в образы шаблонов Azure RemoteApp? | Документация Майкрософт"
description: "Узнайте об образах шаблона, которые поставляются с Azure RemoteApp."
services: remoteapp
documentationcenter: 
author: msmbaldwin
manager: mbaldwin
ms.assetid: 7f8442b2-81da-421e-a453-aa53ba2066b7
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/26/2017
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: 348306795fd3c8275b21e4ec6dceae408916bf72
ms.lasthandoff: 03/31/2017


---
# <a name="what-is-in-the-azure-remoteapp-template-images"></a>Что входит в образы шаблонов Azure RemoteApp?
> [!IMPORTANT]
> Мы выводим службу Azure RemoteApp из эксплуатации 31 августа 2017 года. Дополнительные сведения см. в [объявлении](https://go.microsoft.com/fwlink/?linkid=821148).
> 
> 

Подписка на Azure RemoteApp включает три образа шаблонов:

* Windows Server 2012
* Microsoft Office 365 профессиональный плюс (необходима подписка на Office 365);
* Microsoft Office 2013 профессиональный плюс (только пробная версия)

> [!IMPORTANT]
> Подписки Azure RemoteApp предоставляют доступ к программному обеспечению в образах, за исключением выпуска "Office 365 профессиональный плюс", требующего отдельной подписки, и выпуска Office 2013, который нельзя использовать в рабочей среде. Это означает, что вы можете представлять программы или приложения на образах шаблона своим пользователям для общего доступа. Например, если создать коллекцию, которая использует образ Windows Server 2012 R2, можно опубликовать System Center Endpoint Protection, чтобы пользователи могли получить к нему доступ через RemoteApp.
> 
> Дополнительные сведения см. в [статье о лицензировании RemoteApp](remoteapp-licensing.md). Сведения о лицензировании Office см. в статье [Использование Office с Azure RemoteApp](remoteapp-o365.md).
> 
> 

Ниже описывает содержимое каждого образа.

## <a name="windows-server-2012-r2--the-vanilla-image"></a>Windows Server 2012 R2 (стандартный образ)
Этот образ основан на операционной системе Microsoft Windows Server 2012 R2 для центров обработки данных, и в нем установлены следующие роли и функции, обеспечивающие соответствие требованиям образов шаблонов Azure RemoteApp:

* .NET Framework 4.5, 3.5.1, 3.5
* Возможности рабочего стола
* Службы рукописного ввода
* Media Foundation
* Узел сеансов удаленных рабочих столов
* Windows PowerShell 4.0
* Windows PowerShell ISE
* Поддержка WoW64

В этом образе уже установлены следующие приложения:

* Adobe Flash Player
* Microsoft Silverlight
* Microsoft System Center 2012 Endpoint Protection
* Проигрыватель Windows Media (Microsoft)

## <a name="microsoft-office-365-proplus-subscription-required"></a>Microsoft Office 365 профессиональный плюс (необходима подписка)
Приложение Office 365 пользуется наибольшим спросом, поэтому мы создали для него "специальный" образ.

Этот образ является расширенной версией стандартного образа, и в нем, помимо компонентов образа Windows Server 2012 R2, установлены следующие компоненты Microsoft Office 365 профессиональный плюс:

* Access
* Excel
* Lync
* OneNote
* OneDrive для бизнеса (обратите внимание, что использование агента синхронизации не поддерживается в Azure RemoteApp)
* Outlook
* PowerPoint
* Word
* Средства проверки правописания Microsoft Office

В образ также входят Visio Pro и Project Pro.

Также установлены следующие приложения.

* SQL Native Client
* Драйвер ODBC
* Клиент интеллектуального анализа данных SQL Server
* Клиент MasterDataServices
* Microsoft Publisher
* PowerQuery
* PowerMap

Полный набор функций приложений Office 365 профессиональный плюс доступен только пользователям плана Office 365 профессиональный плюс. Дополнительные сведения о планах подписки на Office 365 см. в статье [Параметры планов Office 365](http://technet.microsoft.com/library/office-365-plan-options.aspx). У вас еще остались вопросы? Ознакомьтесь со сведениями об [Office 365 + RemoteApp](remoteapp-o365.md). Ознакомьтесь также с новой статьей [Как использовать подписку Office 365 с удаленным приложением Azure RemoteApp](remoteapp-officesubscription.md).

Обратите внимание, что для каждого продукта (Office 365 профессиональный плюс, Visio Pro и Project Pro) требуются отдельные лицензии.

## <a name="microsoft-office-2013-professional-plus-trial-only"></a>Microsoft Office 2013 профессиональный плюс (только пробная версия)
Во время пробного периода вы можете тестировать службу с помощью образа Office 2013.

Этот образ является расширенной версией стандартного образа, и в нем, помимо компонентов образа Windows Server 2012 R2, установлены следующие компоненты Microsoft Office 2013 профессиональный плюс:

* Access
* Excel
* Lync
* OneNote
* OneDrive для бизнеса (обратите внимание, что использование агента синхронизации не поддерживается в Azure RemoteApp)
* Outlook
* PowerPoint
* Project
* Visio
* Word
* Средства проверки правописания Microsoft Office

> [!IMPORTANT]
> **Юридическая информация**. Этот образ не включает в себя лицензию Microsoft Office и *не может использоваться в производственных целях*. Образ Office 2013 профессиональный плюс предназначен исключительно для пробного использования. Если вам нужно использовать приложения Office в Azure RemoteApp в рабочей среде, используйте образ Office 365 профессиональный плюс. Дополнительные сведения о лицензировании Office см. в статье [Использование Office с Azure RemoteApp](remoteapp-o365.md).
> 
> 


