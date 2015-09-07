<properties 
   pageTitle="Отладка облачных служб Azure | Microsoft Azure"
	description="Отладка облачных служб Azure"
	services="visual-studio-online"
	documentationCenter="n/a"
	authors="patshea123"
	manager="douge"
	editor="tlee"/>
<tags 
   ms.service="visual-studio-online"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="multiple"
	ms.workload="na"
	ms.date="08/14/2015"
	ms.author="patshea"/>

# Отладка облачных служб

Для отладки приложения Azure с помощью инструментов Azure для Microsoft Visual Studio и пакета SDK Azure можно использовать различные методы.

- Приложение Azure в Visual Studio можно отладить в процессе разработки, точно так же как приложение Visual C# или Visual Basic. Дополнительные сведения см. в статье [Отладка облачной службы или виртуальной машины в Visual Studio](http://go.microsoft.com/fwlink/p/?LinkID=623018)

- Систему диагностики Azure можно использовать для регистрации подробных сведений из кода, выполняющегося в ролях, независимо от того, запущены ли эти роли в среде разработки или в Azure. Дополнительные сведения см. в статье [Сбор данных журнала с помощью системы диагностики Azure](http://go.microsoft.com/fwlink/p/?LinkId=400450).

- Если Visual Studio Enterprise используется для написания ролей для .NET Framework 4 или .NET Framework 4.5, вы можете включить IntelliTrace в момент развертывания облачной службы Azure из Visual Studio. IntelliTrace предоставляет журнал, который можно использовать с Visual Studio для отладки приложения как в Azure. Дополнительные сведения см. в статье [Отладка опубликованной облачной службы с помощью IntelliTrace и Visual Studio](http://go.microsoft.com/fwlink/p/?LinkId=623016).

- Удаленную отладку облачной службы можно включить при развертывании облачной службы в Visual Studio. Если для развертывания выбрана удаленная отладка, службы удаленной отладки будут установлены на все виртуальные машины, где выполняются экземпляры роли. Такие службы (например, msvsmon.exe) не влияют на производительность и не требуют дополнительных расходов. Дополнительные сведения см. в статье [Отладка облачной службы или виртуальной машины в Visual Studio](http://go.microsoft.com/fwlink/p/?LinkID=623018)

<!---HONumber=August15_HO9-->