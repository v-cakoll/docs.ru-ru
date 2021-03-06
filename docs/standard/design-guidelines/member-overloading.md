---
title: Перегрузка членов
ms.date: 10/22/2008
ms.technology: dotnet-standard
helpviewer_keywords:
- default arguments
- members [.NET Framework], overloaded
- member design guidelines [.NET Framework], overloading
- overloaded members
- signatures, members
ms.assetid: 964ba19e-8b94-4b5b-b1e3-5a0b531a0bb1
ms.openlocfilehash: 6a2cd6d4dd293a7f4a408e1ee97a125c9454be41
ms.sourcegitcommit: 33deec3e814238fb18a49b2a7e89278e27888291
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/02/2020
ms.locfileid: "84289009"
---
# <a name="member-overloading"></a>Перегрузка членов
Перегрузка членов означает создание двух или более членов одного типа, которые отличаются только числом или типом параметров, но имеют одинаковые имена. Например, в следующем примере `WriteLine` метод перегружен:

```csharp
public static class Console {
    public void WriteLine();
    public void WriteLine(string value);
    public void WriteLine(bool value);
    ...
}
```

 Поскольку только методы, конструкторы и индексированные свойства могут иметь параметры, только эти члены могут быть перегружены.

 Перегрузка является одним из наиболее важных методов для повышения удобства использования, производительности и удобочитаемости многократно используемых библиотек. Перегрузка по количеству параметров позволяет предоставить более простые версии конструкторов и методов. Перегрузка типа параметра позволяет использовать одно и то же имя элемента для членов, выполняющих идентичные операции с выбранным набором различных типов.

 ✔️ попытаться использовать описательные имена параметров, чтобы указать значение по умолчанию, используемое более короткими перегрузками.

 ❌Избегайте произвольного изменения имен параметров в перегрузках. Если параметр в одной перегрузке представляет те же входные данные, что и параметр в другой перегрузке, параметры должны иметь одинаковые имена.

 ❌Избегайте несоответствия в упорядочении параметров в перегруженных членах. Параметры с одинаковым именем должны отображаться в одной и той же области во всех перегрузках.

 ✔️ сделать только самую длинную виртуальную перегрузку (если требуется расширяемость). Более короткие перегрузки должны просто вызывать более длинную перегрузку.

 ❌НЕ используйте `ref` `out` модификаторы или для перегрузки членов.

 Некоторые языки не могут разрешать вызовы перегрузок следующим образом. Кроме того, такие перегрузки обычно имеют совершенно другую семантику и, вероятно, не должны перегружаться, но вместо этого можно использовать два отдельных метода.

 ❌НЕ применяйте перегрузки с параметрами в той же позиции и аналогичные типы, но с разной семантикой.

 ✔️ Разрешить `null` передачу для необязательных аргументов.

 ✔️ использовать перегрузку членов вместо определения членов с аргументами по умолчанию.

 Аргументы по умолчанию несовместимы с CLS.

 *Части © 2005, 2009 Корпорация Майкрософт. Все права защищены.*

 *Перепечатано с разрешения Pearson Education, Inc. из книги [Инфраструктура программных проектов. Соглашения, идиомы и шаблоны для многократно используемых библиотек .NET (2-е издание)](https://www.informit.com/store/framework-design-guidelines-conventions-idioms-and-9780321545619), авторы: Кржиштоф Цвалина (Krzysztof Cwalina) и Брэд Абрамс (Brad Abrams). Книга опубликована 22 октября 2008 г. издательством Addison-Wesley Professional в рамках серии, посвященной разработке для Microsoft Windows.*

## <a name="see-also"></a>См. также

- [Рекомендации по проектированию членов](member.md)
- [Рекомендации по проектированию платформы](index.md)
