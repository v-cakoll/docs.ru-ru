---
title: Практическое руководство. Проверка совпадения двух объектов
ms.date: 07/20/2015
helpviewer_keywords:
- variables [Visual Basic], reference
- Is operator [Visual Basic], comparing objects
- reference variables [Visual Basic]
- variables [Visual Basic], referring to same object
- objects [Visual Basic], variables referring to same
- Visual Basic code, operators
ms.assetid: f760e828-8704-4256-bc2d-c22a4c93b524
ms.openlocfilehash: b215225dbc2d8c0d2bdfe2206e5d4a4f1faa6d0c
ms.sourcegitcommit: f8c270376ed905f6a8896ce0fe25b4f4b38ff498
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/04/2020
ms.locfileid: "84403425"
---
# <a name="how-to-test-whether-two-objects-are-the-same-visual-basic"></a>Практическое руководство. Проверка совпадения двух объектов (Visual Basic)
При наличии двух переменных, ссылающихся на объекты, можно использовать либо `Is` `IsNot` оператор OR, либо и то, и другое, чтобы определить, ссылаются ли они на один и тот же экземпляр.  
  
### <a name="to-test-whether-two-objects-are-the-same"></a>Проверка того, совпадают ли два объекта  
  
- Используйте [оператор is](../../../language-reference/operators/is-operator.md) или [IsNot](../../../language-reference/operators/isnot-operator.md) с двумя переменными в качестве операндов.  
  
     [!code-vb[VbVbalrOperators#69](~/samples/snippets/visualbasic/VS_Snippets_VBCSharp/VbVbalrOperators/VB/Class1.vb#69)]  
  
 Может потребоваться выполнить определенное действие в зависимости от того, ссылаются ли два объекта на один и тот же экземпляр. Предыдущий пример сравнивает элемент управления с `c` активным элементом управления в форме `f` . Если активный элемент управления отсутствует или имеется, но он не является одним и тем же экземпляром элемента управления `c` , то `If` выполнение инструкции завершается ошибкой и процедура возвращается без дальнейшей обработки.  
  
 Независимо от того, используется ли `Is` `IsNot` для вас персональное удобство. Один из них может быть проще читать, чем другой в данном выражении.  
  
## <a name="see-also"></a>См. также раздел

- [Comparison Operators in Visual Basic](comparison-operators.md)
