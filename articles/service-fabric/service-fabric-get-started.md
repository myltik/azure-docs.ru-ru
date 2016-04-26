<properties
   pageTitle="Настройка среды разработки | Microsoft Azure"
   description="Установите среду выполнения, пакет SDK и инструменты и создайте локальный кластер разработки. После этого вы сможете создавать приложения."
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="04/18/2016"
   ms.author="seanmck"/>

# Подготовка среды разработки
 Чтобы создавать и запускать [приложения Service Fabric][1] на компьютере для разработки, вам нужно установить среду выполнения, пакет SDK и инструменты. Вам также нужно включить выполнение сценариев Windows PowerShell, включенных в пакет SDK.

## Предварительные требования
### Поддерживаемые версии операционных систем
Для разработки поддерживаются следующие операционные системы:

- Windows 7
- Windows 8 и Windows 8.1;
- Windows Server 2012 R2
- Windows 10

>[AZURE.NOTE] Windows 7 по умолчанию поставляется с Windows PowerShell версии 2.0. Для работы с командлетами PowerShell для Service Fabric вам необходимо установить PowerShell 3.0 или более позднюю версию. В центре загрузки Майкрософт можно [скачать Windows PowerShell 5.0][powershell5-download].

## Установка среды выполнения, пакета SDK и инструментов

Установщик веб-платформы предлагает три конфигурации для разработки в Service Fabric:

- [Установка среды выполнения Service Fabric, пакета SDK и средств для Visual Studio 2015][full-bundle-vs2015]
- [Установка среды выполнения Service Fabric, пакета SDK и средств для предварительной версии Visual Studio 2015][full-bundle-dev15]
- [Установка только среды выполнения Service Fabric и пакета SDK (без средств для Visual Studio)][core-sdk]


## Включение сценариев PowerShell

Для создания локального кластера разработки и развертывания приложений из Visual Studio в Service Fabric используются сценарии Windows PowerShell. По умолчанию Windows блокирует выполнение этих сценариев. Чтобы включить их, необходимо изменить политику выполнения PowerShell. Для этого запустите PowerShell с правами администратора и введите следующую команду:

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Force -Scope CurrentUser
```

## Дальнейшие действия
Среда разработки настроена, и вы готовы к созданию и запуску собственных приложений.

- [Создание первого приложения Service Fabric в Visual Studio](service-fabric-create-your-first-application-in-visual-studio.md)
- [Информация о развертывании приложений в локальном кластере и управлении ими](service-fabric-get-started-with-a-local-cluster.md)
- [Информация о моделях программирования: Reliable Services и Reliable Actors](service-fabric-choose-framework.md)
- [Ознакомление с примерами кода Service Fabric на GitHub](https://aka.ms/servicefabricsamples)
- [Визуализация кластера с помощью обозревателя Service Fabric](service-fabric-visualizing-your-cluster.md)
- [Используйте схему обучения Service Fabric, чтобы получить общие сведения о платформе](https://azure.microsoft.com/documentation/learning-paths/service-fabric/)

[1]: http://azure.microsoft.com/campaigns/service-fabric/ "Страница кампании Service Fabric"
[2]: http://go.microsoft.com/fwlink/?LinkId=517106 "VS RC"
[full-bundle-vs2015]: http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015 "Ссылка VS 2015 WebPI"
[full-bundle-dev15]: http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-Dev15 "Ссылка Dev15 WebPI"
[core-sdk]: http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=ServiceFabricSDK "Ссылка Core SDK WebPI"
[powershell5-download]: https://www.microsoft.com/ru-RU/download/details.aspx?id=50395

<!---HONumber=AcomDC_0420_2016-->