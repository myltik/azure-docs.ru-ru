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
   ms.topic="hero-article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="11/17/2015"
   ms.author="seanmck"/>

# Подготовка среды разработки
 Чтобы создавать и запускать [приложения Service Fabric][1] на компьютере для разработки, вам нужно установить среду выполнения, пакет SDK и инструменты. Также необходимо настроить локальный кластер.

## Предварительные требования
### Поддерживаемые версии операционных систем
Поддерживаются следующие операционные системы:

- Windows 8 и Windows 8.1;
- Windows Server 2012 R2
- Windows 10

### Visual Studio 2015

Если установлена платформа Visual Studio 2015, набор инструментов для Service Fabric может отличаться. Загрузить Visual Studio 2015 можно [на веб-сайте Visual Studio][2].

> [AZURE.NOTE]Если у вас не установлена ни одна из поддерживаемых операционных систем или вы не хотите устанавливать на компьютер Visual Studio 2015, настройте виртуальную машину Azure c предустановленным программным обеспечением Windows Server 2012 R2 и Visual Studio 2015. Это можно сделать с помощью образа из коллекции виртуальных машин Azure.

## Установка среды выполнения, пакета SDK и инструментов

Установите компоненты Service Fabric с помощью установщика веб-платформы, следуя этим инструкциям:

1. [Загрузите пакет SDK][3], используя установщик веб-платформы.

2. Чтобы приступить к установке, нажмите кнопку **Установить**.

3. Прочитайте и примите условия лицензионного соглашения.

Установка начнется автоматически.

## Включение сценариев PowerShell

Для создания локального кластера разработки и развертывания приложений из Visual Studio в Service Fabric используются сценарии Windows PowerShell. По умолчанию Windows блокирует выполнение этих сценариев. Чтобы включить их, необходимо изменить политику выполнения PowerShell. Для этого запустите PowerShell с правами администратора и введите следующую команду:

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Force -Scope CurrentUser
```

## Дальнейшие действия
Среда разработки настроена, и вы готовы к созданию и запуску собственных приложений.

- [Создание первого приложения Service Fabric в Visual Studio](service-fabric-create-your-first-application-in-visual-studio.md)
- [Информация о развертывании приложений в локальном кластере и управлении ими](service-fabric-get-started-with-a-local-cluster.md)
- [Информация о моделях программирования на основе надежных субъектов и служб](service-fabric-choose-framework.md)
- [Ознакомление с примерами кода Service Fabric на GitHub](https://aka.ms/servicefabricsamples)
- [Визуализация кластера с помощью обозревателя Service Fabric](service-fabric-visualizing-your-cluster.md)

[1]: http://azure.microsoft.com/campaigns/service-fabric/ "Страница кампании Service Fabric"
[2]: http://go.microsoft.com/fwlink/?LinkId=517106 "VS RC"
[3]: http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric "Ссылка WebPI"

<!---HONumber=AcomDC_0114_2016-->