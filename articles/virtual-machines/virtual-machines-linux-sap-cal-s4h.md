<properties 
pageTitle="Развертывание платформы S/4 HANA или BW/4 HANA в Microsoft Azure | Microsoft Azure" 
description="Развертывание платформы S/4 HANA или BW/4 HANA в Microsoft Azure" 
services="virtual-machines-linux" 
documentationCenter="" 
authors="hermanndms" 
manager="timlt" 
editor="" 
tags="azure-resource-manager" 
  keywords=""/>
<tags  
  ms.service="virtual-machines-linux" 
  ms.devlang="na" 
  ms.topic="article" 
  ms.tgt_pltfrm="vm-linux" 
  ms.workload="infrastructure-services" 
  ms.date="09/15/2016" 
  ms.author="hermannd"/>


# Развертывание платформы S/4 HANA или BW/4 HANA в Microsoft Azure 

В этой статье мы расскажем, как развернуть платформу S/4 HANA в Microsoft Azure с помощью SAP Cloud Appliance Library 3.0. На снимках экрана наглядно показан пошаговый процесс. С точки зрения процесса развертывание других решений на базе SAP Hana (например, BW/4 HANA) выполняется аналогичным образом. Нужно только выбрать другое решение.

Чтобы начать работу с SAP Cloud Appliance Library (SAP CAL), щелкните [здесь](https://cal.sap.com/). Вы перейдете на страницу блога SAP о новой версии [SAP Cloud Appliance Library 3.0](http://scn.sap.com/community/cloud-appliance-library/blog/2016/05/27/sap-cloud-appliance-library-30-came-with-a-new-user-experience).


Далее на снимках экрана показана пошаговая инструкция по развертыванию S/4 HANA в Microsoft Azure. Процесс развертывания других решений, таких как BW/4 HANA, аналогичен описанному ниже.


![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic-1b.jpg)

На первом рисунке показаны все решения на базе SAP CAL HANA, доступные в Microsoft Azure. В качестве примера мы решили продемонстрировать развертывание локального выпуска SAP S/4 HANA (внизу на снимке экрана).

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic-2.jpg)

Сначала нужно создать учетную запись SAP CAL. В настоящее время предлагается два варианта учетной записи Azure: стандартная учетная запись либо учетная запись на платформе Azure для континентальной части Китая под управлением компании-партнера 21Vianet.

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic3b.jpg)

Затем нужно ввести идентификатор подписки Azure, который можно найти на портале Azure (ниже описано, как его получить). После этого необходимо скачать сертификат управления Azure.

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic6b.jpg)

На новом портале Azure с левой стороны находится элемент "Подписки". Щелкните его, чтобы просмотреть все активные подписки пользователя.

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic7b.jpg)

Выберите одну из подписок, а затем щелкните "Сертификаты управления". Это новая концепция использования субъектов-служб для новой модели Azure Resource Manager. Продукт SAP CAL еще не адаптирован под эту модель, поэтому запрашивает классическую модель и прежнюю версию портала Azure для работы с сертификатами управления.

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic4b.jpg)

Здесь мы видим прежнюю версию портала Azure. Отправка сертификата управления дает разрешения SAP CAL на создание виртуальных машин в рамках подписки клиента. На вкладке "Подписки" можно найти идентификатор подписки, который нужно ввести на портале SAP CAL.

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic5.jpg)

На второй вкладке теперь можно отправить сертификат управления, предварительно загруженный из SAP CAL.

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic8.jpg)

На экране появится небольшое диалоговое окно для выбора загруженного файла сертификата.

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic9.jpg)

После отправки сертификата следует проверить подключение между SAP CAL и клиентской подпиской Azure через SAP CAL. Должно появиться небольшое всплывающее окно с сообщением о том, что подключение является действительным.

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic10.jpg)

После настройки учетной записи выберите решение, которое необходимо развернуть, и создайте экземпляр. В базовом режиме сделать это совсем несложно. Введите имя экземпляра, выберите регион Azure и определите основной пароль для доступа к решению.

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic11.jpg)

Через некоторое время, в зависимости от размера и сложности решения (оценку предоставляет SAP CAL), состояние экземпляра сменится на "Активный", и он будет готов к использованию. Все очень просто.

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic12.jpg)

В сведениях о решении можно увидеть, виртуальные машины какого типа были развернуты. В нашем случае были созданы три виртуальные машины Azure разного размера и в разных целях.

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic13.jpg)

На портале Azure виртуальные машины можно найти по имени экземпляра, указанному в SAP CAL.

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic14b.jpg)

Теперь можно подключиться к решению с помощью кнопки "Connect" (Подключиться) на портале SAP CAL. В небольшом диалоговом окне содержится ссылка на руководство пользователя, в котором представлены все учетные данные по умолчанию, необходимые для работы с решением.

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic15.jpg)

Другой вариант — войти на клиентскую виртуальную машину Windows и запустить, например, предварительно настроенный интерфейс SAP GUI.

<!---HONumber=AcomDC_0921_2016-->