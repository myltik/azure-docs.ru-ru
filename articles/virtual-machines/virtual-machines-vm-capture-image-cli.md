<properties
	pageTitle="Запись образа виртуальной машины под управлением Linux с помощью CLI | Microsoft Azure"
	description="Узнайте, как записать образ виртуальной машины (ВМ) Azure под управлением Linux."
	services="virtual-machines"
	documentationCenter=""
	authors="madhana"
	manager="timlt"
	editor="tysonn"
    tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/20/2015"
	ms.author="madhana"/>




# Запись образа виртуальной машины Linux с помощью CLI для использования в качестве шаблона##

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]В этой статье описывается процесс создания ресурсов с помощью классической модели развертывания.

В этой статье показано, как записать виртуальную машину Azure под управлением Linux, чтобы использовать ее в качестве шаблона при создании других виртуальных машин. Данный шаблон виртуальной машины включает в себя диск операционной системы и прочие диски данных, присоединенные к виртуальной машине. Он не включает в себя сетевую конфигурацию, поэтому ее необходимо настроить при создании других виртуальных машин, использующих шаблон.



Azure воспринимает этот шаблон как образ и отображает его в списке образов. В этой папке также хранятся все переданные вами образы. Дополнительные сведения об образах см. в статье [Об образах виртуальной машины в Azure][].



##Перед началом работы##



В шагах этой статьи предполагается, что вы уже создали виртуальную машину Azure и настроили операционную систему, а также присоединили диски данных. Если это еще не сделано, см. данные указания:



- [Создание настраиваемой виртуальной машины][]

- [Подключение диска с данными к виртуальной машине][]



##Запись виртуальной машины##



1. Подключитесь к виртуальной машине. Дополнительную информацию см. в разделе [Как войти в виртуальную машину под управлением Linux][].



2. Для записи образа виртуальной машины она должна находиться в состоянии **StoppedDeallocated**. Чтобы завершить работу виртуальной машины, введите в окне SSH команду:



        vm shutdown [options] <vm-name>



    Обратите внимание, что среди параметров команды `vm shutdown` есть параметр `-p`, который позволяет сохранить вычислительный ресурс после завершения работы виртуальной машины. **Не** включайте эту функцию, так как для создания образа виртуальная машина должна быть отозвана.



3. Чтобы записать образ виртуальной машины, выполните следующую команду:



        vm capture <vm-name> <target-image-name> --deleted



    Для записи образа виртуальную машину нужно удалить. Для этого можно использовать параметр `--deleted` или `-t`.



4. Чтобы убедиться, что образ записан, проверьте список образов виртуальных машин:



        vm image list | grep <target-image-name>



    Фрагмент `| grep <target-image-name>` является необязательным, но упрощает поиск нужного образа в списке образов.



Ниже приведен пример последовательности действий при записи образа виртуальной машины, включая выходные данные терминала:


    ~$ azure vm list

    info:    Executing command vm list

    + Getting virtual machines

    data:    Name         Status            Location  DNS Name                  IP Address

    data:    -----------  ----------------  --------  ------------------------  ------------

    data:    kmorig       RoleStateUnknown  West US   kmorig.cloudapp.net       100.92.56.16

    info:    vm list command OK

    ~$ azure vm shutdown kmorig

    info:    Executing command vm shutdown

    + Getting virtual machines

    + Shutting down VM

    info:    vm shutdown command OK

    ~$ azure vm list

    info:    Executing command vm list

    + Getting virtual machines

    data:    Name         Status              Location  DNS Name                  IP Address

    data:    -----------  ------------------  --------  ------------------------  ----------

    data:    kmorig       StoppedDeallocated  West US   kmorig.cloudapp.net

    info:    vm list command OK

    ~$ azure vm capture kmorig kmcaptured --deleted

    info:    Executing command vm capture

    + Getting virtual machines

    + Checking image with name kmcaptured exists

    + Capturing VM

    info:    vm capture command OK

    ~$ azure vm image list | grep kmcaptured

    data:    kmcaptured



Дополнительную информацию и дополнительные команды см. на [странице документации по интерфейсу командной строки Azure][].


[странице документации по интерфейсу командной строки Azure]: ../virtual-machines-command-line-tools.md

[Как войти в виртуальную машину под управлением Linux]: virtual-machines-linux-how-to-log-on.md

[Об образах виртуальной машины в Azure]: http://msdn.microsoft.com/library/azure/dn790290.aspx

[Создание настраиваемой виртуальной машины]: virtual-machines-create-custom.md

[Подключение диска с данными к виртуальной машине]: storage-windows-attach-disk.md
 

<!---HONumber=Oct15_HO2-->