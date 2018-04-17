---
title: Интеграция Key Vault с SQL Server на виртуальных машинах Windows в Azure (Resource Manager) | Документация Майкрософт
description: Узнайте, как автоматизировать настройку шифрования SQL Server для использования с хранилищем ключей Azure. В этом разделе описываются способы использования интеграции хранилища ключей Azure с виртуальными машинами SQL Server, созданными с помощью диспетчера ресурсов.
services: virtual-machines-windows
documentationcenter: ''
author: rothja
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: cd66dfb1-0e9b-4fb0-a471-9deaf4ab4ab8
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/23/2017
ms.author: jroth
ms.openlocfilehash: 7df0bc4b74694baa6b1c8a30d0c126b248e51168
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/29/2018
---
# <a name="configure-azure-key-vault-integration-for-sql-server-on-azure-virtual-machines-resource-manager"></a>Настройка интеграции Azure Key Vault для SQL Server на виртуальных машинах Azure (Resource Manager)
> [!div class="op_single_selector"]
> * [Resource Manager](virtual-machines-windows-ps-sql-keyvault.md)
> * [Классический](../sqlclassic/virtual-machines-windows-classic-ps-sql-keyvault.md)

## <a name="overview"></a>Обзор
Существует несколько функций шифрования SQL Server, например [прозрачное шифрование данных (TDE)](https://msdn.microsoft.com/library/bb934049.aspx), [шифрование на уровне столбцов (CLE)](https://msdn.microsoft.com/library/ms173744.aspx) и [шифрование резервной копии](https://msdn.microsoft.com/library/dn449489.aspx). Эти формы шифрования требуют хранить используемые для шифрования ключи и управлять ими. Хранилище ключей Azure (AKV) предназначено для повышения безопасности и управления этими ключами в расположении высокой надежности и безопасности. [Соединитель SQL Server](http://www.microsoft.com/download/details.aspx?id=45344) позволяет SQL Server использовать эти ключи из хранилища ключей Azure.

Если вы используете SQL Server с локальными машинами, выполните [шаги для доступа к хранилищу ключей Azure с локального компьютера под управлением SQL Server](https://msdn.microsoft.com/library/dn198405.aspx). Однако в случае SQL Server на виртуальных машинах Azure можно сэкономить время, воспользовавшись функцией *интеграции хранилища ключей Azure* .

Если эта функция включена, она автоматически устанавливает соединитель SQL Server, настраивает поставщик расширенного управления ключами для доступа к хранилищу ключей Azure и создает учетные данные, которые позволяют получить доступ к вашему хранилищу. Если вы изучили шаги в ранее упомянутой документации для локального компьютера, то увидели, что эта функция автоматизирует шаги 2 и 3. Единственное, что останется сделать вручную, — создать хранилище ключей и ключи. Далее вся настройка виртуальной машины SQL выполняется автоматически. После завершения установки этой функции можно выполнять инструкции T-SQL, чтобы начать шифрование базы данных или резервное копирование как обычно.

[!INCLUDE [AKV Integration Prepare](../../../../includes/virtual-machines-sql-server-akv-prepare.md)]

## <a name="enabling-and-configuring-akv-integration"></a>Включение и настройка интеграции AKV
Интеграцию AKV можно включить во время подготовки или настроить ее для существующих виртуальных машин.

### <a name="new-vms"></a>Новые виртуальные машины
Если вы подготавливаете новую виртуальную машину SQL Server с помощью диспетчера ресурсов, портал Azure предоставляет возможность включения интеграции с хранилищем ключей Azure. Хранилище ключей Azure доступно в SQL Server только для выпусков Enterprise, Developer и Evaluation Edition.

![Интеграция SQL с хранилищем ключей Azure](./media/virtual-machines-windows-ps-sql-keyvault/azure-sql-arm-akv.png)

Пошаговое руководство по подготовке см. в статье [Подготовка виртуальной машины SQL Server на портале Azure](virtual-machines-windows-portal-sql-server-provision.md).

### <a name="existing-vms"></a>Существующие виртуальные машины
Выберите существующую виртуальную машину SQL Server. Затем в колонке **Параметры** выберите раздел **Конфигурация SQL Server**.

![Интеграция AKV SQL для существующих виртуальных машин](./media/virtual-machines-windows-ps-sql-keyvault/azure-sql-rm-akv-existing-vms.png)

В колонке **Конфигурация SQL Server** нажмите кнопку **Изменить** в разделе "Automated Key Vault integration" (Автоматизированная интеграция с хранилищем ключей).

![Настройка интеграции AKV SQL для существующих виртуальных машин](./media/virtual-machines-windows-ps-sql-keyvault/azure-sql-rm-akv-configuration.png)

По завершении нажмите кнопку **ОК** в нижней части колонки **Конфигурация SQL Server**, чтобы сохранить изменения.

> [!NOTE]
> Имя учетных данных, которое мы создали в этом разделе, позже будет сопоставлено с именем для входа в SQL. С помощью этих данных для входа в SQL можно получить доступ к хранилищу ключей. 
>
>

> [!NOTE]
> Можно также настроить интеграцию AKV с помощью шаблона. Чтобы получить дополнительные сведения, ознакомьтесь с [шаблоном быстрого запуска Azure для интеграции с хранилищем ключей Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-sql-existing-keyvault-update).
> 
> 

[!INCLUDE [AKV Integration Next Steps](../../../../includes/virtual-machines-sql-server-akv-next-steps.md)]

