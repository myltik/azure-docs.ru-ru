---
title: "Настройка сетевых режимов для службы контейнеров Azure Service Fabric | Документация Майкрософт"
description: "Узнайте, как настроить разные сетевые режимы, поддерживаемые в Azure Service Fabric."
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: d552c8cd-67d1-45e8-91dc-871853f44fc6
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/9/2017
ms.author: subramar
ms.openlocfilehash: 1ecded3af6396f50e67dc5d2a9ef8337699046ea
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="service-fabric-container-networking-modes"></a>Сетевые режимы контейнеров Service Fabric

По умолчанию для служб контейнеров в кластере Service Fabric предлагается сетевой режим `nat`. Если при использовании сетевого режима `nat` несколько служб контейнеров ожидают передачи данных на один и тот же порт, возникнет ошибка развертывания. Для нескольких запущенных служб, которые ожидают передачи данных на один и тот же порт, Service Fabric поддерживает сетевой режим `open` (версии 5.7 или выше). При использовании сетевого режима `open` каждая служба контейнеров изнутри получает динамически назначаемый IP-адрес. Это позволяет нескольким службам ожидать передачи данных на один и тот же порт.   

Таким образом, при использовании одного типа службы со статической конечной точкой, определенной в манифесте службы, с помощью сетевого режима `open` можно создавать и удалять новые службы без ошибок развертывания. Аналогично, для создания нескольких служб можно использовать один и тот же файл `docker-compose.yml` с сопоставлениями статических портов.

Использовать динамически назначаемый IP-адрес для обнаружения служб не рекомендуется, так как IP-адрес изменяется, когда служба перезапускается или перемещается на другой узел. Для обнаружения служб рекомендуется использовать только **службу именования Service Fabric** или **службу DNS**. 


> [!WARNING]
> Для каждой виртуальной сети в Azure допускается максимум 4096 IP-адресов. Таким образом, общее число узлов и число экземпляров службы контейнера (с сетевым режимом `open`) не может превышать 4096 в пределах одной виртуальной сети. Для таких сценариев высокой плотности рекомендуется использовать сетевой режим `nat`.
>

## <a name="setting-up-open-networking-mode"></a>Настройка режима сети open

1. Настройте шаблон Azure Resource Manager, включив службу DNS и поставщик IP-адресов в разделе `fabricSettings`. 

    ```json
    "fabricSettings": [
                {
                    "name": "DnsService",
                    "parameters": [
                       {
                            "name": "IsEnabled",
                            "value": "true"
                      }
                    ]
                },
                {
                    "name": "Hosting",
                    "parameters": [
                      { 
                            "name": "IPProviderEnabled",
                            "value": "true"
                      }
                    ]
                },
                {
                    "name":  "Trace/Etw", 
                    "parameters": [
                    {
                            "name": "Level",
                            "value": "5"
                    }
                    ]
                },
                {
                    "name": "Setup",
                    "parameters": [
                    {
                            "name": "ContainerNetworkSetup",
                            "value": "true"
                    }
                    ]
                }
            ],
    ```

2. Настройте раздел с профилем сети, разрешив настройку нескольких IP-адресов на каждом узле кластера. В следующем примере для каждого узла в кластере Service Fabric на платформе Windows или Linux устанавливаются пять IP-адресов (следовательно, у вас может быть пять экземпляров службы, которые ожидают передачи данных через порт на каждом узле).

    ```json
    "variables": {
        "nicName": "NIC",
        "vmName": "vm",
        "virtualNetworkName": "VNet",
        "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',variables('virtualNetworkName'))]",
        "vmNodeType0Name": "[toLower(concat('NT1', variables('vmName')))]",
        "subnet0Name": "Subnet-0",
        "subnet0Prefix": "10.0.0.0/24",
        "subnet0Ref": "[concat(variables('vnetID'),'/subnets/',variables('subnet0Name'))]",
        "lbID0": "[resourceId('Microsoft.Network/loadBalancers',concat('LB','-', parameters('clusterName'),'-',variables('vmNodeType0Name')))]",
        "lbIPConfig0": "[concat(variables('lbID0'),'/frontendIPConfigurations/LoadBalancerIPConfig')]",
        "lbPoolID0": "[concat(variables('lbID0'),'/backendAddressPools/LoadBalancerBEAddressPool')]",
        "lbProbeID0": "[concat(variables('lbID0'),'/probes/FabricGatewayProbe')]",
        "lbHttpProbeID0": "[concat(variables('lbID0'),'/probes/FabricHttpGatewayProbe')]",
        "lbNatPoolID0": "[concat(variables('lbID0'),'/inboundNatPools/LoadBalancerBEAddressNatPool')]"
    }
    "networkProfile": {
                "networkInterfaceConfigurations": [
                  {
                    "name": "[concat(parameters('nicName'), '-0')]",
                    "properties": {
                      "ipConfigurations": [
                        {
                          "name": "[concat(parameters('nicName'),'-',0)]",
                          "properties": {
                            "primary": "true",
                            "loadBalancerBackendAddressPools": [
                              {
                                "id": "[variables('lbPoolID0')]"
                              }
                            ],
                            "loadBalancerInboundNatPools": [
                              {
                                "id": "[variables('lbNatPoolID0')]"
                              }
                            ],
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        },
                        {
                          "name": "[concat(parameters('nicName'),'-', 1)]",
                          "properties": {
                            "primary": "false",
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        },
                        {
                          "name": "[concat(parameters('nicName'),'-', 2)]",
                          "properties": {
                            "primary": "false",
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        },
                        {
                          "name": "[concat(parameters('nicName'),'-', 3)]",
                          "properties": {
                            "primary": "false",
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        },
                        {
                          "name": "[concat(parameters('nicName'),'-', 4)]",
                          "properties": {
                            "primary": "false",
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        },
                        {
                          "name": "[concat(parameters('nicName'),'-', 5)]",
                          "properties": {
                            "primary": "false",
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        }
                      ],
                      "primary": true
                    }
                  }
                ]
              }
   ```
 

3. Только для кластеров Windows можно настроить правило NSG, открывая порт UDP/53 для виртуальной сети со следующими значениями:

   | Приоритет |    Имя    |    Источник      |  Место назначения   |   служба    | Действие |
   |:--------:|:----------:|:--------------:|:--------------:|:------------:|:------:|
   |     2000 | Custom_Dns | Виртуальная сеть | Виртуальная сеть | DNS (UDP/53) | РАЗРЕШИТЬ  |


4. Укажите сетевой режим в манифесте приложения для каждой службы `<NetworkConfig NetworkType="open">`.  При использовании сетевого режима `open` служба получит выделенный IP-адрес. Если режим не указан, по умолчанию используется базовый режим `nat`. Таким образом, в следующем примере манифеста видно, что `NodeContainerServicePackage1` и `NodeContainerServicePackage2` могут ожидать передачи данных на одном и том же порту (обе службы прослушивают `Endpoint1`).

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <ApplicationManifest ApplicationTypeName="NodeJsApp" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
      <Description>Calculator Application</Description>
      <Parameters>
        <Parameter Name="ServiceInstanceCount" DefaultValue="3"></Parameter>
        <Parameter Name="MyCpuShares" DefaultValue="3"></Parameter>
      </Parameters>
      <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="NodeContainerServicePackage1" ServiceManifestVersion="1.0"/>
        <Policies>
          <ContainerHostPolicies CodePackageRef="NodeContainerService1.Code" Isolation="hyperv">
           <NetworkConfig NetworkType="open"/>
           <PortBinding ContainerPort="8905" EndpointRef="Endpoint1"/>
          </ContainerHostPolicies>
        </Policies>
      </ServiceManifestImport>
      <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="NodeContainerServicePackage2" ServiceManifestVersion="1.0"/>
        <Policies>
          <ContainerHostPolicies CodePackageRef="NodeContainerService2.Code" Isolation="default">
            <NetworkConfig NetworkType="open"/>
            <PortBinding ContainerPort="8910" EndpointRef="Endpoint1"/>
          </ContainerHostPolicies>
        </Policies>
      </ServiceManifestImport>
    </ApplicationManifest>
    ```
Вы можете комбинировать и сопоставлять разные сетевые режимы для использования со службами в рамках приложения для кластера Windows. Таким образом, некоторые службы будут работать в режиме `open`, а некоторые — в режиме `nat`. Если служба настроена для работы в режиме `nat`, прослушиваемый ею порт должен быть уникальным. Использование разных сетевых режимов для разных служб не поддерживается в кластерах Linux. 


## <a name="next-steps"></a>Дальнейшие действия
Из этой статьи вы узнали о сетевых режимах, поддерживаемых Service Fabric.  

* [Модель приложений Service Fabric](service-fabric-application-model.md)
* [Ресурсы манифеста службы Service Fabric](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-service-manifest-resources)
* [Развертывание контейнера Windows в Service Fabric на платформе Windows Server 2016](service-fabric-get-started-containers.md)
* [Развертывание контейнера Docker в Service Fabric на платформе Linux](service-fabric-get-started-containers-linux.md)
