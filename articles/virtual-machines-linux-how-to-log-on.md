<properties 
	pageTitle="Вход в виртуальную машину под управлением Linux в Azure" 
	description="Узнайте, как войти в виртуальную машину Azure под управлением Linux, используя клиент Secure Shell (SSH)." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="KBDAzure" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-linux" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/12/2014" 
	ms.author="kathydav"/>




#Как войти в виртуальную машину под управлением Linux #

Для входа на виртуальную машину под управлением операционной системы Linux используется клиент SSH.

Вам понадобится установить клиент SSH на компьютер, который вы будете использовать для входа на виртуальную машину. Существует множество клиентских программ SSH. Вы можете выбрать один из следующих вариантов.

- На компьютере под управлением Windows вы можете использовать такой клиент SSH, как PuTTY. Дополнительную информацию см. на [странице загрузки PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).
- На компьютере под управлением Linux вы можете использовать такой клиент SSH, как OpenSSH. Дополнительную информацию см. на странице [OpenSSH](http://www.openssh.org/).

>[AZURE.NOTE] Дополнительную информацию о требованиях и советы по устранению неполадок см. в разделе [Подключение к виртуальной машине Azure с помощью RDP или SSH](http://go.microsoft.com/fwlink/p/?LinkId=398294). 

В этой процедуре рассматривается использование программы PuTTY для доступа к виртуальной машине.

1. Найдите **Имя узла** и **Данные порта** на [портале управления](http://manage.windowsazure.com). Необходимые сведения можно найти на панели мониторинга виртуальной машины. Щелкните имя виртуальной машины и найдите **Сведения об SSH** в разделе **Сводка** панели мониторинга.

	![Obtain SSH details](./media/virtual-machines-linux-how-to-log-on/sshdetails.png)

2. Откройте программу PuTTY.

3. Введите "Имя узла" и "Данные порта" с панели мониторинга и щелкните **Открыть**.

	![Open PuTTY](./media/virtual-machines-linux-how-to-log-on/putty.png)

4. Войдите на виртуальную машину с помощью учетной записи, указанной при создании машины. Более подробную информацию о создании виртуальной машины с именем пользователя и паролем см. в разделе [Создание виртуальной машины под управлением Linux](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-tutorial/). 

	![Log on to the virtual machine](./media/virtual-machines-linux-how-to-log-on/sshlogin.png)

>[AZURE.NOTE] Расширение VMAccess поможет вам сбросить значения ключа или пароля SSH, если вы их забыли. Если вы забыли имя пользователя, то можете воспользоваться этим расширением для создания нового имени с полномочиями sudo. Указания см. в разделе [Как сбросить пароль или ключ SSH в виртуальных машинах Linux]. 
	
Теперь вы сможете работать с виртуальной машиной, так же как и с любым сервером.

<!-- LINKS -->
[Как сбросить пароль или ключ SSH в виртуальных машинах Linux]: http://go.microsoft.com/fwlink/p/?LinkId=512138


<!--HONumber=42-->
