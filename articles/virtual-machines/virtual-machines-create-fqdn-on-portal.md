<properties
   pageTitle="Создание полного доменного имени для виртуальной машины на портале Azure | Microsoft Azure"
   description="Узнайте, как создать полное доменное имя (FQDN) для виртуальной машины на основе диспетчера ресурсов на портале Azure."
   services="virtual-machines"
   documentationCenter=""
   authors="dsk-2015"
   manager="timlt"
   editor="tysonn"
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="01/19/2016"
   ms.author="dkshir"/>

# Создание полного доменного имени на портале Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]Классическая модель развертывания.


При создании виртуальной машины на [портале Azure](https://portal.azure.com) с помощью модели развертывания **диспетчера ресурсов** портал создает ресурс общедоступного IP-адреса для виртуальной машины. Этот IP-адрес можно использовать для удаленного доступа к виртуальной машине. Несмотря на то, что портал не создает [полное доменное имя](https://en.wikipedia.org/wiki/Fully_qualified_domain_name) по умолчанию, это легко можно сделать после того, как будет создана виртуальная машина. В этой статье показан процесс создания DNS-имени или полного доменного имени.

В этой статье предполагается, что вы вошли в свою подписку в портале и создали виртуальную машину из доступных образов с помощью **диспетчера ресурсов**. После запуска виртуальной машины выполните следующие действия.

1.  Откройте параметры виртуальной машины в портале и щелкните в поле "Общедоступный IP-адрес".

    ![найдите ресурс IP-адреса](media/virtual-machines-create-fqdn-on-portal/locatePublicIP.PNG)

2.  Обратите внимание на то, что для общедоступного IP-адреса DNS-имя будет пустым. Щелкните **Все параметры** в колонке "Общедоступный IP-адрес".

    ![settings ip](media/virtual-machines-create-fqdn-on-portal/settingsIP.PNG)

3.  Откройте вкладку **Конфигурация** в разделе параметров общедоступного IP-адреса. Введите нужное DNS-имя и **сохраните** конфигурацию.

    ![введите метку имени DNS](media/virtual-machines-create-fqdn-on-portal/dnsNameLabel.PNG)

    Новое DNS-имя появится на вкладке ресурса с общедоступным IP-адресом.

4.  Закройте колонки общедоступного IP-адреса и вернитесь в колонку виртуальной машины на портале. Убедитесь, что DNS-имя или полное доменное имя отображается рядом с IP-адресом ресурса с общедоступным IP-адресом.

    ![создается полное доменное имя](media/virtual-machines-create-fqdn-on-portal/fqdnCreated.PNG)


    Теперь вы можете удаленно подключиться к виртуальной машине с помощью этого имени DNS. Например, воспользуйтесь командой `SSH adminuser@testdnslabel.centralus.cloudapp.azure.com` для подключения к виртуальной машине Linux с полным доменным именем `testdnslabel.centralus.cloudapp.azure.com` от имени пользователя `adminuser`.

<!---HONumber=AcomDC_0128_2016-->