---
title: "Синхронизация Azure AD Connect: рабочие задачи и рекомендации | Документация Майкрософт"
description: "В этой статье описываются рабочие задачи служб синхронизации Azure AD Connect и подготовка к работе с этим компонентом."
services: active-directory
documentationcenter: 
author: AndKjell
manager: femila
editor: 
ms.assetid: b29c1790-37a3-470f-ab69-3cee824d220d
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/13/2017
ms.author: billmath
ms.translationtype: Human Translation
ms.sourcegitcommit: db7cb109a0131beee9beae4958232e1ec5a1d730
ms.openlocfilehash: 0288d70bb5c0094b5c738b2d0c597e4c6d38a5aa
ms.contentlocale: ru-ru
ms.lasthandoff: 04/18/2017

---
# Службы синхронизации Azure AD Connect: рабочие задачи и рекомендации
<a id="azure-ad-connect-sync-operational-tasks-and-consideration" class="xliff"></a>
В этой статье описаны рабочие задачи служб синхронизации Azure AD Connect.

## Промежуточный режим
<a id="staging-mode" class="xliff"></a>
Промежуточный режим может использоваться в нескольких ситуациях, например:

* обеспечение высокой доступности;
* тестирование и развертывание новых изменений в конфигурации;
* введение в эксплуатацию нового сервера и списание старого.

Когда сервер работает в промежуточном режиме, вы можете вносить изменения в конфигурацию и просматривать их, прежде чем активировать сервер. Этот режим также позволяет запустить полный импорт и полную синхронизацию, чтобы проверить, все ли изменения имеют нужный вид, прежде чем вносить их в рабочую среду.

Во время установки для сервера можно выбрать **промежуточный режим**. В таком случае сервер будет выполнять импорт и синхронизацию, но не будет выполнять операции экспорта. Сервер в промежуточном режиме не выполняет синхронизацию и обратную запись паролей, даже если эти функции активированы при установке. При отключении промежуточного режима сервер начнет выполнять экспорт, а также синхронизацию и обратную запись паролей.

Вы по-прежнему можете выполнить экспорт принудительно с помощью Synchronization Service Manager.

Сервер в промежуточном режиме продолжает получать изменения из Active Directory и Azure AD. На сервере всегда есть копии последних изменений, и он сможет очень быстро принять на себя функции другого сервера. При внесении изменений в конфигурацию сервера-источника обязательно внесите те же изменения в конфигурацию серверов в промежуточном режиме.

Тем, кто знаком с более ранними технологиями синхронизации, следует помнить об отличиях промежуточного режима, поскольку сервер имеет собственную базу данных SQL. Благодаря такой архитектуре сервер в промежуточном режиме может располагаться в другом центре обработки данных.

### Проверка конфигурации сервера
<a id="verify-the-configuration-of-a-server" class="xliff"></a>
Чтобы использовать этот метод, выполните следующие действия.

1. [Подготовка](#prepare)
2. [Конфигурация](#configuration)
3. [Импорт и синхронизация](#import-and-synchronize)
4. [Проверка](#verify)
5. [Переключение активного сервера](#switch-active-server)

#### Подготовка.
<a id="prepare" class="xliff"></a>
1. Установите Azure AD Connect, выберите **промежуточный режим** и снимите флажок **Запустить синхронизацию** на последней странице мастера установки. В этом режиме можно вручную запустить модуль синхронизации.
   ![ReadyToConfigure](./media/active-directory-aadconnectsync-operations/readytoconfigure.png)
2. Выйдите из системы и снова войдите в нее, а затем в меню "Пуск" выберите пункт **Служба синхронизации**.

#### Конфигурация
<a id="configuration" class="xliff"></a>
Если вы внесли какие-либо изменения на сервере-источнике и хотите сравнить конфигурацию с промежуточным сервером, то воспользуйтесь [средством документирования конфигураций Azure AD Connect](https://github.com/Microsoft/AADConnectConfigDocumenter).

#### Импорт и синхронизация
<a id="import-and-synchronize" class="xliff"></a>
1. Щелкните **Соединители** и выберите первый соединитель типа **Доменные службы Active Directory**. Щелкните **Запуск**, выберите **Полный импорт** и нажмите кнопку **ОК**. Проделайте это для всех соединителей данного типа.
2. Выберите соединитель типа **Azure Active Directory (Майкрософт)**. Щелкните **Запуск**, выберите **Полный импорт** и нажмите кнопку **ОК**.
3. Убедитесь, что все еще выбрана вкладка "Соединители". Для каждого соединителя типа **Доменные службы Active Directory** щелкните **Запуск**, выберите **Синхронизация изменений** и нажмите кнопку **ОК**.
4. Выберите соединитель типа **Azure Active Directory (Майкрософт)**. Щелкните **Запуск**, выберите**Синхронизация изменений**) и нажмите кнопку **ОК**.

Теперь изменения экспорта размещены в Azure AD и локальном каталоге AD (если используется гибридное развертывание Exchange). С помощью описанных далее действий вы сможете проверить предстоящие изменения, прежде чем фактически запустить экспорт в каталоги.

#### Проверка
<a id="verify" class="xliff"></a>
1. Откройте командную строку и перейдите в каталог `%ProgramFiles%\Microsoft Azure AD Sync\bin`.
2. Выполните команду `csexport "Name of Connector" %temp%\export.xml /f:x`. Имя соединителя можно найти в службе синхронизации. Это будет имя наподобие "contoso.com — AAD" для Azure AD.
3. Скопируйте сценарий PowerShell из раздела [CSAnalyzer](#appendix-csanalyzer) в файл `csanalyzer.ps1`.
4. Откройте окно PowerShell и перейдите в папку, в которой вы создали сценарий PowerShell.
5. Выполните команду `.\csanalyzer.ps1 -xmltoimport %temp%\export.xml`.
6. Теперь у вас есть файл **processedusers1.csv**, который можно просмотреть в Microsoft Excel. В этом файле находятся все изменения, предназначенные для экспорта в Azure AD.
7. Внесите необходимые изменения в данные и конфигурацию и выполните описанные выше действия (импорт, синхронизация и проверка) повторно, чтобы привести изменения, которые предстоит экспортировать, в нужный вид.

#### Переключение активного сервера
<a id="switch-active-server" class="xliff"></a>
1. Отключите текущий сервер (DirSync, FIM или Azure AD Sync), чтобы он не выполнял экспорт в Azure AD, или переведите его в промежуточный режим (Azure AD Connect).
2. Запустите мастер установки на сервере в **промежуточном режиме** и отключите **промежуточный режим**.
   ![ReadyToConfigure](./media/active-directory-aadconnectsync-operations/additionaltasks.png)

## Аварийное восстановление
<a id="disaster-recovery" class="xliff"></a>
Реализация предусматривает возможность планирования действий в случае потери подключения к серверу синхронизации. Вы можете воспользоваться одной из нескольких моделей. Выбор зависит от ответов на следующие вопросы:

* Насколько приемлема для вас ситуация, когда вы не можете вносить изменения в объекты в Azure AD во время простоя?
* Если вы используете синхронизацию паролей, согласятся ли пользователи использовать старые пароли в Azure AD в случае их изменения в локальной среде?
* Зависите ли вы от выполняемых в реальном времени операций, таких как обратная запись паролей?

В зависимости от политики вашей организации и ответов на эти вопросы можно реализовать одну из следующих стратегий:

* Восстановление при необходимости.
* Наличие запасного резервного сервера ( **промежуточный режим**).
* Использование виртуальных машин.

Если встроенная база данных SQL Express не используется, просмотрите раздел [Высокий уровень доступности сервера SQL](#sql-high-availability) .

### Восстановление при необходимости
<a id="rebuild-when-needed" class="xliff"></a>
Одна из эффективных стратегий заключается в возможности восстановления сервера при необходимости. Обычно установка модуля синхронизации и выполнение начального импорта и синхронизации занимают несколько часов. Если нет резервного сервера, модуль синхронизации можно временно разместить на контроллере домена.

Сервер модуля синхронизации не сохраняет состояние объектов, поэтому базу данных можно восстановить из данных в Active Directory и Azure AD. Для объединения объектов из локальной среды и облака используется атрибут **sourceAnchor** . Если восстанавливается сервер с имеющимися объектами в локальной среде и в облаке, то при повторной установке модуль синхронизации повторно сопоставляет эти объекты. Изменения в конфигурации сервера, в частности изменения в правилах фильтрации и синхронизации, необходимо задокументировать и сохранить. Эти пользовательские конфигурации необходимо применить повторно перед началом синхронизации.

### Наличие запасного резервного сервера (промежуточный режим)
<a id="have-a-spare-standby-server---staging-mode" class="xliff"></a>
В более сложной среде рекомендуется использовать один или несколько резервных серверов. Во время установки один из серверов можно перевести в **промежуточный режим**.

Дополнительные сведения см. в разделе [Промежуточный режим](#staging-mode).

### Использование виртуальных машин
<a id="use-virtual-machines" class="xliff"></a>
Также поддерживается распространенный метод, предусматривающий работу модуля синхронизации на виртуальной машине. Если на узле возникнут проблемы, образ сервера с модулем синхронизации можно будет перенести на другой сервер.

### Высокий уровень доступности сервера SQL
<a id="sql-high-availability" class="xliff"></a>
Если вы не используете выпуск SQL Server Express, входящий в Azure AD Connect, вам следует также рассмотреть обеспечение высокого уровня доступности для SQL Server. Единственное поддерживаемое решение для обеспечения высокого уровня доступности — кластеризация SQL. Такие решения, как зеркальное отображение и AlwaysOn, не поддерживаются.

## Приложение CSAnalyzer
<a id="appendix-csanalyzer" class="xliff"></a>
Сведения об использовании этого сценария см. в разделе [Проверка](#verify).

```
Param(
    [Parameter(Mandatory=$true, HelpMessage="Must be a file generated using csexport 'Name of Connector' export.xml /f:x)")]
    [string]$xmltoimport="%temp%\exportedStage1a.xml",
    [Parameter(Mandatory=$false, HelpMessage="Maximum number of users per output file")][int]$batchsize=1000,
    [Parameter(Mandatory=$false, HelpMessage="Show console output")][bool]$showOutput=$false
)

#LINQ isn't loaded automatically, so force it
[Reflection.Assembly]::Load("System.Xml.Linq, Version=3.5.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089") | Out-Null

[int]$count=1
[int]$outputfilecount=1
[array]$objOutputUsers=@()

#XML must be generated using "csexport "Name of Connector" export.xml /f:x"
write-host "Importing XML" -ForegroundColor Yellow

#XmlReader.Create won't properly resolve the file location,
#so expand and then resolve it
$resolvedXMLtoimport=Resolve-Path -Path ([Environment]::ExpandEnvironmentVariables($xmltoimport))

#use an XmlReader to deal with even large files
$result=$reader = [System.Xml.XmlReader]::Create($resolvedXMLtoimport) 
$result=$reader.ReadToDescendant('cs-object')
do 
{
    #create the object placeholder
    #adding them up here means we can enforce consistency
    $objOutputUser=New-Object psobject
    Add-Member -InputObject $objOutputUser -MemberType NoteProperty -Name ID -Value ""
    Add-Member -InputObject $objOutputUser -MemberType NoteProperty -Name Type -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name DN -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name operation -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name UPN -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name displayName -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name sourceAnchor -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name alias -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name primarySMTP -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name onPremisesSamAccountName -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name mail -Value ""

    $user = [System.Xml.Linq.XElement]::ReadFrom($reader)
    if ($showOutput) {Write-Host Found an exported object... -ForegroundColor Green}

    #object id
    $outID=$user.Attribute('id').Value
    if ($showOutput) {Write-Host ID: $outID}
    $objOutputUser.ID=$outID

    #object type
    $outType=$user.Attribute('object-type').Value
    if ($showOutput) {Write-Host Type: $outType}
    $objOutputUser.Type=$outType

    #dn
    $outDN= $user.Element('unapplied-export').Element('delta').Attribute('dn').Value
    if ($showOutput) {Write-Host DN: $outDN}
    $objOutputUser.DN=$outDN

    #operation
    $outOperation= $user.Element('unapplied-export').Element('delta').Attribute('operation').Value
    if ($showOutput) {Write-Host Operation: $outOperation}
    $objOutputUser.operation=$outOperation

    #now that we have the basics, go get the details

    foreach ($attr in $user.Element('unapplied-export-hologram').Element('entry').Elements("attr"))
    {
        $attrvalue=$attr.Attribute('name').Value
        $internalvalue= $attr.Element('value').Value

        switch ($attrvalue)
        {
            "userPrincipalName"
            {
                if ($showOutput) {Write-Host UPN: $internalvalue}
                $objOutputUser.UPN=$internalvalue
            }
            "displayName"
            {
                if ($showOutput) {Write-Host displayName: $internalvalue}
                $objOutputUser.displayName=$internalvalue
            }
            "sourceAnchor"
            {
                if ($showOutput) {Write-Host sourceAnchor: $internalvalue}
                $objOutputUser.sourceAnchor=$internalvalue
            }
            "alias"
            {
                if ($showOutput) {Write-Host alias: $internalvalue}
                $objOutputUser.alias=$internalvalue
            }
            "proxyAddresses"
            {
                if ($showOutput) {Write-Host primarySMTP: ($internalvalue -replace "SMTP:","")}
                $objOutputUser.primarySMTP=$internalvalue -replace "SMTP:",""
            }
        }
    }

    $objOutputUsers += $objOutputUser

    Write-Progress -activity "Processing ${xmltoimport} in batches of ${batchsize}" -status "Batch ${outputfilecount}: " -percentComplete (($objOutputUsers.Count / $batchsize) * 100)

    #every so often, dump the processed users in case we blow up somewhere
    if ($count % $batchsize -eq 0)
    {
        Write-Host Hit the maximum users processed without completion... -ForegroundColor Yellow

        #export the collection of users as as CSV
        Write-Host Writing processedusers${outputfilecount}.csv -ForegroundColor Yellow
        $objOutputUsers | Export-Csv -path processedusers${outputfilecount}.csv -NoTypeInformation

        #increment the output file counter
        $outputfilecount+=1

        #reset the collection and the user counter
        $objOutputUsers = $null
        $count=0
    }

    $count+=1

    #need to bail out of the loop if no more users to process
    if ($reader.NodeType -eq [System.Xml.XmlNodeType]::EndElement)
    {
        break
    }

} while ($reader.Read)

#need to write out any users that didn't get picked up in a batch of 1000
#export the collection of users as as CSV
Write-Host Writing processedusers${outputfilecount}.csv -ForegroundColor Yellow
$objOutputUsers | Export-Csv -path processedusers${outputfilecount}.csv -NoTypeInformation
```

## Дальнейшие действия
<a id="next-steps" class="xliff"></a>
**Обзорные статьи**  

* [Службы синхронизации Azure AD Connect: общие сведений о синхронизации и ее настройка](active-directory-aadconnectsync-whatis.md)  
* [Интеграция локальных удостоверений с Azure Active Directory](active-directory-aadconnect.md)  

