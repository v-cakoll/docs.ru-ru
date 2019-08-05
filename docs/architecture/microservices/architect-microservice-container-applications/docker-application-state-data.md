---
title: Состояние и данные в приложениях Docker
description: Управление состоянием и данными в приложениях Docker. Экземпляры микрослужбы являются невосстановимыми, но к ДАННЫМ ЭТО НЕ ОТНОСИТСЯ. Рассмотрим, как решить эту задачу с помощью микрослужб.
ms.date: 09/20/2018
ms.openlocfilehash: 9d7b0ff0e73267c6b80be2f1c956c3b4eae140e2
ms.sourcegitcommit: f20dd18dbcf2275513281f5d9ad7ece6a62644b4
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/30/2019
ms.locfileid: "68673131"
---
# <a name="state-and-data-in-docker-applications"></a>Состояние и данные в приложениях Docker

Представьте себе контейнер как экземпляр процесса. Процесс не находится в неизменном состоянии. И хотя контейнер может записывать данные в локальное хранилище, предполагать, что экземпляр всегда будет на месте — это все равно, что надеяться на постоянство одной ячейки памяти. Вы должны предполагать, что образы контейнеров, как и процессы, имеют несколько экземпляров или конечном итоге будут удалены. Если для управления ими используется оркестратор контейнеров, следует предполагать, что они могут перемещаться из одного узла (или виртуальной машины) в другой.

Для управления постоянными данными в приложениях Docker используются следующие решения:

Из узла Docker в качестве [томов Docker](https://docs.docker.com/engine/admin/volumes/):

- **Тома** хранятся в той части файловой системы узла, которая находится под управлением Docker.

- **Подключения привязок** можно сопоставить с любой папкой в файловой системе узла, поэтому доступом нельзя управлять из процесса Docker, что может представлять угрозу для безопасности, поскольку контейнер может получить доступ к конфиденциальным папкам операционной системы.

- **Подключения tmpfs** аналогичны виртуальным папкам, которые существуют только в памяти узла и никогда не записываются в файловую систему.

Из удаленного хранилища:

- [Служба хранилища Azure](https://azure.microsoft.com/documentation/services/storage/), предоставляющая геораспределенное хранилище для долгосрочного хранения данных контейнеров.

- Удаленные реляционные базы данных, например [базы данных SQL Azure](https://azure.microsoft.com/services/sql-database/) или базы данных NoSQL, такие как [Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/introduction), или службы кэша, такие как [Redis](https://redis.io/).

Из контейнера Docker:

> Docker имеет функцию под названием *оверлейная файловая система*. Она выполняет копирование при записи, при котором обновленная информация сохраняется в корневой файловой системе контейнера. Эта информация добавляется к изначальному образу, на котором базируется контейнер. Если удалить контейнер из системы, эти изменения будут утеряны. Поэтому, хоть и возможно сохранить состояние контейнера в его локальном хранилище, создание системы по такой модели будет противоречить конструкции контейнера, состояние которого по умолчанию не отслеживается.
>
> Тем не менее ранее представленные тома Docker являются предпочтительным способом обработки локальных данных Docker. Если вам требуются дополнительные сведения о хранении в контейнерах, ознакомьтесь с разделами [Docker storage drivers](https://docs.docker.com/storage/storagedriver/select-storage-driver/) (Драйверы хранилища Docker) и [About storage drivers](https://docs.docker.com/storage/storagedriver/) (Основные сведения о драйверах хранилища).

Ниже подробно описаны эти варианты.

**Тома** — это каталоги из ОС узла, сопоставленные с каталогами в контейнерах. Если код в контейнере имеет доступ к каталогу, на самом деле он обращается к каталогу на ОС узла. Этот каталог не привязан к времени существования самого контейнера, находится под управлением Docker и изолирован от основных функциональных возможностей хост-компьютера. Поэтому тома данных хранят данные независимо от контейнера. Если удалить контейнер или образ из узла Docker, данные из томов данных не будут удалены.

Тома могут быть именованными или анонимными (по умолчанию). Именованные тома — это следующий этап развития **контейнеров томов данных**. Они упрощают обмен данными между контейнерами. Тома также поддерживают драйверы томов, позволяющие хранить данные на удаленных узлах и т. д.

**Подключения привязки** доступны довольно давно и позволяют сопоставлять любую папку с точкой подключения в контейнере. Подключения привязки имеют больше ограничений, чем тома, и с ними связаны некоторые проблемы безопасности, поэтому тома являются рекомендуемым способом.

**Подключения tmpfs** по сути являются виртуальными папками, которые существуют только в памяти узла и никогда не записываются в файловую систему. Они быстрые и безопасные, но используют ресурсы памяти и предназначены только для временных данных.

Как показано на рисунке 4-5, обычные тома Docker могут храниться за пределами самих контейнерами, но в физических границах сервера узла или виртуальной машины. Тем не менее контейнеры Docker с одного сервера узла или виртуальной машины не могут обращаться к тому на другом сервере узла или виртуальной машине. Другими словами, с помощью этих томов невозможно управлять данными контейнеров, которые выполняются на разных узлах Docker, хотя такой режим работы можно реализовать с помощью драйвера томов, который поддерживает удаленные узлы.

![Тома могут совместно использоваться несколькими контейнерами, но только на одном узле, если вы не используете удаленный драйвер с поддержкой удаленных узлов. ](./media/image5.png)

**Рис. 4-5**. Тома и внешние источники данных для приложений на основе контейнера

Кроме того, когда контейнеры Docker управляются оркестратором, контейнеры могут "перемещаться" между узлами в рамках оптимизации, выполняемой кластером. Поэтому тома данных не рекомендуется использовать для бизнес-данных. Но это хороший инструмент для работы с файлами трассировки, временными файлами или подобными элементами, которые не влияют на целостность бизнес-данных.

**Удаленные источники данных и инструменты кэширования**, такие как база данных Azure SQL, Azure Cosmos DB или служба кэша, например Redis, можно использовать в упакованных в контейнеры приложениях так же, как они используются при разработке без контейнеров. Это проверенный способ хранения данных бизнес-приложений.

**Служба хранилища Azure** Бизнес-данные, как правило, необходимо хранить во внешних ресурсах или базах данных, например в службе хранилища Azure. Служба хранилища Azure предоставляет следующие службы в облаке:

- Хранилище BLOB-объектов хранит неструктурированные данные объектов. Большой двоичный объект может представлять собой текстовые или двоичные данные любого типа, например документ или файл мультимедиа (файлы с изображением, аудио и видео). Хранилище BLOB-объектов также называют хранилищем объектов.

- Хранилище файлов обеспечивает хранение приложений прежних версий по стандартному протоколу SMB. Виртуальные машины Azure и облачные службы могут совместно использовать данные файлов в компонентах приложений через подключенный общий ресурс. Локальные приложения могут обращаться к данным файлов в общем ресурсе через файловую службу REST API.

- Хранилище таблиц содержит структурированные наборы данных. Хранилище таблиц — это хранилище данных NoSQL типа "ключ-значение", обеспечивающее быструю разработку и доступ к большим объемам данных.

**Реляционные базы данных и базы данных NoSQL.** Существует множество вариантов внешних баз данных — реляционные базы данных, такие как SQL Server, PostgreSQL, Oracle или базы данных NoSQL, например Azure Cosmos DB, MongoDB и т. д. Эти базы данных являются отдельной темой и не будут описываться в данном руководстве.

>[!div class="step-by-step"]
>[Назад](containerize-monolithic-applications.md)
>[Вперед](service-oriented-architecture.md)