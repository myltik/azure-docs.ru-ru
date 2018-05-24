---
title: Настройка сетевых режимов для службы контейнеров Azure Service Fabric | Документация Майкрософт
description: Узнайте, как настроить разные сетевые режимы, которые поддерживаются в Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: ''
ms.assetid: d552c8cd-67d1-45e8-91dc-871853f44fc6
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: subramar
ms.openlocfilehash: f831c046bcf8f633841f9dc4a0fce6d1e419e6c2
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/16/2018
---
# <a name="service-fabric-container-networking-modes"></a>Сетевые режимы контейнеров Service Fabric

Кластер Azure Service Fabric для служб контейнеров по умолчанию поддерживает сетевой режим **NAT**. Если используется режим NAT и подключения к одному порту прослушивают несколько служб контейнеров, могут возникнуть ошибки развертывания. Чтобы несколько служб контейнеров можно было разместить на одном порту, Service Fabric поддерживает **открытый** сетевой режим (в версии 5.7 и более поздних). В открытом режиме каждая служба контейнеров динамически получает внутренний IP-адрес, что позволяет нескольким службам прослушивать один и тот же порт.  

Если в манифесте службы указана одна служба контейнеров со статической конечной точкой, вы можете создавать и удалять службы в открытом сетевом режиме без ошибок развертывания. При статическом сопоставлении портов один и тот же файл docker-compose.yml можно использовать для создания нескольких служб.

Когда служба контейнеров перезапускается или перемещается на другой узел в кластере, ее IP-адрес изменяется. По этой причине мы не рекомендуем использовать динамически назначаемый IP-адрес для обнаружения служб контейнеров. Для обнаружения служб следует использовать только службу именования Service Fabric или службу DNS. 

>[!WARNING]
>Azure позволяет использовать для каждой виртуальной сети не более 4096 IP-адресов. Общее число узлов и экземпляров службы контейнеров (в открытом сетевом режиме) не может превышать 4096 в пределах одной виртуальной сети. Для сценариев высокой плотности мы рекомендуем применять сетевой режим NAT.
>

## <a name="set-up-open-networking-mode"></a>Настройка открытого сетевого режима

1. Подготовьте шаблон Azure Resource Manager. В разделе **fabricSettings** включите службу DNS и поставщика IP-адресов: 

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

2. Настройте раздел с профилем сети, разрешив настройку нескольких IP-адресов на каждом узле кластера. Следующий пример устанавливает пять IP-адресов на каждом узле для кластера Service Fabric на Windows или Linux. Это означает, что на каждом узле могут существовать по пять экземпляров службы, прослушивающих один порт.

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
 
3. Только для кластеров Windows: настройте правило группы безопасности сети (NSG) Azure, которое открывает порт UDP/53 для виртуальной сети, со следующими значениями:

   |Параметр |Значение | |
   | --- | --- | --- |
   |Приоритет |2000 | |
   |ИМЯ |Custom_Dns  | |
   |Источник |Виртуальная сеть | |
   |Место назначения | Виртуальная сеть | |
   |Service | DNS (UDP/53) | |
   |Действие | РАЗРЕШИТЬ  | |
   | | |

4. Укажите сетевой режим в манифесте приложения для каждой службы: `<NetworkConfig NetworkType="Open">`. При использовании **открытого** сетевого режима служба получит выделенный IP-адрес. Если режим не указан, по умолчанию служба использует режим **NAT**. В следующем примере манифеста видно, что службы `NodeContainerServicePackage1` и `NodeContainerServicePackage2` могут ожидать передачи данных на одном и том же порту (обе службы прослушивают `Endpoint1`). Если установлен открытый режим сети, вы не сможете задать конфигурации `PortBinding`.

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
           <NetworkConfig NetworkType="Open"/>
          </ContainerHostPolicies>
        </Policies>
      </ServiceManifestImport>
      <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="NodeContainerServicePackage2" ServiceManifestVersion="1.0"/>
        <Policies>
          <ContainerHostPolicies CodePackageRef="NodeContainerService2.Code" Isolation="default">
            <NetworkConfig NetworkType="Open"/>
          </ContainerHostPolicies>
        </Policies>
      </ServiceManifestImport>
    </ApplicationManifest>
    ```

    Вы можете комбинировать и сопоставлять разные сетевые режимы для использования со службами в рамках приложения для кластера Windows. Можно настроить систему так, что некоторые службы будут работать в открытом режиме, а другие — в режиме NAT. Если служба настроена для режима NAT, она может прослушивать только уникальный порт.

    >[!NOTE]
    >В кластерах Linux использование разных сетевых режимов для разных служб не поддерживается. 
    >

5. При выборе **открытого** режима определение **конечной точки** манифеста службы должно явно указывать на пакет кода, соответствующий этой конечной точке, даже если пакет службы содержит только один пакет кода. 
   
   ```xml
   <Resources>
     <Endpoints>
       <Endpoint Name="ServiceEndpoint" Protocol="http" Port="80" CodePackageRef="Code"/>
     </Endpoints>
   </Resources>
   ```

## <a name="next-steps"></a>Дополнительная информация
* [Моделирование приложения в Service Fabric](service-fabric-application-model.md)
* [Указание ресурсов в манифесте службы](https://docs.microsoft.com/azure/service-fabric/service-fabric-service-manifest-resources) для Service Fabric
* [Развертывание контейнера Windows в Service Fabric на платформе Windows Server 2016](service-fabric-get-started-containers.md)
* [Развертывание контейнера Docker в Service Fabric на платформе Linux](service-fabric-get-started-containers-linux.md)
