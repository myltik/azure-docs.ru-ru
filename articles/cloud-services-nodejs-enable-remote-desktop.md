<properties linkid="dev-node-remotedesktop" urlDisplayName="Enable Remote Desktop" pageTitle="Enable remote desktop for cloud services (Node.js)" metaKeywords="Azure Node.js remote access, Azure Node.js remote connection, Azure Node.js VM access, Azure Node.js virtual machine access" description="Learn how to enable remote-desktop access for the virtual machines hosting your Azure Node.js application. " metaCanonical="" services="cloud-services" documentationCenter="nodejs" title="Enabling Remote Desktop in Azure" authors="larryfr" solutions="" manager="" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr"></tags>

# Включение удаленного рабочего стола в Azure

Удаленный рабочий стол обеспечивает доступ к рабочему столу экземпляра,
работающего в Azure. Можно использовать подключение к удаленному рабочему столу для
настройки виртуальной машины или устранения неполадок,
связанных с приложением.

<div class="dev-callout">
    <b>Примечание.</b>
    <p>Действия, приведенные в этой статье, применяются только к приложениям узла, размещенным в качестве облачной службы Azure.</p>
    </div>

Эта задача включает следующие шаги:

-   [Шаг 1: Настройка службы для доступа к удаленному рабочему столу с использованием Azure PowerShell][Шаг 1: Настройка службы для доступа к удаленному рабочему столу с использованием Azure PowerShell]
-   [Шаг 2: Подключение к экземпляру роли][Шаг 2: Подключение к экземпляру роли]
-   [Шаг 3: Настройка службы для доступа к удаленному рабочему столу с
    использованием Azure PowerShell][Шаг 3: Настройка службы для доступа к удаленному рабочему столу с
    использованием Azure PowerShell]

## <a name="step1"> </a>Шаг 1: Настройка службы для доступа к удаленному рабочему столу с использованием Azure PowerShell

Чтобы использовать удаленный рабочий стол, необходимо настроить определение службы
и конфигурацию службы с использованием имени пользователя, пароля и
сертификата для проверки подлинности с использованием экземпляров ролей в облаке. [Azure PowerShell][Azure PowerShell] содержит командлет **Enable-AzureServiceProjectRemoteDesktop**,
который выполняет эту конфигурацию.

Выполните следующие действия с компьютера, на котором было создано
определение службы.

1.  В меню **Пуск** выберите **Azure PowerShell**.

    ![Запись меню запуска Azure PowerShell][Запись меню запуска Azure PowerShell]

2.  Измените каталог на каталог службы, введите
    **Enable-AzureServiceProjectRemoteDesktop**, затем введите имя пользователя и
    пароль, которые будут использоваться для проверки подлинности
    в экземплярах служб в облаке.

    ![enable-azureserviceprojectremotedesktop][enable-azureserviceprojectremotedesktop]

3.  Опубликуйте изменения конфигурации службы в облаке. В окне
    **Azure PowerShell** введите
    **Publish-AzureServiceProject**.

    ![publish-azureserviceproject][publish-azureserviceproject]

После выполнения этих действий экземпляры роли службы
в облаке будут настроены для доступа к удаленному рабочему столу.

## <a name="step2"> </a>Шаг 2: Подключение к экземпляру роли

После выполнения развертывания в Azure вы можете подключиться
к экземпляру роли.

1.  На [портале управления Azure][портале управления Azure] выберите **Облачные службы**, затем выберите службу, развернутую в приведенном выше действии 1.

    ![портал управления azure][портал управления azure]

2.  Щелкните **Экземпляры**, затем **Рабочая среда** или **Промежуточная среда** для просмотра экземпляров службы. Выберите экземпляр и щелкните **Подключиться** в нижней части страницы.

    ![Страница экземпляров][Страница экземпляров]

3.  При нажатии пункта **Подключиться** в веб-браузере появляется запрос на сохранение
    RDP-файла. При использовании Internet Explorer щелкните **Открыть**.

    ![запрос на открытие или сохранение RDP-файла][запрос на открытие или сохранение RDP-файла]

4.  При открытии файла появится следующий запрос безопасности:

    ![Запрос безопасности Windows][Запрос безопасности Windows]

5.  Нажмите **Подключиться**, после чего появится запрос безопасности
    на ввод учетных данных для доступа к экземпляру. Введите пароль, который вы создали
    на [шаге 1][Шаг 1: Настройка службы для доступа к удаленному рабочему столу с использованием Azure PowerShell] и нажмите **OK**.

    ![запрос имени пользователя/пароля][запрос имени пользователя/пароля]

После установки подключения к удаленному рабочему столу отображается
удаленный рабочий стол экземпляра в Azure. Вы успешно получили удаленный
доступ к экземпляру и теперь можете выполнять любые необходимые задачи
для управления приложением.

![Сеанс удаленного рабочего стола][Сеанс удаленного рабочего стола]

## <a name="step3"> </a>Шаг 3: Настройка службы для доступа к удаленному рабочему столу с использованием Azure PowerShell

Если вам больше не требуется подключение к удаленным рабочим столам экземпляров
ролей в облаке, отключите доступ к удаленным рабочим столам с помощью [Azure PowerShell][Azure PowerShell].

1.  В меню **Пуск** выберите **Azure PowerShell**.

2.  Измените каталог на каталог службы и введите
    **Disable-AzureServiceProjectRemoteDesktop**.

3.  Опубликуйте изменения конфигурации службы в облаке. В окне
    **Azure PowerShell** введите
    **Publish-AzureServiceProject**.

## Дополнительные ресурсы

-   [Получение удаленного доступа к экземплярам ролей в Azure][Получение удаленного доступа к экземплярам ролей в Azure]
-   [Использование удаленного рабочего стола с ролями Azure][Использование удаленного рабочего стола с ролями Azure]

  [Шаг 1: Настройка службы для доступа к удаленному рабочему столу с использованием Azure PowerShell]: #step1
  [Шаг 2: Подключение к экземпляру роли]: #step2
  [Шаг 3: Настройка службы для доступа к удаленному рабочему столу с
  использованием Azure PowerShell]: #step3
  [Azure PowerShell]: http://go.microsoft.com/?linkid=9790229&clcid=0x409
  [Запись меню запуска Azure PowerShell]: ./media/cloud-services-nodejs-enable-remote-desktop/azure-powershell-menu.png
  [enable-azureserviceprojectremotedesktop]: ./media/cloud-services-nodejs-enable-remote-desktop/enable-rdp.png
  [publish-azureserviceproject]: ./media/cloud-services-nodejs-enable-remote-desktop/publish-rdp.png
  [портале управления Azure]: http://manage.windowsazure.com
  [портал управления azure]: ./media/cloud-services-nodejs-enable-remote-desktop/cloud-services-remote.png
  [Страница экземпляров]: ./media/cloud-services-nodejs-enable-remote-desktop/cloud-service-instance.png
  [запрос на открытие или сохранение RDP-файла]: ./media/cloud-services-nodejs-enable-remote-desktop/rdp-open.png
  [Запрос безопасности Windows]: ./media/cloud-services-nodejs-enable-remote-desktop/remote-desktop-12.png
  [запрос имени пользователя/пароля]: ./media/cloud-services-nodejs-enable-remote-desktop/remote-desktop-13.png
  [Сеанс удаленного рабочего стола]: ./media/cloud-services-nodejs-enable-remote-desktop/remote-desktop-14.png
  [Получение удаленного доступа к экземплярам ролей в Azure]: http://msdn.microsoft.com/ru-ru/library/windowsazure/hh124107.aspx
  [Использование удаленного рабочего стола с ролями Azure]: http://msdn.microsoft.com/ru-ru/library/windowsazure/gg443832.aspx
