---
title: Метод ICorDebugThread4::HadUnhandledException
ms.date: 03/30/2017
api_name:
- ICorDebugThread4.HadUnhandledException Method
api_location:
- mscordbi.dll
api_type:
- COM
f1_keywords:
- ICorDebugThread4::HadUnhandledException
helpviewer_keywords:
- ICorDebugThread4::HadUnhandledException method [.NET Framework debugging]
- HadUnhandledException method [.NET Framework debugging]
ms.assetid: 05558daa-39e2-4c38-aeaf-e2aec4a09468
topic_type:
- apiref
ms.openlocfilehash: 4d954057c519263da49f8aaeeeef6ab9402b6956
ms.sourcegitcommit: d6bd7903d7d46698e9d89d3725f3bb4876891aa3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/13/2020
ms.locfileid: "83378376"
---
# <a name="icordebugthread4hadunhandledexception-method"></a>Метод ICorDebugThread4::HadUnhandledException
Указывает, имел ли когда-либо поток необработанное исключение.  
  
## <a name="syntax"></a>Синтаксис  
  
```cpp  
HRESULT GetBlockingObjects (  
    [out] ICorDebugBlockingObjectEnum **ppBlockingObjectEnum  
    );  
```  
  
## <a name="parameters"></a>Параметры  
 `ppBlockingObjectEnum`  
 заполняет Указатель на адрес упорядоченного перечисления структур [CorDebugBlockingObject](cordebugblockingobject-structure.md) .  
  
## <a name="return-value"></a>Возвращаемое значение  
 Этот метод возвращает следующие конкретные результаты HRESULT, а также ошибки HRESULT, которые указывают на сбой метода.  
  
|HRESULT|Описание|  
|-------------|-----------------|  
|S_OK|В потоке возникло необработанное исключение с момента его создания.|  
|S_FALSE|В потоке никогда не было необработанного исключения.|  
  
## <a name="remarks"></a>Remarks  
 Этот метод указывает, имел ли у потока когда-либо необработанное исключение. К моменту запуска обратного вызова необработанного исключения или инициализации собственного JIT-присоединения этот метод гарантированно возвращает S_OK. Нет никакой гарантии, что метод [ICorDebugThread. жеткуррентексцептион](icordebugthread-getcurrentexception-method.md) будет возвращать необработанное исключение; Тем не менее, если процесс еще не был продолжен после получения обратного вызова необработанного исключения или собственного JIT-присоединения. Кроме того, во время выполнения собственного JIT-присоединения можно (хотя маловероятно) иметь более одного потока с необработанным исключением. В этом случае нет способа определить, какое исключение активировало JIT-присоединение.  
  
## <a name="requirements"></a>Требования  
 **Платформы:** см. раздел [Требования к системе](../../get-started/system-requirements.md).  
  
 **Заголовок:** CorDebug.idl, CorDebug.h  
  
 **Библиотека:** CorGuids.lib  
  
 **.NET Framework версии:**[!INCLUDE[net_current_v40plus](../../../../includes/net-current-v40plus-md.md)]  
  
## <a name="see-also"></a>См. также

- [Интерфейс ICorDebugThread4](icordebugthread4-interface.md)
- [Интерфейсы отладки](debugging-interfaces.md)
- [Отладка](index.md)
