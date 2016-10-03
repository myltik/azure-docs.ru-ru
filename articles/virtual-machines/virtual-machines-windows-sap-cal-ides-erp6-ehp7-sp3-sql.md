<properties 
pageTitle="Развертывание SAP IDES EHP7 SP3 для SAP ERP 6.0 в Microsoft Azure | Microsoft Azure" 
description="В статье описывается развертывание SAP IDES EHP7 SP3 для SAP ERP 6.0 в Microsoft Azure." 
services="virtual-machines-windows" 
documentationCenter="" 
authors="hermanndms" 
manager="timlt" 
editor="" 
tags="azure-resource-manager" 
keywords=""/>
<tags  
ms.service="virtual-machines-windows" 
ms.devlang="na" 
ms.topic="article" 
ms.tgt_pltfrm="vm-windows" 
ms.workload="infrastructure-services" 
ms.date="09/16/2016" 
ms.author="hermannd"/>


# Развертывание SAP IDES EHP7 SP3 для SAP ERP 6.0 в Microsoft Azure 

В этой статье описывается, как развернуть SAP IDES с SQL Server и Windows в Microsoft Azure с помощью SAP Cloud Appliance Library 3.0. На снимках экрана наглядно показан пошаговый процесс. С точки зрения процесса развертывание других решений из списка выполняется аналогичным образом. Нужно только выбрать другое решение.

Чтобы начать работу с SAP Cloud Appliance Library (SAP CAL), щелкните [здесь](https://cal.sap.com/). Вы перейдете на страницу блога SAP о новой версии [SAP Cloud Appliance Library 3.0](http://scn.sap.com/community/cloud-appliance-library/blog/2016/05/27/sap-cloud-appliance-library-30-came-with-a-new-user-experience).


Далее на снимках экрана показана пошаговая инструкция по развертыванию SAP IDES в Microsoft Azure. Процесс развертывания других решений аналогичен описанному ниже.


![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic1.jpg)

На первом рисунке показаны все решения, доступные в Microsoft Azure. Для развертывания выбрано выделенное решение SAP IDES на основе Windows, доступное только в Azure.

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic2.jpg)

Сначала нужно создать учетную запись SAP CAL. В настоящее время предлагается два варианта учетной записи Azure: стандартная учетная запись либо учетная запись на платформе Azure для континентальной части Китая под управлением компании-партнера 21Vianet.

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic3.jpg)

Затем нужно ввести идентификатор подписки Azure, который можно найти на портале Azure (ниже описано, как его получить). После этого необходимо скачать сертификат управления Azure.

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic6.jpg)

На новом портале Azure с левой стороны находится элемент "Подписки". Щелкните его, чтобы просмотреть все активные подписки пользователя.

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic7.jpg)

Выберите одну из подписок, а затем щелкните "Сертификаты управления". Это новая концепция использования субъектов-служб для новой модели Azure Resource Manager. Продукт SAP CAL еще не адаптирован под эту модель, поэтому запрашивает классическую модель и прежнюю версию портала Azure для работы с сертификатами управления.

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic4.jpg)

Здесь мы видим прежнюю версию портала Azure. Отправка сертификата управления дает разрешения SAP CAL на создание виртуальных машин в рамках подписки клиента. На вкладке "Подписки" можно найти идентификатор подписки, который нужно ввести на портале SAP CAL.

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic5.jpg)

На второй вкладке теперь можно отправить сертификат управления, предварительно загруженный из SAP CAL.

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic8.jpg)

На экране появится небольшое диалоговое окно для выбора загруженного файла сертификата.

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic9.jpg)

После отправки сертификата следует проверить подключение между SAP CAL и клиентской подпиской Azure через SAP CAL. Должно появиться небольшое всплывающее окно с сообщением о том, что подключение является действительным.

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic10.jpg)

После настройки учетной записи выберите решение, которое необходимо развернуть, и создайте экземпляр. В базовом режиме сделать это совсем несложно. Введите имя экземпляра, выберите регион Azure и определите основной пароль для доступа к решению.

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic11.jpg)

Через некоторое время, в зависимости от размера и сложности решения (оценку предоставляет SAP CAL), состояние экземпляра сменится на "Активный", и он будет готов к использованию. Все очень просто.

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic12.jpg)

В сведениях о решении можно увидеть, виртуальные машины какого типа были развернуты. В этом случае имеется одна виртуальная машина Azure размера D12, созданная SAP CAL.

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic13.jpg)

На портале Azure виртуальную машину можно найти по имени экземпляра, указанному в SAP CAL.

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic14.jpg)

Теперь можно подключиться к решению с помощью кнопки "Connect" (Подключиться) на портале SAP CAL. В небольшом диалоговом окне содержится ссылка на руководство пользователя, в котором представлены все учетные данные по умолчанию, необходимые для работы с решением. [Вот](https://caldocs.hana.ondemand.com/caldocs/help/Getting_Started_Guide_IDES607MSSQL.pdf) ссылка на руководство по решению IDES.

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic15.jpg)

Другой вариант — войти на виртуальную машину Windows и запустить, например, предварительно настроенный графический пользовательский интерфейс SAP.

<!---HONumber=AcomDC_0921_2016-->