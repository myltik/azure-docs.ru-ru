<properties
	pageTitle="Вход в виртуальную машину Linux в Azure | Microsoft Azure"
	description="Узнайте, как войти в виртуальную машину Azure под управлением Linux, используя клиент Secure Shell (SSH)."
	services="virtual-machines-linux"
	documentationCenter=""
	authors="squillace"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/18/2016"
	ms.author="rasquill"/>


#Как войти в виртуальную машину под управлением Linux #

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] [Resource Manager deployment model](virtual-machines-linux-portal-create.md).

Вам понадобится установить клиент SSH на компьютер, который вы будете использовать для входа на виртуальную машину. Существует множество клиентских программ SSH. Вы можете выбрать один из следующих вариантов.

- Для виртуальной машины под управлением операционной системы Linux используйте клиент Secure Shell (SSH) для входа в систему. Трудно представить дистрибутив, в котором он установлен по умолчанию. Подробнее о Linux см. в разделе [Использование SSH с Linux и Mac в Azure](virtual-machines-linux-ssh-from-linux.md).
- На компьютере под управлением Windows вы можете использовать такой клиент SSH, как PuTTY. Дополнительные сведения см. на [странице загрузки PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).


>[AZURE.NOTE] Дополнительную информацию о требованиях и советы по устранению неполадок см. в статье [Подключение к виртуальной машине Azure с помощью RDP или SSH](http://go.microsoft.com/fwlink/p/?LinkId=398294).

В этой процедуре рассматривается использование клиента SSH в OS X для доступа к виртуальной машине.

1. Найдите **Имя узла** и **Данные порта** с [портала управления](http://manage.windowsazure.com). Необходимые сведения можно найти на панели мониторинга виртуальной машины. Щелкните имя виртуальной машины и найдите **Сведения об SSH** в разделе **Сводка** панели мониторинга.

	![Получить сведения о SSH](./media/virtual-machines-linux-classic-log-on/portalsshdetails.png)

2. Войдите на виртуальную машину с помощью учетной записи, указанной при создании машины. Также укажите правильные имя узла и номер порта. Подробную информацию о создании виртуальной машины с именем пользователя и паролем см. в статье [Создание виртуальной машины с ОС Linux](virtual-machines-linux-classic-createportal.md).

	![Вход на виртуальную машину](./media/virtual-machines-linux-classic-log-on/sshport.png)

>[AZURE.NOTE] Расширение VMAccess поможет вам сбросить значения ключа или пароля SSH, если вы их забыли. Если вы забыли имя пользователя, то можете воспользоваться этим расширением для создания нового имени с полномочиями sudo. Указания см. в статье [Как сбросить пароль или ключ SSH в виртуальных машинах Linux].

Теперь вы сможете работать с виртуальной машиной, так же как и с любым сервером.

<!-- LINKS -->
[Как сбросить пароль или ключ SSH в виртуальных машинах Linux]: http://go.microsoft.com/fwlink/p/?LinkId=512138

<!---HONumber=AcomDC_0511_2016-->