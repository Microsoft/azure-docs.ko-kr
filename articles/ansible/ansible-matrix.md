---
title: Azure 용 Ansible 모듈 및 버전 매트릭스 | Microsoft Docs
description: Azure용 Ansible 모듈 및 버전 매트릭스
keywords: Ansible, 역할, 매트릭스, 버전, Azure, DevOps
ms.topic: reference
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/22/2019
ms.openlocfilehash: 3f5bda36368ee1fe1f37527422c6072c3ffda177
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63763318"
---
# <a name="ansible-module-and-version-matrix"></a>Ansible 모듈 및 버전 매트릭스

Ansible 프로 비전 및 Azure 리소스 구성에서 사용할 모듈의 제품군이 포함 됩니다. 이러한 리소스는 virtual machines 포함, 확장 집합, 네트워킹 서비스 및 컨테이너 서비스입니다. 이 문서에서는 Azure 및 제공 하는 Ansible 버전에 대 한 다양 한 Ansible 모듈을 나열 합니다.

## <a name="ansible-modules-for-azure"></a>Azure용 Ansible 모듈

원격 호스트에서 직접 또는 플레이 북을 통해 다음 모듈을 실행할 수 있습니다.

이러한 모듈은 Ansible의 공식 릴리스 및 다음 Microsoft 플레이 북 역할에서 사용할 수 있습니다.

| Azure용 Ansible 모듈                   |  Ansible 2.4 |  Ansible 2.5 |  Ansible 2.6 | Ansible 2.7 | Ansible 2.8 | Ansible 역할 | 
|---------------------------------------------|--------------|--------------|-----------------------------|-------------------------------------|--------------|--------------| 
| **Compute**                    |           |                          |                          |                            |           |           |
| azure_rm_availabilityset                    | 예          | 예                         | 예          | 예          | 예          | 예          |
| azure_rm_availabilityset_facts              | 예          | 예                         | 예          | 예          | 예          | 예          |
| azure_rm_deployment                         | 예          | 예                         | 예          | 예          | 예          | 예          |
| azure_rm_deployment_facts                   | -            | -                           | -            | -            | 예          | 예          |
| azure_rm_functionapp                        | 예          | 예                         | 예          | 예          | 예          | 예          |
| azure_rm_functionapp_facts                  | 예          | 예                         | 예          | 예          | 예          | 예          |
| azure_rm_image                              | -            | 예                         | 예          | 예          | 예          | 예          |
| azure_rm_image_facts                        | -            | -                           | -            | -            | 예          | 예          |
| azure_rm_resource                           | -            | -                           | 예          | 예          | 예          | 예          |
| azure_rm_resource_facts                     | -            | -                           | 예          | 예          | 예          | 예          |
| azure_rm_resourcegroup                      | 예          | 예                         | 예          | 예          | 예          | 예          |
| azure_rm_resourcegroup_facts                | 예          | 예                         | 예          | 예          | 예          | 예          |
| azure_rm_virtualmachine                     | 예          | 예                         | 예          | 예          | 예          | 예          |
| azure_rm_virtualmachine_facts               | -            | -                           | -            | 예          | 예          | 예          |
| azure_rm_virtualmachineextension           | 예          | 예                         | 예          | 예          | 예          | 예          |
| azure_rm_virtualmachineextension_facts      | -            | -                           | -            | -            | 예          | 예          |
| azure_rm_virtualmachineimage_facts          | 예          | 예                         | 예          | 예          | 예          | 예          |
| azure_rm_virtualmachinescaleset            | 예          | 예                         | 예          | 예          | 예          | 예          |
| azure_rm_virtualmachinescaleset_facts      | 예          | 예                         | 예          | 예          | 예          | 예          |
| azure_rm_virtualmachinescalesetextension    | -            | -                           | -            | -            | 예          | 예          |
| azure_rm_virtualmachinescalesetextension_facts | -            | -                        | -            | -            | 예          | 예          |
| azure_rm_virtualmachinescalesetinstance     | -            | -                           | -            | -            | 예          | 예          |
| azure_rm_virtualmachinescalesetinstance_facts | -            | -                         | -            | -            | 예          | 예          |
| **네트워킹**                              |              |                             |              |              |              |              |
| azure_rm_appgateway                         | -            | -                           | -            | 예          | 예          | 예          |
| azure_rm_appgwroute                         | -            | -                           | -            | -            | 예          | 예          |
| azure_rm_appgwroute                         | -            | -                           | -            | -            | 예          | 예          |
| azure_rm_appgwroute_facts                   | -            | -                           | -            | -            | 예          | 예          |
| azure_rm_appgwroutetable                    | -            | -                           | -            | -            | 예          | 예          |
| azure_rm_appgwroutetable_facts              | -            | -                           | -            | -            | 예          | 예          |
| azure_rm_applicationsecuritygroup           | -            | -                           | -            | -            | 예          | 예          |
| azure_rm_applicationsecuritygroup_facts     | -            | -                           | -            | -            | 예          | 예          |
| azure_rm_cdnendpoint                        | -            | -                         | -          | -            | 예          | 예          |
| azure_rm_cdnendpoint_facts                  | -            | -                         | -          | -            | 예          | 예          |
| azure_rm_cdnprofile                         | -            | -                         | -          | -            | 예          | 예          |
| azure_rm_cdnprofile_facts                   | -            | -                         | -          | -            | 예          | 예          |
| azure_rm_dnsrecordset                       | 예          | 예                         | 예          | 예          | 예          | 예          |
| azure_rm_dnsrecordset_facts                 | 예          | 예                         | 예          | 예          | 예          | 예          |
| azure_rm_dnszone                            | 예          | 예                         | 예          | 예          | 예          | 예          |
| azure_rm_dnszone_facts                      | 예          | 예                         | 예          | 예          | 예          | 예          |
| azure_rm_loadbalancer                       | 예          | 예                         | 예          | 예          | 예          | 예          |
| azure_rm_loadbalancer_facts                 | 예          | 예                         | 예          | 예          | 예          | 예          |
| azure_rm_networkinterface                   | 예          | 예                         | 예          | 예          | 예          | 예          |
| azure_rm_networkinterface_facts             | 예          | 예                         | 예          | 예          | 예          | 예          |
| azure_rm_publicipaddress                    | 예          | 예                         | 예          | 예          | 예          | 예          |
| azure_rm_publicipaddress_facts              | 예          | 예                         | 예          | 예          | 예          | 예          |
| azure_rm_route                              | -            | -                           | -            | 예          | 예          | 예          |
| azure_rm_routetable                         | -            | -                           | -            | 예          | 예          | 예          |
| azure_rm_routetable_facts                   | -            | -                           | -            | 예          | 예          | 예          |
| azure_rm_securitygroup                      | 예          | 예                         | 예          | 예          | 예          | 예          |
| azure_rm_subnet                             | 예          | 예                         | 예          | 예          | 예          | 예          |
| azure_rm_subnet_facts                       | -            | -                           | -            | -            | 예          | 예          |
| azure_rm_trafficmanagerendpoint             | -            | -                         | -          | 예          | 예          | 예          |
| azure_rm_trafficmanagerendpoint_facts       | -            | -                         | -          | 예          | 예          | 예          |
| azure_rm_trafficmanagerprofile              | -            | -                         | -          | 예          | 예          | 예          |
| azure_rm_trafficmanagerprofile_facts        | -            | -                         | -          | 예          | 예          | 예          |
| azure_rm_virtualnetwork                     | 예          | 예                         | 예          | 예          | 예          | 예          |
| azure_rm_virtualnetwork_facts               | 예          | 예                         | 예          | 예          | 예          | 예          |
| azure_rm_virtualnetworkpeering              | -            | -                         | -          | -            | 예          | 예          |
| **Storage**                    |           |                          |                          |                            |           |           |
| azure_rm_manageddisk                        | 예          | 예                         | 예          | 예          | 예          | 예          |
| azure_rm_manageddisk_facts                  | 예          | 예                         | 예          | 예          | 예          | 예          |
| azure_rm_storageaccount                     | 예          | 예                         | 예          | 예          | 예          | 예          |
| azure_rm_storageaccount_facts               | 예          | 예                         | 예          | 예          | 예          | 예          |
| azure_rm_storageblob                        | 예          | 예                         | 예          | 예          | 예          | 예          |
| **웹**                    |           |                          |                          |                             |           |           |
| azure_rm_appserviceplan                     | -            | -                         | -          | 예          | 예          | 예          |
| azure_rm_appserviceplan_facts               | -            | -                         | -          | 예          | 예          | 예          |
| azure_rm_webapp                             | -            | -                         | -          | 예          | 예          | 예          |
| azure_rm_webapp_facts                       | -            | -                         | -          | 예          | 예          | 예          |
| azure_rm_webappslot                         | -            | -                         | -          | -            | 예          | 예          |
| **컨테이너**                    |           |                          |                          |                            |           |           |
| azure_rm_acs                                | 예          | 예                         | 예          | 예          | 예          | 예          |
| azure_rm_aks                                | -            | -                           | 예          | 예          | 예          | 예          |
| azure_rm_aks_facts                          | -            | -                           | 예          | 예          | 예          | 예          |
| azure_rm_aksversion_facts                   | -            | -                           | -            | -            | 예          | 예          |
| azure_rm_containerinstance                  | -            | 예                         | 예          | 예          | 예          | 예          |
| azure_rm_containerinstance_facts            | -            | -                           | -            | -            | 예          | 예          |
| azure_rm_containerregistry                  | -            | 예                         | 예          | 예          | 예          | 예          |
| azure_rm_containerregistry_facts            | -            | -                           | -            | 예          | 예          | 예          |
| azure_rm_containerregistryreplication       | -            | -                           | -            | -            | 예          | 예          |
| azure_rm_containerregistryreplication_facts | -            | -                           | -            | -            | 예          | 예          |
| azure_rm_containerregistrywebhook           | -            | -                           | -            | -            | 예          | 예          |
| azure_rm_containerregistrywebhook_facts     | -            | -                           | -            | -            | 예          | 예          |
| **데이터베이스**                    |           |                          |                          |                             |           |           |
| azure_rm_cosmosdbaccount                    | -            | -                           | -            | -            | 예          | 예          |
| azure_rm_cosmosdbaccount_facts              | -            | -                           | -            | -            | 예          | 예          |
| azure_rm_mariadbconfiguration               | -            | -                           | -            | -            | 예          | 예          |
| azure_rm_mariadbconfiguration_facts         | -            | -                           | -            | -            | 예          | 예          |
| azure_rm_mariadbdatabase                    | -            | -                           | -            | -            | 예          | 예          |
| azure_rm_mariadbdatabase_facts              | -            | -                           | -            | -            | 예          | 예          |
| azure_rm_mariadbfirewallrule                | -            | -                           | -            | -            | 예          | 예          |
| azure_rm_mariadbfirewallrule_facts          | -            | -                           | -            | -            | 예          | 예          |
| azure_rm_mariadbserver                      | -            | -                           | -            | -            | 예          | 예          |
| azure_rm_mariadbserver_facts                | -            | -                           | -            | -            | 예          | 예          |
| azure_rm_mysqlconfiguration                 | -            | -                           | -            | -            | 예          | 예          |
| azure_rm_mysqlconfiguration_facts           | -            | -                           | -            | -            | 예          | 예          |
| azure_rm_mysqldatabase                      | -            | 예                         | 예          | 예          | 예          | 예          |
| azure_rm_mysqldatabase_facts                | -            | -                           | -            | 예          | 예          | 예          |
| azure_rm_mysqlfirewallrule                  | -            | -                           | -            | -            | 예          | 예          |
| azure_rm_mysqlfirewallrule_facts            | -            | -                           | -            | -            | 예          | 예          |
| azure_rm_mysqlserver                        | -            | 예                         | 예          | 예          | 예          | 예          |
| azure_rm_mysqlserver_facts                  | -            | -                           | -            | 예          | 예          | 예          |
| azure_rm_postgresqlconfiguration            | -            | -                           | -            | -            | 예          | 예          |
| azure_rm_postgresqlconfiguration_facts      | -            | -                           | -            | -            | 예          | 예          |
| azure_rm_postgresqldatabase                 | -            | 예                         | 예          | 예          | 예          | 예          |
| azure_rm_postgresqldatabase_facts           | -            | -                           | -            | 예          | 예          | 예          |
| azure_rm_postgresqlfirewallrule             | -            | -                           | -            | -            | 예          | 예          |
| azure_rm_postgresqlfirewallrule_facts       | -            | -                           | -            | -            | 예          | 예          |
| azure_rm_postgresqlserver                   | -            | 예                         | 예          | 예          | 예          | 예          |
| azure_rm_postgresqlserver_facts             | -            | -                           | -            | 예          | 예          | 예          |
| azure_rm_rediscache                         | -            | -                           | -            | -            | 예          | 예          |
| azure_rm_rediscache_facts                   | -            | -                           | -            | -            | 예          | 예          |
| azure_rm_rediscachefirewallrule             | -            | -                           | -            | -            | 예          | 예          |
| azure_rm_sqldatabase                        | -            | 예                         | 예          | 예          | 예          | 예          |
| azure_rm_sqldatabase_facts                  | -            | -                           | -            | -            | 예          | 예          |
| azure_rm_sqlelasticpool                     | -            | -                           | -            | -            | 예          | 예          |
| azure_rm_sqlelasticpool_facts               | -            | -                           | -            | -            | 예          | 예          |
| azure_rm_sqlfirewallrule                    | -            | -                           | -            | 예          | 예          | 예          |
| azure_rm_sqlfirewallrule_facts              | -            | -                           | -            | -            | 예          | 예          |
| azure_rm_sqlserver                          | -            | 예                         | 예          | 예          | 예          | 예          |
| azure_rm_sqlserver_facts                    | -            | 예                         | 예          | 예          | 예          | 예          |
| **분석**                    |           |                          |                          |                             |           |           |
| azure_rm_hdinsightcluster                   | -            | -                           | -            | -            | 예          | 예          |
| **통합**                    |           |                          |                          |                             |           |           |
| azure_rm_servicebus                         | -            | -                           | -            | -            | 예          | 예          |
| azure_rm_servicebus_facts                   | -            | -                           | -            | -            | 예          | 예          |
| azure_rm_servicebusqueue                    | -            | -                           | -            | -            | 예          | 예          |
| azure_rm_servicebussaspolicy                | -            | -                           | -            | -            | 예          | 예          |
| azure_rm_servicebustopic                    | -            | -                           | -            | -            | 예          | 예          |
| azure_rm_servicebustopicsubscription        | -            | -                           | -            | -            | 예          | 예          |
| **보안**                    |           |                          |                          |                             |           |           |
| azure_rm_keyvault                           | -            | 예                         | 예          | 예          | 예          | 예          |
| azure_rm_keyvault_facts                     | -            | -                           | -              | -          | 예          | 예          |
| azure_rm_keyvaultkey                        | -            | 예                         | 예          | 예          | 예          | 예          |
| azure_rm_keyvaultsecret                     | -            | 예                         | 예          | 예          | 예          | 예          |
| azure_rm_roleassignment                     | -            | -                           | -            | -            | 예          | 예          |
| azure_rm_roleassignment_facts               | -            | -                           | -            | -            | 예          | 예          |
| azure_rm_roledefinition                     | -            | -                           | -            | -            | 예          | 예          |
| azure_rm_roledefinition_facts               | -            | -                           | -            | -            | 예          | 예          |
| **DevOps**               |           |                          |                          |                             |           |           |
| azure_rm_devtestlab                         | -            | -                           | -            | -            | 예          | 예          |
| azure_rm_devtestlab_facts                   | -            | -                           | -            | -            | 예          | 예          |
| azure_rm_devtestlabarmtemplate_facts        | -            | -                           | -            | -            | 예          | 예          |
| azure_rm_devtestlabartifact_facts           | -            | -                           | -            | -            | 예          | 예          |
| azure_rm_devtestlabartifactsource           | -            | -                           | -            | -            | 예          | 예          |
| azure_rm_devtestlabartifactsource_facts     | -            | -                           | -            | -            | 예          | 예          |
| azure_rm_devtestlabcustomimage              | -            | -                           | -            | -            | 예          | 예          |
| azure_rm_devtestlabenvironment              | -            | -                           | -            | -            | 예          | 예          |
| azure_rm_devtestlabpolicy                   | -            | -                           | -            | -            | 예          | 예          |
| azure_rm_devtestlabschedule                 | -            | -                           | -            | -            | 예          | 예          |
| azure_rm_devtestlabvirtualmachine           | -            | -                           | -            | -            | 예          | 예          |
| azure_rm_devtestlabvirtualmachine_facts | -            | -                           | -            | -            | 예          | 예          |
| azure_rm_devtestlabvirtualnetwork           | -            | -                           | -            | -            | 예          | 예          |
| azure_rm_devtestlabvirtualnetwork_facts     | -            | -                           | -            | -            | 예          | 예          |
| **Azure Monitor**          |           |                          |                          |                             |           |           |
| azure_rm_autoscale                  | -            | -                         | -          | 예          | 예          | 예          |
| azure_rm_autoscale_facts            | -            | -                         | -          | 예          | 예          | 예          |
| azure_rm_loganalyticsworkspace              | -            | -                           | -            | -            | 예          | 예          |
| azure_rm_loganalyticsworkspace_facts        | -            | -                           | -            | -            | 예          | 예          |

## <a name="introduction-to-playbook-role-for-azure"></a>Azure에 대한 플레이북 역할 소개

합니다 [azure_preview_module 플레이 북 역할](https://galaxy.ansible.com/Azure/azure_preview_modules/) 모든 최신 Azure 모듈을 포함 합니다. 공식 Ansible 릴리스보다 업데이트 및 버그 수정이 더 적절하게 수행됩니다. Ansible을 사용 하 여 목적으로 프로 비전 하는 Azure 리소스에 대 한 경우 설치 하는 것이 좋습니다는 `azure_preview_module` 플레이 북 역할입니다.

`azure_preview_module` 플레이 북 역할은 3 주마다 릴리스됩니다.

## <a name="next-steps"></a>다음 단계

플레이 북 역할에 대 한 자세한 내용은 참조 하세요. [재사용 가능한 플레이 북 만들기](https://docs.ansible.com/ansible/latest/playbooks_reuse.html)합니다. 