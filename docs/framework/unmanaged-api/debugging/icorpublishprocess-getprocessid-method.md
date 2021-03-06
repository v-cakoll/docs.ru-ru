---
title: Метод ICorPublishProcess::GetProcessID
ms.date: 03/30/2017
api_name:
- ICorPublishProcess.GetProcessID
api_location:
- mscordbi.dll
api_type:
- COM
f1_keywords:
- ICorPublishProcess::GetProcessID
helpviewer_keywords:
- ICorPublishProcess::GetProcessID method [.NET Framework debugging]
- GetProcessID method [.NET Framework debugging]
ms.assetid: f31185e0-f01d-463a-b392-42163e39bfe9
topic_type:
- apiref
ms.openlocfilehash: 95a4ef3ab77b33c67c63be2c22647075f2f95ce8
ms.sourcegitcommit: 9a4488a3625866335e83a20da5e9c5286b1f034c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/15/2020
ms.locfileid: "83421115"
---
# <a name="icorpublishprocessgetprocessid-method"></a>Метод ICorPublishProcess::GetProcessID
Возвращает идентификатор операционной системы для этого процесса.  
  
## <a name="syntax"></a>Синтаксис  
  
```cpp  
HRESULT GetProcessID (  
    [out] unsigned   *pid  
);  
```  
  
## <a name="parameters"></a>Параметры  
 `pid`  
 заполняет Указатель на идентификатор процесса, представленного этим объектом [ICorPublishProcess](icorpublishprocess-interface.md) .  
  
## <a name="requirements"></a>Требования  
 **Платформы:** см. раздел [Требования к системе](../../get-started/system-requirements.md).  
  
 **Заголовок:** Корпуб. idl, Корпуб. h  
  
 **Библиотека:** CorGuids.lib  
  
 **.NET Framework версии:**[!INCLUDE[net_current_v10plus](../../../../includes/net-current-v10plus-md.md)]  
  
## <a name="see-also"></a>См. также статью

- [Интерфейс ICorPublishProcess](icorpublishprocess-interface.md)
