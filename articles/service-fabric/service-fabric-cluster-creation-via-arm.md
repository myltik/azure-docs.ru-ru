<properties
   pageTitle="Настройка кластера Service Fabric с использованием шаблона диспетчера ресурсов Azure | Microsoft Azure"
   description="Настройка кластера Service Fabric с использованием шаблона диспетчера ресурсов Azure."
   services="service-fabric"
   documentationCenter=".net"
   authors="ChackDan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/02/2016"
   ms.author="chackdan"/>


# Настройка кластера Service Fabric с использованием шаблона диспетчера ресурсов Azure

Эта страница поможет вам настроить кластер Service Fabric с использованием шаблона диспетчера ресурсов Azure. Для выполнения этой задачи ваша подписка должна включать достаточное количество ядер для развертывания виртуальных машин IaaS, которые войдут в состав этого кластера.

## Предварительные требования

- Для настройки безопасного кластера необходимо отправить в хранилище ключей сертификат X.509. Вам потребуется URL-адрес исходного хранилища, URL-адрес сертификата и отпечаток сертификата.
- Дополнительные сведения о настройке безопасного кластера см. в статье [Безопасность кластера Service Fabric](service-fabric-cluster-security.md).

## Образец шаблона диспетчера ресурсов

Образцы шаблонов диспетчера ресурсов доступны в [коллекции шаблонов быстрого запуска Azure на сайте GitHub](https://github.com/Azure/azure-quickstart-templates). Все имена шаблонов Service Fabric начинаются со слов "service-fabric...". Выполните поиск в репозитории по запросу "fabric" или прокрутите список образцов и найдите нужный шаблон. Для упрощения поиска шаблоны именованы следующим образом:

- [service-fabric-unsecure-cluster-5-node-1-nodetype](http://go.microsoft.com/fwlink/?LinkId=716923) для указания шаблона небезопасного кластера с пятью одинарными узлами;

- [service-fabric-secure-cluster-5-node-1-nodetype-wad](http://go.microsoft.com/fwlink/?LinkID=716924) для указания шаблона безопасного кластера с пятью одинарными узлами с поддержкой WAD;

- [service-fabric-secure-cluster-10-node-2-nodetype-wad](http://go.microsoft.com/fwlink/?LinkId=716925) для указания шаблона безопасного кластера с десятью двойными узлами с поддержкой WAD.

## Создание настраиваемого шаблона диспетчера ресурсов Azure

Создать пользовательский шаблон диспетчера ресурсов можно одним из двух способов:

1. загрузить образец шаблона из [коллекции шаблонов быстрого запуска Azure на сайте GitHub](https://github.com/Azure/azure-quickstart-templates) и внести в него изменения;

2. войти на портал Azure и создать, а затем настроить нужный шаблон, используя сведения на страницах портала Service Fabric. Для этого выполните следующие действия:

    а. Войдите на [портал Azure](https://portal.azure.com/).

    b. Пройдите процесс создания кластера, как описано в статье [Создание кластера Service Fabric с помощью портала](service-fabric-cluster-creation-via-portal.md), но не нажимайте кнопку **Создать**. Вместо этого перейдите в раздел **Сводка** и загрузите шаблон, как показано на следующем снимке экрана.

 ![Снимок экрана: страница кластера Service Fabric, где приводится ссылка для загрузки шаблона диспетчера ресурсов][DownloadTemplate]

## Развертывание шаблона диспетчера ресурсов в Azure с помощью Azure PowerShell

Подробные инструкции по развертыванию шаблона с помощью PowerShell см. в статье [Развертывание шаблонов диспетчера ресурсов с помощью PowerShell](../resource-group-template-deploy.md).

>[AZURE.NOTE] Для постоянной работы кластеров Service Fabric требуется определенное количество узлов, чтобы все время поддерживать доступность и сохранять состояние, которое называется "поддержание кворума". Поэтому обычно не рекомендуется завершать работу всех машин в кластере, пока не будет выполнено [полное резервное копирование состояния](service-fabric-reliable-services-backup-restore.md), так как это может быть небезопасно.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Дальнейшие действия
- [Защита кластера Service Fabric](service-fabric-cluster-security.md)
- [Управление приложениями Service Fabric в Visual Studio](service-fabric-manage-application-in-visual-studio.md)
- [Общие сведения о наблюдении за работоспособностью системы в Service Fabric](service-fabric-health-introduction.md)

<!--Image references-->
[DownloadTemplate]: ./media/service-fabric-cluster-creation-via-arm/DownloadTemplate.png

<!---HONumber=AcomDC_0511_2016-->