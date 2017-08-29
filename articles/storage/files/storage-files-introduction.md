---
title: "Общие сведения о хранилище файлов Azure | Документация Майкрософт"
description: "Общие сведения о хранилище файлов Azure, которое предоставляет сетевые файловые ресурсы в Microsoft Cloud."
services: storage
documentationcenter: 
author: RenaShahMSFT
manager: aungoo
editor: tysonn
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/27/2017
ms.author: renash
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 498af5cffb76e026c9a87127cab238f0f23b668a
ms.contentlocale: ru-ru
ms.lasthandoff: 08/21/2017

---

# <a name="introduction-to-azure-file-storage"></a>Знакомство с хранилищем файлов Azure

Хранилище файлов Azure предлагает сетевые файловые ресурсы в облаке, используя стандартные отраслевые протоколы [SMB](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx) и [CIFS](https://technet.microsoft.com/library/cc939973.aspx). Файловые ресурсы Azure могут параллельно подключаться службами "Виртуальные машины Azure" и клиентами локального развертывания под управлением Windows, macOS или Linux. Учетная запись хранения общего назначения предоставляет доступ к хранилищу файлов Azure, хранилищу BLOB-объектов Azure и хранилищу очередей Azure.

## <a name="videos"></a>Видеоролики
| Введение в хранилище файлов Azure (27 мин) | Руководство по файловому хранилищу Azure (5 мин)  |
|-|-|
| [![Ознакомительное видео о хранилище файлов Azure. Щелкните, чтобы воспроизвести.](./media/storage-files-introduction/azure-files-introduction-video-snapshot1.png)](https://www.youtube.com/watch?v=zlrpomv5RLs) | [![Видеоруководство по хранилищу файлов Azure. Щелкните, чтобы воспроизвести.](./media/storage-files-introduction/azure-files-introduction-video-snapshot2.png)](https://channel9.msdn.com/Blogs/Azure/Azure-File-storage-with-Windows/) |

## <a name="why-azure-file-storage-is-useful"></a>Почему нужно использовать хранилище файлов Azure

Хранилище файлов Azure позволит заменить Windows Server, Linux или файловые серверы на основе NAS, размещенные локально или в облаке, облачными файловыми ресурсами без ОС. Преимущества хранилища файлов Azure:

* **Совместный доступ.** Файловые ресурсы Azure поддерживают стандартный отраслевой протокол SMB. Это означает,что вы можете беспрепятственно заменить локальные файловые ресурсы файловыми ресурсами Azure, не беспокоясь о совместимости приложений. Возможность доступа к файловому ресурсу с нескольких виртуальных машин, приложений и экземпляров является значимым преимуществом хранилища файлов Azure.

* **Полная управляемость.** Файловые ресурсы Azure могут создаваться без необходимости управления аппаратным обеспечением или ОС. Это означает, что вам не нужно устанавливать исправления для серверной ОС, включая критически важные обновления безопасности, а также заменять неисправные жесткие диски.

* **Написание скриптов и создание средств.** Командлеты PowerShell и Azure CLI можно использовать для создания и подключения файловых ресурсов Azure, а также для управления ими в рамках администрирования приложений Azure. Вы можете создавать файловые ресурсы Azure и управлять ими с помощью [портала Azure](https://portal.azure.com) и [обозревателя хранилищ Azure](https://storageexplorer.com). 

* **Устойчивость.** Хранилище файлов Azure было создано с нуля для обеспечения постоянной доступности. Замена локальных файловых ресурсов хранилищем файлов Azure означает, что вам больше не нужно заниматься локальными перебоями электропитания или проблемами сети. 

* **Знакомые возможности программирования.** Приложения, работающие в Azure, могут получать доступ к данным в общем ресурсе через [API-интерфейсы ввода-вывода файловой системы](https://msdn.microsoft.com/library/system.io.file.aspx). Таким образом, разработчики могут использовать имеющиеся у них коды и навыки для миграции существующих приложений. Помимо системных API-интерфейсов ввода-вывода, вы также можете использовать любые клиентские библиотеки службы хранилища Azure, например библиотеки для [.NET](/dotnet/api/overview/azure/storage?view=azure-dotnet), или [REST API службы хранилища Azure](/rest/api/storageservices/file-service-rest-api).

Файловые ресурсы Azure можно использовать следующим образом:

* **Для замены локальных файловых серверов.** Хранилище файлов Azure можно использовать для полной замены файловых ресурсов на традиционных локальных файловых серверах или запоминающих устройствах, подключаемых к сети. Файловые ресурсы Azure можно легко подключить с помощью распространенных операционных систем, например Windows, Linux и macOS, независимо от их расположения.

* **Для переноса приложений по методике lift-and-shift.**

    С хранилищем файлов Azure можно легко переместить в облако те приложения, которые используют локальные файловые ресурсы для совместного использования данных между частями приложения. Для этого каждая виртуальная машина подключается к файловому ресурсу и затем может выполнять чтение и запись файлов так же, как в локальном файловом ресурсе.

* **Для упрощения разработки в облаке.**
    
    Хранилище файлов Azure можно использовать самыми различными способами, чтобы упростить новые проекты разработки в облаке.
    
    * **Общие параметры приложения**
    
        Стандартная процедура для распределенных приложений — поместить файлы конфигурации в централизованное расположение, где доступ к ним можно получить из множества разных виртуальных машин. Эти файлы конфигурации можно хранить в общем ресурсе файлов Azure, и его могут читать все экземпляры приложений. Этими параметрами можно также управлять через интерфейс REST, который разрешает доступ к файлам конфигурации по всему миру.

    * **Общий ресурс диагностики**
    
        Общий файловый ресурс Azure можно также использовать, чтобы сохранить файлы диагностики, например журналы, метрики и аварийные дампы. Доступность файловых ресурсов в интерфейсе SMB и REST позволяет приложениям создавать или использовать разные средства анализа для обработки и анализа данных диагностики.

    * **Разработка, тестирование и отладка**

        Когда разработчики или администраторы работают на виртуальных машинах в облаке, им часто требуется набор средств или служебных программ. Установка и распространение этих служебных программ на каждой виртуальной машине, где они требуются, занимает много времени. С хранилищем файлов Azure разработчик или администратор может хранить в общем файловом ресурсе избранные средства и легко подключаться к этому хранилищу с любой виртуальной машины.
        
## <a name="how-does-it-work"></a>Как это работает?

Управление файловыми ресурсами Azure гораздо проще, чем управление локальными файловыми ресурсами. На схеме ниже показаны конструкции управления хранилищем файлов Azure.

![Структура файла](./media/storage-files-introduction/files-concepts.png)

* **Учетная запись хранения.** Весь доступ к службе хранилища Azure осуществляется с помощью учетной записи хранения. Сведения о емкости учетной записи хранения см. в статье [о целевых показателях масштабируемости и производительности службы хранилища Azure](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

* **Общая папка.** Общая папка хранилища файлов представляет собой общую папку с файлами в Azure, использующую протокол SMB. Все каталоги и файлы должны быть созданы в родительской общей папке. Учетная запись может содержать любое количество общих папок, а общая папка может содержать любое количество файлов, общий размер которых не превышает 5 ТБ.

* **Каталог.** Необязательная иерархия каталогов.

* **Файл.** Файл в общей папке. Файлы могут иметь размер до 1 ТБ.

* **Формат URL-адреса.** К файлам можно обращаться, используя следующий формат URL-адреса:  

    ```
    https://<storage account>.file.core.windows.net/<share>/<directory/directories>/<file>
    ```

## <a name="next-steps"></a>Дальнейшие действия

* [Create a File Share in Azure File storage](storage-how-to-create-file-share.md) (Создание файлового ресурса в хранилище файлов Azure)
* [Mount an Azure File share and access the share in Windows](storage-how-to-use-files-windows.md) (Подключение файлового ресурса Azure и доступ к нему в Windows)
* [Использование хранилища файлов Azure в Linux](storage-how-to-use-files-linux.md)
* [Mount Azure File share over SMB with macOS](storage-how-to-use-files-mac.md) (Использование хранилища файлов Azure с помощью протокола SMB в macOS)
* [Часто задаваемые вопросы](../storage-files-faq.md)
* [Troubleshoot Azure File storage problems in Windows](storage-troubleshoot-windows-file-connection-problems.md) (Устранение неполадок хранилища файлов Azure в Windows)   
* [Troubleshoot Azure File storage problems in Linux](storage-troubleshoot-linux-file-connection-problems.md) (Устранение неполадок хранилища файлов Azure в Linux)   

<!-- Rena I would remove any articles from here that are more than a year old. - Robin-->
### <a name="conceptual-articles-and-videos"></a>Тематические статьи и видео
* [Azure Files Storage: a frictionless cloud SMB file system for Windows and Linux](https://azure.microsoft.com/documentation/videos/azurecon-2015-azure-files-storage-a-frictionless-cloud-smb-file-system-for-windows-and-linux/) (Хранилище файлов Azure: удобная облачная файловая система SMB для Windows и Linux)

### <a name="tooling-support-for-azure-file-storage"></a>Поддержка средств для работы с хранилищем файлов Azure
* [Использование AzCopy со службой хранилища Microsoft Azure](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)
* [Использование интерфейса командной строки (CLI) Azure со службой хранилища Azure](../common/storage-azure-cli.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#create-and-manage-file-shares)

### <a name="blog-posts"></a>Записи блога
* [Хранилище файлов Azure стало общедоступным](https://azure.microsoft.com/blog/azure-file-storage-now-generally-available/)
* [Inside Azure File Storage](https://azure.microsoft.com/blog/inside-azure-file-storage/) (Хранилище файлов Azure: взгляд изнутри)
* [Введение в службы файлов Microsoft Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx)
* [Migrating Data to Microsoft Azure Files](https://azure.microsoft.com/blog/migrating-data-to-microsoft-azure-files/) (Перемещение данных в файлы Microsoft Azure)

### <a name="reference"></a>Справочные материалы
* [Справочник по клиентской библиотеке хранилища для .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx)
* [Справочник по REST API службы файлов](http://msdn.microsoft.com/library/azure/dn167006.aspx)

