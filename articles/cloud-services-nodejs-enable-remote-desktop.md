<properties urlDisplayName="Enable Remote Desktop" pageTitle="Включение удаленного рабочего стола для облачных служб (Node.js) "metaKeywords =" удаленный доступ, удаленное подключение Azure Node.js, доступ к ВМ Azure Node.js, с доступом к виртуальной машине Azure Node.js" description="Узнайте, как обеспечить доступ к удаленному рабочему столу для виртуальных машин, на которых размещается приложение Node.js для Azure. " metaCanonical="" services="cloud-services" documentationCenter="nodejs" title="Enabling Remote Desktop in Azure" authors="larryfr" solutions="" manager="wpickett" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="wpickett" />






# Включение удаленного рабочего стола в Azure

Удаленный рабочий стол обеспечивает доступ к рабочему столу экземпляра роли в Azure. Его можно использовать для настройки виртуальной машины или устранения проблем с приложением.

<div class="dev-callout">
	<b>Примечание.</b>
	<p>Действия, приведенные в этой статье, применяются только к приложениям узла, размещенным в качестве облачной службы Azure.</p>
	</div>

Эта задача включает следующие шаги:

-   [Шаг 1. Настройка службы для доступа к удаленному рабочему столу с использованием Azure PowerShell]
-   [Шаг 2. Подключение к экземпляру роли]
-   [Шаг 3. Настройте службу, отключив доступ к удаленному рабочему столу
    с помощью Azure PowerShell]

## <a name="step1"> </a>Шаг 1. Настройка службы для доступа к удаленному рабочему столу с использованием Azure PowerShell

Чтобы можно было использовать удаленный рабочий стол, необходимо настроить определения службы и конфигурации службы с именем пользователя, паролем и сертификатом для проверки подлинности с экземплярами ролей в облаке. [Azure PowerShell] включает командлет **Enable-AzureServiceProjectRemoteDesktop**, который выполняет эту настройку.

На компьютере, где создано определение службы, выполните следующие действия.

1.  В меню **Пуск** выберите **Azure PowerShell**.

	![Azure PowerShell start menu entry][powershell-menu]

2.  Перейдите в каталог службы, введите **Enable-AzureServiceProjectRemoteDesktop**, а затем введите имя пользователя и пароль для проверки подлинности с экземплярами ролей в облаке.

	![enable-azureserviceprojectremotedesktop][enable-rdp]

3.  Опубликуйте изменения конфигурации службы в облаке. В запросе **Azure PowerShell** введите **Publish-AzureServiceProject**.

	![publish-azureserviceproject][publish-project]

После завершения этих шагов экземпляры роли службы в облаке будут настроены для доступа к удаленному рабочему столу.

## <a name="step2"> </a>Шаг 2. Подключение к экземпляру роли

Теперь, когда развертывание выполнено и выполняется в Azure, можно подключиться к экземпляру роли.

1.  На [портале управления Azure] выберите **Облачные службы** а затем выберите службу, развернутую на шаге 1 выше

	![azure management portal][cloud-services]

2.  Щелкните **Экземпляры** и нажмите кнопку **Рабочий** или **Промежуточный** для просмотра экземпляров службы. Выберите экземпляр и щелкните **Подключиться** в нижней части страницы.

    ![The instances page][3]

2.  Когда нажата кнопка **Подключить**, веб-браузер предложит сохранить RDP-файл. При использовании Internet Explorer щелкните **Открыть**.

    ![prompt to open or save the .rdp file][4]

3.  При открытии файла появится следующий запрос безопасности:

    ![Windows security prompt][5]

4.  Нажмите **Подключить** и в ответ на запрос безопасности введите учетные данные для доступа к экземпляру. Введите пароль, созданный на [шаге 1][Step 1: Configure the service for Remote Desktop access using Azure PowerShell], а затем нажмите кнопку **ОК**.

    ![username/password prompt][6]

После установления соединения подключение к удаленному рабочему столу покажет рабочий стол экземпляра в Azure. Вы успешно получили удаленный доступ к экземпляру и можете выполнять все необходимые задачи для управления приложением.

![Remote desktop session][7]

## <a name="step3"> </a>Шаг 3. Настройка службы для доступа к удаленному рабочему столу с использованием Azure PowerShell

Если подключение удаленного рабочего стола к экземплярам ролей в облаке больше не требуется, отключите доступ через удаленный рабочий стол с помощью [Azure PowerShell]

1.  В меню **Пуск** выберите **Azure PowerShell**.

2.  Перейдите в каталог службы и введите **Disable-AzureServiceProjectRemoteDesktop**:

3.  Опубликуйте изменения конфигурации службы в облаке. В запросе **Azure PowerShell** введите **Publish-AzureServiceProject**:

## Дополнительные ресурсы

- [Удаленный доступ к экземплярам ролей в Azure]
- [Использование удаленного рабочего стола с ролями Azure]

  [Шаг 1. Настройка службы для доступа к удаленному рабочему столу с использованием Azure PowerShell]: #step1
  [Шаг 2. Подключение к экземпляру роли]: #step2
  [Шаг 3. Настройте службу, отключив доступ к удаленному рабочему столу с помощью Azure PowerShell]: #step3
  [Azure PowerShell]: http://go.microsoft.com/?linkid=9790229&clcid=0x409

[Портал управления Azure]: http://manage.windowsazure.com
[powershell-menu]: ./media/cloud-services-nodejs-enable-remote-desktop/azure-powershell-menu.png
[publish-project]: ./media/cloud-services-nodejs-enable-remote-desktop/publish-rdp.png
[enable-rdp]: ./media/cloud-services-nodejs-enable-remote-desktop/enable-rdp.png
[cloud-services]: ./media/cloud-services-nodejs-enable-remote-desktop/cloud-services-remote.png
  [3]: ./media/cloud-services-nodejs-enable-remote-desktop/cloud-service-instance.png
  [4]: ./media/cloud-services-nodejs-enable-remote-desktop/rdp-open.png
  [5]: ./media/cloud-services-nodejs-enable-remote-desktop/remote-desktop-12.png
  [6]: ./media/cloud-services-nodejs-enable-remote-desktop/remote-desktop-13.png
  [7]: ./media/cloud-services-nodejs-enable-remote-desktop/remote-desktop-14.png
  
  [Получение удаленного доступа к экземплярам ролей в Azure]: http://msdn.microsoft.com/ru-ru/library/windowsazure/hh124107.aspx
  [Использование удаленного рабочего стола с ролями Azure]: http://msdn.microsoft.com/ru-ru/library/windowsazure/gg443832.aspx

<!--HONumber=35.2-->
