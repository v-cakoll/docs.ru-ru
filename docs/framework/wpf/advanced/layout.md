---
title: Макет
description: Узнайте, как и когда выполняются вычисления макета в системе макета Windows Presentation Foundation.
ms.date: 03/30/2017
dev_langs:
- csharp
- vb
helpviewer_keywords:
- WPF layout system [WPF]
- controls [WPF], layout system
- layout system [WPF]
ms.assetid: 3eecdced-3623-403a-a077-7595453a9221
ms.openlocfilehash: 0db3f2a6cbabc610362435d64de3fc970f01a73c
ms.sourcegitcommit: 87cfeb69226fef01acb17c56c86f978f4f4a13db
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/24/2020
ms.locfileid: "87164769"
---
# <a name="layout"></a>Макет

В этом разделе описывается система макета Windows Presentation Foundation (WPF). Понимание того, как и когда происходят вычисления макета, важно для создания пользовательских интерфейсов в WPF.

Этот раздел состоит из следующих подразделов.

- [Ограничивающие прямоугольники элемента](#LayoutSystem_BoundingBox)

- [Система макета](#LayoutSystem_Overview)

- [Измерение и расположение дочерних элементов](#LayoutSystem_Measure_Arrange)

- [Элементы панели и пользовательские расширения функциональности макета](#LayoutSystem_PanelsCustom)

- [Вопросы производительности макета](#LayoutSystem_Performance)

- [Субпиксельная отрисовка и округление макета](#LayoutSystem_LayoutRounding)

- [Что дальше](#LayoutSystem_whatsnext)

<a name="LayoutSystem_BoundingBox"></a>

## <a name="element-bounding-boxes"></a>Ограничивающие прямоугольники элемента

При обдумывании макета в WPF важно понимать ограничивающий прямоугольник, который окружает все элементы. Каждый, <xref:System.Windows.FrameworkElement> который используется системой макета, можно рассматривать как прямоугольник, размещенный в макете. <xref:System.Windows.Controls.Primitives.LayoutInformation>Класс возвращает границы распределения макета элемента или области. Размер прямоугольника определяется путем вычисления доступного пространства на экране, размера любых ограничений, свойств макета (таких как поля и заполнение) и индивидуального поведения родительского <xref:System.Windows.Controls.Panel> элемента. Обрабатывая эти данные, система макета может вычислить положение всех дочерних элементов определенного объекта <xref:System.Windows.Controls.Panel> . Важно помнить, что характеристики размера, определенные для родительского элемента, например <xref:System.Windows.Controls.Border> , влияют на его дочерние элементы.

На рисунке ниже представлен простой макет.

![Снимок экрана, на котором показана типичная сетка без наложенного ограничивающего прямоугольника.](./media/layout/grid-no-bounding-box-superimpose.png)

Этот макет можно получить с помощью приведенного ниже кода [!INCLUDE[TLA2#tla_xaml](../../../../includes/tla2sharptla-xaml-md.md)].

[!code-xaml[LayoutInformation#1](~/samples/snippets/csharp/VS_Snippets_Wpf/LayoutInformation/CSharp/Window1.xaml#1)]

Один <xref:System.Windows.Controls.TextBlock> элемент размещается в <xref:System.Windows.Controls.Grid> . Хотя текст заполняет только верхний левый угол первого столбца, выделенное пространство для объекта на <xref:System.Windows.Controls.TextBlock> самом деле гораздо больше. Ограничивающий прямоугольник любого типа <xref:System.Windows.FrameworkElement> можно получить с помощью <xref:System.Windows.Controls.Primitives.LayoutInformation.GetLayoutSlot%2A> метода. На следующем рисунке показан ограничивающий прямоугольник для <xref:System.Windows.Controls.TextBlock> элемента.

![Снимок экрана, на котором показано, что ограничивающий прямоугольник TextBlock теперь виден.](./media/layout/visible-textblock-bounding-box.png)

Как показано в желтом прямоугольнике, выделенное пространство для <xref:System.Windows.Controls.TextBlock> элемента фактически намного больше, чем оно отображается. При добавлении в объект дополнительных элементов <xref:System.Windows.Controls.Grid> это выделение может сжиматься или расширяться в зависимости от типа и размера добавляемых элементов.

Область макета преобразуется <xref:System.Windows.Controls.TextBlock> в объект с <xref:System.Windows.Shapes.Path> помощью <xref:System.Windows.Controls.Primitives.LayoutInformation.GetLayoutSlot%2A> метода. Этот способ может быть полезен для отображения ограничивающего прямоугольника элемента.

[!code-csharp[LayoutInformation#2](~/samples/snippets/csharp/VS_Snippets_Wpf/LayoutInformation/CSharp/Window1.xaml.cs#2)]
[!code-vb[LayoutInformation#2](~/samples/snippets/visualbasic/VS_Snippets_Wpf/LayoutInformation/VisualBasic/Window1.xaml.vb#2)]

<a name="LayoutSystem_Overview"></a>

## <a name="the-layout-system"></a>Система макета

В самом простом случае макет является рекурсивной системой, которая изменяет размер и расположение элемента и отображает его на экране. В частности, макет описывает процесс измерения и упорядочивания элементов <xref:System.Windows.Controls.Panel> <xref:System.Windows.Controls.Panel.Children%2A> коллекции элементов. Макет является интенсивным процессом. Чем больше <xref:System.Windows.Controls.Panel.Children%2A> коллекция, тем больше число вычислений, которое необходимо выполнить. Сложность также может быть введена в зависимости от поведения макета, определенного <xref:System.Windows.Controls.Panel> элементом, которому принадлежит коллекция. Относительно простой <xref:System.Windows.Controls.Panel> , такой как <xref:System.Windows.Controls.Canvas> , может значительно повысить производительность, чем более сложная <xref:System.Windows.Controls.Panel> , например <xref:System.Windows.Controls.Grid> .

Каждый раз, когда дочерний элемент <xref:System.Windows.UIElement> изменяет свое положение, он может активировать новый проход системой макета. Таким образом, важно понимать события, которые может вызывать система макета, так как ненужный вызов может привести к ухудшению производительности приложения. Далее описывается процесс, который происходит при вызове системы макета.

1. Дочерний элемент <xref:System.Windows.UIElement> начинает процесс макета, сначала изменяя его основные свойства.

2. Свойства размера, определенные для, <xref:System.Windows.FrameworkElement> оцениваются, например <xref:System.Windows.FrameworkElement.Width%2A> , <xref:System.Windows.FrameworkElement.Height%2A> и <xref:System.Windows.FrameworkElement.Margin%2A> .

3. <xref:System.Windows.Controls.Panel>применяется специальная логика, например <xref:System.Windows.Controls.Dock> направление или стек <xref:System.Windows.Controls.StackPanel.Orientation%2A> .

4. Содержимое располагается после того, как все потомки были измерены.

5. <xref:System.Windows.Controls.Panel.Children%2A>Коллекция отображается на экране.

6. Процесс вызывается снова, если <xref:System.Windows.Controls.Panel.Children%2A> к коллекции добавляется дополнительный объект, <xref:System.Windows.FrameworkElement.LayoutTransform%2A> применяется или <xref:System.Windows.UIElement.UpdateLayout%2A> вызывается метод.

Этот процесс и порядок его вызова более подробно рассматриваются в следующих разделах.

<a name="LayoutSystem_Measure_Arrange"></a>

## <a name="measuring-and-arranging-children"></a>Измерение и расположение дочерних элементов

Система макета выполняет два прохода для каждого элемента <xref:System.Windows.Controls.Panel.Children%2A> коллекции, проход меры и проход компоновки. Каждый дочерний элемент <xref:System.Windows.Controls.Panel> предоставляет собственные <xref:System.Windows.FrameworkElement.MeasureOverride%2A> <xref:System.Windows.FrameworkElement.ArrangeOverride%2A> методы и для достижения конкретного поведения макета.

Во время этапа мер <xref:System.Windows.Controls.Panel.Children%2A> вычисляется каждый элемент коллекции. Процесс начинается с вызова <xref:System.Windows.UIElement.Measure%2A> метода. Этот метод вызывается внутри реализации родительского <xref:System.Windows.Controls.Panel> элемента и не должен вызываться явным образом для выполнения макета.

Сначала оцениваются собственные свойства размера <xref:System.Windows.UIElement> , например <xref:System.Windows.UIElement.Clip%2A> и <xref:System.Windows.UIElement.Visibility%2A> . При этом создается значение с именем `constraintSize` , которое передается в <xref:System.Windows.FrameworkElement.MeasureCore%2A> .

Во вторых, свойства платформы, определенные для, <xref:System.Windows.FrameworkElement> обрабатываются, что влияет на значение `constraintSize` . Эти свойства обычно описывают характеристики размера базового объекта <xref:System.Windows.UIElement> , такие как <xref:System.Windows.FrameworkElement.Height%2A> ,, <xref:System.Windows.FrameworkElement.Width%2A> <xref:System.Windows.FrameworkElement.Margin%2A> и <xref:System.Windows.FrameworkElement.Style%2A> . Каждое из этих свойств может изменить пространство, необходимое для отображения элемента. <xref:System.Windows.FrameworkElement.MeasureOverride%2A>Затем вызывается с `constraintSize` параметром в качестве параметра.

> [!NOTE]
> Существуют различия между свойствами <xref:System.Windows.FrameworkElement.Height%2A> и и <xref:System.Windows.FrameworkElement.Width%2A> <xref:System.Windows.FrameworkElement.ActualHeight%2A> <xref:System.Windows.FrameworkElement.ActualWidth%2A> . Например, <xref:System.Windows.FrameworkElement.ActualHeight%2A> свойство является вычисляемым значением на основе других входных значений высоты и системы макета. Значение задается самой системой макета в зависимости от фактического прохода отрисовки, и поэтому может немного отставать от набора значений свойств, таких как <xref:System.Windows.FrameworkElement.Height%2A> , которые являются основанием для изменения входных данных.
>
> Поскольку <xref:System.Windows.FrameworkElement.ActualHeight%2A> — это вычисляемое значение, следует иметь в виду, что в результате выполнения различных операций в системе макета в нее могут быть внесены несколько или более добавочных отчетов. Система макета может вычислять требуемое место измерения для дочерних элементов, ограничения родительского элемента и т. д.

Конечная цель меры «оценка» заключается в том, чтобы дочерним объектом определить его <xref:System.Windows.UIElement.DesiredSize%2A> , что происходит во время <xref:System.Windows.FrameworkElement.MeasureCore%2A> вызова. <xref:System.Windows.UIElement.DesiredSize%2A>Значение сохраняется <xref:System.Windows.UIElement.Measure%2A> для использования во время прохода по компоновке содержимого.

Проход компоновки начинается с вызова <xref:System.Windows.UIElement.Arrange%2A> метода. Во время прохода компоновки родительский <xref:System.Windows.Controls.Panel> элемент создает прямоугольник, представляющий границы дочернего элемента. Это значение передается в <xref:System.Windows.FrameworkElement.ArrangeCore%2A> метод для обработки.

<xref:System.Windows.FrameworkElement.ArrangeCore%2A>Метод вычисляет объект <xref:System.Windows.UIElement.DesiredSize%2A> дочернего элемента и вычисляет все дополнительные поля, которые могут повлиять на отображаемый размер элемента. <xref:System.Windows.FrameworkElement.ArrangeCore%2A>Создает объект `arrangeSize` , который передается в <xref:System.Windows.FrameworkElement.ArrangeOverride%2A> метод в <xref:System.Windows.Controls.Panel> качестве параметра. <xref:System.Windows.FrameworkElement.ArrangeOverride%2A>Создает объект `finalSize` дочернего элемента. Наконец, <xref:System.Windows.FrameworkElement.ArrangeCore%2A> метод выполняет окончательную оценку свойств смещения, таких как поля и выравнивание, и помещает дочерний элемент в его слот макета. Дочерний объект не должен обязательно заполнять все выделенное пространство (и в большинстве случаев не делает этого). Затем управление возвращается родительскому элементу <xref:System.Windows.Controls.Panel> , а процесс макета завершается.

<a name="LayoutSystem_PanelsCustom"></a>

## <a name="panel-elements-and-custom-layout-behaviors"></a>Элементы панели и пользовательские расширения функциональности макета

WPF включает группу элементов, которые являются производными от <xref:System.Windows.Controls.Panel> . Эти <xref:System.Windows.Controls.Panel> элементы позволяют использовать множество сложных макетов. Например, элементы с накоплением можно легко достичь с помощью <xref:System.Windows.Controls.StackPanel> элемента, в то время как более сложные и свободные макеты можно использовать с помощью <xref:System.Windows.Controls.Canvas> .

В следующей таблице перечислены доступные <xref:System.Windows.Controls.Panel> элементы макета.

|Имя панели|Описание|
|----------------|-----------------|
|<xref:System.Windows.Controls.Canvas>|Определяет область, в которой можно явно располагать дочерние элементы по координатам относительно <xref:System.Windows.Controls.Canvas> области.|
|<xref:System.Windows.Controls.DockPanel>|Определяет область, в которой можно горизонтально либо вертикально упорядочивать дочерние элементы относительно друг друга.|
|<xref:System.Windows.Controls.Grid>|Задание области с таблицей переменного размера, состоящей из столбцов и строк.|
|<xref:System.Windows.Controls.StackPanel>|Выравнивает дочерние элементы в одну линию, ориентированную горизонтально или вертикально.|
|<xref:System.Windows.Controls.VirtualizingPanel>|Предоставляет структуру для элементов <xref:System.Windows.Controls.Panel>, которые виртуализируют свою коллекцию дочерних данных. Этот класс является абстрактным.|
|<xref:System.Windows.Controls.WrapPanel>|Размещает дочерние элементы последовательно слева направо, перенося содержимое на следующую строку на краю содержащего поля. Последующее упорядочение выполняется последовательно сверху вниз или справа налево в зависимости от значения <xref:System.Windows.Controls.WrapPanel.Orientation%2A> Свойства.|

Для приложений, для которых требуется макет, недоступный с помощью предопределенных <xref:System.Windows.Controls.Panel> элементов, пользовательские поведения макета можно получить, наследуя от <xref:System.Windows.Controls.Panel> переопределения <xref:System.Windows.FrameworkElement.MeasureOverride%2A> <xref:System.Windows.FrameworkElement.ArrangeOverride%2A> методов и.

<a name="LayoutSystem_Performance"></a>

## <a name="layout-performance-considerations"></a>Вопросы производительности макета

Макет является рекурсивным процессом. Каждый дочерний элемент в <xref:System.Windows.Controls.Panel.Children%2A> коллекции обрабатывается при каждом вызове системы макета. Это означает, что следует избегать запуска системы макета при отсутствии необходимости. Выполнение приведенных ниже рекомендаций поможет добиться более высокой производительности.

- Следует учитывать, что определенные изменения значений свойств могут привести к выполнению системой макета рекурсивного обновления.

  Свойства зависимостей, значения которых могут привести к инициализации системы макета, помечаются общими флагами. <xref:System.Windows.FrameworkPropertyMetadata.AffectsMeasure%2A>и <xref:System.Windows.FrameworkPropertyMetadata.AffectsArrange%2A> предоставляют полезные подсказки относительно того, какие изменения значений свойств приводят к принудительному рекурсивному обновлению системой макета. Как правило, любое свойство, которое может повлиять на размер ограничивающего прямоугольника элемента, должно иметь <xref:System.Windows.FrameworkPropertyMetadata.AffectsMeasure%2A> флаг, установленный в значение true. Дополнительные сведения см. в [обзоре свойств зависимостей](dependency-properties-overview.md).

- По возможности используйте <xref:System.Windows.UIElement.RenderTransform%2A> вместо <xref:System.Windows.FrameworkElement.LayoutTransform%2A> .

  <xref:System.Windows.FrameworkElement.LayoutTransform%2A>Может быть очень полезным способом влияния на содержимое [!INCLUDE[TLA#tla_ui](../../../../includes/tlasharptla-ui-md.md)] . Однако, если результат преобразования не влияет на положение других элементов, лучше использовать <xref:System.Windows.UIElement.RenderTransform%2A> вместо, поскольку <xref:System.Windows.UIElement.RenderTransform%2A> не вызывает систему макета. <xref:System.Windows.FrameworkElement.LayoutTransform%2A>применяет его преобразование и заставляет обновление рекурсивного макета учитывать новое положение затронутого элемента.

- Избегайте ненужных вызовов <xref:System.Windows.UIElement.UpdateLayout%2A> .

  <xref:System.Windows.UIElement.UpdateLayout%2A>Метод принудительно выполняет рекурсивное обновление макета и зачастую не требуется. Если нет уверенности в необходимости обновления, следует предоставить вызов этого метода системе макета.

- При работе с большой <xref:System.Windows.Controls.Panel.Children%2A> коллекцией рекомендуется использовать <xref:System.Windows.Controls.VirtualizingStackPanel> вместо обычного <xref:System.Windows.Controls.StackPanel> .

  Виртуализация дочерней коллекции <xref:System.Windows.Controls.VirtualizingStackPanel> позволяет хранить в памяти только объекты, которые в данный момент находятся в окне просмотра родителя. В результате этого производительность значительно увеличивается в большинстве сценариев.

<a name="LayoutSystem_LayoutRounding"></a>

## <a name="sub-pixel-rendering-and-layout-rounding"></a>Субпиксельная отрисовка и округление макета

Графическая система WPF использует аппаратно-независимые единицы, чтобы обеспечить независимость от разрешения и устройства. Каждое устройство, независимое от устройства, автоматически масштабируется с помощью параметра системы в точках на дюйм (DPI). Это обеспечивает правильное масштабирование приложений WPF для различных параметров dpi и позволяет приложению автоматически учитывать dpi.

Однако эта независимость от dpi может создать неравномерный рендеринг краев из-за сглаживания. Эти эффекты, обычно выглядящие как смазанные или полупрозрачные границы, могут появиться, когда положение границы попадает в середину пикселя устройства, а не между пикселями. В системе макета имеется способ настройки границ с помощью округления макета. Округление макета заключается в том, что система макета округляет все нецелочисленные значения пикселей во время прохода макета.

По умолчанию округление макета отключено. Чтобы включить округление макета, установите <xref:System.Windows.FrameworkElement.UseLayoutRounding%2A> свойство в значение `true` On <xref:System.Windows.FrameworkElement> . Так как это свойство зависимости, значение будет распространено на все дочерние объекты в визуальном дереве. Чтобы включить округление макета для всего пользовательского интерфейса, задайте <xref:System.Windows.FrameworkElement.UseLayoutRounding%2A> для параметра значение `true` в корневом контейнере. Пример см. в разделе <xref:System.Windows.FrameworkElement.UseLayoutRounding%2A>.

<a name="LayoutSystem_whatsnext"></a>

## <a name="whats-next"></a>Дальнейшие действия

Понимание механизма измерения и упорядочивания элементов — это первый шаг к пониманию макета. Дополнительные сведения о доступных <xref:System.Windows.Controls.Panel> элементах см. в разделе [Общие сведения о панелях](../controls/panels-overview.md). Чтобы лучше понять различные свойства размещения, которые могут повлиять на макет, см. раздел [Общие сведения о свойствах Alignment, Margin, Padding](alignment-margins-and-padding-overview.md). Когда все будет готово к объединению в упрощенное приложение, см. раздел [Пошаговое руководство. мое первое классическое приложение WPF](../getting-started/walkthrough-my-first-wpf-desktop-application.md).

## <a name="see-also"></a>См. также раздел

- <xref:System.Windows.FrameworkElement>
- <xref:System.Windows.UIElement>
- [Общие сведения о панелях](../controls/panels-overview.md)
- [Общие сведения о свойствах Alignment, Margin, Padding](alignment-margins-and-padding-overview.md)
- [Разметка и разработка](optimizing-performance-layout-and-design.md)
