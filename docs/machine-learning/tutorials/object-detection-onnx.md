---
title: Учебник. Обнаружение объектов с использованием глубокого обучения с помощью ONNX и ML.NET
description: В этом учебнике показано, как использовать предварительно обученную модель глубокого обучения ONNX в ML.NET для обнаружения объектов в изображениях.
author: luisquintanilla
ms.author: luquinta
ms.date: 08/01/2019
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: e44ea5795beb90bafe3faf0bafb463d49ba1fc41
ms.sourcegitcommit: 9ee6cd851b6e176a5811ea28ed0d5935c71950f9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68868725"
---
# <a name="tutorial-detect-objects-using-onnx-in-mlnet"></a><span data-ttu-id="dbb28-103">Учебник. Обнаружение объектов с помощью ONNX в ML.NET</span><span class="sxs-lookup"><span data-stu-id="dbb28-103">Tutorial: Detect objects using ONNX in ML.NET</span></span>

<span data-ttu-id="dbb28-104">Узнайте, как использовать предварительно обученную модель ONNX в ML.NET для обнаружения объектов в изображениях.</span><span class="sxs-lookup"><span data-stu-id="dbb28-104">Learn how to use a pre-trained ONNX model in ML.NET to detect objects in images.</span></span>

<span data-ttu-id="dbb28-105">Обучение модели для обнаружения объектов с нуля предусматривает настройку нескольких миллионов параметров, а также использование больших объемов помеченных данных обучения и вычислительных ресурсов (сотни часов работы GPU).</span><span class="sxs-lookup"><span data-stu-id="dbb28-105">Training an object detection model from scratch requires setting millions of parameters, a large amount of labeled training data and a vast amount of compute resources (hundreds of GPU hours).</span></span> <span data-ttu-id="dbb28-106">Использование предварительно обученной модели позволяет упростить процесс обучения.</span><span class="sxs-lookup"><span data-stu-id="dbb28-106">Using a pre-trained model allows you to shortcut the training process.</span></span>

<span data-ttu-id="dbb28-107">В этом руководстве вы узнаете, как:</span><span class="sxs-lookup"><span data-stu-id="dbb28-107">In this tutorial, you learn how to:</span></span>
> [!div class="checklist"]
> * <span data-ttu-id="dbb28-108">Определение проблемы</span><span class="sxs-lookup"><span data-stu-id="dbb28-108">Understand the problem</span></span>
> * <span data-ttu-id="dbb28-109">Узнайте, что такое ONNX и как он работает с ML.NET</span><span class="sxs-lookup"><span data-stu-id="dbb28-109">Learn what ONNX is and how it works with ML.NET</span></span>
> * <span data-ttu-id="dbb28-110">Общие сведения о модели</span><span class="sxs-lookup"><span data-stu-id="dbb28-110">Understand the model</span></span>
> * <span data-ttu-id="dbb28-111">Повторное использование предварительно обученной модели</span><span class="sxs-lookup"><span data-stu-id="dbb28-111">Reuse the pre-trained model</span></span>
> * <span data-ttu-id="dbb28-112">Обнаружение объектов в загруженной модели</span><span class="sxs-lookup"><span data-stu-id="dbb28-112">Detect objects with a loaded model</span></span>

## <a name="pre-requisites"></a><span data-ttu-id="dbb28-113">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="dbb28-113">Pre-requisites</span></span>

- <span data-ttu-id="dbb28-114">[Visual Studio 2017 15.6 или более поздней версии](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2017) с установленной рабочей нагрузкой "Кроссплатформенная разработка .NET Core".</span><span class="sxs-lookup"><span data-stu-id="dbb28-114">[Visual Studio 2017 15.6 or later](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2017) with the ".NET Core cross-platform development" workload installed.</span></span>
- [<span data-ttu-id="dbb28-115">Пакет Nuget Microsoft.ML</span><span class="sxs-lookup"><span data-stu-id="dbb28-115">Microsoft.ML NuGet Package</span></span>](https://www.nuget.org/packages/Microsoft.ML/)
- [<span data-ttu-id="dbb28-116">Пакет Nuget Microsoft.ML.ImageAnalytics</span><span class="sxs-lookup"><span data-stu-id="dbb28-116">Microsoft.ML.ImageAnalytics NuGet Package</span></span>](https://www.nuget.org/packages/Microsoft.ML.ImageAnalytics/)
- [<span data-ttu-id="dbb28-117">Пакет NuGet Microsoft.ML.OnnxTransformer</span><span class="sxs-lookup"><span data-stu-id="dbb28-117">Microsoft.ML.OnnxTransformer NuGet Package</span></span>](https://www.nuget.org/packages/Microsoft.ML.OnnxTransformer/)
- [<span data-ttu-id="dbb28-118">Предварительно обученная модель Tiny YOLOv2</span><span class="sxs-lookup"><span data-stu-id="dbb28-118">Tiny YOLOv2 pre-trained model</span></span>](https://github.com/onnx/models/tree/master/tiny_yolov2)
- <span data-ttu-id="dbb28-119">[Netron](https://github.com/lutzroeder/netron) (необязательно)</span><span class="sxs-lookup"><span data-stu-id="dbb28-119">[Netron](https://github.com/lutzroeder/netron) (optional)</span></span>

## <a name="onnx-object-detection-sample-overview"></a><span data-ttu-id="dbb28-120">Обзор примера обнаружения объектов в ONNX</span><span class="sxs-lookup"><span data-stu-id="dbb28-120">ONNX object detection sample overview</span></span>

<span data-ttu-id="dbb28-121">В этом примере создается консольное приложение .NET Core, которое обнаруживает объекты на изображении с помощью предварительно обученной модели ONNX для глубокого обучения.</span><span class="sxs-lookup"><span data-stu-id="dbb28-121">This sample creates a .NET core console application that detects objects within an image using a pre-trained deep learning ONNX model.</span></span> <span data-ttu-id="dbb28-122">Код для этого примера можно найти в репозитории [dotnet/machinelearning-samples](https://github.com/dotnet/machinelearning-samples/tree/master/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx) на сайте GitHub.</span><span class="sxs-lookup"><span data-stu-id="dbb28-122">The code for this sample can be found on the [dotnet/machinelearning-samples repository](https://github.com/dotnet/machinelearning-samples/tree/master/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx) on GitHub.</span></span>

## <a name="what-is-object-detection"></a><span data-ttu-id="dbb28-123">Что такое обнаружение объектов?</span><span class="sxs-lookup"><span data-stu-id="dbb28-123">What is object detection?</span></span>

<span data-ttu-id="dbb28-124">Обнаружение объектов — это задача в области компьютерного зрения.</span><span class="sxs-lookup"><span data-stu-id="dbb28-124">Object detection is a computer vision problem.</span></span> <span data-ttu-id="dbb28-125">Несмотря на связь с классификацией изображений, обнаружение объектов выполняет классификацию изображений в более детализированном масштабе.</span><span class="sxs-lookup"><span data-stu-id="dbb28-125">While closely related to image classification, object detection performs image classification at a more granular scale.</span></span> <span data-ttu-id="dbb28-126">Обнаружение объектов находит _и_ категоризует сущности на изображениях.</span><span class="sxs-lookup"><span data-stu-id="dbb28-126">Object detection both locates _and_ categorizes entities within images.</span></span> <span data-ttu-id="dbb28-127">Используйте обнаружение объектов, если изображения содержат несколько объектов разных типов.</span><span class="sxs-lookup"><span data-stu-id="dbb28-127">Use object detection when images contain multiple objects of different types.</span></span>

![](./media/object-detection-onnx/img-classification-obj-detection.PNG)

<span data-ttu-id="dbb28-128">Некоторые варианты применения обнаружения объектов:</span><span class="sxs-lookup"><span data-stu-id="dbb28-128">Some use cases for object detection include:</span></span>

- <span data-ttu-id="dbb28-129">Автомобили с автономным управлением</span><span class="sxs-lookup"><span data-stu-id="dbb28-129">Self-Driving Cars</span></span>
- <span data-ttu-id="dbb28-130">Робототехника</span><span class="sxs-lookup"><span data-stu-id="dbb28-130">Robotics</span></span>
- <span data-ttu-id="dbb28-131">Обнаружение лиц</span><span class="sxs-lookup"><span data-stu-id="dbb28-131">Face Detection</span></span>
- <span data-ttu-id="dbb28-132">Техника безопасности</span><span class="sxs-lookup"><span data-stu-id="dbb28-132">Workplace Safety</span></span>
- <span data-ttu-id="dbb28-133">Подсчет объектов</span><span class="sxs-lookup"><span data-stu-id="dbb28-133">Object Counting</span></span>
- <span data-ttu-id="dbb28-134">Распознавание активности</span><span class="sxs-lookup"><span data-stu-id="dbb28-134">Activity Recognition</span></span>

## <a name="select-a-deep-learning-model"></a><span data-ttu-id="dbb28-135">Выбор модели глубокого обучения</span><span class="sxs-lookup"><span data-stu-id="dbb28-135">Select a deep learning model</span></span>

<span data-ttu-id="dbb28-136">Глубокое обучение — это подмножество машинного обучения.</span><span class="sxs-lookup"><span data-stu-id="dbb28-136">Deep learning is a subset of machine learning.</span></span> <span data-ttu-id="dbb28-137">Для обучения моделей глубокого обучения требуются большие объемы данных.</span><span class="sxs-lookup"><span data-stu-id="dbb28-137">To train deep learning models, large quantities of data are required.</span></span> <span data-ttu-id="dbb28-138">Закономерности в данных представлены рядом слоев.</span><span class="sxs-lookup"><span data-stu-id="dbb28-138">Patterns in the data are represented by a series of layers.</span></span> <span data-ttu-id="dbb28-139">Связи в данных кодируются как соединения между слоями, имеющие веса.</span><span class="sxs-lookup"><span data-stu-id="dbb28-139">The relationships in the data are encoded as connections between the layers containing weights.</span></span> <span data-ttu-id="dbb28-140">Чем выше вес, тем сильнее связь.</span><span class="sxs-lookup"><span data-stu-id="dbb28-140">The higher the weight, the stronger the relationship.</span></span> <span data-ttu-id="dbb28-141">В совокупности этот ряд уровней и соединений называют искусственными нейронными сетями.</span><span class="sxs-lookup"><span data-stu-id="dbb28-141">Collectively, this series of layers and connections are known as artificial neural networks.</span></span> <span data-ttu-id="dbb28-142">Чем больше уровней в сети, тем глубже нейронная сеть.</span><span class="sxs-lookup"><span data-stu-id="dbb28-142">The more layers in a network, the "deeper" it is, making it a deep neural network.</span></span> 

<span data-ttu-id="dbb28-143">Существуют различные типы нейронных сетей; наиболее распространенные — многоуровневый перцептрон (MLP), сверточная нейронная сеть (CNN) и рекуррентная нейронная сеть (RNN).</span><span class="sxs-lookup"><span data-stu-id="dbb28-143">There are different types of neural networks, the most common being Multi-Layered Perceptron (MLP), Convolutional Neural Network (CNN) and Recurrent Neural Network (RNN).</span></span> <span data-ttu-id="dbb28-144">Самым простым вариантом является MLP, который сопоставляет набор входных данных с набором выходов.</span><span class="sxs-lookup"><span data-stu-id="dbb28-144">The most basic is the MLP, which maps a set of inputs to a set of outputs.</span></span> <span data-ttu-id="dbb28-145">Эта нейронная сеть хорошо подходит, когда в данных отсутствует пространственный или временный компонент.</span><span class="sxs-lookup"><span data-stu-id="dbb28-145">This neural network is good when the data does not have a spatial or time component.</span></span> <span data-ttu-id="dbb28-146">CNN использует слои свертки для обработки пространственных данных, содержащихся в данных.</span><span class="sxs-lookup"><span data-stu-id="dbb28-146">The CNN makes use of convolutional layers to process spatial information contained in the data.</span></span> <span data-ttu-id="dbb28-147">Хорошим вариантом использования CNN является обработка изображений, позволяющая обнаружить присутствие некой черты в определенном регионе изображения (например, есть ли нос в центре изображения).</span><span class="sxs-lookup"><span data-stu-id="dbb28-147">A good use case for CNNs is image processing to detect the presence of a feature in a region of an image (for example, is there a nose in the center of an image?).</span></span> <span data-ttu-id="dbb28-148">Наконец, RNN позволяет использовать сохраняемость состояния или памяти, используемых в качестве входных данных.</span><span class="sxs-lookup"><span data-stu-id="dbb28-148">Finally, RNNs allow for the persistence of state or memory to be used as input.</span></span> <span data-ttu-id="dbb28-149">RNN используются для анализа временных рядов, где важны упорядочение и контекст событий.</span><span class="sxs-lookup"><span data-stu-id="dbb28-149">RNNs are used for time-series analysis, where the sequential ordering and context of events is important.</span></span> 

### <a name="understand-the-model"></a><span data-ttu-id="dbb28-150">Общие сведения о модели</span><span class="sxs-lookup"><span data-stu-id="dbb28-150">Understand the model</span></span>

<span data-ttu-id="dbb28-151">Обнаружение объектов — это задача обработки изображений.</span><span class="sxs-lookup"><span data-stu-id="dbb28-151">Object detection is an image processing task.</span></span> <span data-ttu-id="dbb28-152">Поэтому большинство моделей глубокого обучения, предназначенных для решения этой проблемы, относятся к CNN.</span><span class="sxs-lookup"><span data-stu-id="dbb28-152">Therefore, most deep learning models trained to solve this problem are CNNs.</span></span> <span data-ttu-id="dbb28-153">Модель, используемая в этом руководстве, — Tiny YOLOv2, более компактная версия модели YOLOv2, описанной в документе ["YOLO9000: Лучше, быстрее и надежнее" (Редмон, Фадхари)](https://arxiv.org/pdf/1612.08242.pdf).</span><span class="sxs-lookup"><span data-stu-id="dbb28-153">The model used in this tutorial is the Tiny YOLOv2 model, a more compact version of the YOLOv2 model described in the paper: ["YOLO9000: Better, Faster, Stronger" by Redmon and Fadhari](https://arxiv.org/pdf/1612.08242.pdf).</span></span> <span data-ttu-id="dbb28-154">Модель Tiny YOLOv2 обучена на наборе данных Pascal ВОК и состоит из 15 уровней, которые могут прогнозировать 20 различных классов объектов.</span><span class="sxs-lookup"><span data-stu-id="dbb28-154">Tiny YOLOv2 is trained on the Pascal VOC dataset and is made up of 15 layers that can predict 20 different classes of objects.</span></span> <span data-ttu-id="dbb28-155">Так как Tiny YOLOv2 является сжатой версией исходной модели YOLOv2, между скоростью и точностью есть компромисс.</span><span class="sxs-lookup"><span data-stu-id="dbb28-155">Because Tiny YOLOv2 is a condensed version of the original YOLOv2 model, a tradeoff is made between speed and accuracy.</span></span> <span data-ttu-id="dbb28-156">Различные слои, составляющие модель, можно использовать для визуализации с помощью таких средств, как Netron.</span><span class="sxs-lookup"><span data-stu-id="dbb28-156">The different layers that make up the model can be visualized using tools like Netron.</span></span> <span data-ttu-id="dbb28-157">Изучение модели приведет к сопоставлению соединений между всеми слоями, которые составляют нейронную сеть, где каждый слой будет содержать имя слоя вместе с размерами соответствующих входных и выходных данных.</span><span class="sxs-lookup"><span data-stu-id="dbb28-157">Inspecting the model would yield a mapping of the connections between all the layers that make up the neural network, where each layer would contain the name of the layer along with the dimensions of the respective input / output.</span></span> <span data-ttu-id="dbb28-158">Структуры данных, используемые для описания входных и выходных данных модели, называются тензорами.</span><span class="sxs-lookup"><span data-stu-id="dbb28-158">The data structures used to describe the inputs and outputs of the model are known as tensors.</span></span> <span data-ttu-id="dbb28-159">Их можно рассматривать как контейнеры, в которых данные хранятся в N измерениях.</span><span class="sxs-lookup"><span data-stu-id="dbb28-159">Tensors can be thought of as containers that store data in N-dimensions.</span></span> <span data-ttu-id="dbb28-160">В случае Tiny YOLOv2 имя входного слоя — `image`, и он ожидает `3 x 416 x 416` измерений в тензоре.</span><span class="sxs-lookup"><span data-stu-id="dbb28-160">In the case of Tiny YOLOv2, the name of the input layer is `image` and it expects a tensor of dimensions `3 x 416 x 416`.</span></span> <span data-ttu-id="dbb28-161">Имя выходного слоя — `grid`; он создает выходной тензор из `125 x 13 x 13` измерений.</span><span class="sxs-lookup"><span data-stu-id="dbb28-161">The name of the output layer is `grid` and generates an output tensor of dimensions `125 x 13 x 13`.</span></span>  

![](./media/object-detection-onnx/netron-model-map.png)

<span data-ttu-id="dbb28-162">Модель YOLO принимает изображение `3(RGB) x 416px x 416px`.</span><span class="sxs-lookup"><span data-stu-id="dbb28-162">The YOLO model takes an image `3(RGB) x 416px x 416px`.</span></span> <span data-ttu-id="dbb28-163">Модель принимает эти входные данные и передает их через различные слои для создания выходных данных.</span><span class="sxs-lookup"><span data-stu-id="dbb28-163">The model takes this input and passes it through the different layers to produce an output.</span></span> <span data-ttu-id="dbb28-164">Выходные данные делят входное изображение на сетку `13 x 13`, при этом каждая ячейка в сетке состоит из значений `125`.</span><span class="sxs-lookup"><span data-stu-id="dbb28-164">The output divides the input image into a `13 x 13` grid, with each cell in the grid consisting of `125` values.</span></span> 

### <a name="what-is-an-onnx-model"></a><span data-ttu-id="dbb28-165">Что такое модель ONNX?</span><span class="sxs-lookup"><span data-stu-id="dbb28-165">What is an ONNX model?</span></span>

<span data-ttu-id="dbb28-166">Open Neural Network Exchange (ONNX) — это формат с открытым исходным кодом для моделей ИИ.</span><span class="sxs-lookup"><span data-stu-id="dbb28-166">The Open Neural Network Exchange (ONNX) is an open source format for AI models.</span></span> <span data-ttu-id="dbb28-167">ONNX поддерживает взаимодействие между разными платформами.</span><span class="sxs-lookup"><span data-stu-id="dbb28-167">ONNX supports interoperability between frameworks.</span></span> <span data-ttu-id="dbb28-168">Это означает, что модель можно обучить в одной из многих популярных платформ машинного обучения, таких как PyTorch, преобразовать ее в формат ONNX и использовать модель ONNX в другой инфраструктуре, такой как ML.NET.</span><span class="sxs-lookup"><span data-stu-id="dbb28-168">This means you can train a model in one of the many popular machine learning frameworks like PyTorch, convert it into ONNX format and consume the ONNX model in a different framework like ML.NET.</span></span> <span data-ttu-id="dbb28-169">Дополнительные сведения см. на [веб-сайте ONNX](https://onnx.ai/).</span><span class="sxs-lookup"><span data-stu-id="dbb28-169">To learn more, visit the [ONNX website](https://onnx.ai/).</span></span> 

![](./media/object-detection-onnx/onnx-frameworks.png)

<span data-ttu-id="dbb28-170">Предварительно обученная модель Tiny YOLOv2 хранится в формате ONNX: сериализованном представлении слоев и полученных закономерностях этих слоев.</span><span class="sxs-lookup"><span data-stu-id="dbb28-170">The pre-trained Tiny YOLOv2 model is stored in ONNX format, a serialized representation of the layers and learned patterns of those layers.</span></span> <span data-ttu-id="dbb28-171">В ML.NET взаимодействие с ONNX достигается с помощью пакетов NuGet [`ImageAnalytics`](xref:Microsoft.ML.Transforms.Image) и [`OnnxTransformer`](xref:Microsoft.ML.Transforms.Onnx.OnnxTransformer).</span><span class="sxs-lookup"><span data-stu-id="dbb28-171">In ML.NET, interoperability with ONNX is achieved with the [`ImageAnalytics`](xref:Microsoft.ML.Transforms.Image) and [`OnnxTransformer`](xref:Microsoft.ML.Transforms.Onnx.OnnxTransformer) NuGet packages.</span></span> <span data-ttu-id="dbb28-172">Пакет [`ImageAnalytics`](xref:Microsoft.ML.Transforms.Image) содержит ряд преобразований, которые принимают изображение и кодируют его в числовые значения, которые можно использовать в качестве входных данных в конвейере прогнозирования или обучения.</span><span class="sxs-lookup"><span data-stu-id="dbb28-172">The [`ImageAnalytics`](xref:Microsoft.ML.Transforms.Image) package contains a series of transforms that take an image and encode it into numerical values that can be used as input into a prediction or training pipeline.</span></span> <span data-ttu-id="dbb28-173">Пакет [`OnnxTransformer`](xref:Microsoft.ML.Transforms.Onnx.OnnxTransformer) использует среду выполнения ONNX для загрузки модели ONNX и использует ее для создания прогнозов на основе предоставленных входных данных.</span><span class="sxs-lookup"><span data-stu-id="dbb28-173">The [`OnnxTransformer`](xref:Microsoft.ML.Transforms.Onnx.OnnxTransformer) package leverages the ONNX Runtime to load an ONNX model and use it to make predictions based on input provided.</span></span> 

![](./media/object-detection-onnx/onnx-ml-net-integration.png)

## <a name="set-up-the-net-core-project"></a><span data-ttu-id="dbb28-174">Создание проекта .NET Core</span><span class="sxs-lookup"><span data-stu-id="dbb28-174">Set up the .NET Core project</span></span>

<span data-ttu-id="dbb28-175">Теперь, когда у вас есть общее представление о том, что такое ONNX и как работает Tiny YOLOv2, пришло время создать приложение.</span><span class="sxs-lookup"><span data-stu-id="dbb28-175">Now that you have a general understanding of what ONNX is and how Tiny YOLOv2 works, it's time to build the application.</span></span>

### <a name="create-a-console-application"></a><span data-ttu-id="dbb28-176">Создание консольного приложения</span><span class="sxs-lookup"><span data-stu-id="dbb28-176">Create a console application</span></span>

1. <span data-ttu-id="dbb28-177">Создайте **консольное приложение .NET Core** с именем ObjectDetection.</span><span class="sxs-lookup"><span data-stu-id="dbb28-177">Create a **.NET Core Console Application** called "ObjectDetection".</span></span>

1. <span data-ttu-id="dbb28-178">Установите **пакет NuGet для Microsoft.ML**:</span><span class="sxs-lookup"><span data-stu-id="dbb28-178">Install the **Microsoft.ML NuGet Package**:</span></span>

    - <span data-ttu-id="dbb28-179">В обозревателе решений щелкните проект правой кнопкой мыши и выберите **Управление пакетами NuGet**.</span><span class="sxs-lookup"><span data-stu-id="dbb28-179">In Solution Explorer, right-click on your project and select **Manage NuGet Packages**.</span></span> 
    - <span data-ttu-id="dbb28-180">Выберите nuget.org в качестве источника пакета, откройте вкладку "Обзор" и выполните поиск **Microsoft.ML**.</span><span class="sxs-lookup"><span data-stu-id="dbb28-180">Choose "nuget.org" as the Package source, select the Browse tab, search for **Microsoft.ML**.</span></span> 
    - <span data-ttu-id="dbb28-181">Нажмите кнопку **Установить**.</span><span class="sxs-lookup"><span data-stu-id="dbb28-181">Select the **Install** button.</span></span> 
    - <span data-ttu-id="dbb28-182">Нажмите кнопку **ОК** в диалоговом окне **Предварительный просмотр изменений**, а затем нажмите кнопку **Принимаю** в диалоговом окне **Принятие условий лицензионного соглашения**, если вы согласны с указанными условиями лицензионного соглашения для выбранных пакетов.</span><span class="sxs-lookup"><span data-stu-id="dbb28-182">Select the **OK** button on the **Preview Changes** dialog and then select the **I Accept** button on the **License Acceptance** dialog if you agree with the license terms for the packages listed.</span></span> 
    - <span data-ttu-id="dbb28-183">Повторите эти шаги для пакетов **Microsoft.ML.ImageAnalytics** и **Microsoft.ML.OnnxTransformer**.</span><span class="sxs-lookup"><span data-stu-id="dbb28-183">Repeat these steps for **Microsoft.ML.ImageAnalytics** and **Microsoft.ML.OnnxTransformer**.</span></span>

### <a name="prepare-your-data-and-pre-trained-model"></a><span data-ttu-id="dbb28-184">Подготовка данных и предварительно обученной модели</span><span class="sxs-lookup"><span data-stu-id="dbb28-184">Prepare your data and pre-trained model</span></span>

1. <span data-ttu-id="dbb28-185">Скачайте [ZIP-файл каталога с ресурсами проекта](https://github.com/dotnet/machinelearning-samples/raw/master/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/assets.zip) и распакуйте его.</span><span class="sxs-lookup"><span data-stu-id="dbb28-185">Download [The project assets directory zip file](https://github.com/dotnet/machinelearning-samples/raw/master/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/assets.zip) and unzip.</span></span>

1. <span data-ttu-id="dbb28-186">Скопируйте каталог `assets` в каталог проекта *ObjectDetection*.</span><span class="sxs-lookup"><span data-stu-id="dbb28-186">Copy the `assets` directory into your *ObjectDetection* project directory.</span></span> <span data-ttu-id="dbb28-187">Этот каталог и его подкаталоги содержат файлы изображений (за исключением модели Tiny YOLOv2, которую вы скачаете и добавите на следующем шаге), необходимые для работы с этим руководством.</span><span class="sxs-lookup"><span data-stu-id="dbb28-187">This directory and its subdirectories contain the image files (except for the Tiny YOLOv2 model, which you'll download and add in the next step) needed for this tutorial.</span></span>

1. <span data-ttu-id="dbb28-188">Скачайте модель [Tiny YOLOv2](https://onnxzoo.blob.core.windows.net/models/opset_8/tiny_yolov2/tiny_yolov2.tar.gz) из репозитория [ONNX Model Zoo](https://github.com/onnx/models/tree/master/vision/object_detection_segmentation/tiny_yolov2) и распакуйте.</span><span class="sxs-lookup"><span data-stu-id="dbb28-188">Download the [Tiny YOLOv2 model](https://onnxzoo.blob.core.windows.net/models/opset_8/tiny_yolov2/tiny_yolov2.tar.gz) from the [ONNX Model Zoo](https://github.com/onnx/models/tree/master/vision/object_detection_segmentation/tiny_yolov2), and unzip.</span></span>

    <span data-ttu-id="dbb28-189">Введите приведенные ниже команды в окне командной строки.</span><span class="sxs-lookup"><span data-stu-id="dbb28-189">Open the command prompt and enter the following command:</span></span>

    ```shell
    tar -xvzf tiny_yolov2.tar.gz 
    ```

1. <span data-ttu-id="dbb28-190">Скопируйте извлеченный файл `model.onnx` из распакованного каталога в каталог `assets\Model` проекта *ObjectDetection* и переименуйте его в `TinyYolo2_model.onnx`.</span><span class="sxs-lookup"><span data-stu-id="dbb28-190">Copy the extracted `model.onnx` file from the directory just unzipped into your *ObjectDetection* project `assets\Model` directory and rename it to `TinyYolo2_model.onnx`.</span></span> <span data-ttu-id="dbb28-191">Этот каталог содержит модель, необходимую для работы с этим руководством.</span><span class="sxs-lookup"><span data-stu-id="dbb28-191">This directory contains the model needed for this tutorial.</span></span>

1. <span data-ttu-id="dbb28-192">В обозревателе решений щелкните правой кнопкой мыши каждый файл в каталоге и подкаталогах ресурсов и выберите **Свойства**.</span><span class="sxs-lookup"><span data-stu-id="dbb28-192">In Solution Explorer, right-click each of the files in the asset directory and subdirectories and select **Properties**.</span></span> <span data-ttu-id="dbb28-193">В разделе **Дополнительно** для параметра **Копировать в выходной каталог** установите значение **Копировать более позднюю версию**.</span><span class="sxs-lookup"><span data-stu-id="dbb28-193">Under **Advanced**, change the value of **Copy to Output Directory** to **Copy if newer**.</span></span>

### <a name="create-classes-and-define-paths"></a><span data-ttu-id="dbb28-194">Создание классов и определение путей</span><span class="sxs-lookup"><span data-stu-id="dbb28-194">Create classes and define paths</span></span>

<span data-ttu-id="dbb28-195">Добавьте следующие новые операторы `using` в начало файла *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="dbb28-195">Open the *Program.cs* file and add the following additional `using` statements to the top of the file:</span></span>

[!code-csharp [ProgramUsings](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/Program.cs#L1-L9)]

<span data-ttu-id="dbb28-196">Затем определите пути к различным ресурсам.</span><span class="sxs-lookup"><span data-stu-id="dbb28-196">Next, define the paths of the various assets.</span></span> 

1. <span data-ttu-id="dbb28-197">Сначала добавьте метод `GetAbsolutePath` под методом `Main` в классе `Program`.</span><span class="sxs-lookup"><span data-stu-id="dbb28-197">First, add the `GetAbsolutePath` method below the `Main` method in the `Program` class.</span></span> 

    [!code-csharp [GetAbsolutePath](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/Program.cs#L66-L74)]

1. <span data-ttu-id="dbb28-198">Затем внутри метода `Main` создайте поля для хранения расположения ресурсов:</span><span class="sxs-lookup"><span data-stu-id="dbb28-198">Then, inside the `Main` method, create fields to store the location of your assets:</span></span>

    [!code-csharp [AssetDefinition](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/Program.cs#L17-L21)]

<span data-ttu-id="dbb28-199">Добавьте в проект новый каталог для хранения входных данных и классов прогнозов.</span><span class="sxs-lookup"><span data-stu-id="dbb28-199">Add a new directory to your project to store your input data and prediction classes.</span></span>

<span data-ttu-id="dbb28-200">В **обозревателе решений** щелкните проект правой кнопкой мыши и выберите пункты **Добавить** > **Новая папка**.</span><span class="sxs-lookup"><span data-stu-id="dbb28-200">In **Solution Explorer**, right-click the project, and then select **Add** > **New Folder**.</span></span> <span data-ttu-id="dbb28-201">Когда новая папка появится в обозревателе решений, присвойте ей имя "DataStructures".</span><span class="sxs-lookup"><span data-stu-id="dbb28-201">When the new folder appears in the Solution Explorer, name it "DataStructures".</span></span>

<span data-ttu-id="dbb28-202">Создайте класс входных данных в только что созданном каталоге *DataStructures*.</span><span class="sxs-lookup"><span data-stu-id="dbb28-202">Create your input data class in the newly created *DataStructures* directory.</span></span>

1. <span data-ttu-id="dbb28-203">В **обозревателе решений** щелкните правой кнопкой мыши папку *DataStructures* и выберите **Добавить** > **Новый элемент**.</span><span class="sxs-lookup"><span data-stu-id="dbb28-203">In **Solution Explorer**, right-click the *DataStructures* directory, and then select **Add** > **New Item**.</span></span>
1. <span data-ttu-id="dbb28-204">В диалоговом окне **Добавление нового элемента** выберите **Класс** и измените значение поля **Имя** на *ImageNetData.cs*.</span><span class="sxs-lookup"><span data-stu-id="dbb28-204">In the **Add New Item** dialog box, select **Class** and change the **Name** field to *ImageNetData.cs*.</span></span> <span data-ttu-id="dbb28-205">Теперь нажмите кнопку **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="dbb28-205">Then, select the **Add** button.</span></span>
     
    <span data-ttu-id="dbb28-206">Файл *ImageNetData.cs* откроется в редакторе кода.</span><span class="sxs-lookup"><span data-stu-id="dbb28-206">The *ImageNetData.cs* file opens in the code editor.</span></span> <span data-ttu-id="dbb28-207">Добавьте следующую инструкцию `using` в начало файла *ImageNetData.cs*:</span><span class="sxs-lookup"><span data-stu-id="dbb28-207">Add the following `using` statement to the top of *ImageNetData.cs*:</span></span>

    [!code-csharp [ImageNetDataUsings](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/DataStructures/ImageNetData.cs#L1-L4)]

    <span data-ttu-id="dbb28-208">Удалите существующее определение класса и добавьте следующий код для класса `ImageNetData` в файл *ImageNetData.cs*:</span><span class="sxs-lookup"><span data-stu-id="dbb28-208">Remove the existing class definition and add the following code for the `ImageNetData` class to the *ImageNetData.cs* file:</span></span>
    
    [!code-csharp [ImageNetDataClass](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/DataStructures/ImageNetData.cs#L8-L23)]

    <span data-ttu-id="dbb28-209">`ImageNetData` является классом входных данных изображения и имеет следующие поля <xref:System.String>:</span><span class="sxs-lookup"><span data-stu-id="dbb28-209">`ImageNetData` is the input image data class and has the following <xref:System.String> fields:</span></span>

    - <span data-ttu-id="dbb28-210">`ImagePath` содержит путь, по которому хранится изображение.</span><span class="sxs-lookup"><span data-stu-id="dbb28-210">`ImagePath` contains the path where the image is stored.</span></span>
    - <span data-ttu-id="dbb28-211">`Label` содержит имя файла.</span><span class="sxs-lookup"><span data-stu-id="dbb28-211">`Label` contains the name of the file.</span></span>

    <span data-ttu-id="dbb28-212">Кроме того, `ImageNetData` содержит метод `ReadFromFile`, который загружает несколько файлов изображений, хранящихся по указанном пути `imageFolder`, и возвращает их в виде коллекции объектов `ImageNetData`.</span><span class="sxs-lookup"><span data-stu-id="dbb28-212">Additionally, `ImageNetData` contains a method `ReadFromFile` which loads multiple image files stored in the `imageFolder` path specified and returns them as a collection of `ImageNetData` objects.</span></span>

<span data-ttu-id="dbb28-213">Создайте класс прогноза в каталоге структур структуры *DataStructures*.</span><span class="sxs-lookup"><span data-stu-id="dbb28-213">Create your prediction class in the *DataStructures* directory.</span></span>

1. <span data-ttu-id="dbb28-214">В **обозревателе решений** щелкните правой кнопкой мыши папку *DataStructures* и выберите **Добавить** > **Новый элемент**.</span><span class="sxs-lookup"><span data-stu-id="dbb28-214">In **Solution Explorer**, right-click the *DataStructures* directory, and then select **Add** > **New Item**.</span></span>
1. <span data-ttu-id="dbb28-215">В диалоговом окне **Добавление нового элемента** выберите **Класс** и измените значение поля **Имя** на *ImageNetPrediction.cs*.</span><span class="sxs-lookup"><span data-stu-id="dbb28-215">In the **Add New Item** dialog box, select **Class** and change the **Name** field to *ImageNetPrediction.cs*.</span></span> <span data-ttu-id="dbb28-216">Теперь нажмите кнопку **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="dbb28-216">Then, select the **Add** button.</span></span>

    <span data-ttu-id="dbb28-217">Файл *ImageNetPrediction.cs* откроется в редакторе кода.</span><span class="sxs-lookup"><span data-stu-id="dbb28-217">The *ImageNetPrediction.cs* file opens in the code editor.</span></span> <span data-ttu-id="dbb28-218">Добавьте следующую инструкцию `using` в начало файла *ImageNetPrediction.cs*:</span><span class="sxs-lookup"><span data-stu-id="dbb28-218">Add the following `using` statement to the top of *ImageNetPrediction.cs*:</span></span>

    [!code-csharp [ImageNetPredictionUsings](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/DataStructures/ImageNetPrediction.cs#L1)]

    <span data-ttu-id="dbb28-219">Удалите существующее определение класса и добавьте следующий код для класса `ImageNetPrediction` в файл *ImageNetPrediction.cs*:</span><span class="sxs-lookup"><span data-stu-id="dbb28-219">Remove the existing class definition and add the following code for the `ImageNetPrediction` class to the *ImageNetPrediction.cs* file:</span></span>

    [!code-csharp [ImageNetPredictionClass](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/DataStructures/ImageNetPrediction.cs#L5-L9)]

    <span data-ttu-id="dbb28-220">`ImageNetPrediction` является классом прогноза данных и имеет следующее поле `float[]`:</span><span class="sxs-lookup"><span data-stu-id="dbb28-220">`ImageNetPrediction` is the prediction data class and has the following `float[]` field:</span></span>

    - <span data-ttu-id="dbb28-221">`PredictedLabel` содержит измерения, оценку объекта и вероятности класса для каждого ограничивающего прямоугольника, обнаруженного в изображении.</span><span class="sxs-lookup"><span data-stu-id="dbb28-221">`PredictedLabel` contains the dimensions, objectness score and class probabilities for each of the bounding boxes detected in an image.</span></span>

### <a name="initialize-variables-in-main"></a><span data-ttu-id="dbb28-222">Инициализация переменных в методе Main</span><span class="sxs-lookup"><span data-stu-id="dbb28-222">Initialize variables in Main</span></span>

<span data-ttu-id="dbb28-223">[Класс MLContext](xref:Microsoft.ML.MLContext) является отправной точкой для любых операций ML.NET. В результате инициализации класса `mlContext` создается среда ML.NET, которая может использоваться всеми объектами в рамках процесса создания модели.</span><span class="sxs-lookup"><span data-stu-id="dbb28-223">The [MLContext class](xref:Microsoft.ML.MLContext) is a starting point for all ML.NET operations, and initializing `mlContext` creates a new ML.NET environment that can be shared across the model creation workflow objects.</span></span> <span data-ttu-id="dbb28-224">По существу он аналогичен классу `DBContext` в Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="dbb28-224">It's similar, conceptually, to `DBContext` in Entity Framework.</span></span>

<span data-ttu-id="dbb28-225">Инициализируйте переменную `mlContext` новым экземпляром `MLContext`, добавив следующую строку в метод `Main` в файле *Program.cs* под полем `outputFolder`.</span><span class="sxs-lookup"><span data-stu-id="dbb28-225">Initialize the `mlContext` variable with a new instance of `MLContext` by adding the following line to the `Main` method of *Program.cs* below the `outputFolder` field.</span></span>

[!code-csharp [InitMLContext](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/Program.cs#L24)]

### <a name="add-helper-methods"></a><span data-ttu-id="dbb28-226">Добавление вспомогательных методов</span><span class="sxs-lookup"><span data-stu-id="dbb28-226">Add Helper Methods</span></span>

<span data-ttu-id="dbb28-227">После того как модель предоставила прогноз, обычно называемый оценкой, и выходные данные обработаны, ограничивающие прямоугольники должны быть выведены на изображении.</span><span class="sxs-lookup"><span data-stu-id="dbb28-227">After the model has made a prediction, commonly referred to as scoring, and the outputs have been processed, the bounding boxes have to be drawn on the image.</span></span> <span data-ttu-id="dbb28-228">Для этого добавьте метод `DrawBoundingBox` под методом `GetAbsolutePath` в *Program.cs*.</span><span class="sxs-lookup"><span data-stu-id="dbb28-228">To do so, add a method called `DrawBoundingBox` below the `GetAbsolutePath` method insode of *Program.cs*.</span></span>

```csharp
private static void DrawBoundingBox(string inputImageLocation, string outputImageLocation, string imageName, IList<YoloBoundingBox> filteredBoundingBoxes)
{

}
```

<span data-ttu-id="dbb28-229">Сначала загрузите изображение и получите измерения Height и Width в методе `DrawBoundingBox`.</span><span class="sxs-lookup"><span data-stu-id="dbb28-229">First, load the image and get the height and width dimensions in the `DrawBoundingBox` method.</span></span>

[!code-csharp [LoadImage](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/Program.cs#L78-L81)]

<span data-ttu-id="dbb28-230">Затем создайте цикл for-each для прохода по каждому ограничивающему прямоугольнику, обнаруженному моделью.</span><span class="sxs-lookup"><span data-stu-id="dbb28-230">Then, create a for-each loop to iterate over each of the bounding boxes detected by the model.</span></span>

```csharp
foreach (var box in filteredBoundingBoxes)
{

}
```

<span data-ttu-id="dbb28-231">В цикле for-each получите размеры ограничивающего прямоугольника.</span><span class="sxs-lookup"><span data-stu-id="dbb28-231">Inside of the for-each loop, get the dimensions of the bounding box.</span></span>

[!code-csharp [GetBBoxDimensions](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/Program.cs#L86-L89)]

<span data-ttu-id="dbb28-232">Поскольку размеры ограничивающего прямоугольника соответствуют входным данным модели `416 x 416`, масштабируйте размеры ограничивающего прямоугольника в соответствии с фактическим размером изображения.</span><span class="sxs-lookup"><span data-stu-id="dbb28-232">Because the dimensions of the bounding box correspond to the model input of `416 x 416`, scale the bounding box dimensions to match the actual size of the image.</span></span>

[!code-csharp [ScaleImage](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/Program.cs#L92-L95)]

<span data-ttu-id="dbb28-233">Затем определите шаблон для текста, который будет отображен над каждым ограничивающим прямоугольником.</span><span class="sxs-lookup"><span data-stu-id="dbb28-233">Then, define a template for text that will apear above each bounding box.</span></span> <span data-ttu-id="dbb28-234">Текст будет содержать класс объекта внутри соответствующего ограничивающего прямоугольника, а также его достоверность.</span><span class="sxs-lookup"><span data-stu-id="dbb28-234">The text will contain the class of the object inside of the respective bounding box as well as the confidence.</span></span>

[!code-csharp [DefineBBoxText](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/Program.cs#L98)]

<span data-ttu-id="dbb28-235">Чтобы нарисовать что-то на изображении, преобразуйте его в объект [`Graphics`](xref:System.Drawing.Graphics).</span><span class="sxs-lookup"><span data-stu-id="dbb28-235">In order to draw on the image, convert it to a [`Graphics`](xref:System.Drawing.Graphics) object.</span></span>

```csharp
using (Graphics thumbnailGraphic = Graphics.FromImage(image))
{
    
}
```

<span data-ttu-id="dbb28-236">Внутри блока кода `using` настройте параметры графического объекта [`Graphics`](xref:System.Drawing.Graphics).</span><span class="sxs-lookup"><span data-stu-id="dbb28-236">Inside the `using` code block, tune the graphic's [`Graphics`](xref:System.Drawing.Graphics) object settings.</span></span>

[!code-csharp [TuneGraphicSettings](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/Program.cs#L102-L104)]

<span data-ttu-id="dbb28-237">Ниже задайте параметры шрифта и цвета для текста и ограничивающего прямоугольника.</span><span class="sxs-lookup"><span data-stu-id="dbb28-237">Below that, set the font and color options for the text and bounding box.</span></span>

[!code-csharp [SetColorOptions](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/Program.cs#L106-L114)]

<span data-ttu-id="dbb28-238">Создайте и заполните прямоугольник над ограничивающей рамкой, которая будет содержать текст, с помощью метода [`FillRectangle`](xref:System.Drawing.Graphics.FillRectangle*).</span><span class="sxs-lookup"><span data-stu-id="dbb28-238">Create and fill a rectangle above the bounding box to contain the text using the [`FillRectangle`](xref:System.Drawing.Graphics.FillRectangle*) method.</span></span> <span data-ttu-id="dbb28-239">Это поможет выделить текст и улучшить удобочитаемость.</span><span class="sxs-lookup"><span data-stu-id="dbb28-239">This will help contrast the text and improve readability.</span></span>

[!code-csharp [DrawTextBackground](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/Program.cs#L117)]

<span data-ttu-id="dbb28-240">Затем нарисуйте текст и ограничивающий прямоугольник на изображении с помощью методов [`DrawString`](xref:System.Drawing.Graphics.DrawString*) и [`DrawRectangle`](xref:System.Drawing.Graphics.DrawRectangle*).</span><span class="sxs-lookup"><span data-stu-id="dbb28-240">Then, Draw the text and bounding box on the image using the [`DrawString`](xref:System.Drawing.Graphics.DrawString*) and [`DrawRectangle`](xref:System.Drawing.Graphics.DrawRectangle*) methods.</span></span>

[!code-csharp [DrawClassAndBBox](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/Program.cs#L118-L121)]

<span data-ttu-id="dbb28-241">За пределами цикла for-each добавьте код для сохранения изображений в `outputDirectory`.</span><span class="sxs-lookup"><span data-stu-id="dbb28-241">Outside of the for-each loop, add code to save the images in the `outputDirectory`.</span></span>

[!code-csharp [SaveImage](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/Program.cs#L125-L130)]

<span data-ttu-id="dbb28-242">Чтобы получить дополнительные данные о том, что приложение делает ожидаемые прогнозы во время выполнения, добавьте метод `LogDetectedObjects` под методом `DrawBoundingBox` в файле *Program.cs* для вывода обнаруженных объектов на консоль.</span><span class="sxs-lookup"><span data-stu-id="dbb28-242">To get additional feedback that the application is making predictions as expected at runtime, add a method called `LogDetectedObjects` below the `DrawBoundingBox` method in the *Program.cs* file to output the detected objects to the console.</span></span>

[!code-csharp [LogOuptuts](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/Program.cs#L133-L143)]

<span data-ttu-id="dbb28-243">Оба эти метода будут полезны, когда модель выдала выходные данные и они обработались.</span><span class="sxs-lookup"><span data-stu-id="dbb28-243">Both of these methods will be useful when the model has produced outputs and those have been processed.</span></span> <span data-ttu-id="dbb28-244">Сначала же создайте функциональные возможности для обработки выходных данных модели.</span><span class="sxs-lookup"><span data-stu-id="dbb28-244">First though, create the functionality to process the model outputs.</span></span>

## <a name="create-a-parser-to-post-process-model-outputs"></a><span data-ttu-id="dbb28-245">Создание средства анализа для выходных данных модели после обработки</span><span class="sxs-lookup"><span data-stu-id="dbb28-245">Create a parser to post-process model outputs</span></span>

<span data-ttu-id="dbb28-246">Модель разделяет изображение на сетку `13 x 13`, где каждая ячейка сетки — это `32px x 32px`.</span><span class="sxs-lookup"><span data-stu-id="dbb28-246">The model segments an image into a `13 x 13` grid, where each grid cell is `32px x 32px`.</span></span> <span data-ttu-id="dbb28-247">Каждая ячейка сетки содержит 5 возможных ограничивающих прямоугольников объекта.</span><span class="sxs-lookup"><span data-stu-id="dbb28-247">Each grid cell contains 5 potential object bounding boxes.</span></span> <span data-ttu-id="dbb28-248">Ограничивающий прямоугольник содержит 25 элементов:</span><span class="sxs-lookup"><span data-stu-id="dbb28-248">A bounding box has  25 elements:</span></span>

![](./media/object-detection-onnx/model-output-description.png)

- <span data-ttu-id="dbb28-249">`x` — координата по оси X для центра ограничивающего прямоугольника относительно ячейки сетки, с которой он связан.</span><span class="sxs-lookup"><span data-stu-id="dbb28-249">`x` the x position of the bounding box center relative to the grid cell it's associated with.</span></span>
- <span data-ttu-id="dbb28-250">`y` — координата по оси Y для центра ограничивающего прямоугольника относительно ячейки сетки, с которой он связан.</span><span class="sxs-lookup"><span data-stu-id="dbb28-250">`y` the y position of the bounding box center relative to the grid cell it's associated with.</span></span>
- <span data-ttu-id="dbb28-251">`w` — ширина ограничивающего прямоугольника.</span><span class="sxs-lookup"><span data-stu-id="dbb28-251">`w` the width of the bounding box.</span></span>
- <span data-ttu-id="dbb28-252">`h` — высота ограничивающего прямоугольника.</span><span class="sxs-lookup"><span data-stu-id="dbb28-252">`h` the height of the bounding box.</span></span> 
- <span data-ttu-id="dbb28-253">`o` — значение достоверности того, что объект существует в пределах ограничивающего прямоугольника, также известный как оценка объекта.</span><span class="sxs-lookup"><span data-stu-id="dbb28-253">`o` the confidence value that an object exists within the bounding box, also known as objectness score.</span></span>
- <span data-ttu-id="dbb28-254">`p1-p20` — вероятности для каждого из 20 классов, прогнозируемых моделью.</span><span class="sxs-lookup"><span data-stu-id="dbb28-254">`p1-p20` class probabilities for each of the 20 classes predicted by the model.</span></span>

<span data-ttu-id="dbb28-255">В итоге 25 элементов, описывающих каждый из пяти ограничивающих прямоугольников, составляют 125 элементов, содержащихся в каждой ячейке сетки.</span><span class="sxs-lookup"><span data-stu-id="dbb28-255">In total, the 25 elements describing each of the 5 bounding boxes make up the 125 elements contained in each grid cell.</span></span>

<span data-ttu-id="dbb28-256">Выходные данные, формируемые предварительно обученной моделью ONNX, представляют собой массив длины `21125`, представляющий тензорные элементы с `125 x 13 x 13` измерениями.</span><span class="sxs-lookup"><span data-stu-id="dbb28-256">The output generated by the pre-trained ONNX model is a float array of length `21125`, representing the elements of a tensor with dimensions `125 x 13 x 13`.</span></span> <span data-ttu-id="dbb28-257">Чтобы преобразовать прогнозы, созданные моделью, в тензоры, необходимо выполнить некоторые действия для постобработки.</span><span class="sxs-lookup"><span data-stu-id="dbb28-257">In order to transform the predictions generated by the model into a tensor, some post-processing work is required.</span></span> <span data-ttu-id="dbb28-258">Для этого создайте набор классов для упрощения анализа выходных данных.</span><span class="sxs-lookup"><span data-stu-id="dbb28-258">To do so, create a set of classes to help parse the output.</span></span>

<span data-ttu-id="dbb28-259">Добавьте в проект новый каталог для упорядочения набора классов средства анализа.</span><span class="sxs-lookup"><span data-stu-id="dbb28-259">Add a new directory to your project to organize the set of parser classes.</span></span>

1. <span data-ttu-id="dbb28-260">В **обозревателе решений** щелкните проект правой кнопкой мыши и выберите пункты **Добавить** > **Новая папка**.</span><span class="sxs-lookup"><span data-stu-id="dbb28-260">In **Solution Explorer**, right-click the project, and then select **Add** > **New Folder**.</span></span> <span data-ttu-id="dbb28-261">Когда новая папка появится в обозревателе решений, присвойте ей имя "YoloParser".</span><span class="sxs-lookup"><span data-stu-id="dbb28-261">When the new folder appears in the Solution Explorer, name it "YoloParser".</span></span>

### <a name="create-bounding-boxes-and-dimensions"></a><span data-ttu-id="dbb28-262">Создание ограничивающих прямоугольников и измерений</span><span class="sxs-lookup"><span data-stu-id="dbb28-262">Create bounding boxes and dimensions</span></span>

<span data-ttu-id="dbb28-263">Выходные данные модели содержат координаты и размеры ограничивающих прямоугольников объектов в изображении.</span><span class="sxs-lookup"><span data-stu-id="dbb28-263">The data output by the model contains coordinates and dimensions of the bounding boxes of objects within the image.</span></span> <span data-ttu-id="dbb28-264">Создайте базовый класс для измерений.</span><span class="sxs-lookup"><span data-stu-id="dbb28-264">Create a base class for dimensions.</span></span>

1. <span data-ttu-id="dbb28-265">В **обозревателе решений** щелкните правой кнопкой мыши папку *YoloParser* и выберите **Добавить** > **Новый элемент**.</span><span class="sxs-lookup"><span data-stu-id="dbb28-265">In **Solution Explorer**, right-click the *YoloParser* directory, and then select **Add** > **New Item**.</span></span>
1. <span data-ttu-id="dbb28-266">В диалоговом окне **Добавление нового элемента** выберите **Класс** и измените значение поля **Имя** на *DimensionsBase.cs*.</span><span class="sxs-lookup"><span data-stu-id="dbb28-266">In the **Add New Item** dialog box, select **Class** and change the **Name** field to *DimensionsBase.cs*.</span></span> <span data-ttu-id="dbb28-267">Теперь нажмите кнопку **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="dbb28-267">Then, select the **Add** button.</span></span>

    <span data-ttu-id="dbb28-268">В редакторе кода откроется файл *DimensionsBase.cs*.</span><span class="sxs-lookup"><span data-stu-id="dbb28-268">The *DimensionsBase.cs* file opens in the code editor.</span></span> <span data-ttu-id="dbb28-269">Удалите все инструкции `using` и существующее определение класса.</span><span class="sxs-lookup"><span data-stu-id="dbb28-269">Remove all `using` statements and existing class definition.</span></span> 

    <span data-ttu-id="dbb28-270">Добавьте следующий код для класса `DimensionsBase` в файл *DimensionsBase.cs*:</span><span class="sxs-lookup"><span data-stu-id="dbb28-270">Add the following code for the `DimensionsBase` class to the *DimensionsBase.cs* file:</span></span>

    [!code-csharp [DimensionsBaseClass](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/YoloParser/DimensionsBase.cs#L3-L9)]

    <span data-ttu-id="dbb28-271">`DimensionsBase` имеет следующие поля `float`:</span><span class="sxs-lookup"><span data-stu-id="dbb28-271">`DimensionsBase` has the following `float` fields:</span></span>

    - <span data-ttu-id="dbb28-272">`X` содержит расположение объекта вдоль оси X.</span><span class="sxs-lookup"><span data-stu-id="dbb28-272">`X` contains the position of the object along the x-axis.</span></span>
    - <span data-ttu-id="dbb28-273">`Y` содержит расположение объекта вдоль оси Y.</span><span class="sxs-lookup"><span data-stu-id="dbb28-273">`Y` contains the position of the object along the y-axis.</span></span>
    - <span data-ttu-id="dbb28-274">`Height` содержит высоту объекта.</span><span class="sxs-lookup"><span data-stu-id="dbb28-274">`Height` contains the height of the object.</span></span>
    - <span data-ttu-id="dbb28-275">`Width` содержит ширину объекта.</span><span class="sxs-lookup"><span data-stu-id="dbb28-275">`Width` contains the width of the object.</span></span>

<span data-ttu-id="dbb28-276">Затем создайте класс для ограничивающих прямоугольников.</span><span class="sxs-lookup"><span data-stu-id="dbb28-276">Next, create a class for your bounding boxes.</span></span>

1. <span data-ttu-id="dbb28-277">В **обозревателе решений** щелкните правой кнопкой мыши папку *YoloParser* и выберите **Добавить** > **Новый элемент**.</span><span class="sxs-lookup"><span data-stu-id="dbb28-277">In **Solution Explorer**, right-click the *YoloParser* directory, and then select **Add** > **New Item**.</span></span>
1. <span data-ttu-id="dbb28-278">В диалоговом окне **Добавление нового элемента** выберите **Класс** и измените значение поля **Имя** на *YoloBoundingBox.cs*.</span><span class="sxs-lookup"><span data-stu-id="dbb28-278">In the **Add New Item** dialog box, select **Class** and change the **Name** field to *YoloBoundingBox.cs*.</span></span> <span data-ttu-id="dbb28-279">Теперь нажмите кнопку **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="dbb28-279">Then, select the **Add** button.</span></span>

    <span data-ttu-id="dbb28-280">В редакторе кода откроется файл *YoloBoundingBox.cs*.</span><span class="sxs-lookup"><span data-stu-id="dbb28-280">The *YoloBoundingBox.cs* file opens in the code editor.</span></span> <span data-ttu-id="dbb28-281">Добавьте следующую инструкцию `using` в начало файла *YoloBoundingBox.cs*:</span><span class="sxs-lookup"><span data-stu-id="dbb28-281">Add the following `using` statement to the top of *YoloBoundingBox.cs*:</span></span>

    [!code-csharp [YoloBoundingBoxUsings](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/YoloParser/YoloBoundingBox.cs#L1)]

    <span data-ttu-id="dbb28-282">Непосредственно над существующим определением класса добавьте новое определение класса с именем `BoundingBoxDimensions`, который наследует от класса `DimensionsBase`, чтобы вместить размеры соответствующего ограничивающего прямоугольника.</span><span class="sxs-lookup"><span data-stu-id="dbb28-282">Just above the existing class definition, add a new class definition called `BoundingBoxDimensions` which inherits from the `DimensionsBase` class to contain the dimensions of the respective bounding box.</span></span>

    [!code-csharp [BoundingBoxDimClass](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/YoloParser/YoloBoundingBox.cs#L5)]

    <span data-ttu-id="dbb28-283">Удалите существующее определение класса `YoloBoundingBox` и добавьте следующий код для класса `YoloBoundingBox` в файл *YoloBoundingBox.cs*:</span><span class="sxs-lookup"><span data-stu-id="dbb28-283">Remove the existing `YoloBoundingBox` class definition and add the following code for the `YoloBoundingBox` class to the *YoloBoundingBox.cs* file:</span></span>

    [!code-csharp [YoloBoundingBoxClass](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/YoloParser/YoloBoundingBox.cs#L7-L21)]
    
    <span data-ttu-id="dbb28-284">`YoloBoundingBox` имеет следующие поля:</span><span class="sxs-lookup"><span data-stu-id="dbb28-284">`YoloBoundingBox` has the following fields:</span></span>

    - <span data-ttu-id="dbb28-285">`Dimensions` содержит размеры ограничивающего прямоугольника.</span><span class="sxs-lookup"><span data-stu-id="dbb28-285">`Dimensions` contains dimensions of the bounding box.</span></span>
    - <span data-ttu-id="dbb28-286">`Label` содержит класс объекта, обнаруженного в ограничивающем прямоугольнике.</span><span class="sxs-lookup"><span data-stu-id="dbb28-286">`Label` contains the class of object detected within the bounding box.</span></span>
    - <span data-ttu-id="dbb28-287">`Confidence` содержит достоверность класса.</span><span class="sxs-lookup"><span data-stu-id="dbb28-287">`Confidence` contains the confidence of the class.</span></span>
    - <span data-ttu-id="dbb28-288">`Rect` содержит прямоугольное представление измерений ограничивающего прямоугольника.</span><span class="sxs-lookup"><span data-stu-id="dbb28-288">`Rect` contains the rectangle representation of the bounding box's dimensions.</span></span>
    - <span data-ttu-id="dbb28-289">`BoxColor` содержит цвет, связанный с соответствующим классом, который используется для рисования изображения.</span><span class="sxs-lookup"><span data-stu-id="dbb28-289">`BoxColor` contains the color associated with the respective class used to draw on the image.</span></span>

### <a name="create-the-parser"></a><span data-ttu-id="dbb28-290">Создание средства анализа</span><span class="sxs-lookup"><span data-stu-id="dbb28-290">Create the parser</span></span>

<span data-ttu-id="dbb28-291">Теперь, когда созданы классы для измерений и ограничивающих прямоугольников, пришло время создать средство анализа.</span><span class="sxs-lookup"><span data-stu-id="dbb28-291">Now that the classes for dimensions and bounding boxes are created, it's time to create the parser.</span></span>

1. <span data-ttu-id="dbb28-292">В **обозревателе решений** щелкните правой кнопкой мыши папку *YoloParser* и выберите **Добавить** > **Новый элемент**.</span><span class="sxs-lookup"><span data-stu-id="dbb28-292">In **Solution Explorer**, right-click the *YoloParser* directory, and then select **Add** > **New Item**.</span></span>
1. <span data-ttu-id="dbb28-293">В диалоговом окне **Добавление нового элемента** выберите **Класс** и измените значение поля **Имя** на *YoloOutputParser.cs*.</span><span class="sxs-lookup"><span data-stu-id="dbb28-293">In the **Add New Item** dialog box, select **Class** and change the **Name** field to *YoloOutputParser.cs*.</span></span> <span data-ttu-id="dbb28-294">Теперь нажмите кнопку **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="dbb28-294">Then, select the **Add** button.</span></span>

    <span data-ttu-id="dbb28-295">В редакторе кода откроется файл *YoloOutputParser.cs*.</span><span class="sxs-lookup"><span data-stu-id="dbb28-295">The *YoloOutputParser.cs* file opens in the code editor.</span></span> <span data-ttu-id="dbb28-296">Добавьте следующую инструкцию `using` в начало файла *YoloOutputParser.cs*:</span><span class="sxs-lookup"><span data-stu-id="dbb28-296">Add the following `using` statement to the top of *YoloOutputParser.cs*:</span></span>

    [!code-csharp [YoloParserUsings](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/YoloParser/YoloOutputParser.cs#L1-L4)]

    <span data-ttu-id="dbb28-297">В существующем определении класса `YoloOutputParser` добавьте вложенный класс, который содержит размеры каждой ячейки в изображении.</span><span class="sxs-lookup"><span data-stu-id="dbb28-297">Inside the existing `YoloOutputParser` class definition, add a nested class that contains the dimensions of each of the cells in the image.</span></span> <span data-ttu-id="dbb28-298">Добавьте следующий код для класса `CellDimensions`, который наследует от класса `DimensionsBase`, в верхней части определения класса `YoloOutputParser`.</span><span class="sxs-lookup"><span data-stu-id="dbb28-298">Add the following code for the `CellDimensions` class which inherits from the `DimensionsBase` class at the top of the `YoloOutputParser` class definition.</span></span>

    [!code-csharp [YoloParserUsings](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/YoloParser/YoloOutputParser.cs#L10)]

1. <span data-ttu-id="dbb28-299">Добавьте в определение класса `YoloOutputParser` следующую константу и поля.</span><span class="sxs-lookup"><span data-stu-id="dbb28-299">Inside the `YoloOutputParser` class definition, add the following constant and fields.</span></span>

    [!code-csharp [ParserVarDefinitions](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/YoloParser/YoloOutputParser.cs#L12-L21)]    

    - <span data-ttu-id="dbb28-300">`ROW_COUNT` задает число строк в сетке, на которые делится изображение.</span><span class="sxs-lookup"><span data-stu-id="dbb28-300">`ROW_COUNT` is the number of rows in the grid the image is divided into.</span></span>
    - <span data-ttu-id="dbb28-301">`COL_COUNT` задает число столбцов в сетке, на которые делится изображение.</span><span class="sxs-lookup"><span data-stu-id="dbb28-301">`COL_COUNT` is the number of columns in the grid the image is divided into.</span></span>
    - <span data-ttu-id="dbb28-302">`CHANNEL_COUNT` задает общее число значений, содержащихся в одной ячейке сетки.</span><span class="sxs-lookup"><span data-stu-id="dbb28-302">`CHANNEL_COUNT` is the total number of values contained in one cell of the grid.</span></span>
    - <span data-ttu-id="dbb28-303">`BOXES_PER_CELL` задает число ограничивающих прямоугольников в ячейке.</span><span class="sxs-lookup"><span data-stu-id="dbb28-303">`BOXES_PER_CELL` is the number of bounding boxes in a cell,</span></span>
    - <span data-ttu-id="dbb28-304">`BOX_INFO_FEATURE_COUNT` — число компонентов, содержащихся в поле (X, Y, высота, ширина, достоверность).</span><span class="sxs-lookup"><span data-stu-id="dbb28-304">`BOX_INFO_FEATURE_COUNT` is the number of features contained within a box (x,y,height,width,confidence).</span></span>
    - <span data-ttu-id="dbb28-305">`CLASS_COUNT` — число прогнозов класса, содержащихся в каждом ограничивающем прямоугольнике.</span><span class="sxs-lookup"><span data-stu-id="dbb28-305">`CLASS_COUNT` is the number of class predictions contained in each bounding box.</span></span>
    - <span data-ttu-id="dbb28-306">`CELL_WIDTH` — ширина одной ячейки в сетке изображения.</span><span class="sxs-lookup"><span data-stu-id="dbb28-306">`CELL_WIDTH` is the width of one cell in the image grid.</span></span>
    - <span data-ttu-id="dbb28-307">`CELL_HEIGHT` — высота одной ячейки в сетке изображения.</span><span class="sxs-lookup"><span data-stu-id="dbb28-307">`CELL_HEIGHT` is the height of one cell in the image grid.</span></span>
    - <span data-ttu-id="dbb28-308">`channelStride` — начальная координата текущей ячейки в сетке.</span><span class="sxs-lookup"><span data-stu-id="dbb28-308">`channelStride` is the starting position of the current cell in the grid.</span></span>

    <span data-ttu-id="dbb28-309">Когда модель оценивает изображение, она делит входные данные `416px x 416px` на сетку ячеек размером `13 x 13`.</span><span class="sxs-lookup"><span data-stu-id="dbb28-309">When the model scores an image, it divides the `416px x 416px`input into a grid of cells the size of `13 x 13`.</span></span> <span data-ttu-id="dbb28-310">Каждая ячейка содержит `32px x 32px`.</span><span class="sxs-lookup"><span data-stu-id="dbb28-310">Each cell contains is `32px x 32px`.</span></span> <span data-ttu-id="dbb28-311">В каждой ячейке есть пять ограничивающих прямоугольников, каждый из которых содержит пять компонентов (X, Y, ширина, высота, достоверность).</span><span class="sxs-lookup"><span data-stu-id="dbb28-311">Within each cell, there are 5 bounding boxes each containing 5 features (x, y, width, height, confidence).</span></span> <span data-ttu-id="dbb28-312">Кроме того, каждый ограничивающий прямоугольник содержит вероятность каждого из классов, которых в данном случае насчитывается 20.</span><span class="sxs-lookup"><span data-stu-id="dbb28-312">In addition, each bounding box contains the probability of each of the classes which in this case is 20.</span></span> <span data-ttu-id="dbb28-313">Таким образом, каждая ячейка содержит 125 элементов данных (пять функций + 20 вероятностей классов).</span><span class="sxs-lookup"><span data-stu-id="dbb28-313">Therefore, each cell contains 125 pieces of information (5 features + 20 class probabilities).</span></span> 

<span data-ttu-id="dbb28-314">Создайте список привязок ниже `channelStride` для всех пяти ограничивающих прямоугольников:</span><span class="sxs-lookup"><span data-stu-id="dbb28-314">Create a list of anchors below `channelStride` for all 5 bounding boxes:</span></span>

[!code-csharp [ParserAnchors](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/YoloParser/YoloOutputParser.cs#L23-L26)]   

<span data-ttu-id="dbb28-315">Привязки — это предварительно определенные коэффициенты высоты и ширины ограничивающих прямоугольников.</span><span class="sxs-lookup"><span data-stu-id="dbb28-315">Anchors are pre-defined height and width ratios of bounding boxes.</span></span> <span data-ttu-id="dbb28-316">Большинство объектов или классов, обнаруживаемых моделью, имеют схожие коэффициенты.</span><span class="sxs-lookup"><span data-stu-id="dbb28-316">Most object or classes detected by a model have similar ratios.</span></span> <span data-ttu-id="dbb28-317">Это полезно, когда дело доходит до создания ограничивающих прямоугольников.</span><span class="sxs-lookup"><span data-stu-id="dbb28-317">This is valuable when it comes to creating bounding boxes.</span></span> <span data-ttu-id="dbb28-318">Вместо прогнозирования ограничивающих прямоугольников вычисляется смещение от предварительно определенных измерений, что сокращает число вычислений, необходимое для прогнозирования ограничивающего прямоугольника.</span><span class="sxs-lookup"><span data-stu-id="dbb28-318">Instead of predicting the bounding boxes, the offset from the pre-defined dimensions is calculated therefore reducing the computation required to predict the bounding box.</span></span> <span data-ttu-id="dbb28-319">Обычно эти коэффициенты привязки вычисляются на основе используемого набора данных.</span><span class="sxs-lookup"><span data-stu-id="dbb28-319">Typically these anchor ratios are calculated based on the dataset used.</span></span> <span data-ttu-id="dbb28-320">В этом случае, поскольку набор данных известен и значения предварительно вычислены, привязки могут быть жестко запрограммированы.</span><span class="sxs-lookup"><span data-stu-id="dbb28-320">In this case because the dataset is known and the values have been pre-computed, the anchors can be hard-coded.</span></span>

<span data-ttu-id="dbb28-321">Затем определите метки или классы, которые будет прогнозировать модель.</span><span class="sxs-lookup"><span data-stu-id="dbb28-321">Next, define the labels or classes that the model will predict.</span></span> <span data-ttu-id="dbb28-322">Эта модель прогнозирует 20 классов, которые являются подмножеством общего числа классов, прогнозируемых исходной моделью YOLOv2.</span><span class="sxs-lookup"><span data-stu-id="dbb28-322">This model predicts 20 classes which is a subset of the total number of classes predicted by the original YOLOv2 model.</span></span>

<span data-ttu-id="dbb28-323">Добавьте список меток под `anchors`.</span><span class="sxs-lookup"><span data-stu-id="dbb28-323">Add your list of labels below the `anchors`.</span></span> 

[!code-csharp [ParserLabels](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/YoloParser/YoloOutputParser.cs#L28-L34)]

<span data-ttu-id="dbb28-324">С каждым из классов связаны цвета.</span><span class="sxs-lookup"><span data-stu-id="dbb28-324">There are colors associated with each of the classes.</span></span> <span data-ttu-id="dbb28-325">Назначьте цвета классов под `labels`:</span><span class="sxs-lookup"><span data-stu-id="dbb28-325">Assign your class colors below your `labels`:</span></span>

[!code-csharp [ParserColors](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/YoloParser/YoloOutputParser.cs#L36-L59)]

### <a name="create-helper-functions"></a><span data-ttu-id="dbb28-326">Создание вспомогательных функций</span><span class="sxs-lookup"><span data-stu-id="dbb28-326">Create helper functions</span></span>

<span data-ttu-id="dbb28-327">Этап постобработки включает ряд действий.</span><span class="sxs-lookup"><span data-stu-id="dbb28-327">There are a series of steps involved in the post-processing phase.</span></span> <span data-ttu-id="dbb28-328">Для этого можно применять несколько вспомогательных методов.</span><span class="sxs-lookup"><span data-stu-id="dbb28-328">To help with that, several helper methods can be employed.</span></span> 

<span data-ttu-id="dbb28-329">Средство анализа использует следующие вспомогательные методы:</span><span class="sxs-lookup"><span data-stu-id="dbb28-329">The helper methods used in by the parser are:</span></span>

- <span data-ttu-id="dbb28-330">`Sigmoid` применяет функцию-сигмоиду, которая выводит число от 0 до 1.</span><span class="sxs-lookup"><span data-stu-id="dbb28-330">`Sigmoid` applies the sigmoid function that outputs a number between 0 and 1.</span></span>
- <span data-ttu-id="dbb28-331">`Softmax` нормализует входной вектор в распределение вероятности.</span><span class="sxs-lookup"><span data-stu-id="dbb28-331">`Softmax` normalizes an input vector into a probability distribution.</span></span>
- <span data-ttu-id="dbb28-332">`GetOffset` сопоставляет элементы в выходных данных одномерной модели с соответствующей позицией в тензоре `125 x 13 x 13`.</span><span class="sxs-lookup"><span data-stu-id="dbb28-332">`GetOffset` maps elements in the one-dimensional model output to the corresponding position in a `125 x 13 x 13` tensor.</span></span>
- <span data-ttu-id="dbb28-333">`ExtractBoundingBoxes` извлекает измерения ограничивающего прямоугольника с помощью метода `GetOffset` из выходных данных модели.</span><span class="sxs-lookup"><span data-stu-id="dbb28-333">`ExtractBoundingBoxes` extracts the bounding box dimensions using the `GetOffset` method from the model output.</span></span>
- <span data-ttu-id="dbb28-334">`GetConfidence` извлекает значение достоверности того, что модель обнаружила объект, и использует функцию `Sigmoid`, чтобы преобразовать ее в процент.</span><span class="sxs-lookup"><span data-stu-id="dbb28-334">`GetConfidence` extracts the confidence value which states how sure the model is that it has detected an object and uses the `Sigmoid` function to turn it into a percentage.</span></span>
- <span data-ttu-id="dbb28-335">`MapBoundingBoxToCell` использует измерения ограничивающего прямоугольника и сопоставляет их с соответствующей ячейкой на изображении.</span><span class="sxs-lookup"><span data-stu-id="dbb28-335">`MapBoundingBoxToCell` uses the bounding box dimensions and maps them onto its respective cell within the image.</span></span>
- <span data-ttu-id="dbb28-336">`ExtractClasses` извлекает прогнозы класса для ограничивающего прямоугольника из выходных данных модели с помощью метода `GetOffset` и превращает их в распределение вероятности с помощью метода `Softmax`.</span><span class="sxs-lookup"><span data-stu-id="dbb28-336">`ExtractClasses` extracts the class predictions for the bounding box from the model output using the `GetOffset` method and turns them into a probability distribution using the `Softmax` method.</span></span>
- <span data-ttu-id="dbb28-337">`GetTopResult` выбирает из списка прогнозируемых классов класс с наибольшей вероятностью.</span><span class="sxs-lookup"><span data-stu-id="dbb28-337">`GetTopResult` selects the class from the list of predicted classes with the highest probability.</span></span>
- <span data-ttu-id="dbb28-338">`IntersectionOverUnion` фильтрует перекрывающиеся ограничивающие прямоугольники с более низкими вероятностями.</span><span class="sxs-lookup"><span data-stu-id="dbb28-338">`IntersectionOverUnion` filters overlapping bounding boxes with lower probabilities.</span></span>

<span data-ttu-id="dbb28-339">Добавьте код для всех вспомогательных методов под списком `classColors`.</span><span class="sxs-lookup"><span data-stu-id="dbb28-339">Add the code for all the helper methods below your list of `classColors`.</span></span>

[!code-csharp [ParserHelperMethods](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/YoloParser/YoloOutputParser.cs#L61-L151)]

<span data-ttu-id="dbb28-340">Определив все вспомогательные методы, можно использовать их для обработки выходных данных модели.</span><span class="sxs-lookup"><span data-stu-id="dbb28-340">Once you have defined all of the helper methods, it's time to use them to process the model output.</span></span>

<span data-ttu-id="dbb28-341">Под методом `IntersectionOverUnion` создайте метод `ParseOutputs` для обработки выходных данных, создаваемых моделью.</span><span class="sxs-lookup"><span data-stu-id="dbb28-341">Below the `IntersectionOverUnion` method, create the `ParseOutputs` method to process the output generated by the model.</span></span>

```csharp
public IList<YoloBoundingBox> ParseOutputs(float[] yoloModelOutputs, float threshold = .3F)
{

}
```
    
<span data-ttu-id="dbb28-342">Создайте список для хранения ограничивающих прямоугольников и определите переменные внутри метода `ParseOutputs`.</span><span class="sxs-lookup"><span data-stu-id="dbb28-342">Create a list to store your bounding boxes and define variables inside the `ParseOutputs` method.</span></span>

[!code-csharp [BBoxList](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/YoloParser/YoloOutputParser.cs#L155)]

<span data-ttu-id="dbb28-343">Каждое изображение делится на сетку из ячеек `13 x 13`.</span><span class="sxs-lookup"><span data-stu-id="dbb28-343">Each image is divided into a grid of `13 x 13` cells.</span></span> <span data-ttu-id="dbb28-344">Каждая ячейка содержит пять ограничивающих прямоугольников.</span><span class="sxs-lookup"><span data-stu-id="dbb28-344">Each cell contains five bounding boxes.</span></span> <span data-ttu-id="dbb28-345">Под переменной `boxes` добавьте код для обработки всех полей в каждой ячейке.</span><span class="sxs-lookup"><span data-stu-id="dbb28-345">Below the `boxes` variable, add code to process all of the boxes in each of the cells.</span></span>

```csharp
for (int row = 0; row < ROW_COUNT; row++)
{
    for (int column = 0; column < COL_COUNT; column++)
    {
        for (int box = 0; box < BOXES_PER_CELL; box++)
        {

        }
    }
}
```

<span data-ttu-id="dbb28-346">Внутри самого глубокого цикла вычислите начальную точку текущего поля в выходных данных одномерной модели.</span><span class="sxs-lookup"><span data-stu-id="dbb28-346">Inside the inner-most loop, calculate the starting position of the current box within the one-dimensional model output.</span></span>

[!code-csharp [ChannelDef](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/YoloParser/YoloOutputParser.cs#L163)]

<span data-ttu-id="dbb28-347">Непосредственно под этим используйте метод `ExtractBoundingBoxDimensions`, чтобы получить размеры текущего ограничивающего прямоугольника.</span><span class="sxs-lookup"><span data-stu-id="dbb28-347">Directly below that, use the `ExtractBoundingBoxDimensions` method to get the dimensions of the current bounding box.</span></span>

[!code-csharp [GetBBoxDims](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/YoloParser/YoloOutputParser.cs#L165)]

<span data-ttu-id="dbb28-348">Затем используйте метод `GetConfidence`, чтобы получить достоверность для текущего ограничивающего прямоугольника.</span><span class="sxs-lookup"><span data-stu-id="dbb28-348">Then, use the `GetConfidence` method to get the confidence for the current bounding box.</span></span>

[!code-csharp [GetConfidence](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/YoloParser/YoloOutputParser.cs#L167)]

<span data-ttu-id="dbb28-349">После этого используйте метод `MapBoundingBoxToCell`, чтобы связать текущий ограничивающий прямоугольник с текущей обрабатываемой ячейкой.</span><span class="sxs-lookup"><span data-stu-id="dbb28-349">After that, use the `MapBoundingBoxToCell` method to map the current bounding box to the current cell being processed.</span></span>

[!code-csharp [MapBoundingBoxes](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/YoloParser/YoloOutputParser.cs#L169)]

<span data-ttu-id="dbb28-350">Прежде чем выполнять дальнейшую обработку, проверьте, больше ли значение достоверности, чем предоставленный порог.</span><span class="sxs-lookup"><span data-stu-id="dbb28-350">Before doing any further processing, check whether your confidence value is greater than the threshold provided.</span></span> <span data-ttu-id="dbb28-351">Если нет, обработайте следующий ограничивающий прямоугольник.</span><span class="sxs-lookup"><span data-stu-id="dbb28-351">If not, process the next bounding box.</span></span>

[!code-csharp [CheckThreshold1](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/YoloParser/YoloOutputParser.cs#L171-L172)]

<span data-ttu-id="dbb28-352">В противном случае продолжите обработку выходных данных.</span><span class="sxs-lookup"><span data-stu-id="dbb28-352">Otherwise, continue processing the output.</span></span> <span data-ttu-id="dbb28-353">Следующим шагом является получение вероятности распределения прогнозируемых классов для текущего ограничивающего прямоугольника с помощью метода `ExtractClasses`.</span><span class="sxs-lookup"><span data-stu-id="dbb28-353">The next step is to get the probability distribution of the predicted classes for the current bounding box using the `ExtractClasses` method.</span></span>

[!code-csharp [ExtractClasses](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/YoloParser/YoloOutputParser.cs#L174)]

<span data-ttu-id="dbb28-354">Затем используйте метод `GetTopResult`, чтобы получить значение и индекс класса с наибольшей вероятностью для текущего поля и вычислить его оценку.</span><span class="sxs-lookup"><span data-stu-id="dbb28-354">Then, use the `GetTopResult` method to get the value and index of the class with the highest probability for the current box and compute its score.</span></span>

[!code-csharp [GetTopResult](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/YoloParser/YoloOutputParser.cs#L176-L177)]

<span data-ttu-id="dbb28-355">Используйте `topScore`, чтобы снова оставить только ограничивающие прямоугольники выше указанного порогового значения.</span><span class="sxs-lookup"><span data-stu-id="dbb28-355">Use the `topScore` to once again keep only those bounding boxes that are above the specified threshold.</span></span>

[!code-csharp [CheckThreshold2](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/YoloParser/YoloOutputParser.cs#L179-L180)]

<span data-ttu-id="dbb28-356">Наконец, если текущий ограничивающий прямоугольник превышает пороговое значение, создайте новый объект `BoundingBox` и добавьте его в список `boxes`.</span><span class="sxs-lookup"><span data-stu-id="dbb28-356">Finally, if the current bounding box exceeds the threshold, create a new `BoundingBox` object and add it to the `boxes` list.</span></span>

[!code-csharp [AddBBoxToList](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/YoloParser/YoloOutputParser.cs#L182-L194)]

<span data-ttu-id="dbb28-357">После обработки всех ячеек в изображении возвратите список `boxes`.</span><span class="sxs-lookup"><span data-stu-id="dbb28-357">Once all cells in the image have been processed, return the `boxes` list.</span></span> <span data-ttu-id="dbb28-358">Добавьте следующий оператор return под внешним циклом for в методе `ParseOutputs`.</span><span class="sxs-lookup"><span data-stu-id="dbb28-358">Add the following return statement below the outer-most for-loop in the `ParseOutputs` method.</span></span>

[!code-csharp [ReturnBoxes](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/YoloParser/YoloOutputParser.cs#L198)]

### <a name="filter-overlapping-boxes"></a><span data-ttu-id="dbb28-359">Фильтрация перекрывающихся полей</span><span class="sxs-lookup"><span data-stu-id="dbb28-359">Filter overlapping boxes</span></span>

<span data-ttu-id="dbb28-360">Теперь, когда все надежные ограничивающие прямоугольники были извлечены из выходных данных модели, для удаления перекрывающихся изображений необходимо провести дополнительную фильтрацию.</span><span class="sxs-lookup"><span data-stu-id="dbb28-360">Now that all of the highly confident bounding boxes have been extracted from the model output, additional filtering needs to be done to remove overlapping images.</span></span> <span data-ttu-id="dbb28-361">Добавьте метод `FilterBoundingBoxes` под методом `ParseOutputs`:</span><span class="sxs-lookup"><span data-stu-id="dbb28-361">Add a method called `FilterBoundingBoxes` below the `ParseOutputs` method:</span></span>

```csharp
public IList<YoloBoundingBox> FilterBoundingBoxes(IList<YoloBoundingBox> boxes, int limit, float threshold)
{

}
```

<span data-ttu-id="dbb28-362">В методе `FilterBoundingBoxes` начните с создания массива, который равен размеру обнаруженных полей, помечая все слоты как активные или готовые к обработке.</span><span class="sxs-lookup"><span data-stu-id="dbb28-362">Inside the `FilterBoundingBoxes` method, start off by creating an array equal to the size of detected boxes and marking all slots as active or ready for processing.</span></span>

[!code-csharp [InitActiveSlots](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/YoloParser/YoloOutputParser.cs#L203-L207)]

<span data-ttu-id="dbb28-363">Затем отсортируйте список, содержащий ограничивающие прямоугольники, в порядке убывания.</span><span class="sxs-lookup"><span data-stu-id="dbb28-363">Then, sort the list containing your bounding boxes in descending order based on confidence.</span></span>

[!code-csharp [SortBoxes](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/YoloParser/YoloOutputParser.cs#L209-L211)]

<span data-ttu-id="dbb28-364">После этого создайте список для хранения отфильтрованных результатов.</span><span class="sxs-lookup"><span data-stu-id="dbb28-364">After that, create a list to hold the filtered results.</span></span>

[!code-csharp [InitFilterResult](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/YoloParser/YoloOutputParser.cs#L213)]

<span data-ttu-id="dbb28-365">Приступите к обработке каждого ограничивающего прямоугольника путем прохода по всем ограничивающим прямоугольникам.</span><span class="sxs-lookup"><span data-stu-id="dbb28-365">Begin processing each bounding box by iterating over each of the bounding boxes.</span></span>

```csharp
for (int i = 0; i < boxes.Count; i++)
{

}
```

<span data-ttu-id="dbb28-366">Внутри этого цикла for проверьте, может ли быть обработан текущий ограничивающий прямоугольник.</span><span class="sxs-lookup"><span data-stu-id="dbb28-366">Inside of this for-loop, check whether the current bounding box can be processed.</span></span>

```csharp
if (isActiveBoxes[i])
{
    
}
```

<span data-ttu-id="dbb28-367">Если это так, добавьте ограничивающий прямоугольник в список результатов.</span><span class="sxs-lookup"><span data-stu-id="dbb28-367">If so, add the bounding box to the list of results.</span></span> <span data-ttu-id="dbb28-368">Если результаты больше указанного предельного числа полей для извлечения, следует выйти из цикла.</span><span class="sxs-lookup"><span data-stu-id="dbb28-368">If the results exceeds the specified limit of boxes to be extracted, break out of the loop.</span></span> <span data-ttu-id="dbb28-369">Добавьте следующий код в оператор if.</span><span class="sxs-lookup"><span data-stu-id="dbb28-369">Add the following code inside the if-statement.</span></span>

[!code-csharp [AddFirstBBoxToResults](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/YoloParser/YoloOutputParser.cs#L219-L223)]

<span data-ttu-id="dbb28-370">В противном случае просмотрите соседние ограничивающие прямоугольники.</span><span class="sxs-lookup"><span data-stu-id="dbb28-370">Otherwise, look at the adjacent bounding boxes.</span></span> <span data-ttu-id="dbb28-371">Добавьте следующий код после проверки предела ограничений.</span><span class="sxs-lookup"><span data-stu-id="dbb28-371">Add the following code below the box limit check.</span></span>

```csharp
for (var j = i + 1; j < boxes.Count; j++)
{

}
```

<span data-ttu-id="dbb28-372">Как и с первым полем, если смежное поле активно или готово к обработке, используйте метод `IntersectionOverUnion`, чтобы проверить, превышают ли первое и второе поля указанное пороговое значение.</span><span class="sxs-lookup"><span data-stu-id="dbb28-372">Like the first box, if the adjacent box is active or ready to be processed, use the `IntersectionOverUnion` method to check whether the first box and the second box exceed the specified threshold.</span></span> <span data-ttu-id="dbb28-373">Добавьте следующий код в самый глубокий внутренний цикл for.</span><span class="sxs-lookup"><span data-stu-id="dbb28-373">Add the following code to your inner-most for-loop.</span></span>

[!code-csharp [IOUBBox](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/YoloParser/YoloOutputParser.cs#L227-L239)]

<span data-ttu-id="dbb28-374">За пределами внутреннего цикла for, который проверяет смежные ограничивающие прямоугольники, можно узнать, остались ли для обработки ограничивающие прямоугольники.</span><span class="sxs-lookup"><span data-stu-id="dbb28-374">Outside of the inner-most for-loop that checks adjacent bounding boxes, see whether there are any remaining bounding boxes to be processed.</span></span> <span data-ttu-id="dbb28-375">В противном случае прервите внешний цикл for.</span><span class="sxs-lookup"><span data-stu-id="dbb28-375">If not, break out of the outer for-loop.</span></span>

[!code-csharp [CheckActiveSlots](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/YoloParser/YoloOutputParser.cs#L242-L243)]

<span data-ttu-id="dbb28-376">Наконец, за пределами начального цикла for метода `FilterBoundingBoxes` следует вернуть результаты:</span><span class="sxs-lookup"><span data-stu-id="dbb28-376">Finally, outside of the initial for-loop of the `FilterBoundingBoxes` method, return the results:</span></span>

[!code-csharp [ReturnFilteredBBox](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/YoloParser/YoloOutputParser.cs#L246)]

<span data-ttu-id="dbb28-377">Отлично!</span><span class="sxs-lookup"><span data-stu-id="dbb28-377">Great!</span></span> <span data-ttu-id="dbb28-378">Теперь пришло время использовать этот код вместе с моделью для оценки.</span><span class="sxs-lookup"><span data-stu-id="dbb28-378">Now it's time to use this code along with the model for scoring.</span></span>

## <a name="use-the-model-for-scoring"></a><span data-ttu-id="dbb28-379">Использование модели для оценки</span><span class="sxs-lookup"><span data-stu-id="dbb28-379">Use the model for scoring</span></span>

<span data-ttu-id="dbb28-380">Как и в случае с постобработкой, оценка проводится в несколько шагов.</span><span class="sxs-lookup"><span data-stu-id="dbb28-380">Just like with post-processing, there are a few steps in the scoring steps.</span></span> <span data-ttu-id="dbb28-381">Чтобы помочь в этом, добавьте класс, который будет содержать логику оценки для проекта.</span><span class="sxs-lookup"><span data-stu-id="dbb28-381">To help with this, add a class that will contain the scoring logic to your project.</span></span>

1. <span data-ttu-id="dbb28-382">В **обозревателе решений** щелкните проект правой кнопкой мыши и выберите пункты **Добавить** > **Новый элемент**.</span><span class="sxs-lookup"><span data-stu-id="dbb28-382">In **Solution Explorer**, right-click the project, and then select **Add** > **New Item**.</span></span>
1. <span data-ttu-id="dbb28-383">В диалоговом окне **Добавление нового элемента** выберите **Класс** и измените значение поля **Имя** на *OnnxModelScorer.cs*.</span><span class="sxs-lookup"><span data-stu-id="dbb28-383">In the **Add New Item** dialog box, select **Class** and change the **Name** field to *OnnxModelScorer.cs*.</span></span> <span data-ttu-id="dbb28-384">Теперь нажмите кнопку **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="dbb28-384">Then, select the **Add** button.</span></span>

    <span data-ttu-id="dbb28-385">Файл *OnnxModelScorer.cs* откроется в редакторе кода.</span><span class="sxs-lookup"><span data-stu-id="dbb28-385">The *OnnxModelScorer.cs* file opens in the code editor.</span></span> <span data-ttu-id="dbb28-386">Добавьте следующую инструкцию `using` в начало файла *OnnxModelScorer.cs*:</span><span class="sxs-lookup"><span data-stu-id="dbb28-386">Add the following `using` statement to the top of *OnnxModelScorer.cs*:</span></span>

    [!code-csharp [ScorerUsings](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/OnnxModelScorer.cs#L1-L7)]

    <span data-ttu-id="dbb28-387">Добавьте в определение класса `OnnxModelScorer` следующие переменные:</span><span class="sxs-lookup"><span data-stu-id="dbb28-387">Inside the `OnnxModelScorer` class definition, add the following variables.</span></span>

    [!code-csharp [InitScorerVars](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/OnnxModelScorer.cs#L13-L17)]

    <span data-ttu-id="dbb28-388">Непосредственно под ними создайте конструктор для класса `OnnxModelScorer`, который будет инициализировать ранее определенные переменные.</span><span class="sxs-lookup"><span data-stu-id="dbb28-388">Directly below that, create a constructor for the `OnnxModelScorer` class that will initialize the previously defined variables.</span></span>

    [!code-csharp [ScorerCtor](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/OnnxModelScorer.cs#L19-L24)]

    <span data-ttu-id="dbb28-389">После создания конструктора определите пару структур, содержащих переменные, связанные с изображением и параметрами модели.</span><span class="sxs-lookup"><span data-stu-id="dbb28-389">Once you have created the constructor, define a couple of structs that contain variables related to the image and model settings.</span></span> <span data-ttu-id="dbb28-390">Создайте структуру с именем `ImageNetSettings`, которая будет содержать высоту и ширину, ожидаемые в качестве входных данных для модели.</span><span class="sxs-lookup"><span data-stu-id="dbb28-390">Create a struct called `ImageNetSettings` to contain the height and width expected as input for the model.</span></span>

    [!code-csharp [ImageNetSettingStruct](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/OnnxModelScorer.cs#L26-L30)]

    <span data-ttu-id="dbb28-391">После этого создайте другую структуру с именем `TinyYoloModelSettings`, которая содержит имена входных и выходных слоев модели.</span><span class="sxs-lookup"><span data-stu-id="dbb28-391">After that, create another struct called `TinyYoloModelSettings` which contains the names of the input and output layers of the model.</span></span> <span data-ttu-id="dbb28-392">Чтобы визуализировать имя входного и выходного слоев модели, можно использовать такой инструмент, как [Netron](https://github.com/lutzroeder/netron).</span><span class="sxs-lookup"><span data-stu-id="dbb28-392">To visualize the name of the input and output layers of the model, you can use a tool like [Netron](https://github.com/lutzroeder/netron).</span></span>

    [!code-csharp [YoloSettingsStruct](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/OnnxModelScorer.cs#L32-L43)]

    <span data-ttu-id="dbb28-393">Затем создайте первый набор методов, используемый для оценки.</span><span class="sxs-lookup"><span data-stu-id="dbb28-393">Next, create the first set of methods use for scoring.</span></span> <span data-ttu-id="dbb28-394">Создайте метод `LoadModel` внутри класса `OnnxModelScorer`.</span><span class="sxs-lookup"><span data-stu-id="dbb28-394">Create the `LoadModel` method inside of your `OnnxModelScorer` class.</span></span>

    ```csharp
    private ITransformer LoadModel(string modelLocation)
    {

    }
    ```

    <span data-ttu-id="dbb28-395">Добавьте следующий код журнала в метод `LoadModel`.</span><span class="sxs-lookup"><span data-stu-id="dbb28-395">Inside the `LoadModel` method, add the following code for logging.</span></span>

    [!code-csharp [LoadModelLog](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/OnnxModelScorer.cs#L47-L49)]

    <span data-ttu-id="dbb28-396">Конвейеры ML.NET обычно предполагают, что данные будут обрабатываться при вызове метода [`Fit`](xref:Microsoft.ML.IEstimator%601.Fit*).</span><span class="sxs-lookup"><span data-stu-id="dbb28-396">ML.NET pipelines typically expect data to operate on when the [`Fit`](xref:Microsoft.ML.IEstimator%601.Fit*) method is called.</span></span> <span data-ttu-id="dbb28-397">В этом случае будет использоваться процесс, аналогичный обучению.</span><span class="sxs-lookup"><span data-stu-id="dbb28-397">In this case, a process similar to training will be used.</span></span> <span data-ttu-id="dbb28-398">Однако, поскольку фактического обучения не происходит, допустимо использовать пустое значение [`IDataView`](xref:Microsoft.ML.IDataView).</span><span class="sxs-lookup"><span data-stu-id="dbb28-398">However, because no actual training is happening, it is acceptable to use an empty [`IDataView`](xref:Microsoft.ML.IDataView).</span></span> <span data-ttu-id="dbb28-399">Создайте новый [`IDataView`](xref:Microsoft.ML.IDataView) для конвейера из пустого списка.</span><span class="sxs-lookup"><span data-stu-id="dbb28-399">Create a new [`IDataView`](xref:Microsoft.ML.IDataView) for the pipeline from an empty list.</span></span>

    [!code-csharp [LoadEmptyIDV](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/OnnxModelScorer.cs#L52)]    

    <span data-ttu-id="dbb28-400">Ниже определите конвейер.</span><span class="sxs-lookup"><span data-stu-id="dbb28-400">Below that, define the pipeline.</span></span> <span data-ttu-id="dbb28-401">Конвейер будет состоять из четырех преобразований.</span><span class="sxs-lookup"><span data-stu-id="dbb28-401">The pipeline will consist of four transforms.</span></span>

    - <span data-ttu-id="dbb28-402">[`LoadImages`](xref:Microsoft.ML.ImageEstimatorsCatalog.LoadImages*) загружает изображение в виде точечного рисунка.</span><span class="sxs-lookup"><span data-stu-id="dbb28-402">[`LoadImages`](xref:Microsoft.ML.ImageEstimatorsCatalog.LoadImages*) loads the image as a Bitmap.</span></span>
    - <span data-ttu-id="dbb28-403">[`ResizeImages`](xref:Microsoft.ML.ImageEstimatorsCatalog.ResizeImages*) изменяет масштаб изображения до указанного размера (в данном случае `416 x 416`).</span><span class="sxs-lookup"><span data-stu-id="dbb28-403">[`ResizeImages`](xref:Microsoft.ML.ImageEstimatorsCatalog.ResizeImages*) rescales the image to the size specified (in this case, `416 x 416`).</span></span>
    - <span data-ttu-id="dbb28-404">[`ExtractPixels`](xref:Microsoft.ML.ImageEstimatorsCatalog.ExtractPixels*) изменяет пиксельное представление изображения с точечного рисунка на числовой вектор.</span><span class="sxs-lookup"><span data-stu-id="dbb28-404">[`ExtractPixels`](xref:Microsoft.ML.ImageEstimatorsCatalog.ExtractPixels*) changes the pixel representation of the image from a Bitmap to a numerical vector.</span></span>
    - <span data-ttu-id="dbb28-405">[`ApplyOnnxModel`](xref:Microsoft.ML.OnnxCatalog.ApplyOnnxModel*) загружает модель ONNX и использует ее для оценки предоставленных данных.</span><span class="sxs-lookup"><span data-stu-id="dbb28-405">[`ApplyOnnxModel`](xref:Microsoft.ML.OnnxCatalog.ApplyOnnxModel*) loads the ONNX model and uses it to score on the data provided.</span></span>

    <span data-ttu-id="dbb28-406">Определите конвейер в методе `LoadModel` под переменной `data`.</span><span class="sxs-lookup"><span data-stu-id="dbb28-406">Define your pipeline in the `LoadModel` method below the `data` variable.</span></span>

    [!code-csharp [ScoringPipeline](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/OnnxModelScorer.cs#L55-L58)]

    <span data-ttu-id="dbb28-407">Пришло время создать экземпляр модели для оценки.</span><span class="sxs-lookup"><span data-stu-id="dbb28-407">Now it's time to instantiate the model for scoring.</span></span> <span data-ttu-id="dbb28-408">Вызовите метод [`Fit`](xref:Microsoft.ML.IEstimator%601.Fit*) в конвейере и верните его результат для дальнейшей обработки.</span><span class="sxs-lookup"><span data-stu-id="dbb28-408">Call the [`Fit`](xref:Microsoft.ML.IEstimator%601.Fit*) method on the pipeline and return it for further processing.</span></span>

    [!code-csharp [FitReturnModel](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/OnnxModelScorer.cs#L61-L63)]

<span data-ttu-id="dbb28-409">После загрузки модели ее можно использовать для создания прогнозов.</span><span class="sxs-lookup"><span data-stu-id="dbb28-409">Once the model is loaded, it can then be used to make predictions.</span></span> <span data-ttu-id="dbb28-410">Чтобы упростить этот процесс, создайте метод `PredictDataUsingModel` под методом `LoadModel`.</span><span class="sxs-lookup"><span data-stu-id="dbb28-410">To facilitate that process, create a method called `PredictDataUsingModel` below the `LoadModel` method.</span></span>

```csharp
private IEnumerable<float[]> PredictDataUsingModel(IDataView testData, ITransformer model)
{

}
```

<span data-ttu-id="dbb28-411">Добавьте следующий код журнала в `PredictDataUsingModel`.</span><span class="sxs-lookup"><span data-stu-id="dbb28-411">Inside the `PredictDataUsingModel`, add the following code for logging.</span></span>

[!code-csharp [PredictDataLog](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/OnnxModelScorer.cs#L68-L71)]

<span data-ttu-id="dbb28-412">Затем используйте метод [`Transform`](xref:Microsoft.ML.ITransformer.Transform*) для оценки данных.</span><span class="sxs-lookup"><span data-stu-id="dbb28-412">Then, use the [`Transform`](xref:Microsoft.ML.ITransformer.Transform*) method to score the data.</span></span>

[!code-csharp [ScoreImages](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/OnnxModelScorer.cs#L73)]

<span data-ttu-id="dbb28-413">Извлеките прогнозируемые вероятности и верните их для дополнительной обработки.</span><span class="sxs-lookup"><span data-stu-id="dbb28-413">Extract the predicted probabilities and return them for additional processing.</span></span>

[!code-csharp [ReturnModelOutput](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/OnnxModelScorer.cs#L75-L77)]

<span data-ttu-id="dbb28-414">Теперь, когда оба шага настроены, объедините их в один метод.</span><span class="sxs-lookup"><span data-stu-id="dbb28-414">Now that both steps are set up, combine them into a single method.</span></span> <span data-ttu-id="dbb28-415">Добавьте новый метод `Score` под методом `PredictDataUsingModel`:</span><span class="sxs-lookup"><span data-stu-id="dbb28-415">Below the `PredictDataUsingModel` method, add a new method called `Score`.</span></span>

[!code-csharp [ScoreMethod](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/OnnxModelScorer.cs#L80-L85)]

<span data-ttu-id="dbb28-416">Почти готово!</span><span class="sxs-lookup"><span data-stu-id="dbb28-416">Almost there!</span></span> <span data-ttu-id="dbb28-417">Теперь пора все это использовать.</span><span class="sxs-lookup"><span data-stu-id="dbb28-417">Now it's time to put it all to use.</span></span>

## <a name="detect-objects"></a><span data-ttu-id="dbb28-418">Обнаружение объектов</span><span class="sxs-lookup"><span data-stu-id="dbb28-418">Detect objects</span></span>

<span data-ttu-id="dbb28-419">Теперь, когда все настройки завершены, пришло время обнаружить объекты.</span><span class="sxs-lookup"><span data-stu-id="dbb28-419">Now that all of the setup is complete, it's time to detect some objects.</span></span> <span data-ttu-id="dbb28-420">В методе `Main` класса *Program.cs* добавьте оператор try-catch.</span><span class="sxs-lookup"><span data-stu-id="dbb28-420">Inside the `Main` method of your *Program.cs* class, add a try-catch statement.</span></span>

```csharp
try
{

}
catch (Exception ex)
{
    Console.WriteLine(ex.ToString());
}
```

<span data-ttu-id="dbb28-421">В блоке `try` начните реализовывать логику обнаружения объектов.</span><span class="sxs-lookup"><span data-stu-id="dbb28-421">Inside of the `try` block, start implementing the object detection logic.</span></span> <span data-ttu-id="dbb28-422">Сначала загрузите данные в [`IDataView`](xref:Microsoft.ML.IDataView).</span><span class="sxs-lookup"><span data-stu-id="dbb28-422">First, load the data into an [`IDataView`](xref:Microsoft.ML.IDataView).</span></span>

[!code-csharp [LoadData](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/Program.cs#L29-L30)]

<span data-ttu-id="dbb28-423">Затем создайте экземпляр `OnnxModelScorer` и используйте его для оценки загруженных данных.</span><span class="sxs-lookup"><span data-stu-id="dbb28-423">Then, create an instance of `OnnxModelScorer` and use it to score the loaded data.</span></span>

[!code-csharp [ScoreData](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/Program.cs#L33-L36)]

<span data-ttu-id="dbb28-424">Теперь пора выполнить шаг постобработки.</span><span class="sxs-lookup"><span data-stu-id="dbb28-424">Now it's time for the post-processing step.</span></span> <span data-ttu-id="dbb28-425">Создайте экземпляр `YoloOutputParser` и используйте его для обработки выходных данных модели.</span><span class="sxs-lookup"><span data-stu-id="dbb28-425">Create an instance of `YoloOutputParser` and use it to process the model output.</span></span>

[!code-csharp [ParsePredictions](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/Program.cs#L39-L44)]

<span data-ttu-id="dbb28-426">После обработки выходных данных модели настало время рисования ограничивающих прямоугольников на изображениях.</span><span class="sxs-lookup"><span data-stu-id="dbb28-426">Once the model output has been processed, it's time to draw the bounding boxes on the images.</span></span> <span data-ttu-id="dbb28-427">Создайте цикл for для итерации по каждому из оцененных изображений.</span><span class="sxs-lookup"><span data-stu-id="dbb28-427">Create a for-loop to iterate over each of the scored images.</span></span>

```csharp
for (var i = 0; i < images.Count(); i++)
{

}
```

<span data-ttu-id="dbb28-428">В цикле for получите имя файла изображения и связанных с ним ограничивающих прямоугольников.</span><span class="sxs-lookup"><span data-stu-id="dbb28-428">Inside of the for-loop, get the name of the image file and the bounding boxes associated with it.</span></span>

[!code-csharp [GetImageFileName](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/Program.cs#L49-L50)]

<span data-ttu-id="dbb28-429">Ниже используйте метод `DrawBoundingBox` для рисования ограничивающих прямоугольников на изображении.</span><span class="sxs-lookup"><span data-stu-id="dbb28-429">Below that, use the `DrawBoundingBox` method to draw the bounding boxes on the image.</span></span>

[!code-csharp [DrawBBoxes](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/Program.cs#L52)]

<span data-ttu-id="dbb28-430">Наконец, добавьте логику ведения журнала с помощью метода `LogDetectedObjects`.</span><span class="sxs-lookup"><span data-stu-id="dbb28-430">Lastly, add some logging logic with the `LogDetectedObjects` method.</span></span>

[!code-csharp [LogPredictionsOutput](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/Program.cs#L54)]

<span data-ttu-id="dbb28-431">После оператора try-catch добавьте дополнительную логику, чтобы указать, что процесс завершен.</span><span class="sxs-lookup"><span data-stu-id="dbb28-431">After the try-catch statement, add additional logic to indicate the process is done running.</span></span>

[!code-csharp [EndProcessLog](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/Program.cs#L62-L63)]

<span data-ttu-id="dbb28-432">Вот и все!</span><span class="sxs-lookup"><span data-stu-id="dbb28-432">That's it!</span></span> 

## <a name="results"></a><span data-ttu-id="dbb28-433">Результаты</span><span class="sxs-lookup"><span data-stu-id="dbb28-433">Results</span></span> 

<span data-ttu-id="dbb28-434">Выполнив предыдущие шаги, запустите консольное приложение (CTRL+F5).</span><span class="sxs-lookup"><span data-stu-id="dbb28-434">After following the previous steps, run your console app (Ctrl + F5).</span></span> <span data-ttu-id="dbb28-435">Результаты выполнения должны выглядеть примерно так, как указано ниже.</span><span class="sxs-lookup"><span data-stu-id="dbb28-435">Your results should be similar to the following output.</span></span> <span data-ttu-id="dbb28-436">Кроме того, могут выводиться предупреждения или сообщения об обработке, но для удобства здесь мы убрали их.</span><span class="sxs-lookup"><span data-stu-id="dbb28-436">You may see warnings or processing messages, but these messages have been removed from the following results for clarity.</span></span>

```console
=====Identify the objects in the images=====

.....The objects in the image image1.jpg are detected as below....
car and its Confidence score: 0.9697262
car and its Confidence score: 0.6674225
person and its Confidence score: 0.5226039
car and its Confidence score: 0.5224892
car and its Confidence score: 0.4675332

.....The objects in the image image2.jpg are detected as below....
cat and its Confidence score: 0.6461141
cat and its Confidence score: 0.6400049

.....The objects in the image image3.jpg are detected as below....
chair and its Confidence score: 0.840578
chair and its Confidence score: 0.796363
diningtable and its Confidence score: 0.6056048
diningtable and its Confidence score: 0.3737402

.....The objects in the image image4.jpg are detected as below....
dog and its Confidence score: 0.7608147
person and its Confidence score: 0.6321323
dog and its Confidence score: 0.5967442
person and its Confidence score: 0.5730394
person and its Confidence score: 0.5551759

========= End of Process..Hit any Key ========
```

<span data-ttu-id="dbb28-437">Чтобы просмотреть изображения с ограничивающими рамками, перейдите в каталог `assets/images/output/`.</span><span class="sxs-lookup"><span data-stu-id="dbb28-437">To see the images with bounding boxes, navigate to the `assets/images/output/` directory.</span></span> <span data-ttu-id="dbb28-438">Ниже приведен пример одного из обработанных изображений.</span><span class="sxs-lookup"><span data-stu-id="dbb28-438">Below is a sample from one of the processed images.</span></span> 

![](./media/object-detection-onnx/image3.jpg)

<span data-ttu-id="dbb28-439">Поздравляем!</span><span class="sxs-lookup"><span data-stu-id="dbb28-439">Congratulations!</span></span> <span data-ttu-id="dbb28-440">Вы успешно создали модель машинного обучения для обнаружения объектов на основе предварительно обученной модели `ONNX` в ML.NET.</span><span class="sxs-lookup"><span data-stu-id="dbb28-440">You've now successfully built a machine learning model for object detection by reusing a pre-trained `ONNX` model in ML.NET.</span></span>

<span data-ttu-id="dbb28-441">Исходный код для этого руководства можно найти в репозитории [dotnet/samples](https://github.com/dotnet/machinelearning-samples/tree/master/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx).</span><span class="sxs-lookup"><span data-stu-id="dbb28-441">You can find the source code for this tutorial at the [dotnet/samples](https://github.com/dotnet/machinelearning-samples/tree/master/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx) repository.</span></span>

<span data-ttu-id="dbb28-442">В этом руководстве вы узнали, как:</span><span class="sxs-lookup"><span data-stu-id="dbb28-442">In this tutorial, you learned how to:</span></span>
> [!div class="checklist"]
> * <span data-ttu-id="dbb28-443">Определение проблемы</span><span class="sxs-lookup"><span data-stu-id="dbb28-443">Understand the problem</span></span>
> * <span data-ttu-id="dbb28-444">Узнайте, что такое ONNX и как он работает с ML.NET</span><span class="sxs-lookup"><span data-stu-id="dbb28-444">Learn what ONNX is and how it works with ML.NET</span></span>
> * <span data-ttu-id="dbb28-445">Общие сведения о модели</span><span class="sxs-lookup"><span data-stu-id="dbb28-445">Understand the model</span></span>
> * <span data-ttu-id="dbb28-446">Повторное использование предварительно обученной модели</span><span class="sxs-lookup"><span data-stu-id="dbb28-446">Reuse the pre-trained model</span></span>
> * <span data-ttu-id="dbb28-447">Обнаружение объектов в загруженной модели</span><span class="sxs-lookup"><span data-stu-id="dbb28-447">Detect objects with a loaded model</span></span>

<span data-ttu-id="dbb28-448">Ознакомьтесь с примерами машинного обучения в репозитории GitHub, чтобы подробнее изучить расширенный пример обнаружения объектов.</span><span class="sxs-lookup"><span data-stu-id="dbb28-448">Check out the Machine Learning samples GitHub repository to explore an expanded object detection sample.</span></span>
> [!div class="nextstepaction"]
> [<span data-ttu-id="dbb28-449">Репозиторий GitHub dotnet/machinelearning-samples</span><span class="sxs-lookup"><span data-stu-id="dbb28-449">dotnet/machinelearning-samples GitHub repository</span></span>](https://github.com/dotnet/machinelearning-samples/tree/master/samples/csharp/end-to-end-apps/DeepLearning_ObjectDetection_Onnx)