---
title: Метод DacpGetModuleAddress::Request
ms.date: 01/16/2019
api.name:
- DacpGetModuleAddress::Request Method
api.location:
- mscordacwks.dll
api.type:
- COM
f1.keywords:
- DacpGetModuleAddress::Request Method
helpviewer.keywords:
- DacpGetModuleAddress::Request Method [.NET Framework debugging]
topic_type:
- apiref
author: cshung
ms.author: andrewau
ms.openlocfilehash: 1755526636bed6d78663112e4c2ad5ab7c3f731c
ms.sourcegitcommit: d9c7ac5d06735a01c1fafe34efe9486734841a72
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/06/2020
ms.locfileid: "82860839"
---
# <a name="dacpgetmoduleaddressrequest-method"></a>Метод DacpGetModuleAddress::Request

Выполняет запрос на заполнение структуры из заданной структуры среды выполнения.

[!INCLUDE[debugging-api-recommended-note](../../../../includes/debugging-api-recommended-note.md)]

## <a name="syntax"></a>Синтаксис

```cpp
HRESULT Request(
    [in] IXCLRDataModule* pDataModule
);
```

## <a name="parameters"></a>Параметры

`pDataModule`\
окне Указатель на модуль начальных данных.

## <a name="remarks"></a>Примечания

Эта структура находится внутри среды выполнения и не предоставляется через все файлы заголовков или библиотек. Чтобы использовать его, самый простой способ — имитировать реализацию:

- Возвращает значение, полученное от вызова `Request` метода для `IXCLRDataModule*` параметра со следующими параметрами:`((uint32) 0xf0000000, 0, 0, (uint32) sizeof(*this), (uint8*) this)`

## <a name="requirements"></a>Требования

**Платформы:** См. [требования к системе](../../get-started/system-requirements.md)\
**Заголовок:** None
**Библиотека:** None
**.NET Framework версии:**[!INCLUDE[net_current_v47plus](../../../../includes/net-current-v47plus.md)]

## <a name="see-also"></a>См. также раздел

- [Отладка](index.md)
- [Структура Дакпжетмодулеаддресс](dacpgetmoduleaddress-structure.md)
