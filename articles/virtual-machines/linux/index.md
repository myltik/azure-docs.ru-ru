---
title: "Документация по виртуальным машинам Linux в Azure. Руководства и справочник по API | Документация Майкрософт"
description: "Узнайте, как создать виртуальную машину с помощью дистрибутива Linux на свой выбор. В справочной документации показаны различные способы создания шаблонов виртуальных машин."
services: virtual-machines\linux
author: carolz
manager: carolz
layout: LandingPage
ms.service: virtual-machines\linux
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: landing-page
ms.date: 01/23/2017
ms.author: carolz
translationtype: Human Translation
ms.sourcegitcommit: 0d6f6fb24f1f01d703104f925dcd03ee1ff46062
ms.openlocfilehash: af1b164891a6d8af27b05578a975d1fa54e2d408
ms.lasthandoff: 04/17/2017

---
<div class="content">
    <h1>Документация по виртуальным машинам Linux</h1>
    <p style="padding-bottom: 0px; max-width: 1050px;">Виртуальные машины Linux Azure предоставляют по запросу безопасную высокомасштабируемую виртуализированную инфраструктуру с использованием Red Hat, Ubuntu или дистрибутива Linux по вашему выбору.  Узнайте, как создавать, настраивать, масштабировать виртуальные машины Linux и управлять ими, используя наши шаблоны быстрого запуска, примеры и руководства.</p>
<h2 style="margin-top: 36px; margin-bottom: 0px;">Быстрое начало работы</h2>
<p style="margin-top: 6px; margin-bottom: 6px;">Создание виртуальной машины Linux с помощью таких средств:</p>
<div class="ico48Case">
    <div class="ico48Link">
        <a href="/azure/virtual-machines/virtual-machines-linux-quick-create-cli?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json">
            <img src="media/index/cli.svg" alt="">
            <span>Azure CLI</span>
        </a>
    ;</div>
    <div class="ico48Link">
        <a href="/azure/virtual-machines/virtual-machines-linux-quick-create-portal?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json">
        <img src="media/index/portal.svg" alt="">
            <span>портал Azure</span>
        </a>
    ;</div>
    <div class="ico48Link">
        <a href="/azure/virtual-machines/virtual-machines-linux-quick-create-powershell?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json">
            <img src="media/index/logo_powershell.svg" alt="">
            <span>Azure PowerShell</span>
        </a>
    .</div>
<div>
<div class="columnHolder">
    <div class="column50">
        <h2 style="margin-top: 36px">Учебники</h2>
        <p>Сборка и развертывание виртуальных машин Linux.</p>
        <ul class="spaced">
            <li><a href="/azure/virtual-machines/linux/tutorial-manage-vm">Manage Linux virtual machines with the Azure CLI</a> (Управление виртуальными машинами Linux с помощью Azure CLI)</li>
            <li><a href="/azure/virtual-machines/linux/tutorial-load-balance-nodejs">Балансировка нагрузки высокодоступных виртуальных машин</a></li>
            <li><a href="/azure/virtual-machines/linux/tutorial-create-vmss">Создание масштабируемого набора виртуальных машин</a></li>
        </ul>
        <h2>Примеры</h2>
        <p>Развертывание первого приложения в Azure.</p>
        <ul class="spaced">
            <li><a href="/azure/virtual-machines/virtual-machines-linux-cli-samples">Интерфейс командной строки Azure</a></li>
            <li><a href="/azure/virtual-machines/virtual-machines-linux-powershell-samples">Azure PowerShell</a></li>
        </ul>
    </div>
    <div class="column50" style="max-width: 400px">
        <h2 style="margin-top: 36px">Бесплатные учебные видеоматериалы</h2>
        <a href="https://www.pluralsight.com/courses/managing-infrastructure-microsoft-azure-getting-started?twoid=d6abac77-7dcc-4d33-9e03-f85e78989f02">
            <img src="media/index/video-training-infrastructure.png" width="400" alt="" />
            <p style="margin-top: 0px">Бесплатные видеоуроки PluralSight. Управление инфраструктурой</a></p>
            <p style="margin-top: 36px"></p>
                <a href="https://www.pluralsight.com/courses/azure-vms-getting-started?twoid=d6abac77-7dcc-4d33-9e03-f85e78989f02">
            <img src="media/index/video-training-vms.png" width="400" alt="" />
            <p style="margin-top: 0px">Бесплатные видеоуроки PluralSight. Начало работы с виртуальными машинами</a></p>
        <p style="margin-top: 36px"></p>
        <a href="https://www.pluralsight.com/courses/azure-iaas-monitoring-management-getting-started?twoid=d6abac77-7dcc-4d33-9e03-f85e78989f02">
            <img src="media/index/video-training-iaas-monitoring.png" width="400" alt="" />
            <p style="margin-top: 0px">Бесплатные видеоуроки PluralSight. Начало работы с мониторингом IaaS</a></p>
    </div>
</div>
<hr />
<h2 style="margin-top: 36px">Справочные материалы</h2>
<ul class="panelContent cardsW">
    <li>
        <div class="cardSize">
            <div class="cardPadding">
                <div class="card">
                    <div class="cardText">
                        <h3>Командная строка</h3>
                        <p><a href="/cli/azure/vm">Интерфейс командной строки Azure</a></p>
                        <p><a href="/powershell/azureps-cmdlets-docs">Azure PowerShell</a></p>
                    </div>
                </div>
            </div>
        </div>
    </li>
    <li>
        <div class="cardSize">
            <div class="cardPadding">
                <div class="card">
                    <div class="cardText">
                        <h3>Языки</h3>
                        <p><a href="/dotnet/api/microsoft.azure.management.compute">.NET</a></p>
                        <p><a href="/java/api">Java</a></p>
                        <p><a href="https://azure.microsoft.com/en-us/develop/nodejs/#azure-sdk">Node.js</a></p>
                        <p><a href="http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.mgmt.compute.html">Python</a></p>
                    </div>
                </div>
            </div>
        </div>
    </li>
    <li>
        <div class="cardSize">
            <div class="cardPadding">
                <div class="card">
                    <div class="cardText">
                        <h3>REST</h3>
                        <p><a href="/rest/api/compute">справочник по API REST</a></p>
                    </div>
                </div>
            </div>
        </div>
    </li>
</ul>
</div>

