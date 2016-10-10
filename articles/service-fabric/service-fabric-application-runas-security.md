<properties
   pageTitle="Общее сведения о политиках безопасности приложений и служб в Service Fabric | Microsoft Azure"
   description="Общие сведения о запуске приложения Service Fabric с использованием системных и локальных учетных записей безопасности, включая точку SetupEntry, если приложению перед запуском необходимо выполнить привилегированную операцию."
   services="service-fabric"
   documentationCenter=".net"
   authors="msfussell"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/22/2016"
   ms.author="mfussell"/>

# Настройка политик безопасности для приложения
Платформа Azure Service Fabric помогает защищать приложения, работающие в кластере под разными учетными записями. Кроме того, платформа защищает ресурсы, используемые приложениями при развертывании с помощью учетной записи пользователя, — файлы, каталоги и сертификаты. Это позволяет изолировать друг от друга все приложения, выполняемые в общей среде выполнения.

По умолчанию приложения Service Fabric выполняются под учетной записью, используемой процессом Fabric.exe. Платформа также позволяет запускать приложения под учетной записью локального пользователя или локальной системы, указанной в манифесте приложения. Поддерживаются следующие типы учетных записей локальной системы: **LocalUser**, **NetworkService**, **LocalService** и **LocalSystem**.

 Если Service Fabric выполняется с использованием автономного установщика на сервере Windows Server, развернутом в центре обработки данных, можно использовать учетные записи домена Active Directory (AD).

Вы можете определять и создавать группы пользователей, в которые можно добавлять одного или нескольких пользователей для совместного управления. Это полезно, если для разных точек входа службы есть несколько пользователей и у них должны быть определенные общие права, доступные на уровне группы.

## Настройка политики для точки входа SetupEntryPoint

Как описано в [модели приложения](service-fabric-application-model.md), **SetupEntryPoint** — это привилегированная точка входа, которая выполняется с теми же учетными данными, что и Service Fabric (обычно это учетная запись *NetworkService*), до запуска других точек входа. Обычно для точки входа **EntryPoint** в качестве исполняемого файла указывается длительно выполняемый узел службы. Наличие отдельной точки входа позволяет избежать запуска исполняемого файла узла службы с высокими привилегиями на длительное время. Исполняемый файл, указанный в точке входа **EntryPoint**, запускается после успешного выхода из точки **SetupEntryPoint**. Созданный процесс отслеживается и перезапускается (снова начиная с точки входа **SetupEntryPoint**), если произойдет непредвиденное завершение его работы или сбой.

Ниже приведен простой пример манифеста службы с отображением привилегированной точки входа SetupEntryPoint и главной точки входа EntryPoint для службы.

~~~
<?xml version="1.0" encoding="utf-8" ?>
<ServiceManifest Name="MyServiceManifest" Version="SvcManifestVersion1" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Description>An example service manifest</Description>
  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="MyServiceType" />
  </ServiceTypes>
  <CodePackage Name="Code" Version="1.0.0">
    <SetupEntryPoint>
      <ExeHost>
        <Program>MySetup.bat</Program>
        <WorkingFolder>CodePackage</WorkingFolder>
      </ExeHost>
    </SetupEntryPoint>
    <EntryPoint>
      <ExeHost>
        <Program>MyServiceHost.exe</Program>
      </ExeHost>
    </EntryPoint>
  </CodePackage>
  <ConfigPackage Name="Config" Version="1.0.0" />
</ServiceManifest>
~~~

### Настройка политики с использованием локальной учетной записи

Настроив службу для использования точки SetupEntryPoint, можно изменить разрешения безопасности, с которыми она выполняется в манифесте приложения. В приведенном ниже примере показано, как настроить службу для выполнения с правами учетной записи администратора пользователей.

~~~
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyApplicationType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="MyServiceTypePkg" ServiceManifestVersion="1.0.0" />
      <ConfigOverrides />
      <Policies>
         <RunAsPolicy CodePackageRef="Code" UserRef="SetupAdminUser" EntryPointType="Setup" />
      </Policies>
   </ServiceManifestImport>
   <Principals>
      <Users>
         <User Name="SetupAdminUser">
            <MemberOf>
               <SystemGroup Name="Administrators" />
            </MemberOf>
         </User>
      </Users>
   </Principals>
</ApplicationManifest>
~~~

Сначала создайте раздел **Principals** с именем пользователя, например SetupAdminUser. Это означает, что пользователь является членом системной группы "Администраторы".

Затем в разделе **ServiceManifestImport** настройте политику для применения этого субъекта к точке **SetupEntryPoint**. Благодаря этому при выполнении файла **MySetup.bat** платформа Service Fabric будет использовать функцию запуска от имени с привилегиями администратора. Если вы *не* применили другую политику к главной точке входа, код из файла **MyServiceHost.exe** будет запущен от имени системной учетной записи **NetworkService**. Это учетная запись по умолчанию, с использованием которой запускаются все точки входа службы.

Теперь добавим файл MySetup.bat в проект Visual Studio, чтобы проверить привилегии администратора. В Visual Studio щелкните правой кнопкой мыши проект службы и добавьте новый файл MySetup.bat.

Затем убедитесь, что файл MySetup.bat включен в пакет службы. По умолчанию он не включен. Щелкните файл правой кнопкой мыши, чтобы открыть контекстное меню, и выберите **Свойства**. Убедитесь, что в диалоговом окне "Свойства" параметр **Копировать в выходной каталог** имеет значение **Копировать, если новее**. См. пример на следующем снимке экрана.

![Свойство CopyToOutput в Visual Studio для пакетного файла SetupEntryPoint][image1]

Теперь откройте файл MySetup.bat и добавьте следующие команды.

~~~
REM Set a system environment variable. This requires administrator privilege
setx -m TestVariable "MyValue"
echo System TestVariable set to > out.txt
echo %TestVariable% >> out.txt

REM To delete this system variable us
REM REG delete "HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Session Manager\Environment" /v TestVariable /f
~~~

Затем выполните сборку и развертывание решения в локальном кластере разработки. После запуска службы (отображено в обозревателе Service Fabric) вы можете убедиться в успешном выполнении файла MySetup.bat двумя способами. Откройте командную строку PowerShell и введите следующую команду.

~~~
PS C:\ [Environment]::GetEnvironmentVariable("TestVariable","Machine")
MyValue
~~~

Запишите имя узла с развернутой и запущенной службой в обозревателе Service Fabric (например Node 2). Затем перейдите в рабочую папку экземпляра приложения и найдите файл out.txt, в котором хранится значение **TestVariable**. Например, если служба была развернута на узле Node 2, для приложения **MyApplicationType** используйте такой путь:

~~~
C:\SfDevCluster\Data\_App\Node.2\MyApplicationType_App\work\out.txt
~~~

###  Настройка политики с использованием учетных записей локальной системы
Для выполнения скриптов запуска обычно лучше использовать учетную запись локальной системы, а не учетную запись администратора, которую мы применяли выше. Выполнение политики запуска от имени с учетными записями администраторов обычно не рекомендуется, так как по умолчанию включено управление доступом на уровне пользователей. В таких случаях **рекомендуется выполнять SetupEntryPoint с учетной записью LocalSystem, а не добавлять локального пользователя в группу администраторов**. В следующем примере показана настройка SetupEntryPoint для запуска с учетной записью LocalSystem.

~~~
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyApplicationType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="MyServiceTypePkg" ServiceManifestVersion="1.0.0" />
      <ConfigOverrides />
      <Policies>
         <RunAsPolicy CodePackageRef="Code" UserRef="SetupLocalSystem" EntryPointType="Setup" />
      </Policies>
   </ServiceManifestImport>
   <Principals>
      <Users>
         <User Name="SetupLocalSystem" AccountType="LocalSystem" />
      </Users>
   </Principals>
</ApplicationManifest>
~~~

##  Запуск команд PowerShell из точки входа SetupEntryPoint
Чтобы запустить PowerShell из точки входа **SetupEntryPoint**, запустите **PowerShell.exe** в пакетном файле, который указывает на файл PowerShell. Сначала добавьте файл PowerShell в проект службы, например **MySetup.ps1**. Не забудьте указать свойство *Копировать, если новее*, чтобы включить этот файл в состав пакета службы. В следующем примере показан пакетный файл для запуска файла PowerShell с именем MySetup.ps1, который задает системную переменную среды с именем **TestVariable**.


Файл MySetup.bat для запуска файла PowerShell.

~~~
powershell.exe -ExecutionPolicy Bypass -Command ".\MySetup.ps1"
~~~

В файле PowerShell добавьте следующую команду, чтобы задать системную переменную среды.

~~~
[Environment]::SetEnvironmentVariable("TestVariable", "MyValue", "Machine")
[Environment]::GetEnvironmentVariable("TestVariable","Machine") > out.txt
~~~

**Примечание.** По умолчанию пакетный файл ищет файлы в папке приложения с именем **work**. В этом примере нам нужно, чтобы пакетный файл MySetup.bat при запуске нашел файл MySetup.ps1 в той же папке, в которой расположен **пакета кода** приложения. Чтобы изменить эту папку, укажите рабочую папку, как показано ниже.

~~~
<SetupEntryPoint>
    <ExeHost>
    <Program>MySetup.bat</Program>
    <WorkingFolder>CodePackage</WorkingFolder>
    </ExeHost>
</SetupEntryPoint>
~~~

## Использование перенаправления консоли для локальной отладки
Иногда при отладке удобно просматривать вывод консоли, в которой выполняется скрипт. Для этого можно определить политику перенаправления консоли, которая записывает выходные данные в файл. Выходной файл сохраняется в папку приложения с именем **log** на том узле, где развертывается и выполняется приложение (сведения о том, как найти этот узел, см. в примере выше).

**Примечание. Никогда** не используйте политику перенаправления консоли для приложений в рабочей среде, так как она может повлиять на отработку отказа приложения. Используйте этот способ **ТОЛЬКО** для локальной разработки и отладки.

В приведенном ниже примере показано, как установить для перенаправления консоли значение FileRetentionCount.

~~~
<SetupEntryPoint>
    <ExeHost>
    <Program>MySetup.bat</Program>
    <WorkingFolder>CodePackage</WorkingFolder>
    <ConsoleRedirection FileRetentionCount="10"/>
    </ExeHost>
</SetupEntryPoint>
~~~

Если теперь изменить файл MySetup.ps1 для записи команды **Echo**, запись будет выполнена в выходной файл для отладки.

~~~
Echo "Test console redirection which writes to the application log folder on the node that the application is deployed to"
~~~

**Завершив отладку сценария, немедленно удалите эту политику перенаправления консоли.**

## Настройка политики для пакетов кода службы 
Выше мы описали, как применять политику запуска от имени к точке входа SetupEntryPoint. Теперь давайте подробнее рассмотрим процесс создания разных субъектов, которые могут применяться в качестве политик служб.

### Создание локальных групп пользователей
Вы можете определять и создавать группы пользователей, чтобы добавлять в них одного или нескольких пользователей. Это особенно полезно, если для разных точек входа службы существует несколько пользователей и они должны иметь определенные общие права, доступные на уровне группы. В следующем примере представлена локальная группа с именем **LocalAdminGroup** и привилегиями администратора. В нее входят два пользователя: Customer1 и Customer2.

~~~
<Principals>
 <Groups>
   <Group Name="LocalAdminGroup">
     <Membership>
       <SystemGroup Name="Administrators"/>
     </Membership>
   </Group>
 </Groups>
  <Users>
     <User Name="Customer1">
        <MemberOf>
           <Group NameRef="LocalAdminGroup" />
        </MemberOf>
     </User>
    <User Name="Customer2">
      <MemberOf>
        <Group NameRef="LocalAdminGroup" />
      </MemberOf>
    </User>
  </Users>
</Principals>
~~~

### Создание локальных пользователей
Можно создать локального пользователя, используемого для защиты службы в приложении. Если в разделе Principals манифеста приложения указан тип учетной записи **LocalUser**, платформа Service Fabric создает учетные записи локальных пользователей на компьютерах с развернутым приложением. По умолчанию эти учетные записи не соответствуют именам, указанным в манифесте приложения (например, Customer3 в примере ниже). Вместо этого они создаются динамически и имеют случайные пароли.

~~~
<Principals>
  <Users>
     <User Name="Customer3" AccountType="LocalUser" />
  </Users>
</Principals>
~~~

<!-- If an application requires that the user account and password be same on all machines (for example, to enable NTLM authentication), the cluster manifest must set NTLMAuthenticationEnabled to true. The cluster manifest must also specify an NTLMAuthenticationPasswordSecret that will be used to generate the same password across all machines.

<Section Name="Hosting">
      <Parameter Name="EndpointProviderEnabled" Value="true"/>
      <Parameter Name="NTLMAuthenticationEnabled" Value="true"/>
      <Parameter Name="NTLMAuthenticationPassworkSecret" Value="******" IsEncrypted="true"/>
 </Section>
-->

### Назначение политик пакетам кода службы
В разделе **RunAsPolicy** для **ServiceManifestImport** определяется учетная запись из раздела Principals, которая будет использоваться для выполнения пакета кода. Также это позволяет связать пакеты кода из манифеста службы с учетными записями пользователей в разделе Principals. Это можно сделать для точки входа Setup или Main. Также можно указать значение `All`, чтобы применить настройки к обеим точкам входа. Следующий пример демонстрирует применение разных политик.

~~~
<Policies>
<RunAsPolicy CodePackageRef="Code" UserRef="LocalAdmin" EntryPointType="Setup"/>
<RunAsPolicy CodePackageRef="Code" UserRef="Customer3" EntryPointType="Main"/>
</Policies>
~~~

Если параметр **EntryPointType** не указан, по умолчанию используется EntryPointType="Main". Значение **SetupEntryPoint** особенно полезно, если нужно выполнить установку с высокими привилегиями с использованием системной учетной записи. Фактический код может выполняться под учетной записью с более низкими привилегиями.

### Применение политики по умолчанию ко всем пакетам кода службы
С помощью раздела **DefaultRunAsPolicy** можно указать учетную запись пользователя по умолчанию для всех пакетов кода, для которых не определен раздел **RunAsPolicy**. Иногда большую часть пакетов кода, указанных в используемых приложением манифестах служб, нужно запускать от имени одной и той же учетной записи пользователя. В таком случае в приложении можно определить стандартную политику запуска от имени этой учетной записи пользователя. Следующий пример устанавливает такие правила: если для пакета кода не указана политика **RunAsPolicy**, он будет запускаться под учетной записью **MyDefaultAccount**, указанной в разделе Principals.

~~~
<Policies>
  <DefaultRunAsPolicy UserRef="MyDefaultAccount"/>
</Policies>
~~~
### Использование групп и пользователей домена Active Directory
Если Service Fabric установлена на Windows Server с помощью автономного установщика, вы можете запустить службу с учетными данными группы или пользователя Active Directory (AD). Примечание: речь идет о локальной службе Active Directory в домене, а не Azure Active Directory (AAD). С помощью пользователей или групп домена вы можете обращаться к другим ресурсам в домене (например, к общим файловым ресурсам), для которых предоставлены разрешения на доступ.

В следующем примере представлена учетная запись домена AD с именем *TestUser* и паролем, зашифрованным с помощью сертификата с именем *MyCert*. Можно использовать команду Powershell `Invoke-ServiceFabricEncryptText` для создания секретного зашифрованного текста. Этот процесс описан в статье [Управление секретами в приложениях Service Fabric](service-fabric-application-secret-management.md). Чтобы расшифровывать такой пароль, на локальном компьютере следует развернуть закрытый ключ сертификата по внештатному каналу (в Azure для этого используется Resource Manager). Тогда Service Fabric сможет расшифровать секретный код при развертывании пакета службы на компьютере и использовать его вместе с именем пользователя для проверки подлинности в службе AD.

~~~
<Principals>
  <Users>
    <User Name="TestUser" AccountType="DomainUser" AccountName="Domain\User" Password="[Put encrypted password here using MyCert certificate]" PasswordEncrypted="true" />
  </Users>
</Principals>
<Policies>
  <DefaultRunAsPolicy UserRef="TestUser" />
  <SecurityAccessPolicies>
    <SecurityAccessPolicy ResourceRef="MyCert" PrincipalRef="TestUser" GrantRights="Full" ResourceType="Certificate" />
  </SecurityAccessPolicies>
</Policies>
<Certificates>
~~~


## Назначение SecurityAccessPolicy для конечных точек HTTP и HTTPS
Если политика запуска от имени применяется к службе, а манифест служб объявляет ресурсы конечной точки с помощью протокола HTTP, вам нужно указать **SecurityAccessPolicy**. Это гарантирует, что порты, выделенные этим конечным точкам, будут правильно включены в списки управления доступом для учетной записи запуска от имени, используемой для запуска службы. В противном случае файл **http.sys** не получит доступа к службе, а вызовы от клиента будут завершаться ошибками. В следующем примере учетная запись Customer3 применяется к конечной точке с именем **ServiceEndpointName**, предоставляя ей права полного доступа.

~~~
<Policies>
   <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" />
   <!--SecurityAccessPolicy is needed if RunAsPolicy is defined and the Endpoint is http -->
   <SecurityAccessPolicy ResourceRef="EndpointName" PrincipalRef="Customer1" />
</Policies>
~~~

Для конечной точки HTTPS необходимо также указать имя сертификата, который будет возвращен клиенту. Это можно сделать с помощью политики **EndpointBindingPolicy** и сертификата, определенного в разделе Certificates в манифесте приложения.

~~~
<Policies>
   <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" />
  <!--SecurityAccessPolicy is needed if RunAsPolicy is defined and the Endpoint is http -->
   <SecurityAccessPolicy ResourceRef="EndpointName" PrincipalRef="Customer1" />
  <!--EndpointBindingPolicy is needed if the EndpointName is secured with https -->
  <EndpointBindingPolicy EndpointRef="EndpointName" CertificateRef="Cert1" />
</Policies
~~~


## Полный пример манифеста приложения
В следующем манифесте приложения показано, как можно использовать разные параметре.

~~~
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="Application3Type" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Parameters>
      <Parameter Name="Stateless1_InstanceCount" DefaultValue="-1" />
   </Parameters>
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="Stateless1Pkg" ServiceManifestVersion="1.0.0" />
      <ConfigOverrides />
      <Policies>
         <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" />
         <RunAsPolicy CodePackageRef="Code" UserRef="LocalAdmin" EntryPointType="Setup" />
        <!--SecurityAccessPolicy is needed if RunAsPolicy is defined and the Endpoint is http -->
         <SecurityAccessPolicy ResourceRef="EndpointName" PrincipalRef="Customer1" />
        <!--EndpointBindingPolicy is needed the EndpointName is secured with https -->
        <EndpointBindingPolicy EndpointRef="EndpointName" CertificateRef="Cert1" />
     </Policies>
   </ServiceManifestImport>
   <DefaultServices>
      <Service Name="Stateless1">
         <StatelessService ServiceTypeName="Stateless1Type" InstanceCount="[Stateless1_InstanceCount]">
            <SingletonPartition />
         </StatelessService>
      </Service>
   </DefaultServices>
   <Principals>
      <Groups>
         <Group Name="LocalAdminGroup">
            <Membership>
               <SystemGroup Name="Administrators" />
            </Membership>
         </Group>
      </Groups>
      <Users>
         <User Name="LocalAdmin">
            <MemberOf>
               <Group NameRef="LocalAdminGroup" />
            </MemberOf>
         </User>
         <!--Customer1 below create a local account that this service runs under -->
         <User Name="Customer1" />
         <User Name="MyDefaultAccount" AccountType="NetworkService" />
      </Users>
   </Principals>
   <Policies>
      <DefaultRunAsPolicy UserRef="LocalAdmin" />
   </Policies>
   <Certificates>
	 <EndpointCertificate Name="Cert1" X509FindValue="FF EE E0 TT JJ DD JJ EE EE XX 23 4T 66 "/>
  </Certificates>
</ApplicationManifest>
~~~


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Дальнейшие действия

* [Сведения о модели приложения](service-fabric-application-model.md)
* [Указание ресурсов в манифесте службы](service-fabric-service-manifest-resources.md)
* [Развертывание приложения](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png

<!---HONumber=AcomDC_0928_2016-->