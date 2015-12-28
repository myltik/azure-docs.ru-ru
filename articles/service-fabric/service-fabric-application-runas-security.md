<properties
   pageTitle="Общее сведения о политиках безопасности запуска от имени приложения Service Fabric | Microsoft Azure"
   description="Общие сведения о запуске приложения Service Fabric с использованием системных и локальных учетных записей безопасности, включая точку SetupEntry, где приложению перед запуском необходимо выполнить привилегированную операцию."
   services="service-fabric"
   documentationCenter=".net"
   authors="msfussell"
   manager="timlt"
   editor="bscholl"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="11/24/2015"
   ms.author="mfussell"/>

# Запуск от имени: запуск приложения Service Fabric с использованием различных разрешений безопасности
Service Fabric предоставляет возможность защиты приложений, работающих в кластере с разными учетными записями, а именно — "Запуск от имени". Кроме того, она защищает ресурсы, используемые приложениями с учетной записью пользователя, такие как файлы, каталоги и сертификаты.

По умолчанию приложения Service Fabric выполняются под учетной записью, используемой процессом Fabric.exe. Она также позволяет запускать приложения под учетной записью локального пользователя, указанной в манифесте приложения. Функция запуска от имени поддерживает следующие типы учетных записей: **LocalUser**, **NetworkService**, **LocalService** и **LocalSystem**.

> [AZURE.NOTE]Учетные записи доменов поддерживаются в развертываниях Windows Server, где доступна служба Active Directory.

Группы пользователей можно определить и создать в том случае, если в группу можно добавить одного или несколько пользователей для совместного управления. Это особенно полезно, если для разных точек входа службы существует несколько пользователей и они должны иметь определенные общие права, доступные на уровне группы.

## Задание политики запуска от имени для точки входа SetupEntryPoint

Согласно описанию в [модели приложения](service-fabric-application-model.md), **SetupEntryPoint** — это привилегированная точка входа, которая запускается с теми же учетными данными, что и Service Fabric службы (обычно это учетная запись *Network*), перед тем, как будут запущены любые другие точки входа. Исполняемый файл, указанный в точке входа **EntryPoint**, обычно является узлом службы, запускаемым на длительный срок, поэтому наличие отдельной точки входа позволяет избежать запуска узла службы с расширенными правами в течение долгого срока. Исполняемый файл, указанный в точке входа **EntryPoint**, запускается после успешного выхода из точки **SetupEntryPoint**. Возникающий вследствие этого процесс отслеживается и перезапускается (снова начиная с точки входа **SetupEntryPoint**), даже если произошло непредвиденное завершение его работы или сбой.

Ниже приведен простой пример манифеста службы с отображением SetupEntryPoint и главной точки EntryPoint для службы.

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

### Настройка политики запуска от имени

Настроив службу для наличия точки SetupEntryPoint, можно изменить разрешения безопасности, с которыми она выполняется в манифесте приложения. В приведенном ниже примере показано, как настроить службу для запуска с правами учетной записи администратора.

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

Сначала создайте раздел **Principals** и именем пользователя, например SetupAdminUser. Это означает, что пользователь является членом системной группы "Администраторы".

Далее в разделе **ServiceManifestImport** настройте политику для применения этого субъекта к точке **SetupEntryPoint**. Так Service Fabric узнает, что при запуске файла MySetup.bat следует использовать функцию запуска от имени с полномочиями администратора. Учитывая, что вы *не* применили политику к главной точке входа, код в MyServiceHost.exe будет запущен с системной учетной записью NetworkService, которая является учетной записью по умолчанию, с которой запускаются все точки входа службы.

Теперь добавим файл MySetup.bat в проект Visual Studio, чтобы проверить права администратора. В Visual Studio щелкните правой кнопкой мыши проект службы и добавьте новый файл MySetup.bat. Далее необходимо убедиться, что этот файл включен в пакет службы. Чтобы проверить, что файл MySetup.bat включен в пакет, выберите файл, щелкните правой кнопкой мыши для вывода контекстного меню, выберите пункт "Свойства" в диалоговом окне свойств и убедитесь, что для параметра **Копировать в выходной каталог** задано значение **Копировать, если новее**. Это показано на приведенном ниже снимке экрана.

![Свойство CopyToOutput в Visual Studio для пакетного файла SetupEntryPoint][Image1]

Теперь откройте файл MySetup.bat и добавьте следующие команды.

~~~
REM Set a system environment variable. This requires administrator privilege
setx -m TestVariable "MyValue"
echo System TestVariable set to > test.txt
echo %TestVariable% >> test.txt

REM To delete this system variable us
REM REG delete "HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Session Manager\Environment" /v TestVariable /f
~~~

Затем постройте и разверните решение в кластере локальной разработки. После запуска службы, как показано в обозревателе Service Fabric, можно двумя способами увидеть успешное выполнение MySetup.bat. Откройте командную строку PowerShell и введите следующую команду.

~~~
PS C:\ [Environment]::GetEnvironmentVariable("TestVariable","Machine") MyValue
~~~

В обозревателе Service Fabric запомните имя узла, где была развернута и запущена служба, например Node 1, и перейдите к рабочей папке экземпляра приложения, чтобы найти файл out.txt, в котором отображается значение **TestVariable**. Например, если служба была развернута на узле Node 2, можно перейти по этому пути для MyApplicationType.

~~~
C:\SfDevCluster\Data\_App\Node.2\MyApplicationType_App\work\out.txt
~~~

##  Запуск команд PowerShell из точки SetupEntryPoint
Чтобы запустить PowerShell из точки **SetupEntryPoint**, запустите PowerShell.exe в пакетном файле, указывающем на файл PowerShell. Сначала добавьте файл PowerShell в проект службы, например MySetup.ps1. Не забудьте задать свойство *Копировать, если новее*, чтобы этот файл также входил в состав пакета службы. В приведенном ниже примере показан образец пакетного файла для запуска файла PowerShell файл с именем MySetup.ps1, который задает системную переменную среды с именем *TestVariable*.

Файл MySetup.bat для запуска файла PowerShell.

~~~
powershell.exe -ExecutionPolicy Bypass -Command ".\MySetup.ps1"
~~~

В файле PowerShell добавьте следующую команду, чтобы задать системную переменную среды.

```
[Environment]::SetEnvironmentVariable("TestVariable", "MyValue", "Machine")
[Environment]::GetEnvironmentVariable("TestVariable","Machine") > out.txt
```

## Применение политики запуска от имени к службам 
Ранее вы узнали, как применить политику запуска от имени к точке SetupEntryPoint. Давайте более подробно рассмотрим процесс создания разных субъектов, которые могут применяться в качестве политик служб.

### Создание локальных групп пользователей
Группы пользователей можно определить и создать в том случае, если в группу можно добавить одного или несколько пользователей. Это особенно полезно, если для разных точек входа службы существует несколько пользователей и они должны иметь определенные общие права, доступные на уровне группы. В следующем примере показана локальная группа с именем **LocalAdminGroup** и правами администратора. Два пользователя — Customer1 и Customer2 — становятся членами локальной группы.

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
Можно создать локального пользователя, используемого для защиты службы в приложении. Если в разделе "Principals" манифеста приложения указан тип учетной записи LocalUser, Service Fabric создает учетные записи локальных пользователей на компьютерах, где развернуто приложение. Имена этих учетных записей по умолчанию не совпадают с именами в манифесте приложения (например, "Customer3" в примере ниже). Они создаются автоматически и имеют случайные пароли.

~~~
<Principals>
  <Users>
     <User Name="Customer3" AccountType="LocalUser" />
  </Users>
</Principals>
~~~
 
<!-- If an application requires that the user account and password be same on all machines (e.g. to enable NTLM authentication), the cluster manifest must set NTLMAuthenticationEnabled to true and also specify an NTLMAuthenticationPasswordSecret that will be used to generate the same password across all machines.

<Section Name="Hosting">
      <Parameter Name="EndpointProviderEnabled" Value="true"/>
      <Parameter Name="NTLMAuthenticationEnabled" Value="true"/>
      <Parameter Name="NTLMAuthenticationPassworkSecret" Value="******" IsEncrypted="true"/>
 </Section>
-->

## Назначение политик пакетам кода службы
В разделе **RunAsPolicy** **ServiceManifestImport** указывается учетная запись из раздела "Principals", которая должна применяться для запуска пакета кода, и связываются пакеты кода из манифеста службы с учетными записями пользователей в разделе "Principals". Можно использовать этот параметр для точек входа "Setup" или "Main" либо указать "All" для применения к обоим типам. В приведенном ниже примере показаны различные примененные политики.

~~~
<Policies>
<RunAsPolicy CodePackageRef="Code" UserRef="LocalAdmin" EntryPointType="Setup"/>
<RunAsPolicy CodePackageRef="Code" UserRef="Customer3" EntryPointType="Main"/>
</Policies>
~~~

Если параметр **EntryPointType** не указан, по умолчанию используется EntryPointType="Main". Указание **SetupEntryPoint** особенно полезно, когда требуется выполнить определенную операцию установки с высокими привилегиями с использованием системной учетной записи, тогда как фактический код службы может выполняться под менее привилегированной учетной записью.

### Применение политики по умолчанию ко всем пакетам кода службы
Раздел **DefaultRunAsPolicy** необходим для указания учетной записи пользователя по умолчанию для всех пакетов кода, не имеющего конкретного определенного раздела **RunAsPolicy**. Если большую часть пакетов кода, указанных в используемых приложением манифестах служб, требуется запустить с применением одной и той же учетной записи пользователя запуска от имени, в приложении можно определить только политику запуска от имени по умолчанию с этой учетной записью пользователя вместо указания **RunAsPolicy** для каждого пакета кода. Например, в следующем примере указывается, что если в пакете кода не указано **RunAsPolicy**, пакет кода должен запускаться под учетной записью MyDefaultAccount, указанной в разделе "Principals".

~~~
<Policies>
  <DefaultRunAsPolicy UserRef="MyDefaultAccount"/>
</Policies>
~~~

## Назначение SecurityAccessPolicy для конечных точек HTTP и HTTPS
Если политика запуска от имени применяется к службе и манифест служб объявляет ресурсы конечной точки с помощью протокола HTTP, необходимо указать **SecurityAccessPolicy**, чтобы гарантировать, что порты, назначенные этим конечным точкам, правильно вносились в списки ACL для учетной записи запуска от имени, используемой для запуска службы. В противном случае http.sys не получит доступа к службе и ошибки вызовов с клиента будут завершаться ошибками. В следующем примере учетная запись Customer3 применяется к конечной точке с именем *ServiceEndpointName* и предоставляет ей права полного доступа.

~~~
<Policies>
   <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" />
   <!--SecurityAccessPolicy is needed if RunAsPolicy is defined and the Endpoint is http -->
   <SecurityAccessPolicy ResourceRef="EndpointName" PrincipalRef="Customer1" />
</Policies>
~~~

Для конечной точки HTTPS потребуется дополнительно указать имя сертификата, возвращаемого клиенту с помощью **EndpointBindingPolicy**, где сертификат определен в разделе сертификатов в манифесте приложения.

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
В следующем манифесте приложения показано множество различных параметров, описанных выше.

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

<!---HONumber=AcomDC_1217_2015-->