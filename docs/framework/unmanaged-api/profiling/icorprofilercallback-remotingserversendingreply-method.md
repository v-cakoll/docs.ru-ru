---
title: Метод ICorProfilerCallback::RemotingServerSendingReply
ms.date: 03/30/2017
api_name:
- ICorProfilerCallback.RemotingServerSendingReply
api_location:
- mscorwks.dll
api_type:
- COM
f1_keywords:
- ICorProfilerCallback::RemotingServerSendingReply
helpviewer_keywords:
- RemotingServerSendingReply method [.NET Framework profiling]
- ICorProfilerCallback::RemotingServerSendingReply method [.NET Framework profiling]
ms.assetid: dfe84a19-2e03-4be2-8b25-f02bad38e4a9
topic_type:
- apiref
ms.openlocfilehash: bf59d4e418223fd177bc5e19b173674b78e1f2ba
ms.sourcegitcommit: da21fc5a8cce1e028575acf31974681a1bc5aeed
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/08/2020
ms.locfileid: "84499926"
---
# <a name="icorprofilercallbackremotingserversendingreply-method"></a>Метод ICorProfilerCallback::RemotingServerSendingReply
Уведомляет профилировщик о том, что процесс завершил обработку запроса удаленного вызова метода и собирается передать ответ через канал.  
  
## <a name="syntax"></a>Синтаксис  
  
```cpp  
HRESULT RemotingServerSendingReply(  
    [in] GUID *pCookie,  
    [in] BOOL fIsAsync);  
```  
  
## <a name="parameters"></a>Параметры  
 `pCookie`  
 окне Указатель на идентификатор GUID, который будет соответствовать значению, указанному в параметре [ICorProfilerCallback:: ремотингклиентрецеивингрепли](icorprofilercallback-remotingclientreceivingreply-method.md) в следующих условиях:  
  
- Файлы Cookie GUID удаленного взаимодействия активны.  
  
- Канал проходит успешную передачу сообщения.  
  
- Файлы Cookie GUID активны в процессе на стороне клиента.  
  
 Это позволяет легко связывать вызовы удаленного взаимодействия и создавать логические стеки вызовов.  
  
 `fIsAsync`  
 окне Значение, равное, `true` Если вызов является асинхронным; в противном случае — `false` .  
  
## <a name="requirements"></a>Требования  
 **Платформы:** см. раздел [Требования к системе](../../get-started/system-requirements.md).  
  
 **Заголовок:** CorProf.idl, CorProf.h  
  
 **Библиотека:** CorGuids.lib  
  
 **.NET Framework версии:**[!INCLUDE[net_current_v20plus](../../../../includes/net-current-v20plus-md.md)]  
  
## <a name="see-also"></a>См. также

- [Интерфейс ICorProfilerCallback](icorprofilercallback-interface.md)
