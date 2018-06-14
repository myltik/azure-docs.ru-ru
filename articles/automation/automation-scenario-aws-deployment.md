---
title: Автоматизация развертывания виртуальной машины в Amazon Web Services
description: В этой статье показано, как автоматизировать создание виртуальной машины Amazon Web Service с помощью службы автоматизации Azure.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 4f49adf006e8d55337220fad9ee84de65209880b
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34193487"
---
# <a name="azure-automation-scenario---provision-an-aws-virtual-machine"></a>Сценарий службы автоматизации Azure: подготовка виртуальной машины AWS
В этой статье вы узнаете, как с помощью службы автоматизации Azure подготовить виртуальную машину в подписке Amazon Web Service (AWS) и присвоить виртуальной машине конкретное имя, которое в AWS называется "тегом" виртуальной машины.

## <a name="prerequisites"></a>предварительным требованиям
Для выполнения инструкций из этой статьи вам понадобится учетная запись службы автоматизации Azure и подписка AWS. Дополнительные сведения о настройке учетной записи службы автоматизации Azure и об указании в ней учетных данных подписки AWS см. в статье [Проверка подлинности модулей Runbook с помощью Amazon Web Services](automation-config-aws-account.md). Перед выполнением дальнейших действий необходимо создать эту учетную запись с учетными данными подписки AWS или указать в ней эти учетные данные, так как вы будете ссылаться на нее при выполнении указанных ниже шагов.

## <a name="deploy-amazon-web-services-powershell-module"></a>Развертывание модуля PowerShell для Amazon Web Services
В процессе работы ваш модуль Runbook для подготовки виртуальной машины использует модуль PowerShell для AWS. Выполните следующие действия, чтобы добавить в учетную запись службы автоматизации модуль, настроенный с учетными данными подписки AWS.  

1. Откройте браузер и перейдите к [коллекции PowerShell](http://www.powershellgallery.com/packages/AWSPowerShell/), а затем нажмите кнопку **Deploy to Azure Automation** (Развернуть в службе автоматизации Azure).<br><br> ![Импорт модуля AWS PS](./media/automation-scenario-aws-deployment/powershell-gallery-download-awsmodule.png)
2. Вы перейдете на страницу входа в Azure. После проверки подлинности откроется следующая страница портала Azure.<br><br> ![Страница импорта модуля](./media/automation-scenario-aws-deployment/deploy-aws-powershell-module-parameters.png)
3. Выберите учетную запись службы автоматизации и нажмите кнопку **ОК** для запуска развертывания.

   > [!NOTE]
   > Во время импорта модуля PowerShell в службе автоматизации Azure также извлекаются командлеты. Эти действия не будут отображаться, пока модуль не завершит импорт и извлечение командлетов. Это может занять несколько минут.  
   > <br>

1. На портале Azure выберите свою учетную запись службы автоматизации, указанную в шаге 3.
2. Щелкните плитку **Ресурсы** и на странице **Ресурсы** выберите плитку **Модули**.
3. На странице **Модули** вы увидите в списке модуль **AWSPowerShell**.

## <a name="create-aws-deploy-vm-runbook"></a>Создание модуля Runbook для развертывания виртуальной машины в AWS
После развертывания модуля AWS PowerShell можно создать модуль Runbook для автоматизации подготовки виртуальной машины в AWS с помощью сценария PowerShell. Приведенные ниже шаги показывают, как использовать собственный сценарий PowerShell в службе автоматизации Azure.  

> [!NOTE]
> Дополнительные параметры и сведения, касающиеся этого сценария, можно найти в [коллекции PowerShell](https://www.powershellgallery.com/packages/New-AwsVM/DisplayScript).
> 

1. Загрузите сценарий PowerShell New-AwsVM из коллекции PowerShell, открыв сеанс PowerShell и введя следующую команду:<br>
   ```
   Save-Script -Name New-AwsVM -Path <path>
   ```
   <br>
2. На портале Azure в учетной записи автоматизации выберите **Модули runbook** в разделе **Автоматизация процессов** в левой части экрана.  
3. На странице **Модули Runbook** выберите **Добавить Runbook**.
4. На странице **Добавить Runbook** выберите **Быстрое создание** (Создание нового модуля Runbook).
5. На странице свойств **модуля Runbook** введите имя модуля в поле "Имя" и выберите **PowerShell** в раскрывающемся списке **Тип Runbook**, затем нажмите кнопку **Создать**.<br><br> ![Панель создания модуля Runbook](./media/automation-scenario-aws-deployment/runbook-quickcreate-properties.png)
6. При открытии страницы "Изменение сценария PowerShell для модуля Runbook" скопируйте и вставьте сценарий PowerShell в область создания модуля Runbook.<br><br> ![Сценарий PowerShell для модуля Runbook](./media/automation-scenario-aws-deployment/runbook-powershell-script.png)<br>
   
    > [!NOTE]
    > При работе с примером сценария PowerShell имейте в виду следующее:
    > 
    > * Модуль Runbook содержит несколько параметров по умолчанию. Проверьте все значения по умолчанию и при необходимости обновите их.
    > * Если вы сохранили учетные данные AWS как ресурс учетных данных с именем, отличным от **AWScred**, потребуется соответствующим образом обновить скрипт в строке 57.  
    > * При работе с командами AWS в PowerShell, особенно в этом примере модуля Runbook, необходимо указывать регион AWS. В противном случае командлеты завершатся с ошибкой. Дополнительные сведения на сайте AWS см. в разделе [Specify AWS Region](http://docs.aws.amazon.com/powershell/latest/userguide/pstools-installing-specifying-region.html) (Указание региона AWS) документации "AWS Tools for Windows PowerShell" (Инструменты AWS для Windows PowerShell).  
    >

7. Чтобы получить список имен образов из подписки AWS, запустите PowerShell ISE и импортируйте модуль AWS PowerShell. Пройдите аутентификацию в AWS, заменив в среде ISE **Get-AutomationPSCredential** на **AWScred = Get-Credential**. После этого появится запрос на ввод учетных данных, и вы сможете указать **идентификатор ключа доступа** в качестве имени пользователя и **секретный ключ доступа** в качестве пароля. См. пример ниже.  

        #Sample to get the AWS VM available images
        #Please provide the path where you have downloaded the AWS PowerShell module
        Import-Module AWSPowerShell
        $AwsRegion = "us-west-2"
        $AwsCred = Get-Credential
        $AwsAccessKeyId = $AwsCred.UserName
        $AwsSecretKey = $AwsCred.GetNetworkCredential().Password
   
        # Set up the environment to access AWS
        Set-AwsCredentials -AccessKey $AwsAccessKeyId -SecretKey $AwsSecretKey -StoreAs AWSProfile
        Set-DefaultAWSRegion -Region $AwsRegion
   
        Get-EC2ImageByName -ProfileName AWSProfile

    Возвращается следующий результат:<br><br>
   ![Получение образов AWS](./media/automation-scenario-aws-deployment/powershell-ise-output.png)<br>  
8. Скопируйте и вставьте имя одного из образов в переменную службы автоматизации, которая в модуле Runbook указана как **$InstanceType**. Так как в этом примере используется подписка AWS уровня "Бесплатный", в примере модуля Runbook нужно использовать **t2.micro**.  
9. Сохраните модуль Runbook, нажмите кнопку **Опубликовать**, чтобы опубликовать модуль Runbook, а затем кнопку **Да** в появившемся запросе.

### <a name="testing-the-aws-vm-runbook"></a>Тестирование модуля Runbook для AWS
Прежде чем перейти к тестированию модуля Runbook, необходимо проверить несколько моментов. В частности:  

* Ресурс для аутентификации в AWS создан. Ему присвоено имя **AWScred** или другое имя, которое было указано в вашем сценарии.    
* Модуль PowerShell для AWS импортирован в службу автоматизации Azure.  
* Создан новый модуль Runbook, а значения параметров проверены и при необходимости обновлены.  
* Для параметров **Подробные записи в журнале** и **Записи ведения журнала** в настройке **Ведение журналов и трассировка** модуля Runbook задано значение **Включено**.<br><br> ![Ведение журнала и трассировка для модуля Runbook](./media/automation-scenario-aws-deployment/runbook-settings-logging-and-tracing.png)  

1. Нужно запустить модуль Runbook, поэтому щелкните **Запустить**, а затем, когда откроется страница "Запуск Runbook", нажмите кнопку **ОК**.
2. На странице "Запуск модуля Runbook" укажите **имя виртуальной машины**. Примите значения по умолчанию для остальных параметров, которые были ранее указаны в скрипте. Нажмите кнопку **ОК**, чтобы запустить задание Runbook.<br><br> ![Запуск модуля Runbook: New-AwsVM](./media/automation-scenario-aws-deployment/runbook-start-job-parameters.png)
3. Откроется область заданий с созданным заданием runbook. Закройте эту область.
4. Ход выполнения задания и **потоки** вывода можно просмотреть, выбрав плитку **Все журналы** на странице задания Runbook.<br><br> ![Раздел "Поток": выходные данные](./media/automation-scenario-aws-deployment/runbook-job-streams-output.png)
5. Чтобы убедиться, что виртуальная машина подготавливается, войдите в консоль управления AWS, если вы еще не вошли в систему.<br><br> ![Консоль AWS: развернутая виртуальная машина](./media/automation-scenario-aws-deployment/aws-instances-status.png)

## <a name="next-steps"></a>Дополнительная информация
* Чтобы начать работу с графическими модулями Runbook, см. инструкции в статье [Первый графический Runbook](automation-first-runbook-graphical.md).
* Чтобы приступить к работе с модулями Runbook рабочих процессов PowerShell, обратитесь к статье [Мой первый модуль Runbook рабочего процесса PowerShell](automation-first-runbook-textual.md)
* Чтобы получить дополнительные сведения о типах модулей Runbook, их преимуществах и ограничениях, обратитесь к статье [Типы модулей Runbook в службе автоматизации Azure](automation-runbook-types.md)
* Дополнительные сведения о функции поддержки скриптов PowerShell см. в статье [Announcing Native PowerShell Script Support in Azure Automation](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/) (Встроенная поддержка скриптов PowerShell в службе автоматизации Azure).

