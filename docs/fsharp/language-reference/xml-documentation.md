---
title: Документация XML
description: Дополнительные сведения о поддержке F# в для создания документации из комментариев.
ms.date: 05/16/2016
ms.openlocfilehash: 0a87915c361fc88f0c05264e1c17278fd656a167
ms.sourcegitcommit: 30a558d23e3ac5a52071121a52c305c85fe15726
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75344690"
---
# <a name="xml-documentation"></a>Документация XML

Вы можете создать документацию по комментариям к коду с тройной косой чертой F#(///) в. Комментарии XML могут предшествовать объявлениям в файлах кода (FS) или файлах сигнатуры (FSI).

## <a name="generating-documentation-from-comments"></a>Создание документации из комментариев

Поддержка в F# для создания документации из комментариев такая же, как и на других языках .NET Framework. Как и в других .NET Framework языках, [параметр компилятора-doc](https://msdn.microsoft.com/library/434394ae-0d4a-459c-a684-bffede519a04) позволяет создать XML-файл, содержащий сведения, которые можно преобразовать в документацию с помощью такого средства, как [DocFX](https://dotnet.github.io/docfx/) или [Sandcastle](https://github.com/EWSoftware/SHFB). Документация, созданная с помощью средств, предназначенных для использования со сборками, написанными на других языках .NET Framework, обычно создает представление интерфейсов API, основанных на скомпилированной F# форме конструкций. Если средства специально не F#поддерживаются, документация, создаваемая этими инструментами, F# не соответствует представлению API.

Дополнительные сведения о создании документации из XML см. в разделе [ &#40;комментарии XML-документации руководство&#35; по&#41;программированию C](https://msdn.microsoft.com/library/b2s063f7).

## <a name="recommended-tags"></a>Рекомендуемые теги

Существует два способа написания комментариев XML-документации. Один из них — просто написать документацию непосредственно в комментарии с тройной косой чертой без использования XML-тегов. В этом случае весь текст комментария будет создан как сводная документация для конструкции кода, которая сразу же следует за. Используйте этот метод, если нужно написать только краткую сводку по каждой конструкции. Другой способ — использовать XML-теги для предоставления более структурированной документации. Второй метод позволяет указать отдельные примечания для краткой сводки, дополнительные примечания, документацию для каждого параметра и параметра типа, а также описание возвращаемого значения. В следующей таблице описаны XML-теги, распознаваемые F# в комментариях к коду XML.

|Синтаксис тега|Описание|
|----------|-----------|
|**\<c\>** _text_ **\</c\>**|Указывает, что *текст* является кодом. Этот тег может использоваться генераторами документации для показа текста в шрифте, подходящем для кода.|
|**\<summary\>** _text_ **\</Summary\>**|Указывает, что *текст* является кратким описанием элемента Program. Описание обычно состоит из одного или двух предложений.|
|**\<замечания\>** _text_ **\</РЕМАРКС\>**|Указывает, что *текст* содержит дополнительные сведения об элементе Program.|
|**\<param Name = "** _Name_ **"\>** _Description_ **\</param Returns\>**|Задает имя и описание для параметра функции или метода.|
|**\<typeparam Name = "** _имя_ **"\>** _Description_ **\</типепарам\>**|Задает имя и описание для параметра типа.|
|**\<возвращает\>** _text_ **\</Returns\>**|Указывает, что *текст* описывает возвращаемое значение функции или метода.|
|**\<cref Exception = "** _тип_ **"\>** _Description_ **\</ексцептион\>**|Указывает тип исключения, которое может быть создано, и обстоятельства, при которых оно вызывается.|
|**\<см. в разделе cref = "** _Reference_ **"\>** _Text_ **\</Си\>**|Указывает встроенную ссылку на другой элемент программы. *Ссылка* — это имя, которое отображается в XML-файле документации. *Текст* — это текст, отображаемый в ссылке.|
|**\<seeAlso cref = "** _reference_ **"/\>**|Указывает см. также ссылку на документацию для другого типа. *Ссылка* — это имя, которое отображается в XML-файле документации. См. также ссылки, которые обычно отображаются в нижней части страницы документации.|
|**\<para\>** _text_ **\</пара\>**|Задает абзац текста. Используется для разделения текста внутри тега **remarks** .|

## <a name="example"></a>Пример

### <a name="description"></a>Описание

Ниже приведен типичный комментарий XML-документации в файле сигнатуры.

### <a name="code"></a>Код

[!code-fsharp[Main](~/samples/snippets/fsharp/lang-ref-2/snippet7101.fs)]

## <a name="example"></a>Пример

### <a name="description"></a>Описание

В следующем примере показан альтернативный метод без XML-тегов. В этом примере весь текст комментария рассматривается как сводка. Обратите внимание, что если тег Summary не указан явно, не следует указывать другие теги, такие как **param** или **Returns** .

### <a name="code"></a>Код

[!code-fsharp[Main](~/samples/snippets/fsharp/lang-ref-2/snippet7102.fs)]

## <a name="see-also"></a>См. также:

- [Справочник по языку F#](index.md)
- [Параметры компилятора](compiler-options.md)
