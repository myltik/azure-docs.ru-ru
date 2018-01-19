<!--save a copy of this file to your local repo. It's important that you follow the naming conventions by starting with the service name, and use lowercase only for the file name. See "file-names-and-locations.md" under the "contributor-guide" folder in your repo.

Info to help you use the template are enclosed in the Markdown comments using the caret, hyphen, dash syntax. Delete these from your file.

Text not wrapped in comment syntax is intended to be used as is, or with updates enclosed in [  ]. Add the info and delete the bracket. 

Pay attention to spacing and indents. They affect formatting. 

--> 

<!--replace this with Properties and Tags sections. These are required sections. See "article-metadata.md" in under the "contributor-guide" folder in your repo. Attributes in each section can be placed on separate lines to make them easier to read and check-->

# <a name="use-azure-powershell-to-task"></a>[задача] с помощью Azure PowerShell
В этой статье показано, как [задача] с помощью команд в модулях Azure и Azure Resource Manager. Это поможет вам изучить новые команды, а также перенести имеющиеся сценарии в новые команды.

## <a name="prerequisite-install-a-recent-version-of-azure-powershell"></a>Предварительные требования: установка последней версии Azure PowerShell
Установите на локальном компьютере Azure PowerShell версии [номер версии], если вы еще не сделали этого. В более ранних версиях отсутствуют необходимые нам командлеты Azure Resource Manager. Дополнительные сведения см. в статье:

* Инструкции по установке и настройке Azure PowerShell см. в [этой статье](install-configure-powershell.md).
* Основные сведения об использовании диспетчера ресурсов см. в статье [Управление ресурсами с помощью Azure PowerShell и Resource Manager](powershell-azure-resource-manager.md).

> [!NOTE]
> Для выполнения большинства задач понадобится командная строка Azure PowerShell уровня администратора.
> 
> 

## <a name="command-comparison"></a>Сравнение команд
В этой [таблица | раздел] показан синтаксис команды.

<!--[optional image - to use an image in this article, add a folder with the same name as the article file name without extension, inside the Media folder of the repo. Use only this folder to store the images. Don't attempt to use a common folder to share images you want to use in more than 1 file.]
Then, use the following syntax to add a reference to the image in your article:
![](./media/name-of-file-without-extension/image-name-no-spaces.png)
-->

<!--if a command string uses variables, define the variables first, using the  following construction. In some cases the variable is straightforward and doesn't need much explanation. But parameters such as location and size can benefit from brief explanation or listing all accepted values:--> 

В этих примерах команд используются следующие переменные:

$FriendlyName"<Describe value>"

<!-- if it makes more sense to present this in a table, use this. Otherwise, delete. The table won't render until it's in GitHub or published to Sandbox.-->

| Service Management | Диспетчер ресурсов |
| --- | --- |
| `syntax` |`syntax` |

<!--if it makes more sense to present this one command block after the other instead of a table, use this. Otherwise, delete-->

[Короткое вступительное предложение о команде. Если дополнительной информации нет, опустите этот шаг. Но если используются такие подходы, как конвейер, предоставьте объяснение]:

    [command string]

## <a name="script-examples"></a>Примеры сценариев
Здесь представлен пример, в котором используется [имена командлетов] для [задача]. Он включает команды, которые:

* [короткий глагол: использует, имеет, является и т. д.]
* [следующий глагол] 

<!--include this statement if it uses variables that weren't introduced earlier--> It includes the following variables:

* [переменная 1]
* [переменная 2]

<!--This shows you how a recent example was presented as well as how it was formatted. Preceding each line with one tab or four spaces to format in a code block-->

    $family="Windows Server 2012 R2 Datacenter"
    $image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
    $vmname="AZDC1"
    $vmsize="Medium"
    $vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

    $cred=Get-Credential -Message "Type the name and password of the local administrator account."
    $vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password

    $vm1 | Set-AzureSubnet -SubnetNames "BackEnd"

    $vm1 | Set-AzureStaticVNetIP -IPAddress 192.168.244.4

    $disksize=20
    $disklabel="DCData"
    $lun=0
    $hcaching="None"
    $vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB $disksize -DiskLabel $disklabel -LUN $lun -HostCaching $hcaching

    $svcname="Azure-TailspinToys"
    $vnetname="AZDatacenter"
    New-AzureVM –ServiceName $svcname -VMs $vm1 -VNetName $vnetname


## <a name="additional-resources"></a>Дополнительные ресурсы
<!--At a minimum, include a link back to the migration task list article. Use the formats shown below. See create-links-markdown.md for more info -->
<!--use this format for links to other articles, such as the migration task list. -->
[Manage the availability of Windows virtual machines in Azure](../articles/virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Управление доступностью виртуальных машин Windows в Azure)

<!--To link to an ACOM page outside the /documentation/ subdomain (such as a pricing page, SLA page or anything else that is not a documentation article), use an absolute URL, but omit the locale:

    [link text](http://azure.microsoft.com/pricing/details/virtual-machines/)-->

<!--use this for URLs outside of ACOM. Be sure to locale, and if you're linking to the Azure library on MSDN, include the '/azure/' part of the URL-->
[Документация по Microsoft Azure](https://msdn.microsoft.com/library/azure/jj156003.aspx)

