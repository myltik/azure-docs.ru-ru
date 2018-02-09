---
title: "Таблица версий и модулей Ansible для Azure"
description: "Таблица версий и модулей Ansible для Azure"
ms.service: ansible
keywords: ansible, roles, matrix, version, azure, devops
author: tomarcher
manager: routlaw
ms.author: tarcher
ms.date: 01/19/2018
ms.topic: article
ms.openlocfilehash: f62cc2df9e4ce815c4427b80e271ddc672748e4f
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/01/2018
---
# <a name="ansible-module-and-version-matrix"></a>Таблица версий и модулей Ansible

## <a name="ansible-modules-for-azure"></a>Модули Ansible для Azure
Ansible поставляется с большим количеством модулей, которые могут выполняться прямо на удаленных узлах или с помощью наборов инструкций playbook.
В этой статье перечислены модули Ansible для Azure, которые могут использоваться для подготовки таких облачных ресурсов Azure, как виртуальные машины, сети и службы контейнеров. Эти модули можно получить из официального выпуска Ansible или из указанных далее ролей playbook, опубликованных корпорацией Майкрософт.

| Модуль Ansible для Azure                   |  Ansible 2.4 |  Роль playbook [azure_module](#introduction-to-azuremodule) |  Роль playbook [azure_preview_module](#introduction-to-azurepreviewmodule) | 
|---------------------------------------------|--------------|-----------------------------|-------------------------------------| 
| **Среда выполнения приложений**                    |           |                          |                                  | 
| azure_rm_availabilityset                    | Yes          | Yes                         | Yes                                 | 
| azure_rm_availabilityset_facts              | Yes          | Yes                         | Yes                                 | 
| azure_rm_deployment                         | Yes          | Yes                         | Yes                                 | 
| azure_rm_virtualmachine_scaleset_facts      | Yes          | Yes                         | Yes                                 | 
| azure_rm_virtualmachineimage_facts          | Yes          | Yes                         | Yes                                 | 
| azure_rm_resourcegroup                      | Yes          | Yes                         | Yes                                 | 
| azure_rm_resourcegroup_facts                | Yes          | Yes                         | Yes                                 | 
| azure_rm_virtualmachine                     | Yes          | Yes                         | Yes                                 | 
| azure_rm_virtualmachine_extension           | Yes          | Yes                         | Yes                                 | 
| azure_rm_virtualmachine_scaleset            | Yes          | Yes                         | Yes                                 | 
| azure_rm_image                              |              | Yes                         | Yes                                 | 
| **Сеть**                    |           |                          |                                  | 
| azure_rm_virtualnetwork                     | Yes          | Yes                         | Yes                                 | 
| azure_rm_virtualnetwork_facts               | Yes          | Yes                         | Yes                                 | 
| azure_rm_subnet                             | Yes          | Yes                         | Yes                                 | 
| azure_rm_networkinterface                   | Yes          | Yes                         | Yes                                 | 
| azure_rm_networkinterface_facts             | Yes          | Yes                         | Yes                                 | 
| azure_rm_publicipaddress                    | Yes          | Yes                         | Yes                                 | 
| azure_rm_publicipaddress_facts              | Yes          | Yes                         | Yes                                 | 
| azure_rm_dnsrecordset                       | Yes          | Yes                         | Yes                                 | 
| azure_rm_dnsrecordset_facts                 | Yes          | Yes                         | Yes                                 | 
| azure_rm_dnszone                            | Yes          | Yes                         | Yes                                 | 
| azure_rm_dnszone_facts                      | Yes          | Yes                         | Yes                                 | 
| azure_rm_loadbalancer                       | Yes          | Yes                         | Yes                                 | 
| azure_rm_loadbalancer_facts                 | Yes          | Yes                         | Yes                                 | 
| azure_rm_appgw                              | -            | -                           | Yes                                 | 
| azure_rm_appgwroute                         | -            | -                           | Yes                                 | 
| azure_rm_appgwroute                         | -            | -                           | Yes                                 |
| azure_rm_appgwroute_facts                   | -            | -                           | Yes                                 |
| azure_rm_appgwroutetable                    | -            | -                           | Yes                                 |
| azure_rm_securitygroup                      | Yes          | Yes                         | Yes                                 | 
| azure_rm_appgwroutetable_facts              | Yes          | Yes                         | Yes                                 | 
| **Хранилище**                    |           |                          |                                  | 
| azure_rm_storageaccount                     | Yes          | Yes                         | Yes                                 | 
| azure_rm_storageaccount_facts               | Yes          | Yes                         | Yes                                 | 
| azure_rm_storageblob                        | Yes          | Yes                         | Yes                                 | 
| azure_rm_managed_disk                       | Yes          | Yes                         | Yes                                 | 
| azure_rm_managed_disk_facts                 | Yes          | Yes                         | Yes                                 | 
| **Контейнеры**                    |           |                          |                                  | 
| azure_rm_acs                                | Yes          | Yes                         | Yes                                 | 
| azure_rm_containerinstance                  | -            | Yes                         | Yes                                 | 
| azure_rm_containerinstance_facts            | -            | -                           | Yes                                 | 
| azure_rm_containerregistry                  | -            | Yes                         | Yes                                 | 
| azure_rm_containerregistry_facts            | -            | -                           | Yes                                 | 
| azure_rm_containerregistryreplication       | -            | -                           | Yes                                 | 
| azure_rm_containerregistryreplication_facts | -            | -                           | Yes                                 | 
| azure_rm_containerregistrywebhook           | -            | -                           | Yes                                 | 
| azure_rm_containerregistrywebhook_facts     | -            | -                           | Yes                                 | 
| **Функции Azure**                    |           |                          |                                  | 
| azure_rm_functionapp                        | Yes          | Yes                         | Yes                                 | 
| azure_rm_functionapp_facts                  | Yes          | Yes                         | Yes                                 | 
| **Базы данных**                    |           |                          |                                  | 
| azure_rm_sqlserver                          | -            | Yes                         | Yes                                 | 
| azure_rm_sqlserver_facts                    | -            | -                           | Yes                                 | 
| azure_rm_sqldatabase                        | -            | Yes                         | Yes                                 | 
| azure_rm_sqldatabase_facts                  | -            | -                           | Yes                                 | 
| azure_rm_sqlelasticpool                     | -            | -                           | Yes                                 | 
| azure_rm_sqlelasticpool_facts               | -            | -                           | Yes                                 | 
| azure_rm_sqlfirewallrule                    | -            | -                           | Yes                                 | 
| azure_rm_sqlfirewallrule_facts              | -            | -                           | Yes                                 | 
| azure_rm_mysqlserver                        | -            | Yes                         | Yes                                 | 
| azure_rm_mysqlserver_facts                  | -            | -                           | Yes                                 | 
| azure_rm_mysqldatabase                      | -            | Yes                         | Yes                                 | 
| azure_rm_mysqldatabase_facts                | -            | -                           | Yes                                 | 
| azure_rm_mysqlfirewallrule                  | -            | -                           | Yes                                 | 
| azure_rm_mysqlfirewallrule_facts            | -            | -                           | Yes                                 | 
| azure_rm_mysqlconfiguration                 | -            | -                           | Yes                                 | 
| azure_rm_mysqlconfiguration_facts           | -            | -                           | Yes                                 | 
| azure_rm_postgresqlserver                   | -            | Yes                         | Yes                                 | 
| azure_rm_postgresqlserver_facts             | -            | -                           | Yes                                 | 
| azure_rm_postgresqldatabase                 | -            | Yes                         | Yes                                 | 
| azure_rm_postgresqldatabase_facts           | -            | -                           | Yes                                 | 
| azure_rm_postgresqlfirewallrule             | -            | -                           | Yes                                 | 
| azure_rm_postgresqlfirewallrule_facts       | -            | -                           | Yes                                 | 
| azure_rm_postgresqlconfiguration            | -            | -                           | Yes                                 | 
| azure_rm_postgresqlconfiguration_facts      | -            | -                           | Yes                                 | 
| **хранилище ключей;**                    |           |                          |                                  | 
| azure_rm_keyvault                           | -            | -                           | Yes                                 |
| azure_rm_keyvault_facts                     | -            | -                           | Yes                                 |
| azure_rm_keyvaultkey                        | -            | -                           | Yes                                 |
| azure_rm_keyvaultsecret                     | -            | -                           | Yes                                 |

## <a name="introduction-to-azuremodule"></a>Общие сведения о роли azure_module
[Роль playbook azure_module](https://galaxy.ansible.com/Azure/azure_modules/) содержит последние изменения и исправления ошибок для модулей Azure из [ветви разработки репозитория Ansible](https://github.com/ansible/ansible/tree/devel). Если вы не можете ждать следующего выпуска Ansible, оптимальным решением будет установка роли azure_module.

Роль playbook azure_module выпускается каждые три недели.

## <a name="introduction-to-azurepreviewmodule"></a>Общие сведения о роли azure_preview_module
[Роль azure_preview_module](https://galaxy.ansible.com/Azure/azure_preview_modules/) — наиболее полная роль playbook, которая включает в себя все последние модули Azure. Обновления и исправления выполняются более оперативно, чем для официального выпуска Ansible. Если вы используете Ansible для подготовки ресурсов Azure, рекомендуем установить роль azure_preview_module.

Роль playbook azure_preview_module выпускается каждые три недели.

## <a name="next-steps"></a>Дополнительная информация
Дополнительные сведения о ролях playbook см. в документе [о создании повторно используемых наборов инструкций playbook](http://docs.ansible.com/ansible/latest/playbooks_reuse.html). 
