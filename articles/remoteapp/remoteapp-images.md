<properties
    pageTitle="Что входит в образы шаблонов Azure RemoteApp | Microsoft Azure"
    description="Узнайте об образах шаблона, которые поставляются с Azure RemoteApp."
    services="remoteapp"
    documentationCenter=""
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/02/2015"
    ms.author="elizapo" />

# Что входит в образы шаблонов Azure RemoteApp?

Подписка на Azure RemoteApp включает три образа шаблонов:


- Windows Server 2012
- Microsoft Office 365 профессиональный плюс (необходима подписка на Office 365)
- Microsoft Office 2013 профессиональный плюс (только пробная версия)

> [AZURE.IMPORTANT]Подписки Azure RemoteApp предоставляют доступ к программному обеспечению в образах, за исключением Office 365 профессиональный плюс, требующего отдельной подписки, и Office 2013, который нельзя использовать в рабочей среде. Это означает, что вы можете представлять программы или приложения на образах шаблона своим пользователям для общего доступа. Например, если создать коллекцию, которая использует образ Windows Server 2012 R2, можно опубликовать System Center Endpoint Protection, чтобы пользователи могли получить к нему доступ через RemoteApp.
>
> Дополнительные сведения см. в [статье о лицензировании RemoteApp](remoteapp-licensing.md). Сведения о лицензировании Office см. в статье [Использование Office 365 с Azure RemoteApp](remoteapp-o365.md).

Ниже описывает содержимое каждого образа.

## Windows Server 2012 R2 (стандартный образ)
Этот образ основан на операционной системе Microsoft Windows Server 2012 R2 для центров обработки данных, и в нем установлены следующие роли и функции, обеспечивающие соответствие требованиям образов шаблонов Azure RemoteApp:


- .NET Framework 4.5, 3.5.1, 3.5
- Возможности рабочего стола
- Службы рукописного ввода
- Media Foundation
- Узел сеансов удаленных рабочих столов
- Windows PowerShell 4.0
- Windows PowerShell ISE
- Поддержка WoW64

В этом образе уже установлены следующие приложения:

- Adobe Flash Player
- Microsoft Silverlight
- Microsoft System Center 2012 Endpoint Protection
- Проигрыватель Windows Media (Microsoft)


## Microsoft Office 365 профессиональный плюс (необходима подписка)
Приложение Office 365 пользуется наибольшим спросом, поэтому мы создали для него "специальный" образ.

Этот образ является расширенной версией стандартного образа, и в нем, помимо компонентов образа Windows Server 2012 R2, установлены следующие компоненты Microsoft Office 365 профессиональный плюс:


- Access
- Excel
- Lync
- OneNote
- OneDrive для бизнеса
- Outlook
- PowerPoint
- Word
- Средства проверки правописания Microsoft Office

В образ также входят Visio Pro и Project Pro.

Также установлены следующие приложения.

- SQL Native Client
- Драйвер ODBC
- Клиент интеллектуального анализа данных SQL Server
- Клиент MasterDataServices
- Microsoft Publisher
- PowerQuery
- PowerMap


Полный набор функций приложений Office 365 профессиональный плюс доступен только пользователям плана Office 365 профессиональный плюс. Дополнительные сведения о планах подписки на Office 365 см. в статье [Параметры планов Office 365](http://technet.microsoft.com/library/office-365-plan-options.aspx). У вас еще остались вопросы? Ознакомьтесь со сведениями об [Office 365 + RemoteApp](remoteapp-o365.md). Ознакомьтесь также с новой статьей [Использование подписки на Office 365 с Azure RemoteApp](remoteapp-officesubscription.md).

Обратите внимание, что для каждого продукта (Office 365 профессиональный плюс, Visio Pro и Project Pro) требуются отдельные лицензии.

## Microsoft Office 2013 профессиональный плюс (только пробная версия)
Во время пробного периода вы можете тестировать службу с помощью образа Office 2013.

Этот образ является расширенной версией стандартного образа, и в нем, помимо компонентов образа Windows Server 2012 R2, установлены следующие компоненты Microsoft Office 2013 профессиональный плюс:


- Access
- Excel
- Lync
- OneNote
- OneDrive для бизнеса
- Outlook
- PowerPoint
- Project
- Visio
- Word
- Средства проверки правописания Microsoft Office

> [AZURE.IMPORTANT]**Юридическая информация.** Этот образ не включает в себя лицензию Microsoft Office и *не может использоваться в производственных целях*. Образ Office 2013 профессиональный плюс предназначен исключительно для пробного использования. Если вам нужно использовать приложения Office в Azure RemoteApp в рабочей среде, используйте образ Office 365 профессиональный плюс. Дополнительные сведения о лицензировании Office см. в разделе [Использование Office 365 с Azure RemoteApp](remoteapp-o365.md)

<!---HONumber=AcomDC_1210_2015-->