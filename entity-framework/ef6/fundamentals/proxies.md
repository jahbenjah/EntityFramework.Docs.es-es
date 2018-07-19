---
title: Trabajar con servidores proxy - EF6
author: divega
ms.date: 2016-10-23
ms.prod: entity-framework
ms.author: divega
ms.manager: avickers
ms.technology: entity-framework-6
ms.topic: article
ms.assetid: 869ee4dc-06f1-471d-8e0e-0a1a2bc59c30
caps.latest.revision: 3
ms.openlocfilehash: 4632e246d28a3cd53dabe5ac76e44f4538739abc
ms.sourcegitcommit: f05e7b62584cf228f17390bb086a61d505712e1b
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/08/2018
ms.locfileid: "39121879"
---
# <a name="working-with-proxies"></a><span data-ttu-id="a04e0-102">Trabajar con servidores proxy</span><span class="sxs-lookup"><span data-stu-id="a04e0-102">Working with proxies</span></span>
<span data-ttu-id="a04e0-103">Al crear instancias de tipos de entidad POCO, Entity Framework con frecuencia crea instancias de un tipo derivado generada dinámicamente que actúa como un proxy para la entidad.</span><span class="sxs-lookup"><span data-stu-id="a04e0-103">When creating instances of POCO entity types, Entity Framework often creates instances of a dynamically generated derived type that acts as a proxy for the entity.</span></span> <span data-ttu-id="a04e0-104">Este proxy reemplaza algunas propiedades de la entidad que se va a insertar enlaces para realizar automáticamente acciones cuando se tiene acceso a la propiedad virtuales.</span><span class="sxs-lookup"><span data-stu-id="a04e0-104">This proxy overrides some virtual properties of the entity to insert hooks for performing actions automatically when the property is accessed.</span></span> <span data-ttu-id="a04e0-105">Por ejemplo, este mecanismo se utiliza para admitir la carga diferida de relaciones.</span><span class="sxs-lookup"><span data-stu-id="a04e0-105">For example, this mechanism is used to support lazy loading of relationships.</span></span> <span data-ttu-id="a04e0-106">Las técnicas que se muestran en este tema se aplican igualmente a los modelos creados con Code First y EF Designer.</span><span class="sxs-lookup"><span data-stu-id="a04e0-106">The techniques shown in this topic apply equally to models created with Code First and the EF Designer.</span></span>  

## <a name="disabling-proxy-creation"></a><span data-ttu-id="a04e0-107">Deshabilitar la creación de proxy</span><span class="sxs-lookup"><span data-stu-id="a04e0-107">Disabling proxy creation</span></span>  

<span data-ttu-id="a04e0-108">A veces resulta útil para evitar que Entity Framework desde la creación de instancias de proxy.</span><span class="sxs-lookup"><span data-stu-id="a04e0-108">Sometimes it is useful to prevent Entity Framework from creating proxy instances.</span></span> <span data-ttu-id="a04e0-109">Por ejemplo, serializar instancias de proxy no es considerablemente más fácil que serializar instancias de proxy.</span><span class="sxs-lookup"><span data-stu-id="a04e0-109">For example, serializing non-proxy instances is considerably easier than serializing proxy instances.</span></span> <span data-ttu-id="a04e0-110">Creación de proxy puede desactivarse si desactiva la marca ProxyCreationEnabled.</span><span class="sxs-lookup"><span data-stu-id="a04e0-110">Proxy creation can be turned off by clearing the ProxyCreationEnabled flag.</span></span> <span data-ttu-id="a04e0-111">Un buen podría hacer esto es en el constructor de su contexto.</span><span class="sxs-lookup"><span data-stu-id="a04e0-111">One place you could do this is in the constructor of your context.</span></span> <span data-ttu-id="a04e0-112">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="a04e0-112">For example:</span></span>  

``` csharp
public class BloggingContext : DbContext
{
    public BloggingContext()
    {
        this.Configuration.ProxyCreationEnabled = false;
    }  

    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }
}
```  

<span data-ttu-id="a04e0-113">Tenga en cuenta que EF no creará los servidores proxy para los tipos que no hay nada para que realice el proxy.</span><span class="sxs-lookup"><span data-stu-id="a04e0-113">Note that the EF will not create proxies for types where there is nothing for the proxy to do.</span></span> <span data-ttu-id="a04e0-114">Esto significa que también puede evitar a los servidores proxy al tener tipos que están sellados o no tienen ninguna propiedad virtual.</span><span class="sxs-lookup"><span data-stu-id="a04e0-114">This means that you can also avoid proxies by having types that are sealed and/or have no virtual properties.</span></span>  

## <a name="explicitly-creating-an-instance-of-a-proxy"></a><span data-ttu-id="a04e0-115">Crear explícitamente una instancia de un servidor proxy</span><span class="sxs-lookup"><span data-stu-id="a04e0-115">Explicitly creating an instance of a proxy</span></span>  

<span data-ttu-id="a04e0-116">Si crea una instancia de una entidad mediante el operador new, no se creará una instancia del proxy.</span><span class="sxs-lookup"><span data-stu-id="a04e0-116">A proxy instance will not be created if you create an instance of an entity using the new operator.</span></span> <span data-ttu-id="a04e0-117">Esto puede no ser un problema, pero si necesita crear una instancia del proxy (por ejemplo, para que funcionan diferida carga o el proxy de seguimiento de cambios), a continuación, puede hacerlo mediante el método Create de DbSet.</span><span class="sxs-lookup"><span data-stu-id="a04e0-117">This may not be a problem, but if you need to create a proxy instance (for example, so that lazy loading or proxy change tracking will work) then you can do so using the Create method of DbSet.</span></span> <span data-ttu-id="a04e0-118">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="a04e0-118">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Create();
}
```  

<span data-ttu-id="a04e0-119">La versión genérica de Create puede utilizarse si desea crear una instancia de un tipo de entidad derivada.</span><span class="sxs-lookup"><span data-stu-id="a04e0-119">The generic version of Create can be used if you want to create an instance of a derived entity type.</span></span> <span data-ttu-id="a04e0-120">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="a04e0-120">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var admin = context.Users.Create<Administrator>();
}
```  

<span data-ttu-id="a04e0-121">Tenga en cuenta que el método Create no agregar o asociar la entidad creada en el contexto.</span><span class="sxs-lookup"><span data-stu-id="a04e0-121">Note that the Create method does not add or attach the created entity to the context.</span></span>  

<span data-ttu-id="a04e0-122">Tenga en cuenta que el método Create simplemente creará una instancia del propio tipo de entidad si la creación de un tipo de proxy para la entidad no tendría ningún valor porque podría no hacer nada.</span><span class="sxs-lookup"><span data-stu-id="a04e0-122">Note that the Create method will just create an instance of the entity type itself if creating a proxy type for the entity would have no value because it would not do anything.</span></span> <span data-ttu-id="a04e0-123">Por ejemplo, si el tipo de entidad está sellado o no tiene ninguna propiedad virtual crear simplemente creará una instancia del tipo de entidad.</span><span class="sxs-lookup"><span data-stu-id="a04e0-123">For example, if the entity type is sealed and/or has no virtual properties then Create will just create an instance of the entity type.</span></span>  

## <a name="getting-the-actual-entity-type-from-a-proxy-type"></a><span data-ttu-id="a04e0-124">Obtener el tipo de entidad real de un tipo de proxy</span><span class="sxs-lookup"><span data-stu-id="a04e0-124">Getting the actual entity type from a proxy type</span></span>  

<span data-ttu-id="a04e0-125">Tipos de proxy tienen nombres con un aspecto similar al siguiente:</span><span class="sxs-lookup"><span data-stu-id="a04e0-125">Proxy types have names that look something like this:</span></span>  

<span data-ttu-id="a04e0-126">System.Data.Entity.DynamicProxies.Blog_5E43C6C196972BF0754973E48C9C941092D86818CD94005E9A759B70BF6E48E6</span><span class="sxs-lookup"><span data-stu-id="a04e0-126">System.Data.Entity.DynamicProxies.Blog_5E43C6C196972BF0754973E48C9C941092D86818CD94005E9A759B70BF6E48E6</span></span>  

<span data-ttu-id="a04e0-127">Puede encontrar el tipo de entidad para este tipo de proxy con el método GetObjectType de ObjectContext.</span><span class="sxs-lookup"><span data-stu-id="a04e0-127">You can find the entity type for this proxy type using the GetObjectType method from ObjectContext.</span></span> <span data-ttu-id="a04e0-128">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="a04e0-128">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);
    var entityType = ObjectContext.GetObjectType(blog.GetType());
}
```  

<span data-ttu-id="a04e0-129">Tenga en cuenta que si el tipo pasado a GetObjectType es todavía se devuelve una instancia de un tipo de entidad que no es un tipo de proxy, a continuación, el tipo de entidad.</span><span class="sxs-lookup"><span data-stu-id="a04e0-129">Note that if the type passed to GetObjectType is an instance of an entity type that is not a proxy type then the type of entity is still returned.</span></span> <span data-ttu-id="a04e0-130">Esto significa que siempre puede usar este método para obtener el tipo de entidad real sin otras comprobaciones para ver si el tipo es un tipo de proxy o no.</span><span class="sxs-lookup"><span data-stu-id="a04e0-130">This means you can always use this method to get the actual entity type without any other checking to see if the type is a proxy type or not.</span></span>  