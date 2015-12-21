<properties
   pageTitle="Настройка кластера Service Fabric с помощью шаблона ARM | Microsoft Azure"
   description="Настройка кластера Service Fabric с помощью шаблона ARM."
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
   ms.date="11/19/2015"
   ms.author="chackdan"/>

# Настройка кластера Service Fabric с помощью шаблона ARM

На этой странице содержатся сведения, которые помогут вам настроить кластер Service Fabric с помощью шаблона ARM. Предполагается, что ваша подписка поддерживает достаточное количество ядер для развертывания виртуальных машин IaaS, которые войдут в состав этого кластера.

## Предварительные требования

- Если нужно настроить безопасный кластер, убедитесь, что сертификат X509 отправлен в хранилище ключей. Вам потребуется URL-адрес исходного хранилища, URL-адрес сертификата и отпечаток сертификата.
-  Сведения о получении этих данных см. в статье [Безопасность кластера Service Fabric](service-fabric-cluster-security.md).

## Получение образца шаблона ARM

1. Образцы шаблонов ARM доступны в [коллекции шаблонов быстрого запуска Azure на сайте github](https://github.com/Azure/azure-quickstart-templates). Все шаблоны Service Fabric начинаются с имени "service-fabric-...". Можно выполнить поиск в репозитории по слову "fabric" или просто прокрутить вниз до набора образцов шаблонов.
2. В целях упрощения поиска шаблоны именованы следующим образом:
	1. [service-fabric-unsecure-cluster-5-node-1-nodetype](http://go.microsoft.com/fwlink/?LinkId=716923) для указания шаблона небезопасного кластера с 5 одинарными узлами. 
	3. [service-fabric-secure-cluster-5-node-1-nodetype-wad](http://go.microsoft.com/fwlink/?LinkID=716924) для указания шаблона безопасного кластера с 5 одинарными узлами с поддержкой WAD. 
	4. [service-fabric-secure-cluster-10-node-2-nodetype-wad](http://go.microsoft.com/fwlink/?LinkId=716925) для указания шаблона безопасного кластера с 10 двойными узлами с поддержкой WAD. 
	

## Создание настраиваемого шаблона ARM

2. Выберите один из двух вариантов. 
	1. Можно получить образец шаблона из [коллекции шаблонов быстрого запуска Azure на сайте github ](https://github.com/Azure/azure-quickstart-templates) и внести в него изменения.
	2. Войдите на портал Azure и, используя сведения на страницах портала Service Fabric, создайте шаблон, который затем будет настроен. Эти действия описаны ниже.
3. Выполните вход на портал Azure [http://aka.ms/servicefabricportal](http://aka.ms/servicefabricportal).
2. Пройдите процесс создания кластера, как описано в статье [Создание кластера Service Fabric с помощью портала](service-fabric-cluster-creation-via-portal.md), но не нажимайте кнопку ***Создать**, а перейдите в раздел "Сводка" и загрузите шаблон.

 ![DownloadTemplate][DownloadTemplate]

## Развертывание шаблона ARM Azure с помощью Azure PS

Подробные инструкции по развертыванию шаблона с помощью PowerShell см. в статье [Развертывание шаблонов ARM с помощью PS](resource-group-template-deploy.md).

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Дальнейшие действия
- [Безопасность кластера Service Fabric](service-fabric-cluster-security.md) 
- [Управление приложениями Service Fabric в Visual Studio](service-fabric-manage-application-in-visual-studio.md)
- [Общие сведения о модели работоспособности в Service Fabric](service-fabric-health-introduction.md)

<!--Image references-->
[DownloadTemplate]: ./media/service-fabric-cluster-creation-via-arm/DownloadTemplate.png

<!---HONumber=AcomDC_1210_2015-->