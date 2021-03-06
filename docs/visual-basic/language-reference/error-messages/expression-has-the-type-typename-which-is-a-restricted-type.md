---
title: Выражение имеет тип <typename> который является ограниченным и не может использоваться для доступа к членам, унаследованным от Object или ValueType
ms.date: 07/20/2015
f1_keywords:
- bc31393
- vbc31393
helpviewer_keywords:
- BC31393
ms.assetid: 2963cf3f-c527-4aa7-b67c-ee80b6d23186
ms.openlocfilehash: 0eb30488312cc7519f39a6b819aea15489f2f70a
ms.sourcegitcommit: f8c270376ed905f6a8896ce0fe25b4f4b38ff498
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/04/2020
ms.locfileid: "84409524"
---
# <a name="expression-has-the-type-typename-which-is-a-restricted-type-and-cannot-be-used-to-access-members-inherited-from-object-or-valuetype"></a>Выражение имеет тип \<typename> который является ограниченным и не может использоваться для доступа к членам, унаследованным от Object или ValueType
Выражение принимает тип, который не может быть упакован средой CLR, но обращается к члену, для которого требуется упаковка-преобразование.  
  
 *Упаковкой-преобразованием* называется обработка, необходимая для преобразования типа в `Object` или, в некоторых случаях, в <xref:System.ValueType>. Среда CLR не может быть Box для определенных типов структуры, например <xref:System.ArgIterator> , <xref:System.RuntimeArgumentHandle> и <xref:System.TypedReference> .  
  
 Это выражение пытается использовать ограниченный тип для вызова метода, унаследованного от <xref:System.Object> или <xref:System.ValueType> , например <xref:System.Object.GetHashCode%2A> или <xref:System.Object.ToString%2A> . Для доступа к этому методу Visual Basic предпринималась попытка неявного преобразования упаковки, которое вызывает эту ошибку.  
  
 **Идентификатор ошибки:** BC31393  
  
## <a name="to-correct-this-error"></a>Исправление ошибки  
  
1. Найдите выражение, которое оценивается в указанный тип.  
  
2. Находит часть инструкции, которая пытается вызвать метод, наследуемый от <xref:System.Object> или <xref:System.ValueType> .  
  
3. Перепишите инструкцию, чтобы избежать вызова метода.  
  
## <a name="see-also"></a>См. также раздел

- [Явные и неявные преобразования](../../programming-guide/language-features/data-types/implicit-and-explicit-conversions.md)
