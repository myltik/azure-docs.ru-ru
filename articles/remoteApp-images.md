<properties title="What is in the RemoteApp template images?" pageTitle="Что входит в образы шаблонов RemoteApp?" description="Узнайте, шаблон изображений, включенные в удаленных приложений RemoteApp." metaKeywords="" services="" solutions="" documentationCenter="" authors="elizapo" manager="mbaldwin" />

<tags ms.service="remoteapp" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="12/11/2014" ms.author="elizapo" ms.manager="mbaldwin" />

#Что входит в образы шаблонов RemoteApp?

Подписка на Azure RemoteApp включает три образа шаблонов:


- Windows Server 2012
- Microsoft Office 365 профессиональный плюс (необходима подписка на Office 365)
- Microsoft Office 2013 профессиональный плюс (только пробная версия)

Независимо от используемого образа, предоставляя доступ к приложению пользователям, необходимо учитывать некоторые особенности лицензирования. Дополнительные сведения см. в статье [Подробные сведения о лицензировании RemoteApp](http://azure.microsoft.com/ru-ru/documentation/articles/remoteapp-licensing/).

Ниже описывает содержимое каждого образа.

##Windows Server 2012 R2  (стандартный образ)
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


##Microsoft Office 365 профессиональный плюс (необходима подписка)
Приложение Office 365 пользуется наибольшим спросом, поэтому мы создали для него "специальный" образ. 

Этот образ является расширенной версией стандартного образа, и в нем, помимо компонентов образа Windows Server 2012 R2, установлены следующие компоненты Microsoft Office 365 профессиональный плюс: 


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

Полный набор функций приложений Office 365 профессиональный плюс доступен только пользователям плана Office 365 профессиональный плюс. Дополнительные сведения о планах подписки на Office 365 см. в статье [Планы обслуживания Office 365](http://technet.microsoft.com/library/office-365-plan-options.aspx). Дополнительные сведения о лицензировании в RemoteApp см. в статье [Как работает лицензирование в Azure RemoteApp?](http://azure.microsoft.com/ru-ru/documentation/articles/remoteapp-licensing/) 

##Microsoft Office 2013 профессиональный плюс (только пробная версия)
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

**Важные юридические сведения** Этот образ не включает лицензию Microsoft Office и *не может использоваться в производственных целях*. Образ Office 2013 профессиональный плюс предназначен исключительно для пробного использования. Если вам нужно использовать приложения Office в Azure RemoteApp в рабочей среде, используйте образ Office 365 профессиональный плюс. Дополнительные сведения о лицензировании в RemoteApp см. в статье [Как работает лицензирование в Azure RemoteApp?](http://azure.microsoft.com/ru-ru/documentation/articles/remoteapp-licensing/)  

<!--HONumber=35.2-->
