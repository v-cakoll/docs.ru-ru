---
title: Практическое руководство. Вычисление числовых значений
ms.date: 07/20/2015
helpviewer_keywords:
- operator precedence
- operators [Visual Basic]
- expressions [Visual Basic], numeric
- calculations [Visual Basic], numeric expressions
- precedence [Visual Basic], of operators
- Visual Basic code, operators
- Visual Basic code, expressions
- numeric expressions
ms.assetid: ba6bf43d-bd96-49b8-b1de-4a7797551372
ms.openlocfilehash: 94b02693f308dcfcfa6983f2750a26d9d419f7be
ms.sourcegitcommit: f8c270376ed905f6a8896ce0fe25b4f4b38ff498
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/04/2020
ms.locfileid: "84403463"
---
# <a name="how-to-calculate-numeric-values-visual-basic"></a>Практическое руководство. Вычисление числовых значений (Visual Basic)
Числовые значения можно вычислить с помощью числовых выражений. *Числовое выражение* представляет собой выражение, которое содержит литералы, константы и переменные, представляющие числовые значения, и операторы, действующие на эти значения.  
  
## <a name="calculating-numeric-values"></a>Вычисление числовых значений  
  
#### <a name="to-calculate-a-numeric-value"></a>Вычисление числового значения  
  
- Объедините один или несколько числовых литералов, констант и переменных в числовое выражение. В следующем примере показаны некоторые допустимые числовые выражения.  
  
     `93.217`  
  
     `System.Math.PI`  
  
     `counter`  
  
     `4 * (67 + i)`  
  
     Первые три строки показывают литерал, константу и переменную. Каждый из них формирует допустимое числовое выражение самим собой. В последней строке показано сочетание переменной с двумя литералами.  
  
     Обратите внимание, что числовое выражение не формирует полную инструкцию Visual Basic сам по себе. Выражение необходимо использовать как часть полной инструкции.  
  
#### <a name="to-store-a-numeric-value"></a>Сохранение числового значения  
  
- Оператор присваивания можно использовать для назначения переменной значения, представленного числовым выражением, как показано в следующем примере.  
  
     [!code-vb[VbVbalrOperators#82](~/samples/snippets/visualbasic/VS_Snippets_VBCSharp/VbVbalrOperators/VB/Class1.vb#82)]  
  
     В предыдущем примере значение выражения в правой части оператора равенства ( `=` ) присваивается переменной `j` в левой части оператора, поэтому `j` результатом вычисления будет 276.  
  
     Дополнительные сведения см. в разделе [Инструкции](../../../language-reference/statements/index.md).  
  
## <a name="multiple-operators"></a>Несколько операторов  
 Если числовое выражение содержит более одного оператора, порядок их вычисления определяется правилами приоритета операторов. Чтобы переопределить правила приоритета операторов, заключите выражения в круглые скобки, как в приведенном выше примере. выражения, заключенные в кавычки, оцениваются первыми.  
  
#### <a name="to-override-normal-operator-precedence"></a>Переопределение приоритета обычного оператора  
  
- Используйте круглые скобки, чтобы заключать операции, которые необходимо выполнить первыми. В следующем примере показаны два разных результата с одинаковыми операндами и операторами.  
  
     [!code-vb[VbVbalrOperators#83](~/samples/snippets/visualbasic/VS_Snippets_VBCSharp/VbVbalrOperators/VB/Class1.vb#83)]  
  
     В предыдущем примере вычисление для `j` выполняет оператор сложения ( `+` ) сначала, так как круглые скобки `(67 + i)` обопределяют нормальный приоритет, а присваиваемое значение `j` — 276 (4 раза 69). Вычисление для `k` выполняет операторы в нормальном порядке ( `*` до `+` ), а присваиваемое ему значение `k` равно 270 (268 плюс 2).  
  
     Дополнительные сведения см. [в разделе приоритет операторов в Visual Basic](../../../language-reference/operators/operator-precedence.md).  
  
## <a name="see-also"></a>См. также раздел

- [Операторы и выражения](index.md)
- [Сравнения значений](value-comparisons.md)
- [Операторы](../../../language-reference/statements/index.md)
- [Порядок применения операторов в Visual Basic](../../../language-reference/operators/operator-precedence.md)
- [Арифметические операторы](../../../language-reference/operators/arithmetic-operators.md)
- [Эффективное сочетание операторов](efficient-combination-of-operators.md)
