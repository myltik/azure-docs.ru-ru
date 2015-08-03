
<properties
    pageTitle="Проверка виртуальной сети Azure для использования с Azure RemoteApp"
    description="Узнайте, как проверить готовность виртуальной сети Azure к использованию с Azure RemoteApp"
    services="remoteapp"
	documentationCenter=""
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/02/2015"
    ms.author="elizapo" />



# Проверка виртуальной сети Azure для использования с Azure RemoteApp

Возможно, перед использованием виртуальной сети Azure с Azure RemoteApp ее нужно будет проверить. Это помогает избежать проблем с подключением Azure RemoteApp.

Чтобы проверить виртуальную сеть Azure, выполните указанные ниже действия.

1. Создайте виртуальную машину в подсети виртуальной сети Azure, которую вы хотите использовать с Azure RemoteApp.

2. Подключитесь к этой виртуальной машине с помощью параметра **Подключиться** в портале управления.
3. Подключите виртуальную машину к тому же домену, который вы собираетесь использовать с Azure RemoteApp.

Если это удалось, значит, виртуальная сеть Azure готова к использованию с RemoteApp.

Дополнительные сведения о полном процессе работы с гибридными коллекциями см. в следующих статьях:

- [Создание гибридной коллекции](remoteapp-create-hybrid-deployment.md)
- [Развертывание коллекции Azure RemoteApp для виртуальной сети Azure (с поддержкой ExpressRoute)](http://blogs.msdn.com/b/rds/archive/2015/04/23/deploy-azure-remoteapp-collection-to-your-azure-virtual-network-with-support-for-expressroute.aspx)
 

<!---HONumber=July15_HO4-->