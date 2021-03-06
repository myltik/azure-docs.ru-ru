---
title: Резервное копирование файлов в Azure
description: В этой статье описывается, как создавать резервные копии и восстанавливать файловые ресурсы Azure, а также объясняются задачи управления.
services: backup
author: markgalioto
ms.author: markgal
ms.date: 3/23/2018
ms.topic: tutorial
ms.service: backup
manager: carmonm
ms.openlocfilehash: 440cee4309fad0a22d8964982f3aad2178397124
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2018
---
# <a name="back-up-azure-file-shares"></a>Резервное копирование файловых ресурсов Azure
В этой статье описывается, как с помощью портала Azure создавать резервные копии и восстанавливать [файловые ресурсы Azure](../storage/files/storage-files-introduction.md).

Из этого руководства вы узнаете, как выполнить следующие задачи:
> [!div class="checklist"]
> * Настройка хранилища служб восстановления для резервного копирования файлов Azure.
> * Выполнение задания резервного копирования по запросу для создания точки восстановления.
> * Восстановление файлов (одного или нескольких) из точки восстановления.
> * Управление заданиями архивации
> * Остановка защиты файлов Azure.
> * Удаление данных резервных копий.

## <a name="prerequisites"></a>предварительным требованиям
Прежде чем создавать резервную копию файлового ресурса Azure, убедитесь, что он находится в [учетной записи хранения одного из поддерживаемых типов](troubleshoot-azure-files.md#preview-boundaries). После этого можно защитить файловые ресурсы.

## <a name="limitations-for-azure-file-share-backup-during-preview"></a>Ограничения резервного копирования файловых ресурсов Azure в предварительной версии
Функция резервного копирования файловых ресурсов Azure находится в стадии предварительной версии. На этапе предварительной версии необходимо учитывать следующие ограничения:
- Вы не можете защитить файловые ресурсы Azure в учетных записях хранения, в которых используется репликация в [хранилище, избыточное в пределах зоны (ZRS)](../storage/common/storage-redundancy-zrs.md), или в [геоизбыточное хранилище с доступом на чтение (RA-GRS)](../storage/common/storage-redundancy-grs.md).
- Вы не можете защитить файловые ресурсы Azure в учетных записях хранения, в которых активированы виртуальные сети.
- Для применения защиты файлов Azure использовать PowerShell или CLI нельзя.
- Максимальное число запланированных резервных копий в день — одна.
- Максимальное число резервных копий по запросу в день — четыре.
- Чтобы предотвратить случайное удаление резервных копий в хранилище служб восстановления, используйте [блокировку ресурсов](https://docs.microsoft.com/cli/azure/resource/lock?view=azure-cli-latest) в учетной записи хранения.
- Не удаляйте моментальные снимки, созданные службой Azure Backup. Удаление моментальных снимков может привести к потере точек восстановления и (или) сбоям при выполнении восстановления. 

## <a name="configuring-backup-for-an-azure-file-share"></a>Настройка резервного копирования для файлового ресурса Azure
Все данные резервных копий хранятся в хранилищах служб восстановления. В этом руководстве предполагается, что у вас уже есть файловый ресурс Azure. Чтобы создать резервную копию файлового ресурса Azure, сделайте вот что:

1. Создайте хранилище служб восстановления в регионе вашего файлового ресурса. Если у вас уже есть хранилище, откройте страницу обзора хранилища и щелкните **Резервное копирование**.

    ![Выбор файлового ресурса Azure в качестве цели резервного копирования](./media/backup-file-shares/overview-backup-page.png)

2. В меню цели резервного копирования в поле **What do you want to backup?** (Что необходимо архивировать?) выберите файловый ресурс Azure.

    ![Выбор файлового ресурса Azure в качестве цели резервного копирования](./media/backup-file-shares/choose-azure-fileshare-from-backup-goal.png)

3. Щелкните **Резервное копирование**, чтобы указать для файлового ресурса Azure хранилище служб восстановления. 

   ![Щелкните "Резервное копирование", чтобы связать файловый ресурс Azure с хранилищем.](./media/backup-file-shares/set-backup-goal.png)

    Когда хранилище будет связано с файловым ресурсом Azure, откроется меню резервного копирования, где вам необходимо выбрать учетную запись хранения. В меню отображаются все поддерживаемые учетные записи хранения в регионе вашего хранилища, которые еще не связаны с хранилищем служб восстановления.

   ![Щелкните "Резервное копирование", чтобы связать файловый ресурс Azure с хранилищем.](./media/backup-file-shares/list-of-storage-accounts.png)

4. В списке учетных записей хранения выберите учетную запись и нажмите кнопку **ОК**. Azure ищет учетную запись хранения для файловых ресурсов, которые можно архивировать. Если вы не видите в списке недавно добавленные файловые ресурсы, подождите, пока они появятся.

   ![Щелкните "Резервное копирование", чтобы связать файловый ресурс Azure с хранилищем.](./media/backup-file-shares/discover-file-shares.png)

5. В списке **файловых ресурсов** выберите один или несколько ресурсов для резервного копирования и нажмите кнопку **ОК**.

6. После этого откроется меню **Политика архивации**. В этом меню выберите имеющуюся политику или создайте новую, а затем щелкните **Включить резервное копирование**. 

   ![Щелкните "Резервное копирование", чтобы связать файловый ресурс Azure с хранилищем.](./media/backup-file-shares/apply-backup-policy.png)

    После настройки политики резервного копирования в запланированное время будет сделан моментальный снимок файловых ресурсов, а точка восстановления будет сохранена на выбранный период времени.

## <a name="create-an-on-demand-backup"></a>Создание резервной копии по запросу
В некоторых случаях необходимо создать моментальный снимок резервной копии или точку восстановления вне запланированного времени, указанного в политике резервного копирования. Обычно резервную копию по запросу создают сразу после настройки политики резервного копирования. В зависимости от расписания в политике резервного копирования моментальный снимок может быть создан через несколько часов или дней. Чтобы защитить свои данные до резервного копирования по расписанию, инициируйте резервное копирование по запросу. Часто прежде чем вносить запланированные изменения в файловые ресурсы, необходимо создать резервную копию по запросу. 

### <a name="to-create-an-on-demand-backup"></a>Создание резервной копии по запросу

1. Откройте хранилище служб восстановления, содержащее точки восстановления файловых ресурсов, и щелкните **Элементы архивации**. Откроется список типов элементов архивации.

   ![Щелкните "Резервное копирование", чтобы связать файловый ресурс Azure с хранилищем.](./media/backup-file-shares/list-of-backup-items.png)

2. В списке выберите **Azure Storage (Azure Files)** (Служба хранилища Azure (файлы Azure)). Откроется список файловых ресурсов Azure.

   ![Щелкните "Резервное копирование", чтобы связать файловый ресурс Azure с хранилищем.](./media/backup-file-shares/list-of-azure-files-backup-items.png)

3. В списке файловых ресурсов Azure выберите необходимый ресурс. Для него откроется меню элементов архивации.

   ![Щелкните "Резервное копирование", чтобы связать файловый ресурс Azure с хранилищем.](./media/backup-file-shares/backup-item-menu.png)

4. В меню элементов архивации щелкните **Моментальная архивация**. Так как это задание резервного копирования по запросу, политика хранения не связана с точкой восстановления. Откроется диалоговое окно **Моментальная архивация**. Укажите срок хранения точки восстановления (последний день). 
  
   ![Щелкните "Резервное копирование", чтобы связать файловый ресурс Azure с хранилищем.](./media/backup-file-shares/backup-now-menu.png)

## <a name="restore-from-backup-of-azure-file-share"></a>Восстановление файлового ресурса Azure из резервной копии
Если вам нужно восстановить файловый ресурс или отдельные файлы и папки из точки восстановления, перейдите к элементу архивации, как описано в предыдущем разделе. Выберите **Восстановить общую папку**, чтобы восстановить файловый ресурс с состоянием на нужный момент времени. Выберите одну точку восстановления из появившегося списка, чтобы перезаписать текущий файловый ресурс или восстановить альтернативный ресурс в том же регионе.

   ![Щелкните "Резервное копирование", чтобы связать файловый ресурс Azure с хранилищем.](./media/backup-file-shares/select-restore-location.png)

## <a name="restore-individual-files-or-folders-from-backup-of-azure-file-shares"></a>Восстановление отдельных файлов или папок из резервной копии файловых ресурсов Azure
Служба Azure Backup обеспечивает возможность просмотра точки восстановления на портале Azure. Чтобы восстановить файл или папку, щелкните "Восстановление файлов" на странице "Элемент архивации" и выберите в списке нужную точку восстановления. Выберите место назначения восстановления, а затем щелкните **Выбрать файл**, чтобы перейти к точке восстановления. Выберите файл или папку и нажмите кнопку **Восстановить**.

   ![Щелкните "Резервное копирование", чтобы связать файловый ресурс Azure с хранилищем.](./media/backup-file-shares/restore-individual-files-folders.png)

## <a name="manage-azure-file-share-backups"></a>Управление резервными копиями файловых ресурсов Azure

На странице **Задания резервного копирования** вы можете выполнить следующие задачи управления для резервных копий файловых ресурсов:
- [Мониторинг заданий](backup-azure-files.md#monitor-jobs)
- [Создание политики](backup-azure-files.md#create-a-new-policy).
- [Остановка защиты файлового ресурса](backup-azure-files.md#stop-protecting-an-azure-file-share).
- [Возобновление защиты файлового ресурса](backup-azure-files.md#resume-protection-for-azure-file-share).
- [Удаление данных резервных копий](backup-azure-files.md#delete-backup-data).

### <a name="monitor-jobs"></a>Мониторинг заданий

Вы можете отслеживать ход выполнения всех заданий на странице **Задания резервного копирования**.

Чтобы открыть страницу **Задания резервного копирования**, выполните следующее:

- Откройте хранилище служб восстановления для мониторинга и в меню хранилища щелкните **Задания**, а затем выберите **Задания резервного копирования**.
   ![Выбор задания, данные которого необходимо отследить](./media/backup-file-shares/open-backup-jobs.png)

    Откроется список заданий резервного копирования и их состояние.
   ![Выбор задания, данные которого необходимо отследить](./media/backup-file-shares/backup-jobs-progress-list.png)

### <a name="create-a-new-policy"></a>Создание новой политики

В хранилище служб восстановления в разделе **Политики резервного копирования** можно создать новую политику резервного копирования файловых ресурсов Azure. Всем политикам, создаваемым при настройке резервного копирования файловых ресурсов, присваивается тип "Общая папка Azure".

Чтобы просмотреть имеющиеся политики резервного копирования, выполните следующее:

- Откройте хранилище служб восстановления и в меню хранилища щелкните **Политики архивации**. В списке перечислены все политики резервного копирования.

   ![Выбор задания, данные которого необходимо отследить](./media/backup-file-shares/list-of-backup-policies.png)

Чтобы создать политику резервного копирования, выполните следующее:

1. В меню хранилища служб восстановления щелкните **Политики архивации**.
2. В списке политик резервного копирования щелкните **Добавить**.

   ![Выбор задания, данные которого необходимо отследить](./media/backup-file-shares/new-backup-policy.png)

3. В меню **Добавить** выберите **Общая папка Azure**. Откроется меню политики резервного копирования для файлового ресурса Azure. Укажите имя политики, частоту выполнения резервного копирования и период удержания для точек восстановления. Определив политику, нажмите кнопку "ОК".

   ![Выбор задания, данные которого необходимо отследить](./media/backup-file-shares/create-new-policy.png)

### <a name="stop-protecting-an-azure-file-share"></a>Отключение защиты файлового ресурса Azure

При отключении защиты файлового ресурса Azure появится запрос на сохранение точек восстановления. Есть два способа отключить защиту файловых ресурсов Azure:

- Остановить все будущие операции резервного копирования и удалить все точки восстановления.
- Остановить все будущие операции резервного копирования, но сохранить точки восстановления.

Могут возникнуть затраты, связанные с оставлением точек восстановления в хранилище, так как базовые моментальные снимки, созданные службой Azure Backup, также будут сохранены. Однако позже с помощью этих точек при необходимости можно восстановить файловый ресурс. Дополнительные сведения о тарифах на хранение точек восстановления см. в описании цен. Если вы удалите все точки восстановления, вы не сможете восстановить файловый ресурс.

Чтобы отключить защиту файлового ресурса Azure, сделайте вот что:

1. Откройте хранилище служб восстановления, содержащее точки восстановления файловых ресурсов, и щелкните **Элементы архивации**. Откроется список типов элементов архивации.

   ![Щелкните "Резервное копирование", чтобы связать файловый ресурс Azure с хранилищем.](./media/backup-file-shares/list-of-backup-items.png) 

2. В списке **Backup Management Type** (Тип управления резервным копированием) выберите **Azure Storage (Azure Files)** (Служба хранилища Azure (файлы Azure)). Откроется список элементов архивации для этого типа.

   ![Щелкните элемент, чтобы открыть дополнительное меню](./media/backup-file-shares/azure-file-share-backup-items.png) 

3. В списке элементов резервного копирования (служба хранилища Azure (файлы Azure)) выберите элемент архивации, который вы хотите остановить.

4. Рядом с элементами файловых ресурсов Azure щелкните меню **Дополнительно** и выберите **Остановить архивацию**. 

   ![Щелкните элемент, чтобы открыть дополнительное меню](./media/backup-file-shares/stop-backup.png)

5. В меню остановки резервного копирования выберите команду **сохранения** или **удаления данных архивации** и щелкните **Stop Backup** (Остановить архивацию).

   ![Щелкните элемент, чтобы открыть дополнительное меню](./media/backup-file-shares/retain-data.png)

### <a name="resume-protection-for-azure-file-share"></a>Повторное включение защиты для файлового ресурса Azure

Если при остановке защиты данные резервного копирования были сохранены, их можно использовать для возобновления защиты файловых ресурсов. Если **данные резервного копирования были удалены**, возобновить защиту файловых ресурсов невозможно.

Чтобы возобновить защиту файлового ресурса, перейдите к элементу резервного копирования и щелкните Resume Backup (Возобновить резервное копирование). Откроется меню политики резервного копирования, и вы сможете выбрать политику, чтобы возобновить резервное копирование.

   ![Выбор задания, данные которого необходимо отследить](./media/backup-file-shares/resume-backup-job.png)

### <a name="delete-backup-data"></a>Удаление данных резервных копий 

Вы можете удалить резервную копию файлового ресурса во время выполнения задания остановки резервного копирования или в любое время после остановки защиты. Возможно, стоит подождать несколько дней или недель, прежде чем удалять точки восстановления. В отличие от восстановления точек восстановления при удалении данных резервного копирования нельзя выбрать определенные точки для удаления. При удалении данных резервного копирования удаляются все точки восстановления, связанные с этим элементом.

В следующей процедуре подразумевается, что задание резервного копирования для виртуальной машины остановлено. Только после остановки этого задания на панели мониторинга элемента резервного копирования станут доступными параметры Resume backup (Возобновить резервное копирование) и "Удалить данные архивации". Щелкните "Удалить данные архивации" и введите имя файлового ресурса, чтобы подтвердить удаление. При желании можно указать причину удаления или добавить комментарий.

## <a name="see-also"></a>См. также
Дополнительные сведения о файловых ресурсах Azure см. в следующих статьях:
- [Вопросы о резервном копировании файлов Azure](backup-azure-files-faq.md).
- [Устранение неполадок с резервным копированием файлов Azure](troubleshoot-azure-files.md).
