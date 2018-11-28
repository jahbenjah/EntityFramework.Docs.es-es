---
title: Etiquetas de consulta - EF Core
author: divega
ms.date: 11/14/2018
ms.assetid: 73C7A627-C8E9-452D-9CD5-AFCC8FEFE395
uid: core/querying/tags
ms.openlocfilehash: 3a4d516cab5836c659e42d825c4f1bf89355d671
ms.sourcegitcommit: b3c2b34d5f006ee3b41d6668f16fe7dcad1b4317
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/15/2018
ms.locfileid: "51688770"
---
# <a name="query-tags"></a><span data-ttu-id="7bc26-102">Etiquetas de consulta</span><span class="sxs-lookup"><span data-stu-id="7bc26-102">Query tags</span></span>
> [!NOTE]
> <span data-ttu-id="7bc26-103">Esta característica es nueva en EF Core 2.2.</span><span class="sxs-lookup"><span data-stu-id="7bc26-103">This feature is new in EF Core 2.2.</span></span>

<span data-ttu-id="7bc26-104">Esta característica ayuda a establecer la correlación de las consultas LINQ en el código con las consultas SQL generadas capturadas en los registros.</span><span class="sxs-lookup"><span data-stu-id="7bc26-104">This feature helps correlate LINQ queries in code with generated SQL queries captured in logs.</span></span>
<span data-ttu-id="7bc26-105">El usuario anota una consulta LINQ con el nuevo método `TagWith()`:</span><span class="sxs-lookup"><span data-stu-id="7bc26-105">You annotate a LINQ query using the new `TagWith()` method:</span></span> 

``` csharp
  var nearestFriends =
      (from f in context.Friends.TagWith("This is my spatial query!")
      orderby f.Location.Distance(myLocation) descending
      select f).Take(5).ToList();
```

<span data-ttu-id="7bc26-106">Esta consulta LINQ se traduce a la siguiente instrucción SQL:</span><span class="sxs-lookup"><span data-stu-id="7bc26-106">This LINQ query is translated to the following SQL statement:</span></span>

``` sql
-- This is my spatial query!

SELECT TOP(@__p_1) [f].[Name], [f].[Location]
FROM [Friends] AS [f]
ORDER BY [f].[Location].STDistance(@__myLocation_0) DESC
```

<span data-ttu-id="7bc26-107">Es posible llamar a `TagWith()` muchas veces en la misma consulta.</span><span class="sxs-lookup"><span data-stu-id="7bc26-107">It's possible to call `TagWith()` many times on the same query.</span></span>
<span data-ttu-id="7bc26-108">Las etiquetas de consulta son acumulativas.</span><span class="sxs-lookup"><span data-stu-id="7bc26-108">Query tags are cumulative.</span></span>
<span data-ttu-id="7bc26-109">Por ejemplo, si tenemos los siguientes métodos:</span><span class="sxs-lookup"><span data-stu-id="7bc26-109">For example, given the following methods:</span></span>

``` csharp
IQueryable<Friend> GetNearestFriends(Point myLocation) =>
    from f in context.Friends.TagWith("GetNearestFriends")
    orderby f.Location.Distance(myLocation) descending
    select f;

IQueryable<T> Limit<T>(IQueryable<T> source, int limit) =>
    source.TagWith("Limit").Take(limit);
```

<span data-ttu-id="7bc26-110">La siguiente consulta:</span><span class="sxs-lookup"><span data-stu-id="7bc26-110">The following query:</span></span>   

``` csharp
var results = Limit(GetNearestFriends(myLocation), 25).ToList();
```

<span data-ttu-id="7bc26-111">Se traduce en:</span><span class="sxs-lookup"><span data-stu-id="7bc26-111">Translates to:</span></span>

``` sql
-- GetNearestFriends

-- Limit

SELECT TOP(@__p_1) [f].[Name], [f].[Location]
FROM [Friends] AS [f]
ORDER BY [f].[Location].STDistance(@__myLocation_0) DESC
```

<span data-ttu-id="7bc26-112">También es posible utilizar cadenas de varias líneas como etiquetas de consulta.</span><span class="sxs-lookup"><span data-stu-id="7bc26-112">It's also possible to use multi-line strings as query tags.</span></span>
<span data-ttu-id="7bc26-113">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="7bc26-113">For example:</span></span>

``` csharp
var results = Limit(GetNearestFriends(myLocation), 25).TagWith(
@"This is a multi-line
string").ToList();
```

<span data-ttu-id="7bc26-114">Produce el siguiente SQL:</span><span class="sxs-lookup"><span data-stu-id="7bc26-114">Produces the following SQL:</span></span>

``` sql
-- GetNearestFriends

-- Limit

-- This is a multi-line
-- string

SELECT TOP(@__p_1) [f].[Name], [f].[Location]
FROM [Friends] AS [f]
ORDER BY [f].[Location].STDistance(@__myLocation_0) DESC
```

## <a name="known-limitations"></a><span data-ttu-id="7bc26-115">Limitaciones conocidas</span><span class="sxs-lookup"><span data-stu-id="7bc26-115">Known limitations</span></span>
<span data-ttu-id="7bc26-116">**Las etiquetas de consulta no se pueden parametrizar:** EF Core siempre trata las etiquetas de consulta de la consulta LINQ como literales de cadena que se incluyen en el código SQL generado.</span><span class="sxs-lookup"><span data-stu-id="7bc26-116">**Query tags aren't parameterizable:** EF Core always treats query tags in the LINQ query as string literals that are included in the generated SQL.</span></span>
<span data-ttu-id="7bc26-117">Las consultas compiladas que toman las etiquetas de consulta como parámetros no están permitidas.</span><span class="sxs-lookup"><span data-stu-id="7bc26-117">Compiled queries that take query tags as parameters aren't allowed.</span></span>