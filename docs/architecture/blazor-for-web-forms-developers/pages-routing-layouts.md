---
title: Страницы, маршрутизация и макеты
description: Узнайте, как создавать страницы в Blazor , работать с маршрутизацией на стороне клиента и управлять макетами страниц.
author: danroth27
ms.author: daroth
no-loc:
- Blazor
ms.date: 09/19/2019
ms.openlocfilehash: fc1f6f9420c7149b6e67123f2f68bef75667aa0c
ms.sourcegitcommit: cb27c01a8b0b4630148374638aff4e2221f90b22
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/09/2020
ms.locfileid: "86173111"
---
# <a name="pages-routing-and-layouts"></a>Страницы, маршрутизация и макеты

[!INCLUDE [book-preview](../../../includes/book-preview.md)]

ASP.NET приложения Web Forms состоят из страниц, определенных в *ASPX* -файлах. Адрес каждой страницы зависит от его физического пути к файлу в проекте. Когда браузер выполняет запрос к странице, содержимое страницы динамически отображается на сервере. Учетные записи отрисовки как HTML-разметки страницы, так и ее серверных элементов управления.

В Blazor каждая страница в приложении является компонентом, обычно определяемым в файле *Razor* , с одним или несколькими указанными маршрутами. Маршрутизация в основном происходит на стороне клиента без участия конкретного запроса сервера. Сначала браузер выполняет запрос к корневому адресу приложения. Затем корневой `Router` компонент в Blazor приложении обрабатывает перехват запросов навигации и их в нужный компонент.

Blazorтакже поддерживает *глубокую компоновку*. Глубокое связывание происходит, когда браузер выполняет запрос к конкретному маршруту, отличному от корневого каталога приложения. Запросы на детализированные ссылки, отправленные на сервер, направляются в Blazor приложение, которое затем направляет клиентский запрос к нужному компоненту.

Простая страница в веб-формах ASP.NET может содержать следующую разметку:

*Имя. aspx*

```aspx-csharp
<%@ Page Title="Name" Language="C#" MasterPageFile="~/Site.Master" AutoEventWireup="true" CodeBehind="Name.aspx.cs" Inherits="WebApplication1.Name" %>

<asp:Content ID="BodyContent" ContentPlaceHolderID="MainContent" runat="server">
    <div>
        What is your name?<br />
        <asp:TextBox ID="TextBox1" runat="server"></asp:TextBox>
        <asp:Button ID="Button1" runat="server" Text="Submit" OnClick="Button1_Click" />
    </div>
    <div>
        <asp:Literal ID="Literal1" runat="server" />
    </div>
</asp:Content>
```

*Name.aspx.cs*

```csharp
public partial class Name : System.Web.UI.Page
{
    protected void Button1_Click1(object sender, EventArgs e)
    {
        Literal1.Text = "Hello " + TextBox1.Text;
    }
}
```

Эквивалентная страница в Blazor приложении будет выглядеть следующим образом:

*Name. Razor*

```razor
@page "/Name"
@layout MainLayout

<div>
    What is your name?<br />
    <input @bind="text" />
    <button @onclick="OnClick">Submit</button>
</div>
<div>
    @if (name != null)
    {
        @:Hello @name
    }
</div>

@code {
    string text;
    string name;

    void OnClick() {
        name = text;
    }
}
```

## <a name="create-pages"></a>Создание страниц

Чтобы создать страницу в Blazor , создайте компонент и добавьте `@page` директиву Razor, чтобы указать маршрут для компонента. `@page`Директива принимает один параметр, который является шаблоном маршрута, добавляемым к этому компоненту.

```razor
@page "/counter"
```

Параметр шаблона Route является обязательным. В отличие от веб-форм ASP.NET, маршрут к Blazor компоненту *не* определяется местоположением файла (хотя это может быть компонент, добавляемый в будущем).

Синтаксис шаблона маршрута — это тот же базовый синтаксис, который используется для маршрутизации в веб-формах ASP.NET. Параметры маршрута указываются в шаблоне с помощью фигурных скобок. Blazorпривязывает значения маршрута к параметрам компонента с тем же именем (без учета регистра).

```razor
@page "/product/{id}"

<h1>Product @Id</h1>

@code {
    [Parameter]
    public string Id { get; set; }
}
```

Можно также указать ограничения на значение параметра Route. Например, чтобы ограничить идентификатор продукта, `int` сделайте следующее:

```razor
@page "/product/{id:int}"

<h1>Product @Id</h1>

@code {
    [Parameter]
    public int Id { get; set; }
}
```

Полный список ограничений маршрута Blazor , поддерживаемых, см. в разделе [ограничения маршрутов](/aspnet/core/blazor/routing#route-constraints).

## <a name="router-component"></a>Компонент маршрутизатора

Маршрутизация в Blazor обрабатывается `Router` компонентом. `Router`Компонент обычно используется в корневом компоненте приложения (*app. Razor*).

```razor
<Router AppAssembly="@typeof(Program).Assembly">
    <Found Context="routeData">
        <RouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
    </Found>
    <NotFound>
        <LayoutView Layout="@typeof(MainLayout)">
            <p>Sorry, there's nothing at this address.</p>
        </LayoutView>
    </NotFound>
</Router>
```

`Router`Компонент обнаруживает маршрутизируемый компоненты в указанном `AppAssembly` и в дополнительно указанном параметре `AdditionalAssemblies` . При переходе в браузере объект `Router` перехватывает навигацию и отображает содержимое его `Found` параметра с извлеченным, `RouteData` Если маршрут соответствует адресу, в противном случае — `Router` отрисовывает свой `NotFound` параметр.

`RouteView`Компонент обрабатывает сопоставленный компонент, указанный в, `RouteData` с его макетом, если он имеется. Если сопоставляемый компонент не имеет макета, используется дополнительно указанный параметр `DefaultLayout` .

`LayoutView`Компонент визуализирует свое дочернее содержимое в указанном макете. Далее в этой главе мы рассмотрим макеты более подробно.

## <a name="navigation"></a>Навигация

В ASP.NET Web Forms вы запускаете навигацию на другой странице, возвращая ответ перенаправления в браузер. Пример.

```csharp
protected void NavigateButton_Click(object sender, EventArgs e)
{
    Response.Redirect("Counter");
}
```

Возврат ответа перенаправления обычно не поддерживается в Blazor . Blazorне использует модель "запрос-ответ". Однако вы можете активировать навигацию в браузере напрямую, как и в случае с JavaScript.

Blazorпредоставляет `NavigationManager` службу, которую можно использовать для:

- Получить текущий адрес браузера
- Получить базовый адрес
- Переходы по триггерам
- Получать уведомления при изменении адреса

Для перехода к другому адресу используйте `NavigateTo` метод:

```razor
@page "/"
@inject NavigationManager NavigationManager

<button @onclick="Navigate">Navigate</button>

@code {
    void Navigate() {
        NavigationManager.NavigateTo("counter");
    }
}
```

Описание всех `NavigationManager` членов см. в разделе [вспомогательные методы состояния URI и навигации](/aspnet/core/blazor/routing#uri-and-navigation-state-helpers).

## <a name="base-urls"></a>Базовые URL-адреса

Если Blazor приложение развертывается по базовому пути, необходимо указать базовый URL-адрес в метаданных страницы с помощью `<base>` тега для свойства маршрутизации к работе. Если страница узла для приложения отображается на сервере с помощью Razor, то можно использовать `~/` синтаксис для указания базового адреса приложения. Если страница узла является статическим HTML, необходимо явно указать базовый URL-адрес.

```html
<base href="~/" />
```

## <a name="page-layout"></a>Макет страницы

Макет страницы в веб-формах ASP.NET обрабатывается главными страницами. Главные страницы определяют шаблон с одним или несколькими заполнителями содержимого, которые затем могут быть предоставлены отдельными страницами. Главные страницы определяются в файлах *master* и начинаются с `<%@ Master %>` директивы. Содержимое *главных* файлов задается как страница *ASPX* , но с добавлением `<asp:ContentPlaceHolder>` элементов управления для пометки места, где страницы могут предоставлять содержимое.

*Site.master*

```aspx-csharp
<%@ Master Language="C#" AutoEventWireup="true" CodeBehind="Site.master.cs" Inherits="WebApplication1.SiteMaster" %>

<!DOCTYPE html>
<html lang="en">
<head runat="server">
    <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title><%: Page.Title %> - My ASP.NET Application</title>
    <link href="~/favicon.ico" rel="shortcut icon" type="image/x-icon" />
</head>
<body>
    <form runat="server">
        <div class="container body-content">
            <asp:ContentPlaceHolder ID="MainContent" runat="server">
            </asp:ContentPlaceHolder>
            <hr />
            <footer>
                <p>&copy; <%: DateTime.Now.Year %> - My ASP.NET Application</p>
            </footer>
        </div>
    </form>
</body>
</html>
```

В Blazor среде макет страницы обрабатывается с помощью компонентов макета. Компоненты макета наследуют от класса `LayoutComponentBase` , который определяет одно `Body` свойство типа `RenderFragment` , которое можно использовать для визуализации содержимого страницы.

*Маинлайаут. Razor*

```razor
@inherits LayoutComponentBase
<h1>Main layout</h1>
<div>
    @Body
</div>
```

При отображении страницы с макетом Эта страница подготавливается к просмотру в пределах содержимого указанного макета в расположении, в котором макет визуализирует свое `Body` свойство.

Чтобы применить макет к странице, используйте `@layout` директиву:

```razor
@layout MainLayout
```

Можно указать макет для всех компонентов в папке и вложенных папках, используя файл *_Imports. Razor* . Можно также указать макет по умолчанию для всех страниц с помощью [компонента маршрутизатора](#router-component).

Главные страницы могут определять несколько заполнителей содержимого, но макеты Blazor имеют только одно `Body` свойство. Это ограничение Blazor в отношении компонентов макета будет рассмотрено в следующем выпуске.

Главные страницы в веб-формах ASP.NET могут быть вложенными. То есть Главная страница может также использовать главную страницу. Компоненты макета в Blazor могут быть вложенными. Компонент макета можно применить к компоненту макета. Содержимое внутреннего макета будет отображаться во внешнем макете.

*Чилдлайаут. Razor*

```razor
@layout MainLayout
<h2>Child layout</h2>
<div>
    @Body
</div>
```

*Index. Razor*

```razor
@page "/"
@layout ChildLayout
<p>I'm in a nested layout!</p>
```

Отображаемые выходные данные для страницы будут выглядеть следующим образом:

```html
<h1>Main layout</h1>
<div>
    <h2>Child layout</h2>
    <div>
        <p>I'm in a nested layout!</p>
    </div>
</div>
```

Макеты в Blazor обычно не определяют корневые элементы HTML для страницы ( `<html>` , `<body>` , `<head>` и т. д.). Корневые элементы HTML определяются на Blazor странице узла приложения, которая используется для отрисовки начального содержимого HTML для приложения (см. раздел [Начальная Blazor ](project-structure.md#bootstrap-blazor)загрузка). Страница узла может визуализировать несколько корневых компонентов для приложения с окружающей разметкой.

Компоненты в Blazor , включая страницы, не могут отображать `<script>` теги. Это ограничение отрисовки существует `<script>` , так как теги загружаются один раз, а затем не могут быть изменены. Если вы попытаетесь динамически визуализировать Теги с помощью синтаксис Razor, может произойти непредвиденное поведение. Вместо этого все `<script>` теги должны быть добавлены на страницу узла приложения.

>[!div class="step-by-step"]
>[Назад](components.md)
>[Вперед](state-management.md)
