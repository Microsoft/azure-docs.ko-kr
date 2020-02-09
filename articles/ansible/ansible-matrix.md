---
title: Azure에 대한 Ansible 모듈 및 버전 매트릭스 | Microsoft Docs
description: Azure용 Ansible 모듈 및 버전 매트릭스
keywords: Ansible, 역할, 매트릭스, 버전, Azure, DevOps
ms.topic: reference
ms.date: 10/14/2019
ms.openlocfilehash: 54e27c7570ba1cdbce7355740181d68a2f3efbac
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2019
ms.locfileid: "74155978"
---
# <a name="ansible-module-and-version-matrix"></a>Ansible 모듈 및 버전 매트릭스

Ansible에는 Azure 리소스 프로비저닝 및 구성에 사용할 모듈 모음이 포함되어 있습니다. 이러한 리소스에는 가상 머신포함, 확장 집합, 네트워킹 서비스 및 컨테이너 서비스가 포함됩니다. 이 문서에는 Azure에 대한 다양 한 Ansible 모듈과 이러한 모듈이 제공 되는 Ansible 버전이 나열 되어 있습니다.

## <a name="ansible-modules-for-azure"></a>Azure용 Ansible 모듈

다음 모듈은 원격 호스트에서 직접 실행 하거나 플레이 북을 통해 실행할 수 있습니다.  

이러한 모듈은 Ansible 공식 릴리스와 다음 Microsoft 플레이 북 역할에서 사용할 수 있습니다.

> [!NOTE]
> Ansible 2.9부터 Ansible 명명 규칙을 준수 하기 위해 모든 * _facts 모듈을 * _info으로 바꾸었습니다. 이전 처럼 이름이 바뀐 모듈은 사용 중단 경고를 보지 않아도 연결 됩니다.

| Azure용 Ansible 모듈                   |  Ansible 2.4 |  Ansible 2.5 |  Ansible 2.6 | Ansible 2.7 | Ansible 2.8 | Ansible 2.9 | Ansible 역할 | 
|---------------------------------------------|--------------|--------------|-----------------------------|-------------------------------------|--------------|--------------|--------------|  
| **계산**                    |           |                          |                          |                            |           |           |           |
| azure_rm_availabilityset                   | 예          | 예                         | 예          | 예          | 예          | 예          | 예          |
| azure_rm_availabilityset_info              | 예          | 예                         | 예          | 예          | 예          | 예          | 예          |
| azure_rm_batchaccount                       | -            | -                           | -            | -            | -            | 예          | 예          |
| azure_rm_deployment                         | 예          | 예                         | 예          | 예          | 예          | 예          | 예          |
| azure_rm_deployment_info                   | -            | -                           | -            | -            | 예          | 예          | 예          |
| azure_rm_functionapp                        | 예          | 예                         | 예          | 예          | 예          | 예          | 예          |
| azure_rm_functionapp_info                  | 예          | 예                         | 예          | 예          | 예          | 예          | 예          |
| azure_rm_gallery                            | -            | -                           | -            | -            | -            | 예          | 예          |
| azure_rm_gallery_info                       | -            | -                           | -            | -            | -            | 예          | 예          |
| azure_rm_galleryimage                       | -            | -                           | -            | -            | -            | 예          | 예          |
| azure_rm_galleryimage_info                  | -            | -                           | -            | -            | -            | 예          | 예          |
| azure_rm_galleryimageversion                | -            | -                           | -            | -            | -            | 예          | 예          |
| azure_rm_galleryimageversion_info           | -            | -                           | -            | -            | -            | 예          | 예          |
| azure_rm_image                              | -            | 예                         | 예          | 예          | 예          | 예          | 예          |
| azure_rm_image_info                        | -            | -                           | -            | -            | 예          | 예          | 예          |
| azure_rm_resource                           | -            | -                           | 예          | 예          | 예          | 예          | 예          |
| azure_rm_resource_info                     | -            | -                           | 예          | 예          | 예          | 예          | 예          |
| azure_rm_resourcegroup                      | 예          | 예                         | 예          | 예          | 예          | 예          | 예          |
| azure_rm_resourcegroup_info                | 예          | 예                         | 예          | 예          | 예          | 예          | 예          |
| azure_rm_snapshot                           | -            | -                           | -            | -            | -            | 예          | 예          |
| azure_rm_virtualmachine                     | 예          | 예                         | 예          | 예          | 예          | 예          | 예          |
| azure_rm_virtualmachine_info               | -            | -                           | -            | 예          | 예          | 예          | 예          |
| azure_rm_virtualmachineextension            | 예          | 예                         | 예          | 예          | 예          | 예          | 예          |
| azure_rm_virtualmachineextension_info      | -            | -                           | -            | -            | 예          | 예          | 예          |
| azure_rm_virtualmachineimage_info          | 예          | 예                         | 예          | 예          | 예          | 예          | 예          |
| azure_rm_virtualmachinescaleset             | 예          | 예                         | 예          | 예          | 예          | 예          | 예          |
| azure_rm_virtualmachinescaleset_info       | 예          | 예                         | 예          | 예          | 예          | 예          | 예          |
| azure_rm_virtualmachinescalesetextension    | -            | -                           | -            | -            | 예          | 예          | 예          |
| azure_rm_virtualmachinescalesetextension_info | -            | -                        | -            | -            | 예          | 예          | 예          |
| azure_rm_virtualmachinescalesetinstance     | -            | -                           | -            | -            | 예          | 예          | 예          |
| azure_rm_virtualmachinescalesetinstance_info | -            | -                         | -            | -            | 예          | 예          | 예          |
| **네트워킹**                              |              |                             |              |              |              |              |              |
| azure_rm_appgateway                         | -            | -                           | -            | 예          | 예          | 예          | 예          |
| azure_rm_applicationsecuritygroup           | -            | -                           | -            | -            | 예          | 예          | 예          |
| azure_rm_applicationsecuritygroup_info     | -            | -                           | -            | -            | 예          | 예          | 예          |
| azure_rm_cdnendpoint                        | -            | -                         | -          | -            | 예          | 예          | 예          |
| azure_rm_cdnendpoint_info                  | -            | -                         | -          | -            | 예          | 예          | 예          |
| azure_rm_cdnprofile                         | -            | -                         | -          | -            | 예          | 예          | 예          |
| azure_rm_cdnprofile_info                   | -            | -                         | -          | -            | 예          | 예          | 예          |
| azure_rm_dnsrecordset                       | 예          | 예                         | 예          | 예          | 예          | 예          | 예          |
| azure_rm_dnsrecordset_info                 | 예          | 예                         | 예          | 예          | 예          | 예          | 예          |
| azure_rm_dnszone                            | 예          | 예                         | 예          | 예          | 예          | 예          | 예          |
| azure_rm_dnszone_info                      | 예          | 예                         | 예          | 예          | 예          | 예          | 예          |
| azure_rm_firewall                           | -            | -                           | -            | -            | -            | 예          | 예          |
| azure_rm_firewall_info                      | -            | -                           | -            | -            | -            | 예          | 예          |
| azure_rm_loadbalancer                       | 예          | 예                         | 예          | 예          | 예          | 예          | 예          |
| azure_rm_loadbalancer_info                 | 예          | 예                         | 예          | 예          | 예          | 예          | 예          |
| azure_rm_networkinterface                   | 예          | 예                         | 예          | 예          | 예          | 예          | 예          |
| azure_rm_networkinterface_info             | 예          | 예                         | 예          | 예          | 예          | 예          | 예          |
| azure_rm_publicipaddress                    | 예          | 예                         | 예          | 예          | 예          | 예          | 예          |
| azure_rm_publicipaddress_info              | 예          | 예                         | 예          | 예          | 예          | 예          | 예          |
| azure_rm_route                              | -            | -                           | -            | 예          | 예          | 예          | 예          |
| azure_rm_routetable                         | -            | -                           | -            | 예          | 예          | 예          | 예          |
| azure_rm_routetable_info                   | -            | -                           | -            | 예          | 예          | 예          | 예          |
| azure_rm_securitygroup                      | 예          | 예                         | 예          | 예          | 예          | 예          | 예          |
| azure_rm_securitygroup_info                 | -            | -                           | -            | -            | -            | 예          | 예          |
| azure_rm_subnet                             | 예          | 예                         | 예          | 예          | 예          | 예          | 예          |
| azure_rm_subnet_info                       | -            | -                           | -            | -            | 예          | 예          | 예          |
| azure_rm_trafficmanagerendpoint             | -            | -                         | -          | 예          | 예          | 예          | 예          |
| azure_rm_trafficmanagerendpoint_info       | -            | -                         | -          | 예          | 예          | 예          | 예          |
| azure_rm_trafficmanagerprofile              | -            | -                         | -          | 예          | 예          | 예          | 예          |
| azure_rm_trafficmanagerprofile_info        | -            | -                         | -          | 예          | 예          | 예          | 예          |
| azure_rm_virtualnetwork                     | 예          | 예                         | 예          | 예          | 예          | 예          | 예          |
| azure_rm_virtualnetwork_info               | 예          | 예                         | 예          | 예          | 예          | 예          | 예          |
| azure_rm_virtualnetworkgateway              | -            | -                         | -          | -            | 예          | 예          | 예          |
| azure_rm_virtualnetworkpeering              | -            | -                         | -          | -            | 예          | 예          | 예          |
| azure_rm_virtualnetworkpeering_info         | -            | -                         | -          | -            | -            | 예          | 예          |
| **저장소**                    |           |                          |                          |                            |           |           |         |
| azure_rm_manageddisk                        | 예          | 예                         | 예          | 예          | 예          | 예          | 예          |
| azure_rm_manageddisk_info                  | 예          | 예                         | 예          | 예          | 예          | 예          | 예          |
| azure_rm_storageaccount                     | 예          | 예                         | 예          | 예          | 예          | 예          | 예          |
| azure_rm_storageaccount_info               | 예          | 예                         | 예          | 예          | 예          | 예          | 예          |
| azure_rm_storageblob                        | 예          | 예                         | 예          | 예          | 예          | 예          | 예          |
| **Web**                    |           |                          |                          |                             |           |           |           |
| azure_rm_appserviceplan                     | -            | -                         | -          | 예          | 예          | 예          | 예          |
| azure_rm_appserviceplan_info               | -            | -                         | -          | 예          | 예          | 예          | 예          |
| azure_rm_webapp                             | -            | -                         | -          | 예          | 예          | 예          | 예          |
| azure_rm_webapp_info                       | -            | -                         | -          | 예          | 예          | 예          | 예          |
| azure_rm_webappslot                         | -            | -                         | -          | -            | 예          | 예          | 예          |
| **컨테이너**                    |           |                          |                          |                            |           |           |          |
| azure_rm_acs                                | 예          | 예                         | 예          | 예          | 예          | 예          | 예          |
| azure_rm_aks                                | -            | -                           | 예          | 예          | 예          | 예          | 예          |
| azure_rm_aks_info                          | -            | -                           | 예          | 예          | 예          | 예          | 예          |
| azure_rm_aksversion_info                   | -            | -                           | -            | -            | 예          | 예          | 예          |
| azure_rm_containerinstance                  | -            | 예                         | 예          | 예          | 예          | 예          | 예          |
| azure_rm_containerinstance_info            | -            | -                           | -            | -            | 예          | 예          | 예          |
| azure_rm_containerregistry                  | -            | 예                         | 예          | 예          | 예          | 예          | 예          |
| azure_rm_containerregistry_info            | -            | -                           | -            | 예          | 예          | 예          | 예          |
| azure_rm_containerregistryreplication       | -            | -                           | -            | -            | -          | -          | 예          |
| azure_rm_containerregistryreplication_info  | -            | -                           | -            | -            | -          | -          | 예          |
| azure_rm_containerregistrywebhook           | -            | -                           | -            | -            | -          | -          | 예          |
| azure_rm_containerregistrywebhook_info      | -            | -                           | -            | -            | -          | -          | 예          |
| **데이터베이스**                    |           |                          |                          |                             |           |           |          |
| azure_rm_cosmosdbaccount                    | -            | -                           | -            | -            | 예          | 예          | 예          |
| azure_rm_cosmosdbaccount_info              | -            | -                           | -            | -            | 예          | 예          | 예          |
| azure_rm_mariadbconfiguration               | -            | -                           | -            | -            | 예          | 예          | 예          |
| azure_rm_mariadbconfiguration_info         | -            | -                           | -            | -            | 예          | 예          | 예          |
| azure_rm_mariadbdatabase                    | -            | -                           | -            | -            | 예          | 예          | 예          |
| azure_rm_mariadbdatabase_info              | -            | -                           | -            | -            | 예          | 예          | 예          |
| azure_rm_mariadbfirewallrule                | -            | -                           | -            | -            | 예          | 예          | 예          |
| azure_rm_mariadbfirewallrule_info          | -            | -                           | -            | -            | 예          | 예          | 예          |
| azure_rm_mariadbserver                      | -            | -                           | -            | -            | 예          | 예          | 예          |
| azure_rm_mariadbserver_info                | -            | -                           | -            | -            | 예          | 예          | 예          |
| azure_rm_mysqlconfiguration                 | -            | -                           | -            | -            | 예          | 예          | 예          |
| azure_rm_mysqlconfiguration_info           | -            | -                           | -            | -            | 예          | 예          | 예          |
| azure_rm_mysqldatabase                      | -            | 예                         | 예          | 예          | 예          | 예          | 예          |
| azure_rm_mysqldatabase_info                | -            | -                           | -            | 예          | 예          | 예          | 예          |
| azure_rm_mysqlfirewallrule                  | -            | -                           | -            | -            | 예          | 예          | 예          |
| azure_rm_mysqlfirewallrule_info            | -            | -                           | -            | -            | 예          | 예          | 예          |
| azure_rm_mysqlserver                        | -            | 예                         | 예          | 예          | 예          | 예          | 예          |
| azure_rm_mysqlserver_info                  | -            | -                           | -            | 예          | 예          | 예          | 예          |
| azure_rm_postgresqlconfiguration            | -            | -                           | -            | -            | 예          | 예          | 예          |
| azure_rm_postgresqlconfiguration_info      | -            | -                           | -            | -            | 예          | 예          | 예          |
| azure_rm_postgresqldatabase                 | -            | 예                         | 예          | 예          | 예          | 예          | 예          |
| azure_rm_postgresqldatabase_info           | -            | -                           | -            | 예          | 예          | 예          | 예          |
| azure_rm_postgresqlfirewallrule             | -            | -                           | -            | -            | 예          | 예          | 예          |
| azure_rm_postgresqlfirewallrule_info       | -            | -                           | -            | -            | 예          | 예          | 예          |
| azure_rm_postgresqlserver                   | -            | 예                         | 예          | 예          | 예          | 예          | 예          |
| azure_rm_postgresqlserver_info             | -            | -                           | -            | 예          | 예          | 예          | 예          |
| azure_rm_rediscache                         | -            | -                           | -            | -            | 예          | 예          | 예          |
| azure_rm_rediscache_info                   | -            | -                           | -            | -            | 예          | 예          | 예          |
| azure_rm_rediscachefirewallrule             | -            | -                           | -            | -            | 예          | 예          | 예          |
| azure_rm_sqldatabase                        | -            | 예                         | 예          | 예          | 예          | 예          | 예          |
| azure_rm_sqldatabase_info                  | -            | -                           | -            | -            | 예          | 예          | 예          |
| azure_rm_sqlelasticpool                    | -            | -                           | -            | -            | -          | -          | 예          |
| azure_rm_sqlelasticpool_info               | -            | -                           | -            | -            | -          | -          | 예          |
| azure_rm_sqlfirewallrule                    | -            | -                           | -            | 예          | 예          | 예          | 예          |
| azure_rm_sqlfirewallrule_info              | -            | -                           | -            | -            | 예          | 예          | 예          |
| azure_rm_sqlserver                          | -            | 예                         | 예          | 예          | 예          | 예          | 예          |
| azure_rm_sqlserver_info                    | -            | 예                         | 예          | 예          | 예          | 예          | 예          |
| **분석**                    |           |                          |                          |                             |           |           |          |
| azure_rm_hdinsightcluster                   | -            | -                           | -            | -            | 예          | 예          | 예          |
| azure_rm_hdinsightcluster_info              | -            | -                           | -            | -            | -            | 예          | 예          |
| **통합**                    |           |                          |                          |                             |           |           |          |
| azure_rm_servicebus                         | -            | -                           | -            | -            | 예          | 예          | 예          |
| azure_rm_servicebus_info                   | -            | -                           | -            | -            | 예          | 예          | 예          |
| azure_rm_servicebusqueue                    | -            | -                           | -            | -            | 예          | 예          | 예          |
| azure_rm_servicebussaspolicy                | -            | -                           | -            | -            | 예          | 예          | 예          |
| azure_rm_servicebustopic                    | -            | -                           | -            | -            | 예          | 예          | 예          |
| azure_rm_servicebustopicsubscription        | -            | -                           | -            | -            | 예          | 예          | 예          |
| **보안**                    |           |                          |                          |                             |           |           |           |
| azure_rm_keyvault                           | -            | 예                         | 예          | 예          | 예          | 예          | 예          |
| azure_rm_keyvault_info                     | -            | -                           | -              | -          | 예          | 예          | 예          |
| azure_rm_keyvaultkey                        | -            | 예                         | 예          | 예          | 예          | 예          | 예          |
| azure_rm_keyvaultkey_info                   | -            | -                           | -            | -            | -            | 예          | 예          |
| azure_rm_keyvaultsecret                     | -            | 예                         | 예          | 예          | 예          | 예          | 예          |
| azure_rm_roleassignment                     | -            | -                           | -            | -            | 예          | 예          | 예          |
| azure_rm_roleassignment_info               | -            | -                           | -            | -            | 예          | 예          | 예          |
| azure_rm_roledefinition                     | -            | -                           | -            | -            | 예          | 예          | 예          |
| azure_rm_roledefinition_info               | -            | -                           | -            | -            | 예          | 예          | 예          |
| **DevOps**               |           |                          |                          |                             |           |           |                  |
| azure_rm_devtestlab                         | -            | -                           | -            | -            | 예          | 예          | 예          |
| azure_rm_devtestlab_info                   | -            | -                           | -            | -            | 예          | 예          | 예          |
| azure_rm_devtestlabarmtemplate_info        | -            | -                           | -            | -            | 예          | 예          | 예          |
| azure_rm_devtestlabartifact_info           | -            | -                           | -            | -            | 예          | 예          | 예          |
| azure_rm_devtestlabartifactsource           | -            | -                           | -            | -            | 예          | 예          | 예          |
| azure_rm_devtestlabartifactsource_info     | -            | -                           | -            | -            | 예          | 예          | 예          |
| azure_rm_devtestlabcustomimage              | -            | -                           | -            | -            | 예          | 예          | 예          |
| azure_rm_devtestlabcustomimage_info         | -            | -                           | -            | -            | -            | 예          | 예          |
| azure_rm_devtestlabenvironment              | -            | -                           | -            | -            | 예          | 예          | 예          |
| azure_rm_devtestlabenvironment_info         | -            | -                           | -            | -            | -            | 예          | 예          |
| azure_rm_devtestlabpolicy                   | -            | -                           | -            | -            | 예          | 예          | 예          |
| azure_rm_devtestlabpolicy_info              | -            | -                           | -            | -            | -            | 예          | 예          |
| azure_rm_devtestlabschedule                 | -            | -                           | -            | -            | 예          | 예          | 예          |
| azure_rm_devtestlabschedule_info            | -            | -                           | -            | -            | -            | 예          | 예          |
| azure_rm_devtestlabvirtualmachine           | -            | -                           | -            | -            | 예          | 예          | 예          |
| azure_rm_devtestlabvirtualmachine_info | -            | -                           | -            | -            | 예          | 예          | 예          |
| azure_rm_devtestlabvirtualnetwork           | -            | -                           | -            | -            | 예          | 예          | 예          |
| azure_rm_devtestlabvirtualnetwork_info     | -            | -                           | -            | -            | 예          | 예          | 예          |
| **Azure Monitor**                           |              |                           |            |              |                 |           |              |
| azure_rm_autoscale                          | -            | -                         | -          | 예          | 예          | 예          | 예          |
| azure_rm_autoscale_info                    | -            | -                         | -          | 예          | 예          | 예          | 예          |
| azure_rm_loganalyticsworkspace              | -            | -                           | -            | -            | 예          | 예          | 예          |
| azure_rm_loganalyticsworkspace_info        | -            | -                           | -            | -            | 예          | 예          | 예          |
| azure_rm_monitorlogprofile                  | -            | -                           | -            | -            | -            | 예          | 예          |
| **관리 및 거 버 넌 스**     |              |                           |            |            |            |            |              |
| azure_rm_automationaccount        | -            | -                         | -          | -          | -          | 예        | 예          |
| azure_rm_automationaccount_info   | -            | -                         | -          | -          | -          | 예        | 예          |
| azure_rm_lock                     | -            | -                         | -          | -          | -          | 예        | 예          |
| azure_rm_lock_info                | -            | -                         | -          | -          | -          | 예        | 예          |
| **사물 인터넷**            |              |                           |            |            |            |            |              |
| azure_rm_iotdevice                | -            | -                         | -          | -          | -          | 예        | 예          |
| azure_rm_iotdevice_info           | -            | -                         | -          | -          | -          | 예        | 예          |
| azure_rm_iotdevicemodule          | -            | -                         | -          | -          | -          | 예        | 예          |
| azure_rm_iothub_info              | -            | -                         | -          | -          | -          | 예        | 예          |
| azure_rm_iothub_info              | -            | -                         | -          | -          | -          | 예        | 예          |
| azure_rm_iothubconsumergroup      | -            | -                         | -          | -          | -          | 예        | 예          |

## <a name="introduction-to-playbook-role-for-azure"></a>Azure에 대한 플레이북 역할 소개

[Azure_preview_module 플레이 북 역할](https://galaxy.ansible.com/Azure/azure_preview_modules/) 은 모든 최신 azure 모듈을 포함 합니다. 공식 Ansible 릴리스보다 업데이트 및 버그 수정이 더 적절하게 수행됩니다. Azure 리소스 프로 비전을 위해 Ansible를 사용 하는 경우 `azure_preview_module` 플레이 북 역할을 설치 하는 것이 좋습니다.

`azure_preview_module` 플레이 북 역할은 3 주마다 릴리스됩니다.

## <a name="next-steps"></a>다음 단계

플레이 북 역할에 대한 자세한 내용은 [재사용 가능한 플레이 북 만들기](https://docs.ansible.com/ansible/latest/playbooks_reuse.html)를 참조 하세요. 