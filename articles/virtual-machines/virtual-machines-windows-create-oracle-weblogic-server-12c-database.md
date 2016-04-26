<properties
	pageTitle="Виртуальная машина Oracle WebLogic Server и Oracle Database | Microsoft Azure"
	description="Создайте образ Azure Oracle WebLogic Server 12c и Oracle Database 12c под управлением Windows Server 2012 с помощью модели развертывания диспетчера ресурсов."
	services="virtual-machines-windows"
	authors="bbenz"
	documentationCenter=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows"
	ms.workload="infrastructure-services"
	ms.date="06/22/2015"
	ms.author="bbenz" />

#Создание виртуальной машины Oracle WebLogic Server 12c и Oracle Database 12c в Azure

В этой статье показано, как создать в Azure виртуальную машину на основе предоставленного корпорацией Майкрософт образа Oracle WebLogic Server 12c и Oracle Database 12c под управлением Windows Server 2012 12c.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]Классическая модель развертывания.


##Создание виртуальной машины Oracle WebLogic Server 12c и Oracle Database 12c в Azure

1. Войдите на [портал Azure](https://ms.portal.azure.com/).

2.	Щелкните **Marketplace**, выберите **Среда выполнения приложений**, а затем введите **Oracle** в поле поиска.

3.	Выберите образ **Oracle Database 12c и WebLogic Server 12c Standard Edition на Windows Server 2012** или **Oracle Database 12c и WebLogic Server 12c Enterprise Edition на Windows Server 2012**. Просмотрите информацию о выбранном образе \(например, минимальный рекомендуемый размер\) и нажмите кнопку **Далее**.

4.	Укажите **Имя узла** виртуальной машины.

5.	Укажите **Имя пользователя** виртуальной машины. Обратите внимание, что это имя пользователя для удаленного входа в виртуальную машину, а не для базы данных Oracle.

6.	Укажите и подтвердите пароль для виртуальной машины или предоставьте открытый ключ SSH.

7.	Выберите **ценовую категорию**. Обратите внимание, что по умолчанию отображаются рекомендуемые ценовые уровни. Чтобы просмотреть все параметры конфигурации, щелкните **Просмотреть все** в правом верхнем углу.

8. При необходимости установите дополнительные настройки. Следуйте указанным далее рекомендациям.

	а. Оставьте значение **Учетная запись хранения** как есть, чтобы создать новую учетную запись хранения с именем виртуальной машины.

	b. Оставьте в поле **Группа доступности** значение **Не настроено**.

	c. Пока не добавляйте конечные точки.

9.	Выберите или создайте группу ресурсов. Дополнительные сведения см. в статье [Управление ресурсами Azure с помощью портала Azure](../azure-portal/resource-group-portal.md).

10. Выберите **подписку**.

11. Выберите **расположение**.


##Создание базы данных, размещенной на этой виртуальной машине

Следуйте указаниям в статье [Создание виртуальной машины Oracle Database 12c в Azure](virtual-machines-windows-classic-create-oracle-database.md), начиная с раздела **Создание базы данных с помощью виртуальной машины Oracle Database 12c в Azure**.

##Настройка Oracle WebLogic Server 12c, размещенного на этой виртуальной машине
Следуйте указаниям в разделе [Создание виртуальной машины Oracle WebLogic Server 12c в Azure](virtual-machines-windows-create-oracle-weblogic-server-12c.md), начиная с подраздела **Настройка виртуальной машины Oracle WebLogic Server 12c в Azure**.

##Дополнительные ресурсы
[Различные рекомендации по образам виртуальных машин Oracle](virtual-machines-windows-classic-oracle-considerations.md)

[Список образов виртуальных машин Oracle](virtual-machines-linux-classic-oracle-images.md)

[Подключение к базе данных Oracle из приложения Java](http://docs.oracle.com/cd/E11882_01/appdev.112/e12137/getconn.htm#TDPJD136)

[Oracle WebLogic Server 12c с использованием Linux в Microsoft Azure](http://www.oracle.com/technetwork/middleware/weblogic/learnmore/oracle-weblogic-on-azure-wp-2020930.pdf)

[Oracle Database 2 Day DBA 12c Release 1](http://docs.oracle.com/cd/E16655_01/server.121/e17643/toc.htm)

<!---HONumber=AcomDC_0413_2016-->