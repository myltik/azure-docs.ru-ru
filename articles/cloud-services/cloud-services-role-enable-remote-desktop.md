<properties 
pageTitle="Включение подключения к удаленному рабочему столу для роли в облачных службах Azure" 
description="Настройка приложения в облачной службе Azure для удаленного подключения." 
services="cloud-services" 
documentationCenter="" 
authors="sbtron" 
manager="timlt" 
editor=""/>
<tags 
ms.service="cloud-services" 
ms.workload="tbd" 
ms.tgt_pltfrm="na" 
ms.devlang="na" 
ms.topic="article" 
ms.date="10/14/2015" 
ms.author="saurabh"/>

# Включение подключения к удаленному рабочему столу для роли в облачных службах Azure

>[AZURE.SELECTOR]
- [Azure classic portal](cloud-services-role-enable-remote-desktop.md)
- [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)
- [Visual Studio](https://msdn.microsoft.com/library/gg443832.aspx)


С помощью удаленного рабочего стола обеспечивается доступ к рабочему столу экземпляра, работающего в Azure. Подключение к удаленному рабочему столу позволяет диагностировать и устранять неполадки выполняющегося приложения.

Во время разработки можно разрешить подключения к удаленному рабочему столу для роли, включив модули удаленного рабочего стола в определение службы или включив удаленный рабочий стол с помощью расширения удаленного рабочего стола. Рекомендуется использовать расширение удаленного рабочего стола, так как удаленный рабочий стол можно включить даже после развертывания приложения без необходимости повторного развертывания приложения.


## Настройка удаленного рабочего стола в портале
В портале используется подход с расширением удаленного рабочего стола, поэтому удаленный рабочий стол можно включить даже после развертывания приложения. На странице **Настройка** облачной службы можно включить удаленный рабочий стол, изменить учетную запись локального администратора для подключения к виртуальным машинам, выбрать сертификат для проверки подлинности, а также установить срок его действия.


1. Щелкните **Облачные службы**, затем щелкните имя облачной службы, а после этого **Настройка**.

2. Щелкните **Удаленный доступ**.
    
    ![Удаленный доступ к облачным службам](./media/cloud-services-role-enable-remote-desktop/CloudServices_Remote.png)
    
    > [AZURE.WARNING]Если включить удаленный рабочий стол и нажать кнопку «ОК» (флажок), все экземпляры роли будут перезапущены. Если в роли установлен сертификат для шифрования пароля, перезапуск не производится. Чтобы не выполнять перезапуск, [загрузите сертификат для облачной службы](cloud-services-how-to-create-deploy/#how-to-upload-a-certificate-for-a-cloud-service) и вернитесь в диалоговое окно.
    

3. В разделе **Роли** выберите роль, которую требуется обновить, или щелкните **Все**, чтобы задать все роли.

4. Внесите любые из следующих изменений:
    
    - Чтобы включить удаленный рабочий стол, установите флажок **Включить удаленный рабочий стол**. Если доступ к удаленному рабочему столу не требуется, снимите этот флажок.
    
    - Создайте учетную запись, которая будет использоваться в подключениях к удаленному рабочему столу в экземплярах роли.
    
    - Обновите пароль существующей учетной записи.
    
    - Выберите сертификат, который будет использоваться для аутентификации: новый или переданный с помощью раздела **Отправка** на странице **Сертификаты**.
    
    - Измените срок действия для конфигурации удаленного рабочего стола.

5. По завершении обновления настроек нажмите кнопку **OK** (флажок).


## Удаленное подключение к экземплярам ролей
После включения удаленного рабочего стола для роли можно удаленно подключаться к экземпляру роли с помощью различных средств.

Подключение к экземпляру роли в портале:
    
  1.   Щелкните **Экземпляры**, чтобы открыть страницу **Экземпляры**.
  2.   Выберите экземпляр роли, где настроен удаленный рабочий стол.
  3.   Нажмите **Подключить** и следуйте инструкциям для открытия рабочего стола. 
  4.   Нажмите **Открыть**, а затем **Подключить**, чтобы установить подключение к удаленному рабочему столу. 


### Удаленное подключение к экземпляру роли с помощью Visual Studio

В обозревателе сервера Visual Studio выполните следующие действия:

1. Разверните узел **Azure\\Cloud Services\\[имя облачной службы]**.
2. Разверните узел **Промежуточные** или **Рабочие**.
3. Разверните нужную роль.
4. Щелкните правой кнопкой мыши по одному из экземпляров роли, выберите **Подключиться с помощью удаленного рабочего стола...**, а затем введите имя пользователя и пароль. 

![Удаленный рабочий стол обозревателя сервера](./media/cloud-services-role-enable-remote-desktop/ServerExplorer_RemoteDesktop.png)


### Получение RDP-файла с помощью PowerShell
RDP-файл можно получить с помощью командлета [Get-AzureRemoteDesktopFile](https://msdn.microsoft.com/library/azure/dn495261.aspx). Затем с помощью этого файла вы сможете подключаться к облачной службе по протоколу удаленного рабочего стола.

### Загрузка RDP-файла через API REST управления службами программным способом
Вы можете загрузить RDP-файл с помощью операции REST [Загрузить RDP-файл](https://msdn.microsoft.com/library/jj157183.aspx).



## Настройка удаленного рабочего стола в файле определения службы

Этот метод позволяет включить удаленный рабочий стол для приложения во время разработки. Этот подход требует, чтобы зашифрованные пароли хранились в файле конфигурации службы. При этом любые изменения конфигурации удаленного рабочего стола потребуют повторного развертывания приложения. Если вы хотите избежать этих недостатков, следует использовать подход с расширением удаленного рабочего стола, который описан выше.

Для [включения подключения к удаленному рабочему столу](https://msdn.microsoft.com/library/gg443832.aspx) с помощью файла определения службы можно воспользоваться Visual Studio. Следующие шаги описывают изменения, которые необходимо произвести в файлах модели службы, чтобы включить удаленный рабочий стол. Visual Studio автоматически выполнит эти изменения при публикации.

### Настройка подключения в модели службы 
Используйте элемент **Imports** для импорта модулей **RemoteAccess** и **RemoteForwarder** в файле [ServiceDefinition.csdef](cloud-services-model-and-package.md#csdef).

Файл определения службы должен быть таким, как показано в примере ниже, с добавленным элементом `<Imports>`.

```xml
<ServiceDefinition name="<name-of-cloud-service>" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2013-03.2.0">
    <WebRole name="WebRole1" vmsize="Small">
        <Sites>
            <Site name="Web">
                <Bindings>
                    <Binding name="Endpoint1" endpointName="Endpoint1" />
                </Bindings>
            </Site>
        </Sites>
        <Endpoints>
            <InputEndpoint name="Endpoint1" protocol="http" port="80" />
        </Endpoints>
        <Imports>
            <Import moduleName="Diagnostics" />
            <Import moduleName="RemoteAccess" />
            <Import moduleName="RemoteForwarder" />
        </Imports>
    </WebRole>
</ServiceDefinition>
```
Файл [ServiceConfiguration.cscfg](cloud-services-model-and-package.md#cscfg) должен быть таким, как показано в следующем примере, с добавлением элементов `<ConfigurationSettings>` и `<Certificates>`. Указанный сертификат необходимо [загрузить в облачную службу](cloud-services-how-to-create-deploy/#how-to-upload-a-certificate-for-a-cloud-service).

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceConfiguration serviceName="<name-of-cloud-service>" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="3" osVersion="*" schemaVersion="2013-03.2.0">
    <Role name="WebRole1">
        <Instances count="2" />
        <ConfigurationSettings>
            <Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.Enabled" value="true" />
            <Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.AccountUsername" value="[name-of-user-account]" />
            <Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.AccountEncryptedPassword" value="[base-64-encrypted-user-password]" />
            <Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.AccountExpiration" value="[certificate-expiration]" />
            <Setting name="Microsoft.WindowsAzure.Plugins.RemoteForwarder.Enabled" value="true" />
        </ConfigurationSettings>
        <Certificates>
            <Certificate name="Microsoft.WindowsAzure.Plugins.RemoteAccess.PasswordEncryption" thumbprint="[certificate-thumbprint]" thumbprintAlgorithm="sha1" />
        </Certificates>
    </Role>
</ServiceConfiguration>
```


## Дополнительные ресурсы

[Настройка облачных служб](cloud-services-how-to-configure.md)

<!---HONumber=AcomDC_1203_2015-->