---
title: Практическое руководство. Разработка шрифтов и их семейств
ms.date: 03/30/2017
dev_langs:
- csharp
- vb
helpviewer_keywords:
- font families [Windows Forms], constructing
- fonts [Windows Forms], constructing
ms.assetid: d3a4a223-9492-4b54-9afd-db1c31c3cefd
ms.openlocfilehash: 2d609525858c7a8ff77c0b86900b4fc7d6b4e39a
ms.sourcegitcommit: b1cfd260928d464d91e20121f9bdba7611c94d71
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2019
ms.locfileid: "67505942"
---
# <a name="how-to-construct-font-families-and-fonts"></a>Практическое руководство. Разработка шрифтов и их семейств
GDI + группирует шрифты с тем же шрифт, но различные стили в семейства шрифтов. Например семейство шрифтов Arial содержит следующие шрифты:  
  
- Arial обычного  
  
- Arial полужирным шрифтом  
  
- Arial курсив  
  
- Arial полужирный курсив  
  
 GDI + используются четыре стиля для формирования семейств шрифтов: обычный, полужирный, курсив и полужирный курсив. Прилагательные, такие как *сузить* и *округленное* не учитываются стили; вместо этого они являются частью имя семейства. Например Arial Narrow является семейством шрифтов со следующими членами:  
  
- Arial обычного узкий  
  
- Полужирным шрифтом Arial узкая  
  
- Arial узкий курсив  
  
- Arial узкий полужирный курсив  
  
 Прежде чем можно рисовать текст с использованием GDI +, необходимо создать <xref:System.Drawing.FontFamily> объекта и <xref:System.Drawing.Font> объекта. <xref:System.Drawing.FontFamily> Объект определяет гарнитуру (например, Arial) и <xref:System.Drawing.Font> объект определяет размер, стиль и единиц.  
  
## <a name="example"></a>Пример  
 В следующем примере создается обычного начертания шрифта Arial с размером 16 пикселей. В следующем коде, первый аргумент, переданный <xref:System.Drawing.Font.%23ctor%2A> конструктор является <xref:System.Drawing.FontFamily> объекта. Второй аргумент задает размер шрифта, измеряемая в единицах, идентифицируемый четвертый аргумент. Третий параметр указывает стиль.  
  
 <xref:System.Drawing.GraphicsUnit.Pixel> является членом <xref:System.Drawing.GraphicsUnit> перечисления, и <xref:System.Drawing.FontStyle.Regular> является членом <xref:System.Drawing.FontStyle> перечисления.  
  
 [!code-csharp[System.Drawing.FontsAndText#61](~/samples/snippets/csharp/VS_Snippets_Winforms/System.Drawing.FontsAndText/CS/Class1.cs#61)]
 [!code-vb[System.Drawing.FontsAndText#61](~/samples/snippets/visualbasic/VS_Snippets_Winforms/System.Drawing.FontsAndText/VB/Class1.vb#61)]  
  
## <a name="compiling-the-code"></a>Компиляция кода  
 Предыдущий пример предназначен для работы с Windows Forms и требует <xref:System.Windows.Forms.PaintEventArgs> `e`, который является параметром <xref:System.Windows.Forms.PaintEventHandler>.  
  
## <a name="see-also"></a>См. также

- [Работами со шрифтами и текстом](using-fonts-and-text.md)
- [Объекты Graphics и Drawing в Windows Forms](graphics-and-drawing-in-windows-forms.md)
