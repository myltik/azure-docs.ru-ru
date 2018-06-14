---
title: Распространенные причины перезапуска ролей облачных служб | Документация Майкрософт
description: Внезапный перезапуск роли облачной службы может привести к длительному простою. Ниже приведены некоторые распространенные проблемы, вызывающие перезапуск ролей. Эти сведения могут помочь сократить время простоя.
services: cloud-services
documentationcenter: ''
author: simonxjx
manager: felixwu
editor: ''
tags: top-support-issue
ms.assetid: 533930d1-8035-4402-b16a-cf887b2c4f85
ms.service: cloud-services
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 11/03/2017
ms.author: v-six
ms.openlocfilehash: 7f513a7d3fe44cbbf06855059c87c10ddceef7c9
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2017
ms.locfileid: "23984292"
---
# <a name="common-issues-that-cause-roles-to-recycle"></a>Распространенные проблемы, вызывающие перезапуск ролей
В этой статье рассматриваются некоторые распространенные причины проблем с развертыванием, а также советы по их устранению. На наличие проблем с приложением указывает то, что экземпляр роли не запускается или циклически переключается между состояниями "Инициализация", "Занято" и "Остановлено".

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="missing-runtime-dependencies"></a>Отсутствующие зависимости среды выполнения
Если роль в приложении использует какую-либо сборку, которая не является частью управляемой библиотеки Azure или платформы .NET Framework, необходимо явно включить эту сборку в пакет приложения. Имейте в виду, что другие платформы Майкрософт недоступны в Azure по умолчанию. Если ваша роль использует такую платформу, необходимо добавить эти сборки в пакет приложения.

Перед сборкой и упаковкой приложения проверьте следующее.

* При работе в Visual Studio убедитесь, что свойство **Копировать локально** имеет значение **True** для каждой упоминаемой в проекте сборки, которая не является частью пакета SDK для Azure или платформы .NET Framework.
* Убедитесь, что файл web.config не ссылается на любые неиспользуемые сборки в элементе compilation.
* Параметр **Действие при сборке** для каждого CSHTML-файла имеет значение **Содержимое**. Это обеспечивает правильное отображение файлов в пакете, а также позволяет указать в этом пакете другие файлы, на которые есть ссылки.

## <a name="assembly-targets-wrong-platform"></a>Сборка нацелена на неверную платформу.
Azure является 64-разрядной средой. Таким образом, сборки .NET, скомпилированные для 32-разрядной среды, не будут работать в Azure.

## <a name="role-throws-unhandled-exceptions-while-initializing-or-stopping"></a>Роль вызывает необработанные исключения во время инициализации или остановки.
Любые исключения, выдаваемые методами класса [RoleEntryPoint], к которым относятся [OnStart], [OnStop], и [Run], являются необработанными. В случае возникновения необработанного исключения в одном из этих методов роль будет перезапущена. Если роль постоянно перезапускается, возможно, она вызывает необработанное исключение при каждой попытке запуска.

## <a name="role-returns-from-run-method"></a>Роль возвращается из метода Run.
Метод [Run] должен работать постоянно. Если ваш код переопределяет метод [Run] , он должен постоянно находиться в режиме ожидания. В случае возврата метода [Run] роль перезапускается.

## <a name="incorrect-diagnosticsconnectionstring-setting"></a>Неверный параметр DiagnosticsConnectionString
Если приложение использует систему диагностики Azure, то в CSCFG-файле необходимо указать параметр конфигурации `DiagnosticsConnectionString` . Этот параметр должен указывать HTTPS-подключение к вашей учетной записи хранения в Azure.

Чтобы убедиться в правильности параметра `DiagnosticsConnectionString` перед развертыванием пакета приложения в Azure, проверьте следующее.  

* Параметр `DiagnosticsConnectionString` указывает на действительную учетную запись хранения в Azure.  
  По умолчанию этот параметр указывает на эмулированную учетную запись хранения, поэтому необходимо явным образом изменить его перед развертыванием пакета приложения. Если этого не сделать, то при попытке экземпляра роли запустить монитор диагностики возникает исключение. Это может вызвать бесконечный перезапуск экземпляра роли.
* Строку подключения следует указывать в определенном [формате](../storage/common/storage-configure-connection-string.md). (необходимо задать протокол HTTPS). Замените *MyAccountName* именем своей учетной записи хранения, а *MyAccountKey* — своим ключом доступа:    

        DefaultEndpointsProtocol=https;AccountName=MyAccountName;AccountKey=MyAccountKey

  Если вы разрабатываете приложение с помощью инструментов Azure для Microsoft Visual Studio, чтобы задать это значение, можно использовать страницы свойств.

## <a name="exported-certificate-does-not-include-private-key"></a>Экспортированный сертификат не содержит закрытый ключ.
Чтобы запустить веб-роль с SSL, необходимо убедиться, что экспортированный сертификат управления содержит закрытый ключ. При использовании *диспетчера сертификатов Windows* для экспорта сертификата обязательно выберите **Да** в качестве значения параметра **Экспорт закрытого ключа**. Сертификат следует экспортировать в формате PFX, так как в настоящее время поддерживается только он.

## <a name="next-steps"></a>Дополнительная информация
Просмотрите дополнительные [статьи об устранении неполадок](https://azure.microsoft.com/documentation/articles/?tag=top-support-issue&product=cloud-services) в облачных службах.

Дополнительные сценарии перезапуска ролей см. в [серии статей в блоге Кевина Уильямсона](http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).

[RoleEntryPoint]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.aspx
[OnStart]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.onstart.aspx
[OnStop]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.onstop.aspx
[Run]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx
