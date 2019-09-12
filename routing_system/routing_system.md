# Система маршрутизации 

Задачу, на какой контроллер и действие передать управление, решает система маршрутизации. Она разбивает приходящий URL на части и в соответствии со своими настройками передает управление на нужный контроллер и действие.

Эти настройки располагаются в файле **App_Start/RouteConfing.cs**. Изначально он выглядит так:

```c#
public class RouteConfig
{
    public static void RegisterRoutes(RouteCollection routes)
    {
        routes.IgnoreRoute("{resource}.axd/{*pathInfo}");

        routes.MapRoute(
            name: "Default",
            url: "{controller}/{action}/{id}",
            defaults: new { controller = "Home", 
                           action = "Index", 
                           id = UrlParameter.Optional }
        );
    }
}
```

## Метод .MapRoute

Позволяет зарегистрировать маршрут. Пример с использованием всех параметров:

```c#
Route myRoute = routes.MapRoute(
    name: "Default",
    url: "{controller}/{action}/page{n}",
    defaults: new { controller = "Home", action = "Index", n = UrlParameter.Optional },
    constraints: new {
    	contoller = @"^H.*", action = @"Index|About", // Имя
      	httpMethod = new HttpMethodConstraint("GET"),  // Тип запроса
      	n = new IntRouteConstraint(),  // Тип значения
    },
    namespaces: new[] { "UrlsAndRoutes.Controllers" }
);

myRoute.DataTokens["UseNamespaceFallback"] = false;
```

#### name

Тип string. Имя маршрута. Не является обязательным, но при редиректе можно указывать это имя, поэтому именование может пригодиться.

#### url

Тип string. Содержит шаблон, состоящий из *сегментов*, разделенных косой чертой /. Сегмент может быть динамическим (полностью заключается в фигурные скобки), статическим (полностью пишется без фигурных скобок) или комбинированным (часть в скобках, часть - без). Например, в шаблоне *{controller}/{action}/soccer/page{n}* сегменты {controller} и {action} - динамические, soccer - статический, а page{n} - комбинированный.

Имена **{controller}** и **{action}** имеют для фреймворка особое значение и всегда используются для обозначения контроллера и действия. Остальные сегменты вы можете называть произвольно и используются они обычно двумя способами:

* Как псевдонимы для путей.
* Как параметры для действий.

#### defaults

Тип object. Значения по умолчанию для сегментов. Задаются с помощью анонимного типа.

В основном имеет смысл использовать этот параметр для задания значений по умолчанию для контроллера и действия. Если эти сегменты 

#### constraints

object ограничения на значения и тип сегментов, на метод отправки http-запроса.

#### namespaces 

string[] Можно добавлять сколько угодно маршрутов. Порядок добавления имеет значение - более специфические маршруты должны следовать перед более общими.

