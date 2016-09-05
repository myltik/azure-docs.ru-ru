<properties
	pageTitle="Вход на классическую виртуальную машину Azure | Microsoft Azure"
	description="Использование классического портала Azure для входа в виртуальную машину Windows, созданную с использованием классической модели развертывания."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/28/2016"
	ms.author="cynthn"/>


# Вход в виртуальную машину под управлением Windows с помощью классического портала Azure

На классическом портале Azure для запуска сеанса удаленного рабочего стола и входа в виртуальную машину Windows используйте кнопку **Подключиться**.

Хотите подключиться к виртуальной машине Linux? См. статью [Как войти в виртуальную машину под управлением Linux](virtual-machines-linux-mac-create-ssh-keys.md).

Узнайте, как [выполнить эти действия с помощью нового портала Azure](virtual-machines-windows-connect-logon.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

## Видеоруководство

Ниже приведено пошаговое описание действий в этом руководстве в формате видеоролика. В нем также описаны конечные точки, а также общие и частные порты, используемые для подключения к виртуальной машине Windows в Azure.

[AZURE.VIDEO logging-on-to-vm-running-windows-server-on-azure]


## Подключение к виртуальной машине

1. Войдите на классический портал Azure.

2. Щелкните **Виртуальные машины** и выберите виртуальную машину.

3. На панели команд в нижней части страницы щелкните **Подключиться**.

	![Вход на виртуальную машину](./media/virtual-machines-windows-classic-connect-logon/connectwindows.png)
	
> [AZURE.TIP] Если кнопка **Подключиться** недоступна, ознакомьтесь с советами по устранению неполадок в конце этой статьи.

## Вход на виртуальную машину

[AZURE.INCLUDE [virtual-machines-log-on-win-server](../../includes/virtual-machines-log-on-win-server.md)]

## Дальнейшие действия

-	Если кнопка **Подключиться** неактивна или возникли другие проблемы с подключением к удаленному рабочему столу, попробуйте переустановить конфигурацию. На панели мониторинга виртуальной машины в разделе **Сводка** щелкните **Сброс удаленной конфигурации**.
-	Если возникли проблемы с паролем, попробуйте сбросить его. На панели мониторинга виртуальной машины в разделе **Сводка** щелкните **Сбросить пароль**.

Если эти рекомендации не помогли устранить проблему или они вам не подходят, см. статью [Устранение неполадок с подключением к удаленному рабочему столу на виртуальной машине Azure под управлением Windows](virtual-machines-windows-troubleshoot-rdp-connection.md). В ней описывается процесс диагностики и решения распространенных проблем.

<!---HONumber=AcomDC_0824_2016-->