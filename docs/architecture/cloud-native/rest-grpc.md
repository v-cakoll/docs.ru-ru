---
title: REST и gRPC
description: Узнайте о gRPC, ее роли в собственных приложениях в облаке и о том, как она отличается от протокола HTTP RESTFUL.
author: robvet
ms.date: 09/08/2019
ms.openlocfilehash: 80960a9042b1514fb78e7a8c993a1854067407e8
ms.sourcegitcommit: 14ad34f7c4564ee0f009acb8bfc0ea7af3bc9541
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/01/2019
ms.locfileid: "73842053"
---
# <a name="rest-and-grpc"></a><span data-ttu-id="e18bd-103">REST и gRPC</span><span class="sxs-lookup"><span data-stu-id="e18bd-103">REST and gRPC</span></span>

[!INCLUDE [book-preview](../../../includes/book-preview.md)]

<span data-ttu-id="e18bd-104">До сих пор в этой книге мы сосредоточены на связи на [основе RESTful](https://docs.microsoft.com/azure/architecture/best-practices/api-design) .</span><span class="sxs-lookup"><span data-stu-id="e18bd-104">So far in this book, we’ve focused on [REST-based](https://docs.microsoft.com/azure/architecture/best-practices/api-design) communication.</span></span> <span data-ttu-id="e18bd-105">"ОСТАВШАЯся" — это архитектурный стиль, который способствует взаимодействию между распределенными системами компьютеров.</span><span class="sxs-lookup"><span data-stu-id="e18bd-105">REST is an architectural style that promotes interoperability between distributed computer systems.</span></span> <span data-ttu-id="e18bd-106">Он использует модель "запрос-ответ", где каждый ответ сервера связан с запросом от клиента.</span><span class="sxs-lookup"><span data-stu-id="e18bd-106">It uses a request/response model where every response from the server is to a request from the client.</span></span> <span data-ttu-id="e18bd-107">В то время как широко распространено, остальное не подходит для каждой проблемы.</span><span class="sxs-lookup"><span data-stu-id="e18bd-107">While widely popular, REST isn't a perfect fit for every problem.</span></span> <span data-ttu-id="e18bd-108">Более новая технология связи, озаглавленная gRPC, быстро получает популярность и делает ее в собственном мире в облаке.</span><span class="sxs-lookup"><span data-stu-id="e18bd-108">A newer communication technology, entitled gRPC, is rapidly gaining popularity and making its way into the cloud-native world.</span></span>

## <a name="grpc"></a><span data-ttu-id="e18bd-109">gRPC</span><span class="sxs-lookup"><span data-stu-id="e18bd-109">gRPC</span></span>

<span data-ttu-id="e18bd-110">gRPC — это связь с открытым кодом, полученная от Google.</span><span class="sxs-lookup"><span data-stu-id="e18bd-110">gRPC is an open-source communication that originates from Google.</span></span> <span data-ttu-id="e18bd-111">Она построена на основе модели [удаленного вызова процедур (RPC)](https://en.wikipedia.org/wiki/Remote_procedure_call) , популярной в распределенных вычислениях.</span><span class="sxs-lookup"><span data-stu-id="e18bd-111">It's built upon the [remote procedure call (RPC)](https://en.wikipedia.org/wiki/Remote_procedure_call) model, popular in distributed computing.</span></span> <span data-ttu-id="e18bd-112">После этой модели локальная клиентская программа предоставляет внутрипроцессный метод для выполнения операции.</span><span class="sxs-lookup"><span data-stu-id="e18bd-112">Following this model, a local client program exposes an in-process method to execute an operation.</span></span> <span data-ttu-id="e18bd-113">В фоновом режиме этот вызов вызывается для внешней службы в распределенной сети.</span><span class="sxs-lookup"><span data-stu-id="e18bd-113">Behind the scenes, that call is invoked on an out-of-process microservice across a distributed network.</span></span> <span data-ttu-id="e18bd-114">Разработчик кодирует операцию как вызов локальной процедуры.</span><span class="sxs-lookup"><span data-stu-id="e18bd-114">The developer codes the operation as a local procedure call.</span></span> <span data-ttu-id="e18bd-115">Базовая платформа абстрагирует сетевую связь «точка-точка», сериализацию и выполнение.</span><span class="sxs-lookup"><span data-stu-id="e18bd-115">The underlying platform abstracts the point-to-point networking communication, serialization, and execution.</span></span>

<span data-ttu-id="e18bd-116">gRPC — это современная платформа RPC, которая является простой и высокопроизводительной.</span><span class="sxs-lookup"><span data-stu-id="e18bd-116">gRPC is a modern RPC framework that is lightweight and highly performant.</span></span> <span data-ttu-id="e18bd-117">Он использует HTTP/2 для транспортного протокола.</span><span class="sxs-lookup"><span data-stu-id="e18bd-117">It uses HTTP/2 for its transport protocol.</span></span> <span data-ttu-id="e18bd-118">Несмотря на то, что совместимость с HTTP 1,1, HTTP/2 включает в себя множество дополнительных возможностей:</span><span class="sxs-lookup"><span data-stu-id="e18bd-118">While compatible with HTTP 1.1, HTTP/2 features many advanced capabilities:</span></span>

- <span data-ttu-id="e18bd-119">Хотя HTTP 1,1 отправляет данные в виде открытого текста, HTTP/2 является двоичным протоколом.</span><span class="sxs-lookup"><span data-stu-id="e18bd-119">While HTTP 1.1 sends data as clear text, HTTP/2 is a binary protocol.</span></span> <span data-ttu-id="e18bd-120">Он быстрее анализирует данные с помощью меньшего объема памяти, сокращает задержку в сети с помощью связанных улучшений для ускорения и более эффективного управления сетевыми ресурсами.</span><span class="sxs-lookup"><span data-stu-id="e18bd-120">It parses data faster using less memory, reduces network latency with the related improvements to speed, and manages network resources more efficiently.</span></span>
- <span data-ttu-id="e18bd-121">Хотя HTTP 1,1 ограничена обработкой одного запроса или ответа приема-передачи за раз, HTTP/2 поддерживает мультиплексирование или несколько параллельных запросов по одному соединению.</span><span class="sxs-lookup"><span data-stu-id="e18bd-121">While HTTP 1.1 is limited to processing one round-trip request/response at a time, HTTP/2 supports multiplexing, or multiple parallel requests over the same connection.</span></span>
- <span data-ttu-id="e18bd-122">HTTP/2 поддерживает дуплексную или двунаправленную связь, где как клиент, так и сервер могут обмениваться данными в одно и то же время.</span><span class="sxs-lookup"><span data-stu-id="e18bd-122">HTTP/2 supports full-duplex, or bidirectional communication, where both client and server and can communicate at the same time.</span></span> <span data-ttu-id="e18bd-123">Клиент может отправлять данные запроса одновременно с отправкой данных ответа сервером.</span><span class="sxs-lookup"><span data-stu-id="e18bd-123">The client can be uploading request data at the same time the server is sending back response data.</span></span>
- <span data-ttu-id="e18bd-124">Потоковая передача встроена в HTTP/2, что означает, что запросы и ответы могут асинхронно потокировать большие наборы данных.</span><span class="sxs-lookup"><span data-stu-id="e18bd-124">Streaming is built into HTTP/2 meaning that both requests and responses can asynchronously stream large data sets.</span></span>
- <span data-ttu-id="e18bd-125">Сочетание gRPC и HTTP/2 значительно повышает производительность.</span><span class="sxs-lookup"><span data-stu-id="e18bd-125">Combining gRPC and HTTP/2, performance dramatically increases.</span></span> <span data-ttu-id="e18bd-126">В [Windows Communication Foundation (WCF)](https://docs.microsoft.com/dotnet/framework/wcf/whats-wcf) терминах производительность gRPC соответствует и превышает скорость и эффективность [привязок NetTcp](https://docs.microsoft.com/dotnet/api/system.servicemodel.nettcpbinding?view=netframework-4.8).</span><span class="sxs-lookup"><span data-stu-id="e18bd-126">In [Windows Communication Foundation (WCF)](https://docs.microsoft.com/dotnet/framework/wcf/whats-wcf) parlance, gRPC performance meets and exceeds the speed and efficiency of [NetTCP bindings](https://docs.microsoft.com/dotnet/api/system.servicemodel.nettcpbinding?view=netframework-4.8).</span></span> <span data-ttu-id="e18bd-127">Однако, в отличие от NetTCP, gRPC не ограничивается такими языками Майкрософт, C# как или VB.NET.</span><span class="sxs-lookup"><span data-stu-id="e18bd-127">However, unlike NetTCP, gRPC isn't constrained to Microsoft languages such as C# or VB.NET.</span></span>

<span data-ttu-id="e18bd-128">gRPC поддерживается на самых популярных платформах, включая Java, C#, Golang и NodeJS.</span><span class="sxs-lookup"><span data-stu-id="e18bd-128">gRPC is supported across most popular platforms, including Java, C#, Golang, and NodeJS.</span></span>

## <a name="protocol-buffers"></a><span data-ttu-id="e18bd-129">Protocol Buffers</span><span class="sxs-lookup"><span data-stu-id="e18bd-129">Protocol Buffers</span></span>

<span data-ttu-id="e18bd-130">gRPC использует другую технологию с открытым исходным кодом, которая называется [буфером протоколов](https://developers.google.com/protocol-buffers/docs/overview) или сообщениями protobuf для отправки и получения данных.</span><span class="sxs-lookup"><span data-stu-id="e18bd-130">gRPC embraces another open-source technology called [Protocol Buffers](https://developers.google.com/protocol-buffers/docs/overview) or Protobuf messages to send and receive data.</span></span> <span data-ttu-id="e18bd-131">Как и в случае с [контрактом данных WCF](https://docs.microsoft.com/dotnet/framework/wcf/feature-details/using-data-contracts), protobuf сериализует структурированные данные для систем для чтения и записи.</span><span class="sxs-lookup"><span data-stu-id="e18bd-131">Similar to a [WCF Data Contract](https://docs.microsoft.com/dotnet/framework/wcf/feature-details/using-data-contracts), Protobuf serializes structured data for systems to read and write.</span></span> <span data-ttu-id="e18bd-132">Это сокращает затраты, которые могут быть доступны для удобочитаемых форматов, таких как XML или JSON.</span><span class="sxs-lookup"><span data-stu-id="e18bd-132">It reduces the overhead that human-readable formats like XML or JSON incur.</span></span>

<span data-ttu-id="e18bd-133">Многие методы сериализации объектов соответствуют структуре объектов данных во время выполнения.</span><span class="sxs-lookup"><span data-stu-id="e18bd-133">Many object serialization techniques reflect across the structure of data objects at run-time.</span></span> <span data-ttu-id="e18bd-134">Protobuf требует, чтобы структура была заранее определена с использованием независимого от платформы языка (языкового буфера протокола).</span><span class="sxs-lookup"><span data-stu-id="e18bd-134">Protobuf requires you to define the structure up front with a platform-agnostic language (Protocol Buffer Language).</span></span> <span data-ttu-id="e18bd-135">Каждое определение хранится в файле ". \".</span><span class="sxs-lookup"><span data-stu-id="e18bd-135">Each definition is stored in a ".proto" file.</span></span> <span data-ttu-id="e18bd-136">Затем с помощью компилятора protobuf, "Proton", вы создаете клиентский и серверный код для любой из поддерживаемых платформ.</span><span class="sxs-lookup"><span data-stu-id="e18bd-136">Then using Protobuf compiler, "Proton," you generate client and server code for any of the supported platforms.</span></span> <span data-ttu-id="e18bd-137">Созданный код оптимизирован для быстрой сериализации и десериализации данных.</span><span class="sxs-lookup"><span data-stu-id="e18bd-137">The generated code is optimized for fast serialization/deserialization of data.</span></span> <span data-ttu-id="e18bd-138">Во время выполнения каждое сообщение упаковывается в строго типизированный контракт службы и сериализуется в стандартное представление protobuf.</span><span class="sxs-lookup"><span data-stu-id="e18bd-138">At runtime, each message is wrapped in the strongly-typed service contract and serialized in a standard Protobuf representation.</span></span>

## <a name="grpc-support-in-net"></a><span data-ttu-id="e18bd-139">Поддержка gRPC в .NET</span><span class="sxs-lookup"><span data-stu-id="e18bd-139">gRPC support in .NET</span></span>

<span data-ttu-id="e18bd-140">Microsoft .NET Core Framework 3,0 включает средства и встроенную поддержку gRPC.</span><span class="sxs-lookup"><span data-stu-id="e18bd-140">The Microsoft .NET Core framework 3.0 includes tooling and native support for gRPC.</span></span> <span data-ttu-id="e18bd-141">На рис. 4-20 показан шаблон Visual Studio 2019, который формирует проект схемы gRPC для службы gRPC.</span><span class="sxs-lookup"><span data-stu-id="e18bd-141">Figure 4-20 shows the Visual Studio 2019 template that scaffolds a gRPC skeleton project for a gRPC service.</span></span> <span data-ttu-id="e18bd-142">Обратите внимание, как .NET Core поддерживает платформы Windows, Linux и macOS.</span><span class="sxs-lookup"><span data-stu-id="e18bd-142">Note how .NET Core supports the Windows, Linux, and macOS platforms.</span></span>

![Поддержка gRPC в Visual Studio 2019](./media/visual-studio-2019-grpc-template.png)

<span data-ttu-id="e18bd-144">**Рис. 4-20**.</span><span class="sxs-lookup"><span data-stu-id="e18bd-144">**Figure 4-20**.</span></span> <span data-ttu-id="e18bd-145">Поддержка gRPC в Visual Studio 2019</span><span class="sxs-lookup"><span data-stu-id="e18bd-145">gRPC support in Visual Studio 2019</span></span>

<span data-ttu-id="e18bd-146">.NET Core 3,0 легко интегрирует gRPC в свою инфраструктуру, включая маршрутизацию конечных точек, встроенную поддержку IoC и ведение журнала.</span><span class="sxs-lookup"><span data-stu-id="e18bd-146">.NET Core 3.0 seamlessly integrates gRPC into its framework, including endpoint routing, built-in IoC support, and logging.</span></span> <span data-ttu-id="e18bd-147">Веб-сервер Kestrel с открытым кодом полностью поддерживает подключения HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="e18bd-147">The open-source Kestrel web server fully supports HTTP/2 connections.</span></span>

<span data-ttu-id="e18bd-148">На рис. 4-21 показана структура службы gRPC в Visual Studio 2019.</span><span class="sxs-lookup"><span data-stu-id="e18bd-148">Figure 4-21 shows structure of a gRPC service in Visual Studio 2019.</span></span> <span data-ttu-id="e18bd-149">Обратите внимание, что структура папок включает папки для файлов с указанием и кода службы.</span><span class="sxs-lookup"><span data-stu-id="e18bd-149">Note how the folder structure includes folders for the proto files and service code.</span></span>

![проект gRPC в Visual Studio 2019](./media/grpc-project.png  )

<span data-ttu-id="e18bd-151">**Рис. 4-21**.</span><span class="sxs-lookup"><span data-stu-id="e18bd-151">**Figure 4-21**.</span></span> <span data-ttu-id="e18bd-152">проект gRPC в Visual Studio 2019</span><span class="sxs-lookup"><span data-stu-id="e18bd-152">gRPC project in Visual Studio 2019</span></span>

## <a name="grpc-usage"></a><span data-ttu-id="e18bd-153">Использование gRPC</span><span class="sxs-lookup"><span data-stu-id="e18bd-153">gRPC Usage</span></span>

<span data-ttu-id="e18bd-154">gRPC хорошо подходит для следующих сценариев:</span><span class="sxs-lookup"><span data-stu-id="e18bd-154">gRPC is well suited for the following scenarios:</span></span>

- <span data-ttu-id="e18bd-155">Низкая задержка и обмен данными с высокой пропускной способностью.</span><span class="sxs-lookup"><span data-stu-id="e18bd-155">Low latency and high throughput communication.</span></span> <span data-ttu-id="e18bd-156">gRPC отлично подходит для облегченных микрослужб, в которых важна эффективность.</span><span class="sxs-lookup"><span data-stu-id="e18bd-156">gRPC is great for lightweight microservices where efficiency is critical.</span></span>
- <span data-ttu-id="e18bd-157">Обмен данными «точка-точка» в реальном времени.</span><span class="sxs-lookup"><span data-stu-id="e18bd-157">Point-to-point real-time communication.</span></span> <span data-ttu-id="e18bd-158">gRPC обладает отличной поддержкой двунаправленной потоковой передачи.</span><span class="sxs-lookup"><span data-stu-id="e18bd-158">gRPC has excellent support for bi-directional streaming.</span></span> <span data-ttu-id="e18bd-159">службы gRPC Services могут отправлять сообщения в режиме реального времени без опроса.</span><span class="sxs-lookup"><span data-stu-id="e18bd-159">gRPC services can push messages in real time without polling.</span></span>
- <span data-ttu-id="e18bd-160">Polyglot среды — средства gRPC поддерживают большинство популярных языков разработки, делая их хорошим выбором для многоязыковых сред.</span><span class="sxs-lookup"><span data-stu-id="e18bd-160">Polyglot environments – gRPC tooling supports most popular development languages, making it a good choice for multi-language environments.</span></span>
- <span data-ttu-id="e18bd-161">Ограниченные среды сети — сообщения gRPC сериализуются с помощью protobuf, облегченного формата сообщений.</span><span class="sxs-lookup"><span data-stu-id="e18bd-161">Network constrained environments – gRPC messages are serialized with Protobuf, a lightweight message format.</span></span> <span data-ttu-id="e18bd-162">Сообщение gRPC всегда меньше, чем эквивалентное сообщение JSON.</span><span class="sxs-lookup"><span data-stu-id="e18bd-162">A gRPC message is always smaller than an equivalent JSON message.</span></span>

<span data-ttu-id="e18bd-163">Во время написания этой книги большинство браузеров имеют ограниченную поддержку gRPC.</span><span class="sxs-lookup"><span data-stu-id="e18bd-163">At the time of writing of this book, most browsers have limited support for gRPC.</span></span> <span data-ttu-id="e18bd-164">gRPC сильно использует функции HTTP/2, и ни один браузер не предоставляет необходимый уровень контроля над веб-запросами для поддержки клиента gRPC.</span><span class="sxs-lookup"><span data-stu-id="e18bd-164">gRPC heavily uses HTTP/2 features and no browser provides the level of control required over web requests to support a gRPC client.</span></span> <span data-ttu-id="e18bd-165">gRPC обычно используется для взаимодействия внутренних микрослужб с микрослужбами.</span><span class="sxs-lookup"><span data-stu-id="e18bd-165">gRPC is typically used for internal microservice to microservice communication.</span></span> <span data-ttu-id="e18bd-166">На рис. 4-22 показана простая, но распространенная схема использования.</span><span class="sxs-lookup"><span data-stu-id="e18bd-166">Figure 4-22 shows a simple, but common usage pattern.</span></span>

![Шаблоны использования gRPC](./media/grpc-usage.png)

<span data-ttu-id="e18bd-168">**Рис. 4-22**.</span><span class="sxs-lookup"><span data-stu-id="e18bd-168">**Figure 4-22**.</span></span> <span data-ttu-id="e18bd-169">шаблоны использования gRPC</span><span class="sxs-lookup"><span data-stu-id="e18bd-169">gRPC usage patterns</span></span>

<span data-ttu-id="e18bd-170">Обратите внимание на предыдущее, как интерфейсный трафик вызывается с помощью HTTP, а фоновая микрослужба для микрослужбы использует gRPC.</span><span class="sxs-lookup"><span data-stu-id="e18bd-170">Note in the previous figure how front-end traffic is invoked with HTTP while back-end microservice to microservice uses gRPC.</span></span>

<span data-ttu-id="e18bd-171">Взглянув на будущее, gRPC может играть значительную роль в десронинг превосходствоа для собственных облачных систем.</span><span class="sxs-lookup"><span data-stu-id="e18bd-171">Looking ahead, gRPC could play a major role in dethroning the dominance of REST for cloud-native systems.</span></span> <span data-ttu-id="e18bd-172">Преимущества производительности и простота разработки слишком хороши для передачи.</span><span class="sxs-lookup"><span data-stu-id="e18bd-172">The performance benefits and ease of development are too good to pass up.</span></span> <span data-ttu-id="e18bd-173">Тем не менее, не делайте никаких ошибок, остальное будет продолжаться в течение долгого времени.</span><span class="sxs-lookup"><span data-stu-id="e18bd-173">However, make no mistake, REST will still be around for a long time.</span></span> <span data-ttu-id="e18bd-174">Он по-прежнему предназначен для общедоступных API и для обеспечения обратной совместимости.</span><span class="sxs-lookup"><span data-stu-id="e18bd-174">It still excels for publicly exposed APIs and for backward compatibility reasons.</span></span>

>[!div class="step-by-step"]
><span data-ttu-id="e18bd-175">[Назад](service-to-service-communication.md)
>[Вперед](service-mesh-communication-infrastructure.md)</span><span class="sxs-lookup"><span data-stu-id="e18bd-175">[Previous](service-to-service-communication.md)
[Next](service-mesh-communication-infrastructure.md)</span></span>