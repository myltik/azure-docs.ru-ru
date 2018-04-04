---
title: Средства для использования Ansible с Azure
description: Установка и применение отдельных средств для использования Ansible с Azure
ms.service: ansible
keywords: ansible, azure, devops, tools, vs code, visual studio code, extension
author: tomarcher
manager: routlaw
ms.author: tarcher
ms.date: 01/14/2018
ms.topic: article
ms.openlocfilehash: 73d31054bb0c40dd08bf4b4a93c31c59354b5bed
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/29/2018
---
# <a name="tools-for-using-ansible-with-azure"></a>Средства для использования Ansible с Azure

Следующие средства упрощают работу с Ansible и Azure и делают ее более эффективной.

## <a name="visual-studio-code-extension-for-ansible"></a>Расширение Visual Studio Code для Ansible

[Расширение Visual Studio Code для Ansible](https://marketplace.visualstudio.com/items?itemName=vscoss.vscode-ansible) предоставляет несколько функций для использования Ansible из Visual Studio Code:

- Автозаполнение директив, модулей и подключаемых модулей Ansible из документа Ansible при вводе.
- Отображение фрагментов кода при нажатии клавиш &lt;CTRL >&lt;ПРОБЕЛ > во время создания набора инструкций playbook в Ansible.
- При выделении синтаксиса код playbook Ansible отображается разными цветами и шрифтами в соответствии с синтаксисом YAML.
- Наборы инструкций playbook Ansible можно запускать из окна терминала Visual Studio Code:
    - (только для Windows) Ansible можно запускать из контейнера Docker;
    - (для Linux и macOS) Ansible можно запускать из контейнера Docker или из локально установленной системы Ansible. 
- Наборы инструкций playbook Ansible можно запускать из Azure Cloud Shell.