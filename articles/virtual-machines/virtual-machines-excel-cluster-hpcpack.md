<properties
 pageTitle="Кластер пакета HPC для Excel и SOA | Microsoft Azure"
 description="Приступайте к работе с кластером пакета HPC для запуска рабочих нагрузок Excel и SOA с помощью модели развертывания диспетчера ресурсов."
 services="virtual-machines"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,hpc-pack"/>

<tags
 ms.service="virtual-machines"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-windows"
 ms.workload="big-compute"
 ms.date="11/11/2015"
 ms.author="danlep"/>

# Начало работы с кластером пакета HPC в Azure для запуска рабочих нагрузок Excel и SOA

В этой статье показано, как развернуть кластер пакета HPC в службах инфраструктуры Azure (IaaS) с помощью шаблона быстрого запуска Azure или сценария развертывания Azure PowerShell. Вы будете использовать образы виртуальных машин Azure Marketplace, разработанные для запуска рабочих нагрузок Microsoft Excel или серверно-ориентированной архитектуры (SOA), с пакетом HPC. Кластер можно использовать для запуска простых служб HPC Excel и SOA на локальном клиентском компьютере. Службы Excel HPC включают функцию разгрузки книг и пользовательские функции Excel (или UDF).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]Классическая модель развертывания.


На верхнем уровне следующей схемы показан создаваемый кластер пакета HPC.

![Кластер HPC с узлами, выполняющими рабочие нагрузки Excel][scenario]

## Предварительные требования

* **Клиентский компьютер** — вам потребуется клиентский компьютер под управлением Windows для запуска скрипта развертывания кластера Azure PowerShell (если выбран этот метод развертывания) и отправки примеров заданий Excel и SOA в кластер.

* **Подписка Azure ** — если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure](http://azure.microsoft.com/pricing/free-trial/).

* **Квота ядер** — может потребоваться увеличить квоту ядер, особенно если вы решите развернуть несколько узлов кластера с многоядерными виртуальными машинами. Если вы используете шаблон быстрого запуска Azure, помните, что квота ядер в диспетчере ресурсов указывается для одного региона Azure, а вам может потребоваться повысить квоту в определенном регионе. См. статью [Пределы, квоты и ограничения подписок на Azure](../azure-subscription-service-limits.md). Чтобы увеличить квоту, вы можете бесплатно [отправить запрос в службу поддержки](http://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/).


## Шаг 1. Настройка кластера пакета HPC в Azure

Мы покажем вам два способа настройки кластера: во-первых, с помощью шаблона быстрого запуска Azure и портала Azure, а во-вторых, с помощью сценария развертывания Azure PowerShell.


### Использование шаблона быстрого запуска
Шаблон быстрого запуска Azure позволяет быстро и без проблем развернуть кластер пакета HPC на портале Azure. Открыв шаблон на портале предварительной версии, вы увидите простой пользовательский интерфейс, где вводятся параметры кластера. Для этого выполните следующие действия.

1. Посетите [страницу шаблона создания кластера HPC на сайте GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster). При необходимости просмотрите сведения о шаблоне и исходном коде.

2. Щелкните **Развернуть в Azure**, чтобы начать развертывание шаблона на портале Azure.

    ![Развертывание шаблона в Azure][github]

3. На портале предварительной версии выполните следующие действия, чтобы ввести параметры шаблона кластера HPC.

    а. На странице **Редактирование шаблона** нажмите кнопку **Сохранить**.

    ![Сохранение шаблона][template]

    b. На странице **Параметры** введите значения параметров шаблона. (щелкнув значок рядом с любым параметром, можно получить справочные сведения). Примеры значений показаны на следующем экране. В этом примере создается новый кластер пакета HPC с именем *hpc01* в домене *hpc.local*, состоящий из головного узла и 2 вычислительных узлов. Вычислительные узлы будут созданы из образа виртуальной машины пакета HPC, включающего Microsoft Excel.

    ![Ввод параметров][parameters]

    >[AZURE.NOTE]Виртуальная машина головного узла будет создана автоматически из [последней версии образа Marketplace](http://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/) пакета HPC 2012 R2 на базе ОС Windows Server 2012 R2. В настоящий момент образ основан на пакете HPC 2012 R2 с обновлением 3.
    >
    >Вычислительные узлы будут созданы из последней версии образа выбранного семейства вычислительных узлов. Выберите параметр **ComputeNode** для последней версии вычислительного образа пакета HPC 2012 R2 с обновлением 3 общего назначения. Выберите параметр **ComputeNodeWithExcel** для последней версии образа вычислительного узла пакета HPC, который включает ознакомительную версию Microsoft Excel профессиональный плюс 2013. Если вам нужно развернуть кластер для общих сеансов SOA или разгрузки пользовательских функций Excel, выберите параметр **ComputeNode** (без установленного приложения Excel).
    >
    >При использовании **ComputeNodeWithExcel** для производственных рабочих нагрузок потребуется указать действительную лицензию Excel, чтобы активировать Excel на вычислительных узлах. В противном случае срок действия пробной версии Excel истечет через 30 дней, после чего выполнение книги Excel будет завершаться с ошибкой COMExeption (0x800AC472). В этом случае вы можете выполнить вход головного узла в clusrun "%ProgramFiles(x86)%\\Microsoft Office\\Office15\\OSPPREARM.exe" на всех вычислительных узлах Excel через консоль диспетчера кластеров HPC, чтобы продлить ознакомление с Excel еще на 30 дней. Максимальное время продления для льготного периода равно 2, после чего может потребоваться предоставить действительную лицензию на Microsoft Excel.

    c. Выберите подписку.

    г) Создайте новую группу ресурсов для кластера, например *hpc01RG*.

    д. Выберите расположение группы ресурсов, например Восток США.

    Е. Просмотрите условия на странице **Условия использования**. Если вы согласны с ними, нажмите кнопку **Купить**.

    ж. Завершив настройку значений для шаблона, нажмите кнопку **Создать**.

    ![Создание кластера][create]

3.	По завершении развертывания (как правило, это занимает около 30 минут) экспортируйте файл сертификата кластера с головного узла. На последующем этапе этот общедоступный сертификат будет импортирован на клиентский компьютер для обеспечения проверки подлинности на стороне сервера для безопасной привязки HTTP.

    а. Подключитесь к головному узлу с портала Azure с помощью удаленного рабочего стола.

     ![Подключение к головному узлу][connect]

    b. Используйте стандартные процедуры для экспорта сертификата головного узла (расположенного в папке Cert:\\LocalMachine\\My) без личного ключа с помощью диспетчера сертификатов. В этом примере экспортируйте *CN = hpc01.eastus.cloudapp.azure.com*.

    ![Экспорт сертификата][cert]

### Использование сценария развертывания IaaS пакета HPC

Сценарий развертывания IaaS пакета HPC обеспечивает еще один способ гибкого развертывания кластера пакета HPC. Он работает в режиме управления службами Azure (ASM), в то время как шаблон работает в режиме диспетчера ресурсов Azure (ARM). Кроме того, сценарий совместим с подпиской на службу Azure Global или Azure China.

**Дополнительные требования**

* **Azure PowerShell** — [установите и настройте Azure PowerShell](../powershell-install-configure.md) (версии 0.8.10 или более поздней) на клиентском компьютере.

* **Сценарий развертывания IaaS из пакета HPC** — скачайте и распакуйте последнюю версию сценария из [Центра загрузки Майкрософт](https://www.microsoft.com/download/details.aspx?id=44949). Проверьте версию сценария, запустив `New-HPCIaaSCluster.ps1 –Version`. Эта статья основана на сценарии версии 4.5.0 или выше.

**Создание файла конфигурации**

 Сценарий развертывания IaaS из пакета HPC использует входной XML-файл конфигурации, описывающий инфраструктуру кластера HPC. Чтобы развернуть кластер, состоящий из головного узла и 18 вычислительных узлов, созданных из образа вычислительного узла, который включает Microsoft Excel, вставьте значения для вашей среды в следующий образец файла конфигурации. Дополнительные сведения о файле конфигурации см. в файле Manual.rtf в папке сценария или статье [Создание кластера HPC с помощью сценария развертывания IaaS пакета HPC](virtual-machines-hpcpack-cluster-powershell-script.md).

```
<?xml version="1.0" encoding="utf-8"?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>MySubscription</SubscriptionName>
    <StorageAccount>hpc01</StorageAccount>
  </Subscription>
  <Location>West US</Location>
  <VNet>
    <VNetName>hpc-vnet01</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>
  <Domain>
    <DCOption>NewDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
    <DomainController>
      <VMName>HPCExcelDC01</VMName>
      <ServiceName>HPCExcelDC01</ServiceName>
      <VMSize>Medium</VMSize>
    </DomainController>
  </Domain>
   <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>HPCExcelHN01</VMName>
    <ServiceName>HPCExcelHN01</ServiceName>
    <VMSize>Large</VMSize>
    <EnableRESTAPI/>
    <EnableWebPortal/>
    <PostConfigScript>C:\tests\PostConfig.ps1</PostConfigScript>
  </HeadNode>
  <ComputeNodes>
    <VMNamePattern>HPCExcelCN%00%</VMNamePattern>
    <ServiceName>HPCExcelCN01</ServiceName>
    <VMSize>Medium</VMSize>
    <NodeCount>18</NodeCount>
    <ImageName>HPCPack2012R2_ComputeNodeWithExcel</ImageName>
  </ComputeNodes>
</IaaSClusterConfig>
```

**Примечания по файлу конфигурации**

* Параметр **VMName** головного узла **должен** точно совпадать с параметром **ServiceName**, иначе запуск задания SOA завершится сбоем.

* Обязательно укажите параметр **EnableWebPortal**, чтобы сертификат головного узла был создан и экспортирован.

* Сценарий PowerShell дополнительной настройки PostConfig.ps1 настраивает некоторые параметры головного узла, например строку подключения к хранилищу Azure, удаляет роль вычислительного узла с головного узла и подключает все узлы к сети после их развертывания. Далее представлен пример сценария.

```
    # add the HPC Pack powershell cmdlets
        Add-PSSnapin Microsoft.HPC

    # set the Azure storage connection string for the cluster
        Set-HpcClusterProperty -AzureStorageConnectionString 'DefaultEndpointsProtocol=https;AccountName=<yourstorageaccountname>;AccountKey=<yourstorageaccountkey>'

    # remove the compute node role for head node to make sure the Excel workbook won’t run on head node
        Get-HpcNode -GroupName HeadNodes | Set-HpcNodeState -State offline | Set-HpcNode -Role BrokerNode

    # total number of nodes in the deployment including the head node and compute nodes, which should match the number specified in the XML configuration file
        $TotalNumOfNodes = 19

        $ErrorActionPreference = 'SilentlyContinue'

    # bring nodes online when they are deployed until all nodes are online
        while ($true)
        {
          Get-HpcNode -State Offline | Set-HpcNodeState -State Online -Confirm:$false
          $OnlineNodes = @(Get-HpcNode -State Online)
          if ($OnlineNodes.Count -eq $TotalNumOfNodes)
          {
             break
          }
          sleep 60
        }
```

**Запуск сценария**

1. Откройте консоль PowerShell на клиентском компьютере от имени администратора.

2. Измените каталог на папку сценария (E:\\IaaSClusterScript в этом примере).

    ```
    cd E:\IaaSClusterScript
```

4. Выполните приведенную ниже команду, чтобы развернуть кластер пакета HPC. В этом примере предполагается, что путь к файлу конфигурации — E:\\HPCDemoConfig.xml.

    ```
    .\New-HpcIaaSCluster.ps1 –ConfigFile E:\HPCDemoConfig.xml –AdminUserName MyAdminName
```

Сценарий развертывания пакета HPC будет выполняться некоторое время. Одна из функций сценария — экспорт и загрузка сертификата кластера и его сохранение в папке документов текущего пользователя на клиентском компьютере. Сценарий создаст сообщение примерно следующего вида. На следующем этапе вы импортируете сертификат в соответствующее хранилище сертификатов.

```
You have enabled REST API or web portal on HPC Pack head node. Please import the following certificate in the Trusted Root Certification Authorities certificate store on the computer where you are submitting job or accessing the HPC web portal:
 C:\Users\hpcuser\Documents\HPCWebComponent_HPCExcelHN004_2015070716
2011.cer
```

## Шаг 2. Разгрузка книг Excel и выполнение пользовательских функций из локального клиента

### Разгрузка книг Excel

Выполните следующие действия, чтобы разгрузить книгу Excel для запуска в кластере пакета HPC в Azure. Для этого на клиентском компьютере уже должен быть установлен Excel 2010 или 2013.

1. Используйте один из методов из этапа 1 для развертывания кластера пакета HPC с образом вычислительного узла Excel. Получите файл сертификата кластера (.cer), а также имя пользователя и пароль для кластера.

2. На клиентском компьютере импортируйте сертификат кластера в папку Cert:\\CurrentUser\\Root.

3. Убедитесь, что установлен Excel. Создайте файл Excel.exe.config со следующим содержимым в одной папке с файлом Excel.exe на клиентском компьютере. Это гарантирует, что надстройка COM пакета HPC 2012 R2 для Excel успешно загрузится.

    ```
<?xml version="1.0"?>
<configuration>
    <startup useLegacyV2RuntimeActivationPolicy="true">
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.0"/>
    </startup>
</configuration>
```
4.	Скачайте полный [установочный пакет HPC 2012 R2 с обновлением 3](http://www.microsoft.com/download/details.aspx?id=49922) и установите клиент пакета HPC либо скачайте и установите [служебные программы клиента пакета HPC 2012 R2 с обновлением 3](https://www.microsoft.com/download/details.aspx?id=49923) и соответствующий дистрибутив Visual C++ 2010 для своего компьютера (версии [x64](http://www.microsoft.com/download/details.aspx?id=14632) или [x86](https://www.microsoft.com/download/details.aspx?id=5555)).

5.	В этом примере мы используем пример книги Excel под названием ConvertiblePricing\_Complete.xlsb, который можно скачать [здесь](https://www.microsoft.com/ru-RU/download/details.aspx?id=2939).

6.	Скопируйте книгу Excel в рабочую папку, например D:\\Excel\\Run.

7.	Откройте книгу Excel. На вкладке ленты **Разработка** выберите **Надстройки COM** и убедитесь, что надстройка COM пакета HPC для Excel успешно загружена, как показано на следующем снимке экрана.

    ![Надстройка Excel для пакета HPC][addin]

8.	Отредактируйте макрос VBA HPCControlMacros в Excel, изменив закомментированные строки, как показано в следующем сценарии. Подставьте соответствующие значения для вашей среды.

    ![Макрос Excel для пакета HPC][macro]

    ```
    'Private Const HPC_ClusterScheduler = "HEADNODE_NAME"
    Private Const HPC_ClusterScheduler = "hpc01.eastus.cloudapp.azure.com"

    'Private Const HPC_NetworkShare = "\\PATH\TO\SHARE\DIRECTORY"
    Private Const HPC_DependFiles = "D:\Excel\Upload\ConvertiblePricing_Complete.xlsb=ConvertiblePricing_Complete.xlsb"

    'HPCExcelClient.Initialize ActiveWorkbook
    HPCExcelClient.Initialize ActiveWorkbook, HPC_DependFiles

    'HPCWorkbookPath = HPC_NetworkShare & Application.PathSeparator & ActiveWorkbook.name
    HPCWorkbookPath = "ConvertiblePricing_Complete.xlsb"

    'HPCExcelClient.OpenSession headNode:=HPC_ClusterScheduler, remoteWorkbookPath:=HPCWorkbookPath
    HPCExcelClient.OpenSession headNode:=HPC_ClusterScheduler, remoteWorkbookPath:=HPCWorkbookPath, UserName:="hpc\azureuser", Password:="<YourPassword>"
```

9.	Скопируйте книгу Excel в папку отправки, например D:\\Excel\\Upload, как указано в константе HPC\_DependsFiles в макросе VBA.

10.	Нажмите кнопку **Кластер** на листе, чтобы запустить книгу в кластере IaaS Azure.

### Выполнение пользовательских функций Excel

Для выполнения пользовательских функций Excel выполните предыдущие этапы 1–3, чтобы настроить клиентский компьютер. Для пользовательских функций Excel не требуется устанавливать приложение Excel на вычислительных узлах, поэтому на этапе 1 можно выбрать обычный образ вычислительного узла, а не образ с Excel.

>[AZURE.NOTE]В диалоговом окне соединителя кластера Excel 2010 и 2013 действует ограничение в 34 знака. Если полное имя кластера имеет большую длину, например hpcexcelhn01.southeastasia.cloudapp.azure.com, оно не поместится в диалоговом окне. Обойти это можно, задав переменную, являющуюся глобальной для компьютера, например *CCP\_IAASHN*, с длинным именем кластера и ввести *% CCP\_IAASHN%* в диалоговом окне в качестве имени головного узла кластера. Обратите внимание, что для применения этого обходного пути для кластеров с обновлением 2 требуется установить исправление QFE KB3085833 для обновления 2 (скачать его можно [здесь](http://www.microsoft.com/ru-RU/download/details.aspx?id=48725)) для API сеанса SOA на клиентском компьютере.

После успешного развертывания кластера выполните следующие действия, чтобы запустить встроенные образцы пользовательских функций Excel. Для собственных пользовательских функций Excel см. эти [ресурсы](http://social.technet.microsoft.com/wiki/contents/articles/1198.windows-hpc-and-microsoft-excel-resources-for-building-cluster-ready-workbooks.aspx), чтобы создать XLL-файлы и развернуть их в кластере IaaS.

1.	Откройте новую книгу Excel. На вкладке ленты **Разработка** щелкните **Надстройки**. Затем в диалоговом окне нажмите кнопку **Обзор**, перейдите в папку %CCP\_HOME%Bin\\XLL32 и выберите пример ClusterUDF32.xll. Если ClusterUDF32 не существует на клиентском компьютере, его можно скопировать из папки %CCP\_HOME%Bin\\XLL32 на головном узле.

    ![Выбор пользовательской функции][udf]

2.	Выберите команды **Файл** > **Параметры** > **Дополнительные**. В разделе **Формулы** установите флажок **Разрешить использование вычислительных кластеров для расчета пользовательских XLL-функций**. Затем нажмите кнопку **Параметры** и введите полное имя кластера в поле **Имя головного узла кластера**. (Как упоминалось ранее, длина этого поля ограничена 34 символами, поэтому длинное имя кластера может не поместиться. Здесь для длинных имен кластеров можно использовать глобальные переменные на уровне компьютера.)

    ![Настройка пользовательской функции][options]

3.	Щелкните ячейку со значением =XllGetComputerNameC() и нажмите клавишу ВВОД, чтобы выполнить вычисление пользовательской функции в кластере IaaS. Функция просто получит имя вычислительного узла, на котором выполняется пользовательская функция. При первом выполнении в диалоговом окне учетных данных будет предложено ввести имя пользователя и пароль для подключения к кластеру IaaS.

    ![Запуск пользовательской функции][run]

    Если требуется вычислить много ячеек, нажмите клавиши Alt-Shift-Ctrl + F9, чтобы вычислить все ячейки.

## Шаг 3. Запуск рабочей нагрузки SOA из локального клиента

Для запуска общих приложений SOA в кластере IaaS пакета HPC, сначала используйте один из методов на этапе 1 для развертывания кластера IaaS, используя обычный образ вычислительного узла (поскольку вам не потребуется Excel на вычислительных узлах). Затем выполните следующие действия.

1. После получения сертификата кластера импортируйте его на клиентский компьютер в папку Cert:\\CurrentUser\\Root.

2. Установите [пакет SDK для пакета HPC 2012 R2 с обновлением 3](http://www.microsoft.com/download/details.aspx?id=49921) и [служебные программы клиента пакета HPC 2012 R2 с обновлением 3](https://www.microsoft.com/download/details.aspx?id=49923), чтобы можно было создавать и запускать клиентские приложения SOA.

3. Скачайте [пример кода](https://www.microsoft.com/download/details.aspx?id=41633) HellowWorldR2. Откройте файл HelloWorldR2.sln в Visual Studio 2010 или 2012.

4. Сначала соберите проект EchoService и разверните службу в кластере IaaS так же, как и локальный кластер. Подробные указания см. в файле Readme.doc примера HelloWordR2. Измените и соберите HellowWorldR2 и другие проекты, как описано ниже, чтобы создать клиентские приложения SOA, выполняемые в кластере IaaS Azure с локального клиентского компьютера.

### Использование привязки HTTP с очередью хранилища Azure

Чтобы использовать привязку HTTP с очередью хранилища Azure, внесите несколько изменений в пример кода.

* Измените имя кластера.

    ```
// Before
const string headnode = "[headnode]";
// After e.g.
const string headnode = "hpc01.eastus.cloudapp.azure.com";
or
const string headnode = "hpc01.cloudapp.net";
```

* При необходимости используйте стандартную схему TransportScheme в SessionStartInfo или явно задайте значение Http.

```
    info.TransportScheme = TransportScheme.Http;
```

* Используйте привязку по умолчанию для BrokerClient.

    ```
// Before
using (BrokerClient<IService1> client = new BrokerClient<IService1>(session, binding))
// After
using (BrokerClient<IService1> client = new BrokerClient<IService1>(session))
```

    Либо явно задайте значение basicHttpBinding.

    ```
BasicHttpBinding binding = new BasicHttpBinding(BasicHttpSecurityMode.TransportWithMessageCredential);
binding.Security.Message.ClientCredentialType = BasicHttpMessageCredentialType.UserName;    binding.Security.Transport.ClientCredentialType = HttpClientCredentialType.None;
```

* При необходимости задайте для флага UseAzureQueue значение true в SessionStartInfo. Если он не установлен, значение true будет задано по умолчанию, если имя кластера содержит суффиксы домена Azure, а параметр TransportScheme имеет значение Http.

    ```
    info.UseAzureQueue = true;
```

###Использование привязки HTTP без очереди хранилища Azure

Для этого явно задайте для флага UseAzureQueue значение false в SessionStartInfo.

```
    info.UseAzureQueue = false;
```

### Использование привязки NetTcp

Настройка привязки NetTcp аналогична подключению к локальному кластеру. На виртуальной машине головного узла потребуется открыть несколько конечных точек. На классическом портале Azure выполните следующие действия.


1. Остановите виртуальную машину.

2. Добавьте порты TCP 9090, 9087, 9091, 9094 для служб сеансов, брокера, рабочей роли брокера и данных, соответственно.

    ![Настройка конечных точек][endpoint]

3. Запустите виртуальную машину.

Клиентское приложение SOA не требует изменений, за исключением замены имени головного узла на полное имя кластера IaaS.

## Дальнейшие действия

* Дополнительные сведения о запуске рабочих нагрузок Excel с помощью пакета HPC см. в [этих ресурсах](http://social.technet.microsoft.com/wiki/contents/articles/1198.windows-hpc-and-microsoft-excel-resources-for-building-cluster-ready-workbooks.aspx).

* Дополнительные сведения о развертывании служб SOA и управлении ими с помощью пакета HPC см. в статье [Управление службами SOA в пакете Microsoft HPC](https://technet.microsoft.com/library/ff919412.aspx).

<!--Image references-->
[scenario]: ./media/virtual-machines-excel-cluster-hpcpack/scenario.png
[github]: ./media/virtual-machines-excel-cluster-hpcpack/github.png
[template]: ./media/virtual-machines-excel-cluster-hpcpack/template.png
[parameters]: ./media/virtual-machines-excel-cluster-hpcpack/parameters.png
[create]: ./media/virtual-machines-excel-cluster-hpcpack/create.png
[connect]: ./media/virtual-machines-excel-cluster-hpcpack/connect.png
[cert]: ./media/virtual-machines-excel-cluster-hpcpack/cert.png
[addin]: ./media/virtual-machines-excel-cluster-hpcpack/addin.png
[macro]: ./media/virtual-machines-excel-cluster-hpcpack/macro.png
[options]: ./media/virtual-machines-excel-cluster-hpcpack/options.png
[run]: ./media/virtual-machines-excel-cluster-hpcpack/run.png
[endpoint]: ./media/virtual-machines-excel-cluster-hpcpack/endpoint.png
[udf]: ./media/virtual-machines-excel-cluster-hpcpack/udf.png

<!---HONumber=AcomDC_1203_2015-->