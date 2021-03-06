---
title: Транзакции
ms.date: 12/13/2019
description: Узнайте, как использовать транзакции.
ms.openlocfilehash: 4b72a1573a560ffd1bfd0f54d46ab3b135280976
ms.sourcegitcommit: 30a558d23e3ac5a52071121a52c305c85fe15726
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75450384"
---
# <a name="transactions"></a>Транзакции

Транзакции позволяют сгруппировать несколько инструкций SQL в одну единицу работы, которая фиксируется в базе данных как одна атомарная единица. При сбое любой инструкции в транзакции можно выполнить откат изменений, выполненных предыдущими инструкциями. Начальное состояние базы данных сохраняется при запуске транзакции. Использование транзакций может также повысить производительность SQLite при одновременном внесении многочисленных изменений в базу данных.

## <a name="concurrency"></a>параллелизм

В SQLite только одна транзакция может иметь изменения, ожидающие внесения в базе данных, в любой конкретный момент времени. В связи с этим время ожидания вызовов <xref:Microsoft.Data.Sqlite.SqliteConnection.BeginTransaction%2A> и методов `Execute` в <xref:Microsoft.Data.Sqlite.SqliteCommand> может истечь, если другая транзакция занимает слишком много времени.

Дополнительные сведения о блокировках, повторных попытках и времени ожидания см. в разделе [Ошибки базы данных](database-errors.md).

## <a name="isolation-levels"></a>Уровни изоляции

Транзакции **сериализуемы** по умолчанию в SQLite. Этот уровень изоляции гарантирует, что любые изменения, внесенные в транзакцию, будут полностью изолированы. Изменения транзакции не затрагивают другие инструкции, выполняемые за пределами транзакции.

SQLite также поддерживает **чтение незафиксированных изменений** при использовании общего кэша. Этот уровень допускает "грязные" и неповторяемые операции чтения, а также фантомы:

- *"Грязное" чтение* происходит, когда изменения, ожидающие в одной транзакции, возвращаются запросом вне этой транзакции, но при этом изменения в транзакции откатываются. Результаты содержат данные, которые никогда не фиксировались в базе данных.

- *Неповторяемое чтение* возникает, когда транзакция запрашивает одну и ту же строку дважды, но результаты различаются, так как они были изменены другой транзакцией между этими запросами.

- *Фантомы* — это строки, которые изменяются или добавляются для соответствия предложению запроса where во время транзакции. Если они разрешены, один и тот же запрос может возвращать разные строки при двукратном выполнении в одной транзакции.

Microsoft.Data.Sqlit рассматривает IsolationLevel, переданные в <xref:Microsoft.Data.Sqlite.SqliteConnection.BeginTransaction%2A> как минимальный уровень. Фактический уровень изоляции будет повышен до уровня чтения незафиксированных изменений или сериализуемого.

Следующий код имитирует "грязное" чтение. Обратите внимание, что строка подключения должна включать `Cache=Shared`.

[!code-csharp[](../../../../samples/snippets/standard/data/sqlite/DirtyReadSample/Program.cs?name=snippet_DirtyRead)]
