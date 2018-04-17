---
title: Кластер пакета HPC для Excel и SOA | Документация Майкрософт
description: Приступая к работе с крупномасштабными рабочими нагрузками Excel и SOA в кластере пакета HPC в Azure
services: virtual-machines-windows
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-resource-manager,hpc-pack
ms.assetid: cb6a9abe-caf3-44da-b911-849a50f6cfb3
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 06/01/2017
ms.author: danlep
ms.openlocfilehash: aaf26e04fdb38fd76f4ab8211f9fdda8ebafd668
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2018
---
# <a name="get-started-running-excel-and-soa-workloads-on-an-hpc-pack-cluster-in-azure"></a>Приступая к работе с рабочими нагрузками Excel и SOA в кластере пакета HPC в Azure
В этой статье показано, как развернуть кластер пакета Microsoft HPC 2012 R2 на виртуальных машинах Azure с помощью шаблона быстрого запуска Azure или сценария развертывания Azure PowerShell. В кластере используются образы виртуальных машин из Azure Marketplace, разработанные для выполнения рабочих нагрузок Microsoft Excel или сервисноориентированной архитектуры (SOA) с помощью пакета HPC. Кластер можно использовать для запуска служб HPC Excel и SOA на локальном клиентском компьютере. Службы Excel HPC включают функцию разгрузки книг и пользовательские функции Excel (или UDF).

> [!IMPORTANT] 
> В этой статье используются функции, шаблоны и сценарии для пакета HPC 2012 R2. Этот сценарий пока не поддерживается для пакета HPC 2016.
>

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

На приведенной ниже схеме в общем показан создаваемый кластер пакета HPC.

![Кластер HPC с узлами, выполняющими рабочие нагрузки Excel][scenario]

## <a name="prerequisites"></a>предварительным требованиям
* **Клиентский компьютер.** Необходим клиентский компьютер на основе Windows для отправки примеров заданий Excel и SOA в кластер. Кроме того, нужен компьютер Windows для выполнения сценария развертывания кластера Azure PowerShell (если выбран этот метод развертывания).
* **Подписка Azure.** Если ее нет, можно за пару минут создать [бесплатную учетную запись](https://azure.microsoft.com/pricing/free-trial/).
* **Квота ядер.** Возможно, вам потребуется увеличить квоту на число ядер, особенно при развертывании нескольких узлов кластера с многоядерными виртуальными машинами. Если вы используете шаблон быстрого запуска Azure, то квота ядер в Resource Manager выделяется на регион Azure. В этом случае может потребоваться увеличить квоту в определенном регионе. Ознакомьтесь со статьей [Пределы, квоты и ограничения подписок на Azure](../../azure-subscription-service-limits.md). Чтобы увеличить квоту, бесплатно [отправьте запрос в службу поддержки](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) .
* **Лицензия Microsoft Office.** В случае развертывания вычислительных узлов с помощью образа виртуальной машины пакета HPC 2012 R2 с Microsoft Excel из Marketplace будет установлена 30-дневная ознакомительная версия Microsoft Excel профессиональный плюс 2013. После завершения ознакомительного периода необходимо будет предоставить действительную лицензию Microsoft Office, чтобы активировать Excel и продолжить выполнение рабочих нагрузок. Ознакомьтесь с разделом [Активация Excel](#excel-activation) далее в этой статье. 

## <a name="step-1-set-up-an-hpc-pack-cluster-in-azure"></a>Шаг 1. Настройка кластера пакета HPC в Azure
Мы покажем вам два варианта настройки кластера пакета HPC 2012 R2: во-первых, с помощью шаблона быстрого запуска Azure и портала Azure, а во-вторых, с помощью сценария развертывания Azure PowerShell.

### <a name="option-1-use-a-quickstart-template"></a>Вариант 1. Использование шаблона быстрого запуска
Шаблон быстрого запуска Azure позволяет быстро развернуть кластер пакета HPC на портале Azure. Открыв шаблон на портале, вы увидите простой пользовательский интерфейс, где вводятся параметры кластера. Для этого выполните следующие действия. 

> [!TIP]
> При необходимости используйте [шаблон Azure Markeplace](https://portal.azure.com/?feature.relex=*%2CHubsExtension#create/microsofthpc.newclusterexcelcn) , который создает аналогичный кластер специально для рабочих нагрузок Excel. Действия немного отличаются от следующих.
> 
> 

1. Посетите [страницу шаблона создания кластера HPC на сайте GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster). При необходимости просмотрите сведения о шаблоне и исходном коде.
2. Щелкните **Развернуть в Azure** , чтобы начать развертывание шаблона на портале Azure.
   
   ![Развертывание шаблона в Azure][github]
3. Выполните на портале следующие действия, чтобы ввести параметры шаблона кластера HPC.
   
   a. На странице **Параметры** введите или измените значения параметров шаблона. (щелкнув значок рядом с любым параметром, можно получить справочные сведения). Примеры значений показаны на следующем экране. Этот пример создает кластер *hpc01* в домене *hpc.local*, состоящий из головного узла и 2 вычислительных узлов. Вычислительные узлы создаются из образа виртуальной машины пакета HPC, включающего в себя Microsoft Excel.
   
   ![Ввод параметров][parameters-new-portal]
   
   > [!NOTE]
   > Виртуальная машина головного узла создается автоматически из [последней версии образа Marketplace](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/) на основе пакета HPC 2012 R2 и Windows Server 2012 R2. В настоящий момент образ основан на пакете HPC 2012 R2 с обновлением 3.
   > 
   > Вычислительные узлы создаются из последней версии образа выбранного семейства вычислительных узлов. Выберите параметр **ComputeNodeWithExcel** для последней версии образа вычислительного узла пакета HPC, который включает ознакомительную версию Microsoft Excel профессиональный плюс 2013. Чтобы развернуть кластер для общих сеансов SOA или разгрузки определяемых пользователем функций Excel, выберите параметр **ComputeNode** (без установленного приложения Excel).
   > 
   > 
   
   Б. Выберите подписку.
   
   c. Создайте группу ресурсов для кластера, например *hpc01RG*.
   
   d. Выберите расположение группы ресурсов, например Центральная часть США.
   
   д. Просмотрите условия на странице **Условия использования** . Если вы согласны с ними, щелкните **Приобрести**. Завершив настройку значений для шаблона, нажмите кнопку **Создать**.
4. По завершении развертывания (как правило, это занимает около 30 минут) экспортируйте файл сертификата кластера с головного узла. На последующем шаге этот общедоступный сертификат будет импортирован на клиентский компьютер. Это обеспечит аутентификацию на стороне сервера для безопасной привязки HTTP.
   
   a. Чтобы подключиться с помощью удаленного рабочего стола, на портале Azure перейдите к панели мониторинга, выберите головной узел и щелкните **Подключиться** в верхней части страницы.
   
    <!-- ![Connect to the head node][connect] -->
   
   Б. Используйте стандартные процедуры для экспорта сертификата головного узла (расположенного в папке Cert:\LocalMachine\My) без закрытого ключа с помощью диспетчера сертификатов. В этом примере экспортируйте *CN = hpc01.eastus.cloudapp.azure.com*.
   
   ![Экспорт сертификата][cert]

### <a name="option-2-use-the-hpc-pack-iaas-deployment-script"></a>Вариант 2. Использование сценария развертывания IaaS пакета HPC
Сценарий развертывания IaaS пакета HPC обеспечивает еще один способ гибкого развертывания кластера пакета HPC. Он создает кластер в классической модели развертывания, тогда как в шаблоне используется модель развертывания диспетчера ресурсов Azure. Кроме того, сценарий совместим с подпиской на службу Azure Global или Azure China.

**Дополнительные требования**

* **Azure PowerShell** - [установите и настройте Azure PowerShell](/powershell/azure/overview) (версии 0.8.10 или более поздней) на клиентском компьютере.
* **Сценарий развертывания IaaS из пакета HPC** — скачайте и распакуйте последнюю версию сценария из [Центра загрузки Майкрософт](https://www.microsoft.com/download/details.aspx?id=44949). Проверьте версию сценария, запустив `New-HPCIaaSCluster.ps1 –Version`. Эта статья основана на сценарии версии 4.5.0 или выше.

**Создание файла конфигурации**

 Сценарий развертывания IaaS из пакета HPC использует входной XML-файл конфигурации, описывающий инфраструктуру кластера HPC. Чтобы развернуть кластер, состоящий из головного узла и 18 вычислительных узлов, созданных из образа вычислительного узла, который включает Microsoft Excel, вставьте значения для вашей среды в следующий образец файла конфигурации. Дополнительные сведения о файле конфигурации см. в файле Manual.rtf в папке сценария или статье [Создание кластера HPC с помощью сценария развертывания IaaS пакета HPC](classic/hpcpack-cluster-powershell-script.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

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

* Параметр **VMName** головного узла **ДОЛЖЕН** точно совпадать с параметром **ServiceName**, иначе запуск задания SOA завершится сбоем.
* Обязательно укажите параметр **EnableWebPortal** , чтобы сертификат головного узла был создан и экспортирован.
* В этом файле указывается сценарий PostConfig.ps1, выполняемый на головном узле после настройки PowerShell. Приведенный ниже пример сценария настраивает строки подключения к службе хранилища Azure, удаляет роль вычислительного узла с головного узла и подключает все узлы к сети после развертывания. 

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
2. Измените каталог на папку сценария (E:\IaaSClusterScript в этом примере).
   
   ```
   cd E:\IaaSClusterScript
   ```
3. Выполните следующую команду, чтобы развернуть кластер пакета HPC. В этом примере предполагается, что путь к файлу конфигурации — E:\HPCDemoConfig.xml.
   
   ```
   .\New-HpcIaaSCluster.ps1 –ConfigFile E:\HPCDemoConfig.xml –AdminUserName MyAdminName
   ```

Для выполнения сценария развертывания пакета HPC потребуется некоторое время. Одна из функций сценария — экспорт и скачивание сертификата кластера и его сохранение в папке документов текущего пользователя на клиентском компьютере. Сценарий создаст сообщение следующего вида. На следующем шаге вы импортируете сертификат в соответствующее хранилище сертификатов.    

    You have enabled REST API or web portal on HPC Pack head node. Please import the following certificate in the Trusted Root Certification Authorities certificate store on the computer where you are submitting job or accessing the HPC web portal:
    C:\Users\hpcuser\Documents\HPCWebComponent_HPCExcelHN004_20150707162011.cer

## <a name="step-2-offload-excel-workbooks-and-run-udfs-from-an-on-premises-client"></a>Шаг 2. Разгрузка книг Excel и выполнение пользовательских функций из локального клиента
### <a name="excel-activation"></a>Активация Excel
При использовании образа виртуальной машины ComputeNodeWithExcel для рабочих нагрузок в рабочей среде потребуется указать действительный лицензионный ключ Microsoft Office, чтобы активировать Excel на вычислительных узлах. В противном случае срок действия ознакомительной версии Excel истечет через 30 дней, после чего выполнение книг Excel будет завершаться ошибкой COMExeption (0x800AC472). 

Вы можете вернуться к исходному состоянию активации 30-дневной ознакомительной версии Excel. Для этого войдите на головной узел и выполните clusrun `%ProgramFiles(x86)%\Microsoft Office\Office15\OSPPREARM.exe` во всех вычислительных узлах Excel через диспетчер кластеров HPC. Возвращаться к исходному состоянию активации можно не более двух раз. После этого необходимо будет указать действительный лицензионный ключ Office.

В образе виртуальной машины установлен корпоративный выпуск Office профессиональный плюс 2013 с универсальным ключом многократной установки (GVLK). Вы можете активировать его, воспользовавшись службой управления ключами (KMS), активацией с помощью Active Directory (AD-BA) или ключом многократной активации (MAK). 

    * Чтобы воспользоваться службой управления ключами (KMS) или активацией с помощью Active Directory, используйте существующий сервер службы KMS или настройте новый с помощью корпоративного пакета лицензий Microsoft Office 2013. При необходимости настройте сервер на головном узле. Затем активируйте ключ узла KMS через Интернет или по телефону. Затем выполните команду clusrun с файлом `ospp.vbs` для указания сервера и порта службы KMS и активации Office на всех вычислительных узлах Excel. 

    * Для использования MAK сначала выполните команду clusrun с файлом `ospp.vbs` для ввода ключа, а затем активируйте все вычислительные узлы Excel через Интернет или по телефону. 

> [!NOTE]
> Розничные ключи продукта для Office профессиональный плюс 2013 невозможно использовать с этим образом виртуальной машины. Если у вас имеются действительные ключи и установочный носитель для выпусков Office или Excel, отличных от этого корпоративного выпуска Office профессиональный плюс 2013, то вы можете использовать имеющиеся выпуски. Сначала удалите данный корпоративный выпуск, после чего установите имеющийся выпуск. Повторно установленный вычислительный узел Excel можно захватывать в качестве настраиваемого образа виртуальной машины и использовать при развертывании в масштабе.
> 
> 

### <a name="offload-excel-workbooks"></a>Разгрузка книг Excel
Выполните следующие действия, чтобы выгрузить книгу Excel для выполнения в кластере пакета HPC в Azure. Для этого на клиентском компьютере уже должен быть установлен Excel 2010 или 2013.

1. Используйте один из вариантов шага 1 для развертывания образа вычислительного узла кластера пакета HPC с Excel. Получите файл сертификата кластера (.cer), а также имя пользователя и пароль для кластера.
2. На клиентском компьютере импортируйте сертификат кластера в папку Cert:\CurrentUser\Root.
3. Убедитесь, что установлен Excel. Создайте файл Excel.exe.config со следующим содержимым в одной папке с файлом Excel.exe на клиентском компьютере. Это гарантирует успешную загрузку надстройки COM пакета HPC 2012 R2 для Excel.
   
    ```
    <?xml version="1.0"?>
    <configuration>
        <startup useLegacyV2RuntimeActivationPolicy="true">
            <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.0"/>
        </startup>
    </configuration>
    ```
4. Настройте клиент для отправки заданий в кластер пакета HPC. Один из вариантов — скачать полный установщик [пакета HPC 2012 R2 с обновлением 3](http://www.microsoft.com/download/details.aspx?id=49922) и установить клиент пакета HPC. Либо скачайте и установите [клиентские служебные программы пакета HPC 2012 R2 с обновлением 3](https://www.microsoft.com/download/details.aspx?id=49923) и соответствующий распространяемый компонент Visual C++ 2010 для своего компьютера (версию [x64](http://www.microsoft.com/download/details.aspx?id=14632) или [x86](https://www.microsoft.com/download/details.aspx?id=5555)).
5. В этом примере мы используем пример книги Excel — ConvertiblePricing_Complete.xlsb. Его можно скачать [здесь](https://www.microsoft.com/en-us/download/details.aspx?id=2939).
6. Скопируйте книгу Excel в рабочую папку, например D:\Excel\Run.
7. Откройте книгу Excel. На вкладке ленты **Разработка** щелкните **Надстройки COM** и убедитесь, что надстройка COM пакета HPC для Excel успешно загружена.
   
   ![Надстройка Excel для пакета HPC][addin]
8. Отредактируйте макрос VBA HPCControlMacros в Excel, изменив закомментированные строки, как показано в следующем сценарии. Подставьте соответствующие значения для вашей среды.
   
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
9. Скопируйте книгу Excel в каталог для передаваемых файлов, например D:\Excel\Upload. Этот каталог задается в константе HPC_DependsFiles в макросе VBA.
10. Нажмите кнопку **Кластер** на листе, чтобы запустить книгу в кластере в Azure.

### <a name="run-excel-udfs"></a>Выполнение пользовательских функций Excel
Для выполнения пользовательских функций Excel выполните предыдущие этапы 1–3, чтобы настроить клиентский компьютер. Для использования определяемых пользователем функций Excel не нужно устанавливать приложение Excel на вычислительных узлах. То есть при создании вычислительных узлов кластера можно выбрать обычный образ вычислительного узла, а не образ вычислительного узла с Excel.

> [!NOTE]
> В диалоговом окне соединителя кластера Excel 2010 и 2013 действует ограничение в 34 знака. Используйте это диалоговое окно, чтобы указать кластер, который выполняет определяемые пользователем функции. Если полное имя кластера имеет большую длину (например, hpcexcelhn01.southeastasia.cloudapp.azure.com), то оно не поместится в диалоговом окне. Чтобы обойти эту проблему, задайте переменную уровня компьютера, например *CCP_IAASHN*, и присвойте ей значение полного имени кластера. Затем в диалоговом окне в поле имени головного узла кластера введите *%CCP_IAASHN%*. 
> 
> 

После успешного развертывания кластера выполните следующие действия, чтобы запустить встроенные образцы пользовательских функций Excel. Для собственных пользовательских функций Excel см. эти [ресурсы](http://social.technet.microsoft.com/wiki/contents/articles/1198.windows-hpc-and-microsoft-excel-resources-for-building-cluster-ready-workbooks.aspx), чтобы создать XLL-файлы и развернуть их в кластере IaaS.

1. Откройте новую книгу Excel. На вкладке ленты **Разработка** щелкните **Надстройки**. Затем в диалоговом окне нажмите кнопку **Обзор**, перейдите в папку %CCP_HOME%Bin\XLL32 и выберите пример ClusterUDF32.xll. Если ClusterUDF32.xll не существует на клиентском компьютере, то скопируйте его из папки %CCP_HOME%Bin\XLL32 на головном узле.
   
   ![Выбор пользовательской функции][udf]
2. Щелкните **Файл** > **Параметры** > **Дополнительно**. В разделе **Формулы** установите флажок **Allow user-defined XLL functions to run a compute cluster** (Разрешить использование вычислительных кластеров для расчета определяемых пользователем функций XLL). Затем щелкните **Параметры** и введите полное имя кластера в поле **Cluster head node name** (Имя головного узла кластера). (Как упоминалось ранее, длина этого поля ограничена 34 символами, поэтому длинное имя кластера может не поместиться. Здесь для длинного имени кластера можно использовать переменную уровня компьютера.)
   
   ![Настройка пользовательской функции][options]
3. Щелкните ячейку со значением ==XllGetComputerNameC() и нажмите клавишу ВВОД, чтобы выполнить вычисление определяемой пользователем функции в кластере. Функция просто получает имя вычислительного узла, на котором выполняется определяемая пользователем функция. При первом выполнении в диалоговом окне учетных данных будет предложено ввести имя пользователя и пароль для подключения к кластеру IaaS.
   
   ![Запуск пользовательской функции][run]
   
   Если требуется вычислить много ячеек, нажмите клавиши Alt + Shift + Ctrl + F9, чтобы вычислить все ячейки.

## <a name="step-3-run-a-soa-workload-from-an-on-premises-client"></a>Шаг 3. Запуск рабочей нагрузки SOA из локального клиента
Чтобы выполнять универсальные приложения SOA в кластере IaaS пакета HPC, сначала используйте один из методов шага 1, чтобы развернуть этот кластер. В этом случае укажите универсальный образ вычислительного узла, так как наличие Excel на вычислительных узлах не требуется. Затем выполните следующие действия.

1. После получения сертификата кластера импортируйте его на клиентский компьютер в папку Cert:\CurrentUser\Root.
2. Установите [пакет SDK для пакета HPC 2012 R2 с обновлением 3](http://www.microsoft.com/download/details.aspx?id=49921) и [клиентские служебные программы пакета HPC 2012 R2 с обновлением 3](https://www.microsoft.com/download/details.aspx?id=49923). Эти инструменты позволяют разрабатывать и выполнять клиентские приложения SOA.
3. Скачайте [пример кода](https://www.microsoft.com/download/details.aspx?id=41633)HelloWorldR2. Откройте файл HelloWorldR2.sln в Visual Studio 2010 или 2012. (В настоящее время этот пример несовместим с более поздними версиями Visual Studio.)
4. Сначала выполните сборку проекта EchoService. Затем разверните службу в кластере IaaS так же, как и в локальном кластере. Подробные указания см. в файле Readme.doc примера HelloWordR2. Измените HelloWorldR2 и другие проекты, после чего выполните их сборку, как описано в следующем разделе, чтобы создать клиентские приложения SOA, выполняемые в кластере IaaS Azure.

### <a name="use-http-binding-with-azure-storage-queue"></a>Использование привязки HTTP с очередью хранилища Azure
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

### <a name="use-http-binding-without-azure-storage-queue"></a>Использование привязки HTTP без очереди хранилища Azure
Чтобы использовать привязку Http без очереди службы хранилища Azure, в SessionStartInfo явно задайте значение false для флага UseAzureQueue.

```
    info.UseAzureQueue = false;
```

### <a name="use-nettcp-binding"></a>Использование привязки NetTcp
Настройка привязки NetTcp аналогична подключению к локальному кластеру. На виртуальной машине головного узла требуется открыть несколько конечных точек. Если вы использовали сценарий развертывания IaaS пакета HPC, например для создания кластера, то задайте конечные точки на портале Azure, как описано ниже.

1. Остановите виртуальную машину.
2. Добавьте TCP-порты 9090, 9087, 9091, 9094 для сеансов, брокера, рабочей роли брокера и служб данных, соответственно.
   
    ![Настройка конечных точек][endpoint-new-portal]
3. Запустите виртуальную машину.

Клиентское приложение SOA не требует изменений, за исключением замены имени головного узла на полное имя кластера IaaS.

## <a name="next-steps"></a>Дополнительная информация
* Дополнительные сведения о запуске рабочих нагрузок Excel с помощью пакета HPC см. в [этих ресурсах](http://social.technet.microsoft.com/wiki/contents/articles/1198.windows-hpc-and-microsoft-excel-resources-for-building-cluster-ready-workbooks.aspx).
* Дополнительные сведения о развертывании служб SOA и управлении ими с помощью пакета HPC см. в статье [Управление службами SOA в пакете Microsoft HPC](https://technet.microsoft.com/library/ff919412.aspx).

<!--Image references-->
[scenario]: ./media/excel-cluster-hpcpack/scenario.png
[github]: ./media/excel-cluster-hpcpack/github.png
[template]: ./media/excel-cluster-hpcpack/template.png
[parameters]: ./media/excel-cluster-hpcpack/parameters.png
[parameters-new-portal]: ./media/excel-cluster-hpcpack/parameters-new-portal.png
[create]: ./media/excel-cluster-hpcpack/create.png
[connect]: ./media/excel-cluster-hpcpack/connect.png
[cert]: ./media/excel-cluster-hpcpack/cert.png
[addin]: ./media/excel-cluster-hpcpack/addin.png
[macro]: ./media/excel-cluster-hpcpack/macro.png
[options]: ./media/excel-cluster-hpcpack/options.png
[run]: ./media/excel-cluster-hpcpack/run.png
[endpoint]: ./media/excel-cluster-hpcpack/endpoint.png
[endpoint-new-portal]: ./media/excel-cluster-hpcpack/endpoint-new-portal.png
[udf]: ./media/excel-cluster-hpcpack/udf.png
