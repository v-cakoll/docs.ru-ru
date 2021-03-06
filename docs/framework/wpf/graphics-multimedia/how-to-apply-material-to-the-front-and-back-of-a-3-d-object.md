---
title: Как применить материал к передней и задней части 3D-объекта
ms.date: 03/30/2017
helpviewer_keywords:
- 3D objects [WPF], applying Material class
- Material class [WPF], applying to both sides of 3D object
- classes [WPF], Material
ms.assetid: d93c8ad6-4939-4d29-9544-4d16d98093c1
ms.openlocfilehash: 5c24879d97e7857fb07fcef4a9ba8efa901e4a39
ms.sourcegitcommit: 267d092663aba36b6b2ea853034470aea493bfae
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/21/2020
ms.locfileid: "80112145"
---
# <a name="how-to-apply-material-to-the-front-and-back-of-a-3d-object"></a>Как применить материал к передней и задней части 3D-объекта
В следующем примере показано, как применить <xref:System.Windows.Media.Media3D.Material> к передней и задней части 3D-объекта и оживить объект, чтобы показать обе стороны объекта. Свойство <xref:System.Windows.Media.Media3D.GeometryModel3D.Material%2A> <xref:System.Windows.Media.Media3D.GeometryModel3D> используется для нанесения <xref:System.Windows.Media.Brush> красного цвета на переднюю <xref:System.Windows.Media.Media3D.GeometryModel3D.BackMaterial%2A> сторону <xref:System.Windows.Media.Media3D.GeometryModel3D> объекта, а свойство используемого для нанесения синего <xref:System.Windows.Media.Brush> цвета на заднюю сторону объекта. Приведенный ниже код показывает применение материалов к объекту:  
  
 [!code-xaml[Animation3DGallery_snip#BackMaterialAnimationExampleInline1](~/samples/snippets/csharp/VS_Snippets_Wpf/Animation3DGallery_snip/CS/BackMaterialAnimationExample.xaml#backmaterialanimationexampleinline1)]  
  
## <a name="example"></a>Пример  
 Следующий код показывает весь образец.  
  
 [!code-xaml[Animation3DGallery_snip#BackMaterialAnimationExampleWholePage](~/samples/snippets/csharp/VS_Snippets_Wpf/Animation3DGallery_snip/CS/BackMaterialAnimationExample.xaml#backmaterialanimationexamplewholepage)]  
  
## <a name="see-also"></a>См. также раздел

- [Создание 3D-сцены](how-to-create-a-3-d-scene.md)
- [3D Графика Обзор](3-d-graphics-overview.md)
- [Оживите свойства материала в 3D-сцене](how-to-animate-material-properties-in-a-3-d-scene.md)
- [Применить эмиссионный материал к 3D-объекту](how-to-apply-emissive-material-to-a-3-d-object.md)
