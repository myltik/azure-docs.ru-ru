<properties 
	pageTitle="Включение удаленного рабочего стола для облачных служб (Node.js)" 
	description="Узнайте, как обеспечить доступ к удаленному рабочему столу для виртуальных машин, на которых размещается приложение Node.js для Azure." 
	services="cloud-services" 
	documentationCenter="nodejs" 
	authors="" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
	ms.topic="article" 
	ms.date="09/17/2014" 
	ms.author="wpickett"/>






# Включение удаленного рабочего стола в Azure

Удаленный рабочий стол обеспечивает доступ к рабочему столу экземпляра роли
в Azure. Подключение к нему можно использовать для
настройки виртуальной машины или устранения неполадок
в работе приложения.

> [AZURE.NOTE] Действия, приведенные в этой статье, применяются только к приложениям узла, размещенным в качестве облачной службы Azure.

Эта задача включает следующие шаги:

-   [Шаг 1. Настройка службы для доступа к удаленному рабочему столу с использованием Azure PowerShell]
-   [Шаг 2. Подключение к экземпляру роли]
-   [Шаг 3. Настройте службу, отключив доступ к удаленному рабочему столу
    с помощью Azure PowerShell]

## <a name="step1"> </a>Шаг 1. Настройка службы для доступа к удаленному рабочему столу с использованием Azure PowerShell

Чтобы использовать удаленный рабочий стол, необходимо настроить определение службы
и конфигурацию службы, используя имя пользователя, пароль и сертификат для
прохождения аутентификации с помощью экземпляров ролей в облаке. [В Azure PowerShell] есть командлет **Enable-AzureServiceProjectRemoteDesktop**, который
выполняет эту настройку.

Выполните следующие действия на компьютере, где создано определение службы
выполните следующие действия.

1.  В меню **Пуск** выберите **Azure PowerShell**.

	![Azure PowerShell start menu entry][powershell-menu]

2.  Измените каталог на каталог службы и введите
    **Enable-AzureServiceProjectRemoteDesktop**, а затем введите имя пользователя и 
    пароль для выполнения аутентификации с помощью экземпляров ролей в
    в облаке.

	![enable-azureserviceprojectremotedesktop][enable-rdp]

3.  Опубликуйте изменения конфигурации службы в облаке. В командной строке
    **Azure PowerShell** введите
    **Publish-AzureServiceProject**.

	![publish-azureserviceproject][publish-project]

После завершения этих шагов экземпляры роли службы
в облаке будут настроены для доступа к удаленному рабочему столу.

## <a name="step2"> </a>Шаг 2. Подключение к экземпляру роли

Теперь, когда развертывание выполнено и выполняется в Azure, можно подключиться к
в экземпляре роли.

1.  На [портале управления Azure] выберите **Облачные службы** а затем выберите службу, развернутую на шаге 1 выше

	![azure management portal][cloud-services]

2.  Щелкните **Экземпляры** и нажмите кнопку **Рабочий** или **Промежуточный** для просмотра экземпляров службы. Выберите экземпляр и щелкните **Подключиться** в нижней части страницы.

    ![The instances page][3]

2.  Когда нажата кнопка **Подключить**, веб-браузер предложит сохранить
    RDP-файл. При использовании Internet Explorer щелкните **Открыть**.

    ![prompt to open or save the .rdp file][4]

3.  При открытии файла появится следующий запрос безопасности:

    ![Windows security prompt][5]

4.  Щелкните **Подключить** и в ответ на запрос безопасности введите
    учетные данные для доступа к экземпляру. Введите пароль, созданный
    на [шаге 1][Шаг 1. Настройка службы для доступа к удаленному рабочему столу с использованием Azure PowerShell] и щелкните **OK**.

    ![username/password prompt][6]

При установлении подключения к удаленному рабочему столу отображается
удаленный рабочий стол экземпляра в Azure. Вы получили
удаленный доступ к экземпляру и теперь можете выполнять все необходимые вам задачи по
управлению своим приложением.

![Remote desktop session][7]

## <a name="step3"> </a>Шаг 3. Настройка службы для доступа к удаленному рабочему столу с использованием Azure PowerShell

Если больше нет необходимости в подключении удаленного рабочего стола к экземплярам
ролей в облаке, отключите удаленный рабочий стол с помощью [Azure PowerShell].

1.  В меню **Пуск** выберите **Azure PowerShell**.

2.  Измените каталог на каталог службы и введите
    **Disable-AzureServiceProjectRemoteDesktop**:

3.  Опубликуйте изменения конфигурации службы в облаке. В командной строке
    **Azure PowerShell** введите
    **Publish-AzureServiceProject**:

## Дополнительные ресурсы

- [Получение удаленного доступа к экземплярам ролей в Azure] 
- [Использование удаленного рабочего стола с ролями Azure]

  [Шаг 1. Настройка службы для доступа к удаленному рабочему столу с использованием Azure PowerShell]: #step1
  [Шаг 2. Подключение к экземпляру роли]: #step2
  [Шаг 3. Настройка службы для доступа к удаленному рабочему столу с использованием Azure PowerShell]: #step3
  [Шаг 3. Настройте службу, отключив доступ к удаленному рабочему столу с помощью Azure PowerShell]: #step3
  [В Azure PowerShell]: http://go.microsoft.com/?linkid=9790229&clcid=0x409
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
  
  [Получение удаленного доступа к экземплярам ролей в Azure]: http://msdn.microsoft.com/library/windowsazure/hh124107.aspx
  [Использование удаленного рабочего стола с ролями Azure]: http://msdn.microsoft.com/library/windowsazure/gg443832.aspx

<!--HONumber=45--> 
