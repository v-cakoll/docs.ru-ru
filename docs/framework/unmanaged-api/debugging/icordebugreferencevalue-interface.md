---
title: Интерфейс ICorDebugReferenceValue
ms.date: 03/30/2017
api_name:
- ICorDebugReferenceValue
api_location:
- mscordbi.dll
api_type:
- COM
f1_keywords:
- ICorDebugReferenceValue
helpviewer_keywords:
- ICorDebugReferenceValue interface [.NET Framework debugging]
ms.assetid: 2040e2be-119a-4cfb-ae52-b0b6f052665c
topic_type:
- apiref
ms.openlocfilehash: 6c6ff428e378e973d8846674ffacdcd04b2dbdbc
ms.sourcegitcommit: d6bd7903d7d46698e9d89d3725f3bb4876891aa3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/13/2020
ms.locfileid: "83378349"
---
# <a name="icordebugreferencevalue-interface"></a>Интерфейс ICorDebugReferenceValue
Предоставляет методы, управляющие значением, которое является ссылкой на объект. (Т. е. Этот интерфейс предоставляет методы, управляющие указателем.) Этот интерфейс реализует "ICorDebugValue".  
  
## <a name="methods"></a>Методы  
  
|Метод|Описание|  
|------------|-----------------|  
|[Метод Dereference](icordebugreferencevalue-dereference-method.md)|Возвращает объект, на который указывает ссылка.|  
|[Метод DereferenceStrong](icordebugreferencevalue-dereferencestrong-method.md)|Не реализовано. Этот метод не следует вызывать.|  
|[Метод GetValue](icordebugreferencevalue-getvalue-method.md)|Возвращает текущий адрес памяти объекта, на который указывает ссылка.|  
|[Метод IsNull](icordebugreferencevalue-isnull-method.md)|Возвращает значение, указывающее, является ли `ICorDebugReferenceValue` значение значением NULL, в этом случае, не `ICorDebugReferenceValue` указывает на объект.|  
|[Метод SetValue](icordebugreferencevalue-setvalue-method.md)|Задает текущий адрес памяти. Это значит, что этот метод задает `ICorDebugReferenceValue` значение, которое указывает на объект.|  
  
## <a name="remarks"></a>Remarks  
 Среда CLR может выполнять сборку мусора для объектов при продолжении отлаживаемого процесса. Сборка мусора может перемещать объекты в памяти. Либо взаимодействуют `ICorDebugReferenceValue` со сборкой мусора, так что ее сведения обновляются после сборки мусора, или же она становится неявной до сборки мусора.  
  
 `ICorDebugReferenceValue`После продолжения отлаживаемого процесса объект может быть неявно недействительным. Производный "ICorDebugHandleValue" не является недействительным до тех пор, пока он не будет явно освобожден или открыт.  
  
> [!NOTE]
> Этот интерфейс не поддерживает удаленные вызовы между компьютерами или между процессами.  
  
## <a name="requirements"></a>Требования  
 **Платформы:** см. раздел [Требования к системе](../../get-started/system-requirements.md).  
  
 **Заголовок:** CorDebug.idl, CorDebug.h  
  
 **Библиотека:** CorGuids.lib  
  
 **.NET Framework версии:**[!INCLUDE[net_current_v10plus](../../../../includes/net-current-v10plus-md.md)]  
  
## <a name="see-also"></a>См. также

- [Интерфейсы отладки](debugging-interfaces.md)
