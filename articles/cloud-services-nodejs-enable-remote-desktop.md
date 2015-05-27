<properties 
	pageTitle="Включение удаленного рабочего стола для облачных служб (Node.js)" 
	description="Узнайте, как обеспечить доступ к удаленному рабочему столу для виртуальных машин, на которых размещается приложение Node.js для Azure." 
	services="cloud-services" 
	documentationCenter="nodejs" 
	authors="MikeWasson" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
	ms.topic="article" 
	ms.date="02/24/2015" 
	ms.author="mwasson"/>






# Включение удаленного рабочего стола в Azure

Удаленный рабочий стол обеспечивает доступ к рабочему столу экземпляра роли в Azure. Его можно использовать для настройки виртуальной машины или устранения проблем с приложением.

> [AZURE.NOTE]Эта статья относится к приложениям узла, размещенным в качестве облачной службы Azure.


## Шаг 1. Использование Azure PowerShell для настройки в службе доступа к удаленному рабочему столу

Чтобы можно было использовать удаленный рабочий стол, необходимо настроить определения службы и конфигурации службы с именем пользователя, паролем и сертификатом для проверки подлинности с экземплярами ролей в облаке. [Azure PowerShell] включает командлет **Enable-AzureServiceProjectRemoteDesktop**, который выполняет эту настройку.

На компьютере, где создано определение службы, выполните следующие действия.

1.  В меню **Пуск** выберите **Azure PowerShell**.

	![Запись меню запуска Azure PowerShell][powershell-menu]

2.  Перейдите в каталог службы, введите **Enable-AzureServiceProjectRemoteDesktop**, а затем введите имя пользователя и пароль, которые должны использоваться при проверке подлинности с экземплярами ролей в облаке.

	![enable-azureserviceprojectremotedesktop][enable-rdp]

3.  Опубликуйте изменения конфигурации службы в облаке. В запросе **Azure PowerShell** введите **Publish-AzureServiceProject**.

	![publish-azureserviceproject][publish-project]

После завершения этих шагов экземпляры роли службы в облаке будут настроены для доступа к удаленному рабочему столу.

## Действие 2. Подключитесь к экземпляру роли

Теперь, когда развертывание выполнено и выполняется в Azure, можно подключиться к экземпляру роли.

1.  На [портале управления Azure] выберите **Облачные службы**, затем выберите службу, развернутую в приведенном выше действии 1.

	![портал управления azure][cloud-services]

2.  Щелкните **Экземпляры**, затем **Рабочая среда** или **Промежуточная среда** для просмотра экземпляров службы. Выберите экземпляр и щелкните **Подключиться** в нижней части страницы.

    ![Страница экземпляров][3]

2.  Когда вы нажимаете **Подключить**, веб-браузер предлагает сохранить RDP-файл. При использовании Internet Explorer щелкните **Открыть**.

    ![запрос на открытие или сохранение RDP-файла][4]

3.  При открытии файла появится следующий запрос безопасности:

    ![Запрос безопасности Windows][5]

4.  Нажмите **Подключить**, и появится запрос безопасности на ввод учетных данных для доступа к экземпляру. Введите пароль, созданный на [шаге 1][Step 1: Configure the service for Remote Desktop access using Azure PowerShell], а затем нажмите кнопку **ОК**.

    ![запрос имени пользователя/пароля][6]

После установления соединения подключение к удаленному рабочему столу покажет рабочий стол экземпляра в Azure. Вы успешно получили удаленный доступ к экземпляру и можете выполнять все необходимые задачи для управления приложением.

![Сеанс удаленного рабочего стола][7]

## Шаг 3. Настройка службы для отключения доступа к удаленному рабочему столу 

Если подключение удаленного рабочего стола к экземплярам ролей в облаке больше не требуется, отключите доступ к удаленному рабочему столу с помощью [Azure PowerShell].

1.  В меню **Пуск** выберите **Azure PowerShell**.

2.  Перейдите в каталог службы и введите **Disable-AzureServiceProjectRemoteDesktop**:

3.  Опубликуйте изменения конфигурации службы в облаке. В запросе **Azure PowerShell** введите **Publish-AzureServiceProject**.

## Дополнительные ресурсы

- [Удаленный доступ к экземплярам ролей в Azure] 
- [Использование удаленного рабочего стола с ролями Azure]


[Azure PowerShell]: http://go.microsoft.com/?linkid=9790229&clcid=0x409

[портале управления Azure]: http://manage.windowsazure.com
[powershell-menu]: ./media/cloud-services-nodejs-enable-remote-desktop/azure-powershell-menu.png
[publish-project]: ./media/cloud-services-nodejs-enable-remote-desktop/publish-rdp.png
[enable-rdp]: ./media/cloud-services-nodejs-enable-remote-desktop/enable-rdp.png
[cloud-services]: ./media/cloud-services-nodejs-enable-remote-desktop/cloud-services-remote.png
[3]: ./media/cloud-services-nodejs-enable-remote-desktop/cloud-service-instance.png
[4]: ./media/cloud-services-nodejs-enable-remote-desktop/rdp-open.png
[5]: ./media/cloud-services-nodejs-enable-remote-desktop/remote-desktop-12.png
[6]: ./media/cloud-services-nodejs-enable-remote-desktop/remote-desktop-13.png
[7]: ./media/cloud-services-nodejs-enable-remote-desktop/remote-desktop-14.png
[Удаленный доступ к экземплярам ролей в Azure]: http://msdn.microsoft.com/library/windowsazure/hh124107.aspx
[Использование удаленного рабочего стола с ролями Azure]: http://msdn.microsoft.com/library/windowsazure/gg443832.aspx

<!--HONumber=54-->