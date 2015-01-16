<properties urlDisplayName="Visual Studio 14 CTP" pageTitle="Установка пакета Azure SDK 2.4 для Visual Studio, CTP2-версия 14" metaKeywords="Visual Studio, Azure SDK" description="Установка пакета SDK для Azure 2.4 и CTP-версии 2 Visual Studio 14" metaCanonical="" services="" documentationCenter="" title="Installing Azure SDK 2.4 for Visual Studio 14 CTP" authors="ghogen" solutions="" manager="douge" editor="" />

<tags ms.service="multiple" ms.workload="multiple" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="09/24/2014" ms.author="ghogen" />

# Установка Azure SDK 2.4 для Visual Studio, CTP-версия "14"

Для установки пакета Azure SDK 2.4 для .NET с Visual Studio, CTP-версии 14, сделайте следующее. Эта процедура позволяет установить пакеты SDK, базовые инструменты, а также расширенные инструменты для разработки Azure с Visual Studio, CTP-версии 14, и не предназначена для использования с любой другой версией Visual Studio.

**Примечание**. Пакет Azure SDK 2.4 несовместим с Visual Studio CTP1-версии 14.

Для установки Azure SDK 2.4 для .NET выполните следующую процедуру:

1. Установите последнюю [CTP-версию Visual Studio 14](http://go.microsoft.com/fwlink/p/?LinkId=400776).

2. Установите все компоненты пакета Azure SDK с помощью ссылок в следующем списке в заданном порядке. Выберите 32- или 64-разрядную версию каждого из следующих компонентов.

       <ul>
        <li>Инструменты разработки для Azure: <a href="http://go.microsoft.com/fwlink/p/?LinkId=400892">MicrosoftAzureAuthoringTools-x86.msi</a> или <a href="http://go.microsoft.com/fwlink/p/?LinkId=400893">MicrosoftAzureAuthoringTools-x64.msi</a>.</li>
       <li>Эмулятор среды выполнения приложений Azure: <a href="http://go.microsoft.com/fwlink/p/?LinkId=400894">MicrosoftAzureEmulator-x86.exe</a> или <a href="http://go.microsoft.com/fwlink/p/?LinkId=400895">MicrosoftAzureEmulator-x64.exe</a>.</li>
       <li>Клиентские библиотеки Azure: <a href="http://go.microsoft.com/fwlink/p/?LinkId=400896">MicrosoftAzureLibsForNet-x86.msi</a> или <a href="http://go.microsoft.com/fwlink/p/?LinkId=400897">MicrosoftAzureLibsForNet-x64.msi</a>.</li>
       <li>Эмулятор хранилища: <a href="http://go.microsoft.com/fwlink/p/?LinkId=400904">MicrosoftAzureStorageEmulator.msi</a>.                            Если вы получите предупреждения о локальных базах данных SQL установите SQL Server LocalDB 11.0 из <a href="http://go.microsoft.com/fwlink/p/?LinkId=400778">этого расположения</a> для 32-разрядной системы или <a href="http://go.microsoft.com/fwlink/p/?LinkId=400779">этого расположения</a> - для 64-разрядной.</li><li> Инструменты Azure для Visual Studio: <a href="http://go.microsoft.com/fwlink/p/?LinkId=400903">WindowsAzureTools.vs140.exe</a>.</li></ul>

## Известные проблемы

1. При установке Visual Studio CTP2-версии 14, на машине, где установлена Visual Studio 2013, вы не сможете запускать мобильные службы в Visual Studio CTP2-версии 14. Для устранения этой проблемы добавьте  ссылку на следующие сборки в своем проекте мобильных служб:

	* packages/Microsoft.Data.OData.5.6.0/lib/net40/Microsoft.Data.OData.dll
	* packages/Microsoft.Data.Edm.5.6.0/lib/net40/Microsoft.Data.Edm.dll

2. Удаленная отладка для веб-сайтов и мобильных служб Azure не работает в Visual Studio, CTP2-версия "14".

## Заметки о выпуске

Ознакомьтесь с [заметками о выпуске](http://go.microsoft.com/fwlink/?LinkId=507517) для пакета Azure SDK 2.4.
