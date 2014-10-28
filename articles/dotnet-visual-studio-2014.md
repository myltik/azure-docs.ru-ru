<properties linkid="dotnet-visual-studio-2014" urlDisplayName="Visual Studio 14 CTP2" pageTitle="Installing the Azure SDK 2.4 for Visual Studio 14 CTP2" metaKeywords="Visual Studio, Azure SDK" description="Install Azure SDK 2.4 and Visual Studio 14 CTP2" metaCanonical="" services="" documentationCenter="" title="Installing Azure SDK 2.4 for Visual Studio 14 CTP2" authors="ghogen" solutions="" manager="" editor="" />

<tags ms.service="multiple" ms.workload="multiple" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="ghogen"></tags>

## Установка Azure SDK 2.4 для Visual Studio, CTP-версия «14»

Для установки пакета Azure SDK 2.4 для .NET с Visual Studio, CTP-версии «14», выполните следующую процедуру. Эта процедура позволяет установить пакеты SDK, базовые инструменты, а также расширенные инструменты для разработки Azure с Visual Studio, CTP-версии «14», и не предназначена для использования с любой другой версией Visual Studio.

**Примечание**. Пакет Azure SDK 2.4 несовместим с Visual Studio, CTP1-версия «14».

Для установки Azure SDK 2.4 для .NET выполните следующую процедуру:

1.  Установите последнюю [Visual Studio, CTP-версия «14»][Visual Studio, CTP-версия «14»].

2.  Установите все компоненты пакета Azure SDK с помощью ссылок в следующем списке в заданном порядке. Выберите версию x86 или x64 каждого из следующих компонентов.

    -   Инструменты разработки для Azure: [WindowsAzureAuthoringTools-x86.msi][WindowsAzureAuthoringTools-x86.msi] или [WindowsAzureAuthoringTools-x64.msi][WindowsAzureAuthoringTools-x64.msi].
    -   Эмулятор среды выполнения приложений Azure: [WindowsAzureEmulator-x86.exe][WindowsAzureEmulator-x86.exe] или [WindowsAzureEmulator-x64.exe][WindowsAzureEmulator-x64.exe].
    -   Клиентские библиотеки Azure: [WindowsAzureLibsForNet-x86.msi][WindowsAzureLibsForNet-x86.msi] или [WindowsAzureLibsForNet-x64.msi][WindowsAzureLibsForNet-x64.msi].
    -   Эмулятор хранилища: [WindowsAzureStorageEmulator.msi][WindowsAzureStorageEmulator.msi]. В случае получения предупреждения о локальных базах данных SQL установите SQL Server LocalDB 11.0 из [этого местоположения)][этого местоположения)] для x86 или из [этого местоположения][этого местоположения] для x64.
    -   Инструменты Azure для Visual Studio: [WindowsAzureTools.vs140.exe][WindowsAzureTools.vs140.exe].

    </p>

## Известные проблемы

1.  При установке Visual Studio, CTP2-версия «14», на машине, где установлена Visual Studio 2013, вы не сможете запускать мобильные службы в Visual Studio, CTP2-версия «14». Для устранения этой проблемы добавьте ссылку на следующие сборки в своем проекте мобильных служб:

    -   packages/Microsoft.Data.OData.5.6.0/lib/net40/Microsoft.Data.OData.dll
    -   packages/Microsoft.Data.Edm.5.6.0/lib/net40/Microsoft.Data.Edm.dll

2.  Удаленная отладка для веб-сайтов и мобильных служб Azure не работает в Visual Studio, CTP2-версия «14».

## Заметки о выпуске

Ознакомьтесь с [заметками о выпуске][заметками о выпуске] для Azure SDK 2.4.

  [Visual Studio, CTP-версия «14»]: http://go.microsoft.com/fwlink/p/?LinkId=400776
  [WindowsAzureAuthoringTools-x86.msi]: http://go.microsoft.com/fwlink/p/?LinkId=400892
  [WindowsAzureAuthoringTools-x64.msi]: http://go.microsoft.com/fwlink/p/?LinkId=400893
  [WindowsAzureEmulator-x86.exe]: http://go.microsoft.com/fwlink/p/?LinkId=400894
  [WindowsAzureEmulator-x64.exe]: http://go.microsoft.com/fwlink/p/?LinkId=400895
  [WindowsAzureLibsForNet-x86.msi]: http://go.microsoft.com/fwlink/p/?LinkId=400896
  [WindowsAzureLibsForNet-x64.msi]: http://go.microsoft.com/fwlink/p/?LinkId=400897
  [WindowsAzureStorageEmulator.msi]: http://go.microsoft.com/fwlink/p/?LinkId=400904
  [этого местоположения)]: http://go.microsoft.com/fwlink/p/?LinkId=400778
  [этого местоположения]: http://go.microsoft.com/fwlink/p/?LinkId=400779
  [WindowsAzureTools.vs140.exe]: http://go.microsoft.com/fwlink/p/?LinkId=400903
  [заметками о выпуске]: http://go.microsoft.com/fwlink/?LinkId=507517
