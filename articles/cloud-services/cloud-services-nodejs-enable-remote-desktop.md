<properties 
	pageTitle="Включение удаленного рабочего стола для облачных служб (Node.js)" 
	description="Узнайте, как обеспечить доступ к удаленному рабочему столу для виртуальных машин, на которых размещается приложение Node.js для Azure." 
	services="cloud-services" 
	documentationCenter="nodejs" 
	authors="rmcmurray" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
	ms.topic="article" 
	ms.date="02/04/2016" 
	ms.author="robmcm"/>

# Включение удаленного рабочего стола в Azure

Удаленный рабочий стол обеспечивает доступ к рабочему столу экземпляра роли в Azure. Его можно использовать для настройки виртуальной машины или устранения проблем с приложением.

> [AZURE.NOTE] Эта статья относится к приложениям Node.js, размещенным в качестве облачной службы Azure.


## Предварительные требования

- Установите и настройте [Azure PowerShell](../powershell-install-configure.md).
- Развертывание приложения Node.js в облачной службе Azure. Дополнительную информацию см. в разделе [Построение и развертывание приложения Node.js в облачной службе Azure](cloud-services-nodejs-develop-deploy-app.md).


## Шаг 1. Использование Azure PowerShell для настройки в службе доступа к удаленному рабочему столу

Чтобы использовать удаленный рабочий стол, необходимо обновить определения службы Azure и конфигурации с именем пользователя, паролем и сертификатом.

На компьютере, на котором содержатся исходные файлы вашего приложения, сделайте следующее.

1. Запустите **Windows PowerShell** от имени администратора. (В меню **Пуск** или на **Начальном экране** найдите **Windows PowerShell**.)

2.  Перейдите в каталог, содержащий определения службы (.csdef) и файлы конфигурации службы (cscfg).

3. Введите следующий командлет PowerShell:

		Enable-AzureServiceProjectRemoteDesktop

4. Введите имя пользователя и пароль при запросе.

	![enable-azureserviceprojectremotedesktop][enable-rdp]

3.  Введите следующий командлет PowerShell, чтобы опубликовать изменения:

    	Publish-AzureServiceProject

	![publish-azureserviceproject][publish-project]

## Действие 2. Подключитесь к экземпляру роли

После публикации обновления определения службы, можно подключиться к экземпляру роли.

1.  На [классическом портале Azure] выберите **Облачные службы**, а затем нужную вам службу.

	![Классический портал Azure][cloud-services]

2.  Щелкните **Экземпляры**, затем **Рабочая среда** или **Промежуточная среда** для просмотра экземпляров службы. Выберите экземпляр и щелкните **Подключиться** в нижней части страницы.

    ![Страница экземпляров][3]

2.  Когда вы нажимаете **Подключить**, веб-браузер предлагает сохранить RDP-файл. Откройте этот файл. (Например, при использовании Internet Explorer щелкните **Открыть**).

    ![запрос на открытие или сохранение RDP-файла][4]

3.  При открытии файла появится следующий запрос безопасности:

    ![Запрос безопасности Windows][5]

4.  Нажмите **Подключить**, и появится запрос безопасности на ввод учетных данных для доступа к экземпляру. Введите пароль, созданный в [Действии 1][Действие 1. Настройка службы для доступа к удаленному рабочему столу с использованием Azure PowerShell], затем нажмите кнопку **ОК**.

    ![запрос имени пользователя/пароля][6]

После установления соединения подключение к удаленному рабочему столу покажет рабочий стол экземпляра в Azure.

![Сеанс удаленного рабочего стола][7]

## Шаг 3. Настройка службы для отключения доступа к удаленному рабочему столу 

Если подключение удаленного рабочего стола к экземплярам ролей в облаке больше не требуется, отключите доступ к удаленному рабочему столу с помощью [Azure PowerShell].

1.  Введите следующий командлет PowerShell:

    	Disable-AzureServiceProjectRemoteDesktop

2.  Введите следующий командлет PowerShell, чтобы опубликовать изменения:

    	Publish-AzureServiceProject

## Дополнительные ресурсы

- [Удаленный доступ к экземплярам ролей в Azure] 
- [Использование удаленного рабочего стола с ролями Azure]
- [Центр разработчика Node.js](/develop/nodejs/)

  [Azure PowerShell]: http://go.microsoft.com/?linkid=9790229&clcid=0x409

[классическом портале Azure]: http://manage.windowsazure.com
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
 

<!---HONumber=AcomDC_0218_2016-->