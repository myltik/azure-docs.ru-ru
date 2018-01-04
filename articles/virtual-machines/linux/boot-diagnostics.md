---
title: "Диагностика загрузки виртуальных машин Linux в Azure | Документы Майкрософт"
description: "Общие сведения о двух функциях отладки для виртуальных машин Linux в Azure"
services: virtual-machines-linux
documentationcenter: virtual-machines-linux
author: Deland-Han
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 08/21/2017
ms.author: delhan
ms.openlocfilehash: 70254d39b5c6326166f7e29fdfc99533835502f9
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/03/2018
---
# <a name="how-to-use-boot-diagnostics-to-troubleshoot-linux-virtual-machines-in-azure"></a>Использование диагностики загрузки для устранения неполадок виртуальных машин Linux в Azure

Теперь Azure поддерживает две функции отладки. Виртуальные машины Azure, созданные на основе модели развертывания с помощью Resource Manager, поддерживают выходные данные и снимки экрана консоли. 

При передаче собственного образа в Azure или даже при загрузке одного из образов платформ могут возникать проблемы с загрузкой виртуальной машины. Это может происходить по разным причинам. Новые функции позволяют легко диагностировать и восстанавливать виртуальную машину после сбоев загрузки.

На виртуальных машинах Linux выходные данные журнала консоли можно легко просмотреть на портале:

![Портал Azure](./media/boot-diagnostics/screenshot1.png)
 
Но Azure также позволяет просматривать снимки экранов виртуальных машин Windows и Linux из гипервизора:

![Ошибка](./media/boot-diagnostics/screenshot2.png)

Обе эти функции доступны на виртуальных машинах Azure во всех регионах. Обратите внимание, что отображение снимков экрана и выходных данных в учетной записи хранения может занять до 10 минут.

## <a name="common-boot-errors"></a>Распространенные ошибки загрузки

- [Проблемы с файловой системой](https://blogs.msdn.microsoft.com/linuxonazure/2016/09/13/linux-recovery-cannot-ssh-to-linux-vm-due-to-file-system-errors-fsck-inodes/)
- [Проблемы с ядром](https://blogs.msdn.microsoft.com/linuxonazure/2016/10/09/linux-recovery-manually-fixing-non-boot-issues-related-to-kernel-problems/)
- [Ошибки FSTAB](https://blogs.msdn.microsoft.com/linuxonazure/2016/07/21/cannot-ssh-to-linux-vm-after-adding-data-disk-to-etcfstab-and-rebooting/ )

## <a name="enable-diagnostics-on-a-new-virtual-machine"></a>Включение диагностики на новой виртуальной машине
1. При создании виртуальной машины на портале предварительной версии в раскрывающемся списке выбора модели развертывания выберите **Azure Resource Manager**:
 
    ![Диспетчер ресурсов](./media/boot-diagnostics/screenshot3.jpg)

2. Настройте параметр "Мониторинг", указав учетную запись хранения, в которую вы хотели бы поместить эти файлы диагностики.
 
    ![Создание виртуальной машины](./media/boot-diagnostics/screenshot4.jpg)

3. При развертывании на основе шаблона Azure Resource Manager перейдите к ресурсу виртуальной машины и добавьте раздел профиля диагностики. Не забудьте добавить заголовок версии API 2015-06-15.

    ```json
    {
          "apiVersion": "2015-06-15",
          "type": "Microsoft.Compute/virtualMachines",
          … 
    ```

4. Профиль диагностики позволяет выбрать учетную запись хранения, в которую нужно поместить эти журналы.

    ```json
            "diagnosticsProfile": {
                "bootDiagnostics": {
                "enabled": true,
                "storageUri": "[concat('http://', parameters('newStorageAccountName'), '.blob.core.windows.net')]"
                }
            }
            }
        }
    ```

## <a name="update-an-existing-virtual-machine"></a>Обновление имеющейся виртуальной машины

Диагностику загрузки можно также включить на портале. Для этого обновите на портале имеющуюся виртуальную машину. Установите флажок "Диагностика загрузки" и нажмите кнопку "Сохранить". Чтобы изменения вступили в силу, перезапустите виртуальную машину.

![Обновление имеющейся виртуальной машины](./media/boot-diagnostics/screenshot5.png)