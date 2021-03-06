---
title: Практическое руководство. Создание XML-литералов
ms.date: 07/20/2015
helpviewer_keywords:
- XML literals [Visual Basic], creating
ms.assetid: 573a6db5-b14d-4e42-b356-8cc7e2d77745
ms.openlocfilehash: 61b138c0851c747ed30eedc10cb882cc3b03c4d4
ms.sourcegitcommit: f8c270376ed905f6a8896ce0fe25b4f4b38ff498
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/04/2020
ms.locfileid: "84392614"
---
# <a name="how-to-create-xml-literals-visual-basic"></a>Практическое руководство. Создание XML-литералов (Visual Basic)
XML-документ, фрагмент или элемент можно создать непосредственно в коде с помощью XML-литерала. В примерах этого раздела показано, как создать XML-элемент с тремя дочерними элементами и как создать XML-документ.  
  
 Вы также можете использовать [!INCLUDE[sqltecxlinq](~/includes/sqltecxlinq-md.md)] API для создания [!INCLUDE[sqltecxlinq](~/includes/sqltecxlinq-md.md)] объектов. Дополнительные сведения см. в разделе <xref:System.Xml.Linq.XElement>.  
  
### <a name="to-create-an-xml-element"></a>Создание XML-элемента  
  
- Создайте встроенный XML-файл с помощью синтаксиса XML-литерала, который совпадает с фактическим синтаксисом XML.  
  
     [!code-vb[VbXMLSamples#5](~/samples/snippets/visualbasic/VS_Snippets_VBCSharp/VbXMLSamples/VB/XMLSamples2.vb#5)]  
  
     Выполните код. Выходные данные этого кода:  
  
     `<contact>`  
  
     `<name>Patrick Hines</name>`  
  
     `<phone type="home">206-555-0144</phone>`  
  
     `<phone type="work">425-555-0145</phone>`  
  
     `</contact>`  
  
### <a name="to-create-an-xml-document"></a>Создание XML-документа  
  
- Создайте встроенный XML-документ. Следующий код создает XML-документ с литеральным синтаксисом, объявлением XML, инструкцией по обработке, комментарием и элементом, содержащим другой элемент.  
  
     [!code-vb[VbXMLSamples#30](~/samples/snippets/visualbasic/VS_Snippets_VBCSharp/VbXMLSamples/VB/XMLSamples13.vb#30)]  
  
     Выполните код. Выходные данные этого кода:  
  
     `<?xml-stylesheet type="text/xsl" href="show_book.xsl"?>`  
  
     `<!-- Tests that the application works. -->`  
  
     `<books>`  
  
     `<book/>`  
  
     `</books>`  
  
## <a name="see-also"></a>См. также раздел

- [XML](index.md)
- [Создание XML в Visual Basic](creating-xml.md)
- [XML-литерал элемента](../../../language-reference/xml-literals/xml-element-literal.md)
- [XML-литерал документа](../../../language-reference/xml-literals/xml-document-literal.md)
