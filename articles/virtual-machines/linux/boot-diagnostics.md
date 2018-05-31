---
title: Диагностика загрузки виртуальных машин Linux в Azure | Документы Майкрософт
description: Общие сведения о двух функциях отладки для виртуальных машин Linux в Azure
services: virtual-machines-linux
documentationcenter: virtual-machines-linux
author: Deland-Han
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/19/2018
ms.author: delhan
ms.openlocfilehash: 38cc806cb77af60cda10f3aeac2e5ed13b445b8c
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/10/2018
ms.locfileid: "33941859"
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

- [Проблемы с файловой системой](https://support.microsoft.com/help/3213321/linux-recovery-cannot-ssh-to-linux-vm-due-to-file-system-errors-fsck) 
- [Проблемы с ядром](https://support.microsoft.com/help/4091524/how-recovery-azure-linux-vm-from-kernel-related-boot-related-issues/) 
- [Ошибки FSTAB](https://support.microsoft.com/help/3206699/azure-linux-vm-cannot-start-because-of-fstab-errors)

## <a name="enable-diagnostics-on-a-new-virtual-machine"></a>Включение диагностики на новой виртуальной машине
1. При создании виртуальной машины на портале Azure из раскрывающегося списка выбора модели развертывания выберите **Azure Resource Manager**.
 
    ![Диспетчер ресурсов](./media/boot-diagnostics/screenshot3.jpg)

2. В разделе **Параметры** включите функцию **Диагностика загрузки**, а затем выберите учетную запись хранения, в которую вы хотите поместить эти файлы данных диагностики.
 
    ![Создание виртуальной машины](./media/boot-diagnostics/create-storage-account.png)

    > [!NOTE]
    > Функция диагностики загрузки не поддерживает учетную запись хранения уровня "Премиум". В случае использования учетной записи хранения уровня "Премиум" для диагностики загрузки при запуске виртуальной машины может произойти ошибка StorageAccountTypeNotSupported. 
    >
    > 

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

Чтобы развернуть виртуальную машину c включенной функцией диагностики загрузки, просмотрите наш репозиторий.

## <a name="enable-boot-diagnostics-on-existing-virtual-machine"></a>Включение диагностики загрузки в существующей виртуальной машине 

Чтобы включить диагностику загрузки в существующей виртуальной машине, выполните следующие действия.

1. Войдите на [портал Azure](https://portal.azure.com) и выберите виртуальную машину.
2. В разделе **Support + troubleshooting** (Поддержка и устранение неполадок) выберите **Диагностика загрузки** > **Параметры**, измените состояние на **Включено**, а затем выберите учетную запись хранения. 
4. Убедитесь, что выбран параметр "Диагностика загрузки", и сохраните изменения.

    ![Обновление имеющейся виртуальной машины](./media/boot-diagnostics/enable-for-existing-vm.png)

3. Чтобы изменения вступили в силу, перезапустите виртуальную машину.

## <a name="next-steps"></a>Дополнительная информация

Если при использовании диагностики загрузки виртуальных машин отображается ошибка Failed to get contents of the log (Не удалось получить содержимое журнала), ознакомьтесь с разделом [Failed to get contents of the log error in VM Boot Diagnostics](https://support.microsoft.com/help/4094480/failed-to-get-contents-of-the-log-error-in-vm-boot-diagnostics-in-azur) (Ошибка "Не удалось получить содержимое журнала" при диагностике загрузки виртуальных машин).