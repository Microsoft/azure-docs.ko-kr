---
title: VM (가상 컴퓨터) 이미지를 Azure Marketplace의 관리 디스크 저장소로 이동 하 고 있습니다.
description: 새로운 marketplace 기능 및 기능에 대 한 더 빠르고 안정적인 저장소 및 지원을 제공 하기 위해 marketplace VM 이미지를 관리 되는 디스크 저장소로 이동 하 고 있습니다.
author: MaggiePucciEvans
manager: evansma
ms.author: evansma
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/22/2019
ms.openlocfilehash: 683b35661a41325cfd5baa877acdb0e37529bb94
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77198737"
---
# <a name="were-moving-virtual-machine-vm-images-on-azure-marketplace-to-managed-disk-storage"></a>Azure Marketplace에서 VM (가상 컴퓨터) 이미지를 관리 되는 디스크 저장소로 이동 하 고 있습니다.

새로운 marketplace 기능 및 기능에 대 한 더 빠르고 안정적인 저장소 및 지원을 제공 하기 위해 marketplace VM 이미지를 관리 되는 디스크 저장소로 이동 하 고 있습니다.

2020 년 1 월 2 일부 터 VM 이미지를 관리 되는 디스크 저장소로 이동 합니다. 첫 번째 단계에서는 이전 90 일 동안 새 배포 또는 Vm을 실행 하지 않는 이미지만 이동 합니다. 이미지를 이동 하기 전에 게시자가 이동할 이미지 및 이동 시기를 알려 주는 전자 메일을 보냅니다.

게시자 또는 소비자는 어떤 조치도 취할 필요가 없으며 사용자는 영향을 받지 않습니다. Marketplace 제품은 계속 사용 가능한 상태로 유지 되며, 이동 하는 동안 및 그 후에 고객은 이러한 이미지에서 관리 되는 Vm을 배포할 수 있습니다.

질문이 있는 경우 [문의해](https://support.microsoft.com/supportforbusiness/productselection?sapId=48734891-ee9a-5d77-bf29-82bf8d8111ff)주시기 바랍니다.

## <a name="faqs"></a>FAQ

### <a name="would-the-users-of-my-vm-images-experience-an-outage"></a>내 VM 이미지의 사용자가 중단을 경험 하나요?

VM 이미지의 사용자에 게는 가동 중단이 발생 하지 않습니다. 

첫 번째 단계에서는 실행 중인 vm이 없는 VM 이미지만 이동 합니다. 이러한 이미지에 대 한 사용자가 없으므로 영향을 주지 않습니다. 이후 단계에 대해서도 사용자에 게 영향을 주지 않습니다.

### <a name="how-long-does-it-take-for-the-process-to-complete"></a>프로세스를 완료 하는 데 소요 되는 시간

마이그레이션을 완료 하는 데 최대 24 시간이 걸릴 수 있습니다.

### <a name="do-i-need-to-take-any-action"></a>작업을 수행 해야 하나요?

아니요. 게시자 또는 소비자는 어떤 조치도 취할 필요가 없습니다.

### <a name="do-i-have-to-update-my-system-to-call-the-cloud-portal-apis-in-a-different-way-after-they-are-moved-to-managed-disk-storage"></a>클라우드 포털 Api를 관리 디스크 저장소로 이동한 후 다른 방식으로 호출 하도록 시스템을 업데이트 해야 하나요?

아니요. 기존 API 호출은 계속 작동할 것입니다.

### <a name="would-all-my-vm-images-be-moved-to-managed-disk-at-the-same-time"></a>모든 내 VM 이미지를 동시에 관리 디스크로 이동 하나요?

모든 VM 이미지를 같은 날에 이동할 예정입니다. 이동 된 후 사용자에 게 알려줍니다.

### <a name="can-i-request-to-schedule-the-move-of-my-vm-images-to-a-later-time"></a>내 VM 이미지를 나중에 이동 하도록 예약 하도록 요청할 수 있나요?

예약 된 날짜에 이미지를 이동 하는 것이 좋습니다. 그러나 문제가 있는 경우 microsoft에 문의 하 여 이동 일정을 다시 조정 하세요.

### <a name="can-i-publish-updates-to-my-vm-images-during-the-move"></a>이동 하는 동안 내 VM 이미지에 업데이트를 게시할 수 있나요?

이동 하는 동안에는 VM 이미지를 업데이트할 수 없습니다.

### <a name="will-the-publishing-process-change-after-my-vm-image-is-moved-to-managed-disk"></a>내 VM 이미지를 관리 디스크로 이동한 후에 게시 프로세스가 변경 됩니까?

아니요. 게시 프로세스는 동일 하 게 유지 됩니다. 

### <a name="can-the-publisher-move-their-offers-to-managed-disk"></a>게시자가 해당 제품을 관리 디스크로 이동할 수 있나요?

아니요. 게시자는 해당 제품을 관리 디스크로 이동할 수 없습니다. 대기 해야 하며 해당 이미지가 자동으로 이동 됩니다. 변경을 수행 하기 전에 게시자에 게 알림을 보냅니다.
