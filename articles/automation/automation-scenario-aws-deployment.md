---
title: "Автоматизация развертывания виртуальной машины в Amazon Web Services | Документация Майкрософт"
description: "В этой статье показано, как автоматизировать создание виртуальной машины Amazon Web Service с помощью службы автоматизации Azure."
services: automation
documentationcenter: 
author: mgoedtel
manager: carmonm
editor: 
ms.assetid: 1d85c01a-d795-4523-8194-84fc15b53838
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/14/2017
ms.author: tiandert; bwren
ms.translationtype: Human Translation
ms.sourcegitcommit: e851a3e1b0598345dc8bfdd4341eb1dfb9f6fb5d
ms.openlocfilehash: e0b784006b4933fe986890c09afa965934511784
ms.contentlocale: ru-ru
ms.lasthandoff: 04/15/2017

---
# <a name="azure-automation-scenario---provision-an-aws-virtual-machine"></a>Сценарий службы автоматизации Azure: подготовка виртуальной машины AWS
В этой статье мы покажем, как с помощью службы автоматизации Azure подготовить виртуальную машину в подписке Amazon Web Service (AWS) и присвоить виртуальной машине конкретное имя, которое в AWS называется "тегом" виртуальной машины.

## <a name="prerequisites"></a>Предварительные требования
Для выполнения инструкций из этой статьи вам понадобится учетная запись службы автоматизации Azure и подписка AWS. Дополнительные сведения о настройке учетной записи службы автоматизации Azure и об указании в ней учетных данных подписки AWS см. в статье [Проверка подлинности модулей Runbook с помощью Amazon Web Services](automation-configure-aws-account.md).  Перед выполнением дальнейших действий необходимо создать эту учетную запись с учетными данными подписки AWS или указать в ней эти учетные данные.

## <a name="deploy-amazon-web-services-powershell-module"></a>Развертывание модуля PowerShell для Amazon Web Services
В процессе работы наш модуль Runbook для подготовки виртуальной машины будет использовать модуль PowerShell для AWS. Выполните следующие действия, чтобы добавить в учетную запись службы автоматизации модуль, настроенный с учетными данными подписки AWS.  

1. Откройте браузер и перейдите к [коллекции PowerShell](http://www.powershellgallery.com/packages/AWSPowerShell/), а затем нажмите кнопку **Deploy to Azure Automation** (Развернуть в службе автоматизации Azure).<br><br> ![Импорт модуля AWS PS](./media/automation-scenario-aws-deployment/powershell-gallery-download-awsmodule.png)
2. Вы перейдете на страницу входа в Azure. После аутентификации откроется следующая колонка портала Azure.<br><br> ![Колонка "Импорт модуля"](./media/automation-scenario-aws-deployment/deploy-aws-powershell-module-parameters.png)
3. Выберите группу ресурсов из раскрывающегося списка **Группы ресурсов** и в колонке "Параметры" укажите следующие сведения:
   
   * В раскрывающемся списке **New or Existing Automation Account (string)** (Новая или существующая учетная запись службы автоматизации (строка)) выберите **Existing** (Существующая).  
   * В поле **Имя учетной записи службы автоматизации (строка)** введите точное имя учетной записи службы автоматизации, которая включает учетные данные для подписки AWS.  Например, если вы создали специальную учетную запись с именем **AWSAutomation**, то именно это имя и нужно ввести в поле.
   * Выберите соответствующий регион из раскрывающегося списка **Расположение учетной записи службы автоматизации** .
4. После ввода необходимых сведений нажмите кнопку **Создать**.
   
   > [!NOTE]
   > Во время импорта модуля PowerShell в службе автоматизации Azure также извлекаются командлеты. Эти действия не будут отображаться, пока модуль не завершит импорт и извлечение командлетов. Это может занять несколько минут.  
   > <br>
   > 
   > 
5. На портале Azure выберите свою учетную запись службы автоматизации, указанную в шаге 3.
6. Щелкните элемент **Ресурсы** и в колонке **Ресурсы** выберите элемент **Модули**.
7. В колонке **Модули** вы увидите в списке модуль **AWSPowerShell**.

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
2. На портале Azure откройте свою учетную запись службы автоматизации и щелкните элемент **Модули Runbook**.  
3. В колонке **Модули Runbook** выберите **Добавить Runbook**.
4. В колонке **Добавить Runbook** выберите **Быстрое создание** (Создание нового модуля Runbook).
5. В колонке свойств **модуля Runbook** введите имя модуля в поле "Имя" и выберите **PowerShell** в раскрывающемся списке **Тип Runbook**, затем нажмите кнопку **Создать**.<br><br> ![Колонка "Импорт модуля"](./media/automation-scenario-aws-deployment/runbook-quickcreate-properties.png)
6. При появлении колонки "Изменение сценария PowerShell для модуля Runbook" скопируйте и вставьте сценарий PowerShell в область создания модуля Runbook.<br><br> ![Сценарий PowerShell для модуля Runbook](./media/automation-scenario-aws-deployment/runbook-powershell-script.png)<br>
   
    > [!NOTE]
    > При работе с примером сценария PowerShell имейте в виду следующее:
    > 
    > * Модуль Runbook содержит несколько параметров по умолчанию. Проверьте все значения по умолчанию и при необходимости обновите их.
    > * Если вы сохранили учетные данные AWS как ресурс учетных данных с именем, отличным от **AWScred**, потребуется соответствующим образом обновить скрипт в строке 57.  
    > * При работе с командами AWS в PowerShell, особенно в этом примере модуля Runbook, необходимо указывать регион AWS. В противном случае командлеты завершатся с ошибкой.  Дополнительные сведения на сайте AWS см. в разделе [Specify AWS Region](http://docs.aws.amazon.com/powershell/latest/userguide/pstools-installing-specifying-region.html) (Указание региона AWS) документации "AWS Tools for Windows PowerShell" (Инструменты AWS для Windows PowerShell).  
    >

7. Чтобы получить список имен образов из подписки AWS, запустите PowerShell ISE и импортируйте модуль AWS PowerShell.  Пройдите аутентификацию в AWS, заменив в среде ISE **Get-AutomationPSCredential** на **AWScred = Get-Credential**.  После этого появится запрос на ввод учетных данных, и вы сможете указать **идентификатор ключа доступа** в качестве имени пользователя и **секретный ключ доступа** в качестве пароля.  См. пример ниже.  

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
8. Скопируйте и вставьте имя одного из образов в переменную службы автоматизации, которая в модуле Runbook указана как **$InstanceType**. Так как в этом примере используется подписка AWS уровня "Бесплатный", в примере модуля Runbook нужно использовать **t2.micro** .  
9. Сохраните модуль Runbook, нажмите кнопку **Опубликовать**, чтобы опубликовать модуль Runbook, а затем кнопку **Да** в появившемся запросе.

### <a name="testing-the-aws-vm-runbook"></a>Тестирование модуля Runbook для AWS
Прежде чем перейти к тестированию модуля Runbook, необходимо проверить несколько моментов. В частности:  

* Ресурс для аутентификации в AWS создан. Ему присвоено имя **AWScred** или другое имя, которое было указано в вашем сценарии.    
* Модуль PowerShell для AWS импортирован в службу автоматизации Azure.  
* Создан новый модуль Runbook, а значения параметров проверены и при необходимости обновлены.  
* Для параметров **Подробные записи в журнале** и **Записи ведения журнала** в настройке **Ведение журналов и трассировка** модуля Runbook задано значение **Включено**.<br><br> ![Ведение журнала и трассировка для модуля Runbook](./media/automation-scenario-aws-deployment/runbook-settings-logging-and-tracing.png)  

1. Нам нужно запустить модуль Runbook, поэтому щелкните **Запустить**, а затем, когда откроется колонка "Запуск Runbook", нажмите кнопку **ОК**.
2. В колонке "Запуск модуля Runbook" укажите **VMname**(имя виртуальной машины).  Примите значения по умолчанию для остальных параметров, которые были ранее указаны в скрипте.  Нажмите кнопку **ОК**, чтобы запустить задание Runbook.<br><br> ![Запуск модуля Runbook: New-AwsVM](./media/automation-scenario-aws-deployment/runbook-start-job-parameters.png)
3. Откроется область заданий с созданным нами заданием Runbook. Закройте эту область.
4. Ход выполнения задания и **Потоки** вывода можно просмотреть, выбрав элемент **Все журналы** в колонке задания Runbook.<br><br> ![Раздел "Поток": выходные данные](./media/automation-scenario-aws-deployment/runbook-job-streams-output.png)
5. Чтобы убедиться, что виртуальная машина подготавливается, войдите в консоль управления AWS, если вы еще не вошли в систему.<br><br> ![Консоль AWS: развернутая виртуальная машина](./media/automation-scenario-aws-deployment/aws-instances-status.png)

## <a name="next-steps"></a>Дальнейшие действия
* Чтобы начать работу с графическими модулями Runbook, см. инструкции в статье [Первый графический Runbook](automation-first-runbook-graphical.md).
* Чтобы приступить к работе с модулями Runbook рабочих процессов PowerShell, обратитесь к статье [Мой первый модуль Runbook рабочего процесса PowerShell](automation-first-runbook-textual.md)
* Чтобы получить дополнительные сведения о типах модулей Runbook, их преимуществах и ограничениях, обратитесь к статье [Типы модулей Runbook в службе автоматизации Azure](automation-runbook-types.md)
* Дополнительные сведения о функции поддержки скриптов PowerShell см. в статье [Announcing Native PowerShell Script Support in Azure Automation](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/) (Встроенная поддержка скриптов PowerShell в службе автоматизации Azure).


