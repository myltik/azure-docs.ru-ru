---
title: "Использование записи пакетов для упреждающего мониторинга сети с помощью оповещений и функций Azure | Документы Майкрософт"
description: "В этой статье описывается, как создавать активируемую с использованием оповещений запись пакетов в службе наблюдения за сетями Azure"
services: network-watcher
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
ms.assetid: 75e6e7c4-b3ba-4173-8815-b00d7d824e11
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 1cc1ee946d8eb2214fd05701b495bbce6d471a49
ms.openlocfilehash: aced1550a7b5b41b0a430c26f82c31c023f4d2d4
ms.lasthandoff: 04/26/2017


---
# <a name="use-packet-capture-to-do-proactive-network-monitoring-with-alerts-and-azure-functions"></a>Использование записи пакетов для упреждающего мониторинга сети с помощью оповещений и функций Azure

Функция записи пакетов службы наблюдения за сетями (Наблюдатель за сетями) отслеживает входящий и исходящий трафик виртуальной машины. Файл записи можно создавать с использованием фильтра, чтобы отслеживать только нужный для мониторинга трафик. Эти данные затем сохраняются в хранилище BLOB-объектов или локально на гостевом компьютере.

Эта возможность допускает удаленный запуск из других сценариев службы автоматизации, включая функции Azure. Возможность записи пакетов позволяет создавать упреждающие записи пакетов, активируемые при обнаружении определенных сетевых аномалий. Она также помогает выполнять сбор сетевой статистики, получать сведения о сетевых вторжениях, выполнять отладку передачи данных между клиентом и сервером и многое другое.

Развернутые в Azure ресурсы выполняются круглосуточно и без выходных. И вам и вашим сотрудникам не удастся эффективно отслеживать состояние всех ресурсов без перерывов. Что вы будете делать, если сбой произойдет в два часа ночи?

С помощью Наблюдателя за сетями, оповещений и функций экосистемы Azure вы можете не только заранее подготовиться к проблемам в сети, но также подготовить данные и средства для устранения проблем.

![сценарий][scenario]

## <a name="prerequisites"></a>Предварительные требования

* Установка последней версии [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-3.8.0)
* Наличие или [создание экземпляра наблюдателя за сетями](network-watcher-create.md)
* Наличие виртуальной машины в том же регионе, что и указанный выше наблюдатель за сетями, с [расширением виртуальных машин для Windows](../virtual-machines/windows/extensions-nwa.md) или [Linux](../virtual-machines/linux/extensions-nwa.md)

## <a name="scenario"></a>Сценарий

В этом примере мы рассмотрим виртуальную машину, которая отправляет больше TCP-сегментов, чем обычно. Вы хотите получать оповещения о такой ситуации. TCP-сегменты используются только в качестве примера. Вы можете использовать любое условие оповещения.

Когда создается оповещение, вам нужно получить данные на уровне пакетов, чтобы найти причину необычной активности. Это позволит принять меры для восстановления на компьютере обычного режима обмена данными.

В этом сценарии предполагается, что у вас есть экземпляр наблюдателя за сетями, а также группа ресурсов с допустимой виртуальной машиной.

В этом примере мы рассмотрим виртуальную машину, которая отправляет больше TCP-сегментов, чем обычно. Вы хотите получать оповещения о такой ситуации. TCP-сегменты используются только в качестве примера. Вы можете использовать любое условие оповещения. Когда создается оповещение, вам нужно получить данные на уровне пакетов, чтобы найти причину необычной активности и восстановить на компьютере обычного режима обмена данными.

Ниже приведен обзор соответствующего рабочего процесса.

1. На виртуальной машине активируется оповещение.
1. Это оповещение вызывает функцию Azure через webhook.
1. Функция Azure обрабатывает оповещение и запускает сеанс записи пакетов в наблюдателе за сетями.
1. На виртуальной машине выполняется запись пакетов, которая собирает данные о трафике.
1. Файл записи пакетов передается в учетную запись хранения для проверки и диагностики.

Чтобы автоматизировать этот процесс, мы создадим и подключим на виртуальной машине оповещение, которое будет срабатывать при возникновении проблемы. Также мы создадим функцию для вызова наблюдателя за сетями.

Этот сценарий выполняет следующее:

* создает функцию Azure, которая запускает запись пакетов;
* создает правило генерации оповещений на виртуальной машине и настраивает его для вызова функции Azure.

## <a name="creating-an-azure-function"></a>Создание функции Azure

Первым делом нам нужно создать функцию Azure для обработки оповещения и создания записи пакетов.

1. На [портале Azure](https://portal.azure.com) щелкните **Создать** > **Вычисления** > **Приложение-функция**.

    ![Создание приложения-функции][1-1]

2. В разделе **Приложение-функция** введите следующие значения и нажмите кнопку **ОК** для создания приложения-функции:

    |**Параметр** | **Значение** | **Дополнительные сведения** |
    |---|---|---|
    |**Имя приложения**|PacketCaptureExample|Имя приложения-функции|
    |**Подписка**|[Ваша подписка]|Выберите подписку Azure для создания приложения-функции.||
    |**Группа ресурсов**|PacketCaptureRG|Имя группы ресурсов, куда будет помещено приложение-функция.|
    |**План размещения**|План потребления| Тип плана, который будет использовать приложение-функция. Доступен план потребления или план службы приложений. |
    |**Расположение**|Центральный регион США| Регион для создания приложения-функции.|
    |**Учетная запись хранения**|{autogenerated}| Это учетная запись хранения, необходимая функциям Azure для хранилища общего назначения.|

3. В колонке **PacketCaptureExample** приложений-функций щелкните **+** в разделе **Функции** > **Пользовательская функция**. Выберите **HttpTrigger-Powershell**, введите остальные сведения и нажмите кнопку **Создать** для создания функции.

    |**Параметр** | **Значение** | **Дополнительные сведения** |
    |---|---|---|
    |**Сценарий**|Экспериментальная возможность|Тип сценария|
    |**Имя функции**|AlertPacketCapturePowerShell|Имя функции|
    |**Уровень авторизации**|Функция|Уровень авторизации для функции.|

![Пример функции][functions1]

> [!NOTE]
> Шаблон PowerShell является экспериментальным и не обеспечивает полную поддержку.

Для этого примера требуются настройки, описанные ниже.

### <a name="adding-modules"></a>Добавление модулей

Чтобы использовать командлеты PowerShell для Наблюдателя за сетями, необходимо передать в приложение-функцию последнюю версию модуля PowerShell.

1. На локальном компьютере, на котором установлена последняя версия модулей Azure PowerShell, выполните следующую команду PowerShell.

    ```powershell
    (Get-Module AzureRM.Network).Path
    ```

    Этот пример отображает локальный путь к модулям Azure PowerShell. Мы используем эти папки позже. Ниже перечислены модули, используемые в этом сценарии.

    * AzureRM.Network

    * AzureRM.Profile

    * AzureRM.Resources

    ![Папки PowerShell][functions5]

1. Выберите **Параметры приложения-функции** > **Перейти в редактор службы приложений**.

    ![Функции Kudu][functions2]

1. Щелкните правой кнопкой мыши папку AlertPacketCapturePowershell и создайте папку **azuremodules**. Создайте вложенные папки для каждого необходимого модуля.

    ![Функции Kudu][functions3]

    * AzureRM.Network

    * AzureRM.Profile

    * AzureRM.Resources

1. Щелкните правой кнопкой мыши вложенную папку **AzureRM.Network** и щелкните **Отправить файлы**. Перейдите к папке с установленными модулями Azure. В локальной папке AzureRM.Network выберите все файлы и нажмите кнопку **OK**.  Повторите эти шаги для папок AzureRM.Profile и AzureRM.Resources.

    ![Передача файлов][functions6]

1. По завершении в каждой папке должны находиться файлы модулей PowerShell с локального компьютера.

    ![Файлы PowerShell][functions7]

### <a name="authentication"></a>Аутентификация

Чтобы использовать командлеты PowerShell, нужно пройти аутентификацию. Аутентификация должна быть настроена в приложении-функции. Чтобы настроить аутентификацию, нужно задать переменные среды и передать зашифрованный файл ключа в приложение-функцию.

> [!NOTE]
> В этом сценарии приводится всего один пример того, как реализовать аутентификацию с использованием Функций Azure. Существуют и другие способы сделать это.

#### <a name="encrypted-credentials"></a>Зашифрованные учетные данные

Следующий сценарий PowerShell создает файл ключа **PassEncryptKey.key** и предоставляет зашифрованную версию введенного пароля.  Это тот же пароль, который определен для приложения Azure AD и используется для аутентификации.

```powershell
#variables
$keypath = "C:\temp\PassEncryptKey.key"
$AESKey = New-Object Byte[] 32
$Password = "<insert a password here>"

#keys
[Security.Cryptography.RNGCryptoServiceProvider]::Create().GetBytes($AESKey) 
Set-Content $keypath $AESKey

#get encrypted password
$secPw = ConvertTo-SecureString -AsPlainText $Password -Force
$AESKey = Get-content $KeyPath
$Encryptedpassword = $secPw | ConvertFrom-SecureString -Key $AESKey
$Encryptedpassword
```

В редакторе службы приложений для приложения-функции создайте папку **keys** в папке **AlertPacketCapturePowerShell** и передайте в нее файл **PassEncryptKey.key**, созданный предыдущим примером PowerShell.

![Ключ для функции][functions8]

### <a name="retrieve-values-for-environment-variables"></a>Извлечение значений переменных среды

Чтобы завершить настройку, необходимо задать переменные среды, требуемые для доступа к значениям для аутентификации. Ниже приведен список создаваемых переменных среды.

* AzureClientID

* AzureTenant

* AzureCredPassword


#### <a name="azureclientid"></a>AzureClientID

Идентификатор клиента — это идентификатор приложения в Azure Active Directory.

1. Если используемого приложения еще нет, выполните приведенный ниже пример, чтобы создать приложение.

    ```powershell
    $app = New-AzureRmADApplication -DisplayName "ExampleAutomationAccount_MF" -HomePage "https://exampleapp.com" -IdentifierUris "https://exampleapp1.com/ExampleFunctionsAccount" -Password "<same password as defined earlier>"
    New-AzureRmADServicePrincipal -ApplicationId $app.ApplicationId
    Start-Sleep 15
    New-AzureRmRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $app.ApplicationId
    ```

   > [!NOTE]
   > Пароль, используемый при создании приложения, должен совпадать с паролем, который был создан ранее при сохранении файла ключа.

1. На портале Azure щелкните **Подписки**, выберите подписку и щелкните **Управление доступом (IAM)**.

    ![Функции (IAM)][functions9]

1. Выберите учетную запись и щелкните "Свойства". Скопируйте идентификатор приложения.

    ![Идентификатор приложения-функции][functions10]

#### <a name="azuretenant"></a>AzureTenant

Код клиента можно получить, выполнив следующий пример PowerShell.

```powershell
(Get-AzureRmSubscription -SubscriptionName "<subscriptionName>").TenantId
```

#### <a name="azurecredpassword"></a>AzureCredPassword

Значением переменной среды AzureCredPassword является значение, получаемое с помощью следующего примера PowerShell. Это пример, который был показан в предыдущем разделе **Зашифрованные учетные данные**. Необходимым значением являются выходные данные переменной `$Encryptedpassword`.  Это пароль субъекта-службы, который мы зашифрованы с помощью сценария PowerShell.

```powershell
#variables
$keypath = "C:\temp\PassEncryptKey.key"
$AESKey = New-Object Byte[] 32
$Password = "<insert a password here>"

#keys
[Security.Cryptography.RNGCryptoServiceProvider]::Create().GetBytes($AESKey) 
Set-Content $keypath $AESKey

#get encrypted password
$secPw = ConvertTo-SecureString -AsPlainText $Password -Force
$AESKey = Get-content $KeyPath
$Encryptedpassword = $secPw | ConvertFrom-SecureString -Key $AESKey
$Encryptedpassword
```

### <a name="store-the-environment-variables"></a>Хранение переменных среды

1. Перейдите к приложению-функции и щелкните **Параметры приложения-функции** > **Настроить параметры приложения**.

    ![Настройка параметров приложения][functions11]

1. Добавьте переменные среды и их значения к параметрам приложения и нажмите кнопку **Сохранить**.

    ![параметры приложения][functions12]

### <a name="add-powershell-to-the-function"></a>Добавление PowerShell в функцию

Пришло время выполнить вызов Наблюдателя за сетями из функции Azure. Реализация этой функции будет отличаться в зависимости от конкретных требований. Но общая схема потока кода будет иметь такой вид.

1. Обработка входных параметров.
2. Запрос уже существующих записей пакетов для проверки ограничений и разрешения конфликтов имен.
3. Создание записи пакетов с необходимыми параметрами.
4. Периодический опрос процесса записи пакетов вплоть до его завершения.
5. Уведомление пользователя о завершении сеанса записи пакета.

Следующий пример PowerShell можно использовать в функции Azure. Необходимо заменить значения subscriptionId, resourceGroupName и storageAccountName.

```powershell
#Import Azure PowerShell modules required to make calls to Network Watcher
Import-Module "D:\home\site\wwwroot\AlertPacketCapturePowerShell\azuremodules\AzureRM.Profile\AzureRM.Profile.psd1" -Global
Import-Module "D:\home\site\wwwroot\AlertPacketCapturePowerShell\azuremodules\AzureRM.Network\AzureRM.Network.psd1" -Global
Import-Module "D:\home\site\wwwroot\AlertPacketCapturePowerShell\azuremodules\AzureRM.Resources\AzureRM.Resources.psd1" -Global

#Process Alert Request Body
$requestBody = Get-Content $req -Raw | ConvertFrom-Json

#Storage Account Id to save captures in
$storageaccountid = "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}"

#Packet Capture Vars
$packetcapturename = "PSAzureFunction"
$packetCaptureLimit = 10
$packetCaptureDuration = 10

#Credentials
$tenant = $env:AzureTenant
$pw = $env:AzureCredPassword
$clientid = $env:AzureClientId
$keypath = "D:\home\site\wwwroot\AlertPacketCapturePowerShell\keys\PassEncryptKey.key"

#Authentication
$secpassword = $pw | ConvertTo-SecureString -Key (Get-Content $keypath)
$credential = New-Object System.Management.Automation.PSCredential ($clientid, $secpassword)
Add-AzureRMAccount -ServicePrincipal -Tenant $tenant -Credential $credential #-WarningAction SilentlyContinue | out-null


#Get the VM that fired the Alert
if($requestBody.context.resourceType -eq "Microsoft.Compute/virtualMachines")
{
    Write-Output ("Subscription ID: {0}" -f $requestBody.context.subscriptionId)
    Write-Output ("Resource Group:  {0}" -f $requestBody.context.resourceGroupName)
    Write-Output ("Resource Name:  {0}" -f $requestBody.context.resourceName)
    Write-Output ("Resource Type:  {0}" -f $requestBody.context.resourceType)

    #Get the Network Watcher in the VM's Region
    $nw = Get-AzurermResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq $requestBody.context.resourceRegion}
    $networkWatcher = Get-AzureRmNetworkWatcher -Name $nw.Name -ResourceGroupName $nw.ResourceGroupName

    #Get existing packetCaptures
    $packetCaptures = Get-AzureRmNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher

    #Remove existing packet capture created by the function if it exists
    $packetCaptures | %{if($_.Name -eq $packetCaptureName)
    { 
        Remove-AzureRmNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -PacketCaptureName $packetCaptureName
    }}

    #Initiate Packet Capture on the VM that fired the alert
    if ((Get-AzureRmNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher).Count -lt $packetCaptureLimit){
        echo "Initiating Packet Capture"
        New-AzureRmNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -TargetVirtualMachineId $requestBody.context.resourceId -PacketCaptureName $packetCaptureName -StorageAccountId $storageaccountid -TimeLimitInSeconds $packetCaptureDuration
        Out-File -Encoding Ascii -FilePath $res -inputObject "Packet Capture created on ${requestBody.context.resourceID}"
    }
} 
``` 

Когда функция будет готова, настройте вызов связанного с ней URL-адреса в оповещении. Чтобы получить нужное значение, скопируйте URL-адрес функции из приложения-функции.

![Поиск URL-адреса функции 1][functions13]

Скопируйте URL-адрес функции для приложения-функции.

![Поиск URL-адреса функции 2][2]

Если вы хотите передавать пользовательские свойства в полезных данных запроса POST, передаваемого в webhook, изучите статью [Настройка объектов webhook для оповещений на основе метрик Azure](../monitoring-and-diagnostics/insights-webhooks-alerts.md).

## <a name="configure-an-alert-on-a-vm"></a>Настройка оповещения на виртуальной машине

Оповещения можно настроить так, чтобы они уведомляли определенных пользователей о превышении порогового значения для определенных метрик. В нашем примере оповещение создается по числу отправленных TCP-сегментов, но можно использовать и другие метрики. Наше оповещение обращается к webhook для вызова функции.

### <a name="create-the-alert-rule"></a>Создание правила для оповещения

Перейдите к существующей виртуальной машине и добавьте правило генерации оповещений. Дополнительные сведения о настройке оповещений см. в статье [Создание оповещений в Azure Monitor для служб Azure с помощью портала Azure](../monitoring-and-diagnostics/insights-alerts-portal.md). Введите в колонке приведенные ниже значения и нажмите кнопку **ОК**:

  |**Параметр** | **Значение** | **Дополнительные сведения** |
  |---|---|---|
  |**Имя**|TCP_Segments_Sent_Exceeded|Имя правила генерации оповещений.|
  |**Описание**|Число отправленных TCP-сегментов превысило пороговое значение|Описание для правила генерации оповещений.||
  |**Метрика**|Отправлено TCP-сегментов| Метрика, используемая для активации оповещения. |
  |**Condition**|Больше| Условие, используемое при вычислении метрики.|
  |**Пороговое значение**.|100| Это значение метрики, активирующее оповещение. Оно должно быть допустимым для используемой среды.|
  |**Период**|Период за последние 5 минут| Определяет период, за который проверяется пороговое значение для метрики.|
  |**webhook**|[URL-адрес webhook из приложения-функции]| Это URL-адрес webhook из приложения-функции, созданного в предыдущих шагах.|

> [!NOTE]
> Метрика сегментов TCP не включена по умолчанию. Дополнительные сведения о том, как включить такие метрики, см. в статье [Включение мониторинга и диагностики](../monitoring-and-diagnostics/insights-how-to-use-diagnostics.md).

## <a name="review-the-results"></a>Просмотр результатов

После задания условий для триггеров оповещений создается запись пакетов. Откройте наблюдатель за сетями и щелкните **Запись пакетов**. На этой странице можно щелкнуть ссылку на файла записи пакетов, чтобы скачать ее.

![Просмотр записи пакетов][functions14]

Если же файл записи хранится локально, для доступа к нему войдите в виртуальную машину.

Инструкции по скачиванию файлов из учетных записей хранения Azure см. в статье [Приступая к работе с хранилищем BLOB-объектов Azure с помощью .NET](../storage/storage-dotnet-how-to-use-blobs.md). Кроме того, можно использовать такое средство, как обозреватель хранилищ. Дополнительные сведения об обозревателе хранилищ см. [здесь](http://storageexplorer.com/).

Когда вы скачаете нужную запись, ее можно просмотреть в любом средстве, поддерживающем формат **CAP**. Ниже приведены ссылки на два таких средства.

- [Анализатор сообщений (Майкрософт)](https://technet.microsoft.com/library/jj649776.aspx).
- [Wireshark](https://www.wireshark.org/).

## <a name="next-steps"></a>Дальнейшие действия

Узнайте, как просмотреть запись пакетов, прочитав статью [Packet capture analysis with Wireshark](network-watcher-alert-triggered-packet-capture.md) (Анализ записи пакетов с помощью Wireshark).

[1]: ./media/network-watcher-alert-triggered-packet-capture/figure1.png
[1-1]: ./media/network-watcher-alert-triggered-packet-capture/figure1-1.png
[2]: ./media/network-watcher-alert-triggered-packet-capture/figure2.png
[3]: ./media/network-watcher-alert-triggered-packet-capture/figure3.png
[functions1]:./media/network-watcher-alert-triggered-packet-capture/functions1.png
[functions2]:./media/network-watcher-alert-triggered-packet-capture/functions2.png
[functions3]:./media/network-watcher-alert-triggered-packet-capture/functions3.png
[functions4]:./media/network-watcher-alert-triggered-packet-capture/functions4.png
[functions5]:./media/network-watcher-alert-triggered-packet-capture/functions5.png
[functions6]:./media/network-watcher-alert-triggered-packet-capture/functions6.png
[functions7]:./media/network-watcher-alert-triggered-packet-capture/functions7.png
[functions8]:./media/network-watcher-alert-triggered-packet-capture/functions8.png
[functions9]:./media/network-watcher-alert-triggered-packet-capture/functions9.png
[functions10]:./media/network-watcher-alert-triggered-packet-capture/functions10.png
[functions11]:./media/network-watcher-alert-triggered-packet-capture/functions11.png
[functions12]:./media/network-watcher-alert-triggered-packet-capture/functions12.png
[functions13]:./media/network-watcher-alert-triggered-packet-capture/functions13.png
[functions14]:./media/network-watcher-alert-triggered-packet-capture/functions14.png
[scenario]:./media/network-watcher-alert-triggered-packet-capture/scenario.png

