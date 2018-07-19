---
title: 'Datos locales: EF6'
author: divega
ms.date: 2016-10-23
ms.prod: entity-framework
ms.author: divega
ms.manager: avickers
ms.technology: entity-framework-6
ms.topic: article
ms.assetid: 2eda668b-1e5d-487d-9a8c-0e3beef03fcb
caps.latest.revision: 3
ms.openlocfilehash: 79f0d2175199780d41b43088832bab808ab2fff0
ms.sourcegitcommit: f05e7b62584cf228f17390bb086a61d505712e1b
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/08/2018
ms.locfileid: "39122303"
---
# <a name="local-data"></a><span data-ttu-id="f6fd1-102">Datos locales</span><span class="sxs-lookup"><span data-stu-id="f6fd1-102">Local Data</span></span>
<span data-ttu-id="f6fd1-103">Ejecuta una consulta LINQ directamente en una clase DbSet siempre enviará una consulta a la base de datos, pero puede tener acceso a los datos que están actualmente en memoria mediante la propiedad DbSet.Local.</span><span class="sxs-lookup"><span data-stu-id="f6fd1-103">Running a LINQ query directly against a DbSet will always send a query to the database, but you can access the data that is currently in-memory using the DbSet.Local property.</span></span> <span data-ttu-id="f6fd1-104">También puede tener acceso a la EF está realizando el seguimiento de información adicional acerca de las entidades mediante los métodos DbContext.Entry y DbContext.ChangeTracker.Entries.</span><span class="sxs-lookup"><span data-stu-id="f6fd1-104">You can also access the extra information EF is tracking about your entities using the DbContext.Entry and DbContext.ChangeTracker.Entries methods.</span></span> <span data-ttu-id="f6fd1-105">Las técnicas que se muestran en este tema se aplican igualmente a los modelos creados con Code First y EF Designer.</span><span class="sxs-lookup"><span data-stu-id="f6fd1-105">The techniques shown in this topic apply equally to models created with Code First and the EF Designer.</span></span>  

## <a name="using-local-to-look-at-local-data"></a><span data-ttu-id="f6fd1-106">Para ver los datos locales mediante Local</span><span class="sxs-lookup"><span data-stu-id="f6fd1-106">Using Local to look at local data</span></span>  

<span data-ttu-id="f6fd1-107">La propiedad Local de DbSet ofrece acceso sencillo a las entidades del conjunto que se realiza un seguimiento actualmente por el contexto y no se han marcado como eliminado.</span><span class="sxs-lookup"><span data-stu-id="f6fd1-107">The Local property of DbSet provides simple access to the entities of the set that are currently being tracked by the context and have not been marked as Deleted.</span></span> <span data-ttu-id="f6fd1-108">Acceso a la propiedad Local nunca hace que una consulta para enviarse a la base de datos.</span><span class="sxs-lookup"><span data-stu-id="f6fd1-108">Accessing the Local property never causes a query to be sent to the database.</span></span> <span data-ttu-id="f6fd1-109">Esto significa que se utiliza normalmente después de que ya se ha realizado una consulta.</span><span class="sxs-lookup"><span data-stu-id="f6fd1-109">This means that it is usually used after a query has already been performed.</span></span> <span data-ttu-id="f6fd1-110">El método de extensión de carga puede usarse para ejecutar una consulta para que el contexto realiza un seguimiento de los resultados.</span><span class="sxs-lookup"><span data-stu-id="f6fd1-110">The Load extension method can be used to execute a query so that the context tracks the results.</span></span> <span data-ttu-id="f6fd1-111">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="f6fd1-111">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    // Load all blogs from the database into the context
    context.Blogs.Load();

    // Add a new blog to the context
    context.Blogs.Add(new Blog { Name = "My New Blog" });

    // Mark one of the existing blogs as Deleted
    context.Blogs.Remove(context.Blogs.Find(1));

    // Loop over the blogs in the context.
    Console.WriteLine("In Local: ");
    foreach (var blog in context.Blogs.Local)
    {
        Console.WriteLine(
            "Found {0}: {1} with state {2}",
            blog.BlogId,  
            blog.Name,
            context.Entry(blog).State);
    }

    // Perform a query against the database.
    Console.WriteLine("\nIn DbSet query: ");
    foreach (var blog in context.Blogs)
    {
        Console.WriteLine(
            "Found {0}: {1} with state {2}",
            blog.BlogId,  
            blog.Name,
            context.Entry(blog).State);
    }
}
```  

<span data-ttu-id="f6fd1-112">Si tuviéramos dos blogs en la base de datos - 'ADO.NET Blog' con un BlogId 1 - y "Blog de Visual Studio' con un BlogId 2 podríamos esperamos el siguiente resultado:</span><span class="sxs-lookup"><span data-stu-id="f6fd1-112">If we had two blogs in the database - 'ADO.NET Blog' with a BlogId of 1 and 'The Visual Studio Blog' with a BlogId of 2 - we could expect the following output:</span></span>  

```  
In Local:
Found 0: My New Blog with state Added
Found 2: The Visual Studio Blog with state Unchanged

In DbSet query:
Found 1: ADO.NET Blog with state Deleted
Found 2: The Visual Studio Blog with state Unchanged
```  

<span data-ttu-id="f6fd1-113">Esto muestra tres puntos:</span><span class="sxs-lookup"><span data-stu-id="f6fd1-113">This illustrates three points:</span></span>  

- <span data-ttu-id="f6fd1-114">El nuevo blog de 'Mi nuevo Blog' se incluye en la colección Local, aunque lo aún no se ha guardado en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="f6fd1-114">The new blog 'My New Blog' is included in the Local collection even though it has not yet been saved to the database.</span></span> <span data-ttu-id="f6fd1-115">Esta entrada de blog tiene una clave principal de cero porque la base de datos no ha generado todavía una clave de real para la entidad.</span><span class="sxs-lookup"><span data-stu-id="f6fd1-115">This blog has a primary key of zero because the database has not yet generated a real key for the entity.</span></span>  
- <span data-ttu-id="f6fd1-116">El Blog del ADO.NET no se incluye en la colección local, aunque todavía se está realizando un seguimiento por el contexto.</span><span class="sxs-lookup"><span data-stu-id="f6fd1-116">The 'ADO.NET Blog' is not included in the local collection even though it is still being tracked by the context.</span></span> <span data-ttu-id="f6fd1-117">Esto es porque se ha quitado de la clase DbSet, por tanto, lo marca como eliminada.</span><span class="sxs-lookup"><span data-stu-id="f6fd1-117">This is because we removed it from the DbSet thereby marking it as deleted.</span></span>  
- <span data-ttu-id="f6fd1-118">Cuando se utiliza DbSet para realizar una consulta el blog de marcado para su eliminación (Blog de ADO.NET) se incluye en los resultados y el nuevo blog (Blog de mi nuevo) que aún no se ha guardado en la base de datos no se incluye en los resultados.</span><span class="sxs-lookup"><span data-stu-id="f6fd1-118">When DbSet is used to perform a query the blog marked for deletion (ADO.NET Blog) is included in the results and the new blog (My New Blog) that has not yet been saved to the database is not included in the results.</span></span> <span data-ttu-id="f6fd1-119">Esto es porque DbSet está realizando una consulta en la base de datos y los resultados devueltos siempre reflejan lo que está en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="f6fd1-119">This is because DbSet is performing a query against the database and the results returned always reflect what is in the database.</span></span>  

## <a name="using-local-to-add-and-remove-entities-from-the-context"></a><span data-ttu-id="f6fd1-120">Uso a Local para agregar y quitar las entidades en el contexto</span><span class="sxs-lookup"><span data-stu-id="f6fd1-120">Using Local to add and remove entities from the context</span></span>  

<span data-ttu-id="f6fd1-121">Devuelve la propiedad Local en DbSet un [ObservableCollection](https://msdn.microsoft.com/library/ms668604.aspx) con eventos que se enlazó tal que se mantiene sincronizada con el contenido del contexto.</span><span class="sxs-lookup"><span data-stu-id="f6fd1-121">The Local property on DbSet returns an [ObservableCollection](https://msdn.microsoft.com/library/ms668604.aspx) with events hooked up such that it stays in sync with the contents of the context.</span></span> <span data-ttu-id="f6fd1-122">Esto significa que las entidades se pueden agregar o quitar de la colección Local o la clase DbSet.</span><span class="sxs-lookup"><span data-stu-id="f6fd1-122">This means that entities can be added or removed from either the Local collection or the DbSet.</span></span> <span data-ttu-id="f6fd1-123">También significa que las consultas que introducir nuevas entidades en el contexto dará como resultado de la colección Local que se va a actualizar con esas entidades.</span><span class="sxs-lookup"><span data-stu-id="f6fd1-123">It also means that queries that bring new entities into the context will result in the Local collection being updated with those entities.</span></span> <span data-ttu-id="f6fd1-124">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="f6fd1-124">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    // Load some posts from the database into the context
    context.Posts.Where(p => p.Tags.Contains("entity-framework").Load();  

    // Get the local collection and make some changes to it
    var localPosts = context.Posts.Local;
    localPosts.Add(new Post { Name = "What's New in EF" });
    localPosts.Remove(context.Posts.Find(1));  

    // Loop over the posts in the context.
    Console.WriteLine("In Local after entity-framework query: ");
    foreach (var post in context.Posts.Local)
    {
        Console.WriteLine(
            "Found {0}: {1} with state {2}",
            post.Id,  
            post.Title,
            context.Entry(post).State);
    }

    var post1 = context.Posts.Find(1);
    Console.WriteLine(
        "State of post 1: {0} is {1}",
        post1.Name,  
        context.Entry(post1).State);  

    // Query some more posts from the database
    context.Posts.Where(p => p.Tags.Contains("asp.net").Load();  

    // Loop over the posts in the context again.
    Console.WriteLine("\nIn Local after asp.net query: ");
    foreach (var post in context.Posts.Local)
    {
        Console.WriteLine(
            "Found {0}: {1} with state {2}",
            post.Id,  
            post.Title,
            context.Entry(post).State);
    }
}
```  

<span data-ttu-id="f6fd1-125">Supongamos que contamos con unas pocas entradas etiquetadas con 'entity framework' y 'asp.net' la salida podría tener un aspecto similar al siguiente:</span><span class="sxs-lookup"><span data-stu-id="f6fd1-125">Assuming we had a few posts tagged with 'entity-framework' and 'asp.net' the output may look something like this:</span></span>  

```  
In Local after entity-framework query:
Found 3: EF Designer Basics with state Unchanged
Found 5: EF Code First Basics with state Unchanged
Found 0: What's New in EF with state Added
State of post 1: EF Beginners Guide is Deleted

In Local after asp.net query:
Found 3: EF Designer Basics with state Unchanged
Found 5: EF Code First Basics with state Unchanged
Found 0: What's New in EF with state Added
Found 4: ASP.NET Beginners Guide with state Unchanged
```  

<span data-ttu-id="f6fd1-126">Esto muestra tres puntos:</span><span class="sxs-lookup"><span data-stu-id="f6fd1-126">This illustrates three points:</span></span>  

- <span data-ttu-id="f6fd1-127">La nueva publicación de 'Novedades de EF' que se agregó local colección realiza un seguimiento el contexto en estado Added.</span><span class="sxs-lookup"><span data-stu-id="f6fd1-127">The new post 'What's New in EF' that was added to the Local collection becomes tracked by the context in the Added state.</span></span> <span data-ttu-id="f6fd1-128">Por lo tanto, se insertará en la base de datos cuando se llama a SaveChanges.</span><span class="sxs-lookup"><span data-stu-id="f6fd1-128">It will therefore be inserted into the database when SaveChanges is called.</span></span>  
- <span data-ttu-id="f6fd1-129">La entrada que se ha quitado de la colección Local (Guía básica de EF) ahora se marca como eliminada en el contexto.</span><span class="sxs-lookup"><span data-stu-id="f6fd1-129">The post that was removed from the Local collection (EF Beginners Guide) is now marked as deleted in the context.</span></span> <span data-ttu-id="f6fd1-130">Por lo tanto, se eliminará de la base de datos cuando se llama a SaveChanges.</span><span class="sxs-lookup"><span data-stu-id="f6fd1-130">It will therefore be deleted from the database when SaveChanges is called.</span></span>  
- <span data-ttu-id="f6fd1-131">La entrada adicional (Guía básica de ASP.NET) cargada en el contexto con la segunda consulta se agrega automáticamente a la colección Local.</span><span class="sxs-lookup"><span data-stu-id="f6fd1-131">The additional post (ASP.NET Beginners Guide) loaded into the context with the second query is automatically added to the Local collection.</span></span>  

<span data-ttu-id="f6fd1-132">Una última cosa a tener en cuenta sobre Local es porque es que un ObservableCollection de rendimiento no es excelente para un gran número de entidades.</span><span class="sxs-lookup"><span data-stu-id="f6fd1-132">One final thing to note about Local is that because it is an ObservableCollection performance is not great for large numbers of entities.</span></span> <span data-ttu-id="f6fd1-133">Por lo tanto, si está trabajando con miles de entidades en el contexto puede no ser aconsejable usar a Local.</span><span class="sxs-lookup"><span data-stu-id="f6fd1-133">Therefore if you are dealing with thousands of entities in your context it may not be advisable to use Local.</span></span>  

## <a name="using-local-for-wpf-data-binding"></a><span data-ttu-id="f6fd1-134">Uso a Local para el enlace de datos WPF</span><span class="sxs-lookup"><span data-stu-id="f6fd1-134">Using Local for WPF data binding</span></span>  

<span data-ttu-id="f6fd1-135">La propiedad Local en DbSet puede utilizarse directamente para el enlace de datos en una aplicación de WPF porque es una instancia de ObservableCollection.</span><span class="sxs-lookup"><span data-stu-id="f6fd1-135">The Local property on DbSet can be used directly for data binding in a WPF application because it is an instance of ObservableCollection.</span></span> <span data-ttu-id="f6fd1-136">Como se describe en las secciones anteriores, de que esto significa que automáticamente permanecerá sincronizada con el contenido del contexto y configurará automáticamente y el contenido del contexto estar sincronizados con él.</span><span class="sxs-lookup"><span data-stu-id="f6fd1-136">As described in the previous sections this means that it will automatically stay in sync with the contents of the context and the contents of the context will automatically stay in sync with it.</span></span> <span data-ttu-id="f6fd1-137">Tenga en cuenta que es necesario rellenar previamente la colección Local con los datos para que haya algo para enlazar a, ya que Local nunca produce una consulta de base de datos.</span><span class="sxs-lookup"><span data-stu-id="f6fd1-137">Note that you do need to pre-populate the Local collection with data for there to be anything to bind to since Local never causes a database query.</span></span>  

<span data-ttu-id="f6fd1-138">Esto no es un lugar adecuado para obtener un ejemplo de enlace de datos WPF completo, pero los elementos clave son:</span><span class="sxs-lookup"><span data-stu-id="f6fd1-138">This is not an appropriate place for a full WPF data binding sample but the key elements are:</span></span>  

- <span data-ttu-id="f6fd1-139">Programa de instalación de un origen de enlace</span><span class="sxs-lookup"><span data-stu-id="f6fd1-139">Setup a binding source</span></span>  
- <span data-ttu-id="f6fd1-140">Enlazarla a la propiedad Local del conjunto de</span><span class="sxs-lookup"><span data-stu-id="f6fd1-140">Bind it to the Local property of your set</span></span>  
- <span data-ttu-id="f6fd1-141">Rellenar a Local mediante una consulta a la base de datos.</span><span class="sxs-lookup"><span data-stu-id="f6fd1-141">Populate Local using a query to the database.</span></span>  

## <a name="wpf-binding-to-navigation-properties"></a><span data-ttu-id="f6fd1-142">Enlace de WPF a las propiedades de navegación</span><span class="sxs-lookup"><span data-stu-id="f6fd1-142">WPF binding to navigation properties</span></span>  

<span data-ttu-id="f6fd1-143">Si está realizando enlaces de datos principal-detalle es posible que desee enlazar la vista de detalle para una propiedad de navegación de una de las entidades.</span><span class="sxs-lookup"><span data-stu-id="f6fd1-143">If you are doing master/detail data binding you may want to bind the detail view to a navigation property of one of your entities.</span></span> <span data-ttu-id="f6fd1-144">Una manera fácil para solucionar este problema es usar una colección ObservableCollection para la propiedad de navegación.</span><span class="sxs-lookup"><span data-stu-id="f6fd1-144">An easy way to make this work is to use an ObservableCollection for the navigation property.</span></span> <span data-ttu-id="f6fd1-145">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="f6fd1-145">For example:</span></span>  

``` csharp
public class Blog
{
    private readonly ObservableCollection<Post> _posts =
        new ObservableCollection<Post>();

    public int BlogId { get; set; }
    public string Name { get; set; }

    public virtual ObservableCollection<Post> Posts
    {
        get { return _posts; }
    }
}
```  

## <a name="using-local-to-clean-up-entities-in-savechanges"></a><span data-ttu-id="f6fd1-146">Uso a Local para limpiar las entidades de SaveChanges</span><span class="sxs-lookup"><span data-stu-id="f6fd1-146">Using Local to clean up entities in SaveChanges</span></span>  

<span data-ttu-id="f6fd1-147">En la mayoría de los casos las entidades que se quita de una propiedad de navegación no automáticamente marcará como eliminados en el contexto.</span><span class="sxs-lookup"><span data-stu-id="f6fd1-147">In most cases entities removed from a navigation property will not be automatically marked as deleted in the context.</span></span> <span data-ttu-id="f6fd1-148">Por ejemplo, si quita un objeto de entrada de la colección Blog.Posts registrar no se eliminan automáticamente cuando se llama a SaveChanges.</span><span class="sxs-lookup"><span data-stu-id="f6fd1-148">For example, if you remove a Post object from the Blog.Posts collection then that post will not be automatically deleted when SaveChanges is called.</span></span> <span data-ttu-id="f6fd1-149">Si debe eliminarse, a continuación, deberá buscar estas entidades pendientes y los marca como eliminado antes de llamar a SaveChanges o como parte de un SaveChanges invalidado.</span><span class="sxs-lookup"><span data-stu-id="f6fd1-149">If you need it to be deleted then you may need to find these dangling entities and mark them as deleted before calling SaveChanges or as part of an overridden SaveChanges.</span></span> <span data-ttu-id="f6fd1-150">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="f6fd1-150">For example:</span></span>  

``` csharp
public override int SaveChanges()
{
    foreach (var post in this.Posts.Local.ToList())
    {
        if (post.Blog == null)
        {
            this.Posts.Remove(post);
        }
    }

    return base.SaveChanges();
}
```  

<span data-ttu-id="f6fd1-151">El código anterior usa la colección Local para buscar todas las publicaciones y las marcas de cualquier usuario que no tiene una referencia blog como eliminado.</span><span class="sxs-lookup"><span data-stu-id="f6fd1-151">The code above uses the Local collection to find all posts and marks any that do not have a blog reference as deleted.</span></span> <span data-ttu-id="f6fd1-152">La llamada ToList es necesaria porque de lo contrario, se modificará la colección mediante la eliminación llamar mientras se está enumerando.</span><span class="sxs-lookup"><span data-stu-id="f6fd1-152">The ToList call is required because otherwise the collection will be modified by the Remove call while it is being enumerated.</span></span> <span data-ttu-id="f6fd1-153">En la mayoría de los otra casos puede consultar directamente en la propiedad Local sin usar primero ToList.</span><span class="sxs-lookup"><span data-stu-id="f6fd1-153">In most other situations you can query directly against the Local property without using ToList first.</span></span>  

## <a name="using-local-and-tobindinglist-for-windows-forms-data-binding"></a><span data-ttu-id="f6fd1-154">Uso de enlace de datos Local y ToBindingList for Windows Forms</span><span class="sxs-lookup"><span data-stu-id="f6fd1-154">Using Local and ToBindingList for Windows Forms data binding</span></span>  

<span data-ttu-id="f6fd1-155">Formularios de Windows no admite el enlace de datos con plena fidelidad con ObservableCollection directamente.</span><span class="sxs-lookup"><span data-stu-id="f6fd1-155">Windows Forms does not support full fidelity data binding using ObservableCollection directly.</span></span> <span data-ttu-id="f6fd1-156">Sin embargo, todavía puede usar la propiedad DbSet Local para el enlace de datos para obtener todas las ventajas descritas en las secciones anteriores.</span><span class="sxs-lookup"><span data-stu-id="f6fd1-156">However, you can still use the DbSet Local property for data binding to get all the benefits described in the previous sections.</span></span> <span data-ttu-id="f6fd1-157">Esto se logra mediante el método de extensión ToBindingList que crea un [IBindingList](https://msdn.microsoft.com/library/system.componentmodel.ibindinglist.aspx) implementación respaldada por el objeto ObservableCollection Local.</span><span class="sxs-lookup"><span data-stu-id="f6fd1-157">This is achieved through the ToBindingList extension method which creates an [IBindingList](https://msdn.microsoft.com/library/system.componentmodel.ibindinglist.aspx) implementation backed by the Local ObservableCollection.</span></span>  

<span data-ttu-id="f6fd1-158">Esto no es un lugar adecuado para obtener un ejemplo de enlace de datos de Windows Forms completo, pero los elementos clave son:</span><span class="sxs-lookup"><span data-stu-id="f6fd1-158">This is not an appropriate place for a full Windows Forms data binding sample but the key elements are:</span></span>  

- <span data-ttu-id="f6fd1-159">Configurar un origen de enlace de objetos</span><span class="sxs-lookup"><span data-stu-id="f6fd1-159">Setup an object binding source</span></span>  
- <span data-ttu-id="f6fd1-160">Enlazarla a la propiedad Local de su conjunto mediante Local.ToBindingList()</span><span class="sxs-lookup"><span data-stu-id="f6fd1-160">Bind it to the Local property of your set using Local.ToBindingList()</span></span>  
- <span data-ttu-id="f6fd1-161">Rellenar a Local mediante una consulta a la base de datos</span><span class="sxs-lookup"><span data-stu-id="f6fd1-161">Populate Local using a query to the database</span></span>  

## <a name="getting-detailed-information-about-tracked-entities"></a><span data-ttu-id="f6fd1-162">Obtener información detallada acerca de las entidades sometidas a seguimiento</span><span class="sxs-lookup"><span data-stu-id="f6fd1-162">Getting detailed information about tracked entities</span></span>  

<span data-ttu-id="f6fd1-163">Muchos de los ejemplos en esta serie de usan el método de entrada para devolver una instancia de DbEntityEntry para una entidad.</span><span class="sxs-lookup"><span data-stu-id="f6fd1-163">Many of the examples in this series use the Entry method to return a DbEntityEntry instance for an entity.</span></span> <span data-ttu-id="f6fd1-164">Este objeto de entrada, a continuación, actúa como punto de partida para recopilar información acerca de la entidad, como su estado actual, así como para realizar operaciones en la entidad como la carga explícita de una entidad relacionada.</span><span class="sxs-lookup"><span data-stu-id="f6fd1-164">This entry object then acts as the starting point for gathering information about the entity such as its current state, as well as for performing operations on the entity such as explicitly loading a related entity.</span></span>  

<span data-ttu-id="f6fd1-165">Los métodos de las entradas devuelven objetos DbEntityEntry para muchas o todas las entidades que realiza un seguimiento del contexto.</span><span class="sxs-lookup"><span data-stu-id="f6fd1-165">The Entries methods return DbEntityEntry objects for many or all entities being tracked by the context.</span></span> <span data-ttu-id="f6fd1-166">Esto le permite recopilar información o realizar operaciones en varias entidades en lugar de simplemente una sola entrada.</span><span class="sxs-lookup"><span data-stu-id="f6fd1-166">This allows you to gather information or perform operations on many entities rather than just a single entry.</span></span> <span data-ttu-id="f6fd1-167">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="f6fd1-167">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    // Load some entities into the context
    context.Blogs.Load();
    context.Authors.Load();
    context.Readers.Load();

    // Make some changes
    context.Blogs.Find(1).Title = "The New ADO.NET Blog";
    context.Blogs.Remove(context.Blogs.Find(2));
    context.Authors.Add(new Author { Name = "Jane Doe" });
    context.Readers.Find(1).Username = "johndoe1987";

    // Look at the state of all entities in the context
    Console.WriteLine("All tracked entities: ");
    foreach (var entry in context.ChangeTracker.Entries())
    {
        Console.WriteLine(
            "Found entity of type {0} with state {1}",
            ObjectContext.GetObjectType(entry.Entity.GetType()).Name,
            entry.State);
    }

    // Find modified entities of any type
    Console.WriteLine("\nAll modified entities: ");
    foreach (var entry in context.ChangeTracker.Entries()
                              .Where(e => e.State == EntityState.Modified))
    {
        Console.WriteLine(
            "Found entity of type {0} with state {1}",
            ObjectContext.GetObjectType(entry.Entity.GetType()).Name,
            entry.State);
    }

    // Get some information about just the tracked blogs
    Console.WriteLine("\nTracked blogs: ");
    foreach (var entry in context.ChangeTracker.Entries<Blog>())
    {
        Console.WriteLine(
            "Found Blog {0}: {1} with original Name {2}",
            entry.Entity.BlogId,  
            entry.Entity.Name,
            entry.Property(p => p.Name).OriginalValue);
    }

    // Find all people (author or reader)
    Console.WriteLine("\nPeople: ");
    foreach (var entry in context.ChangeTracker.Entries<IPerson>())
    {
        Console.WriteLine("Found Person {0}", entry.Entity.Name);
    }
}
```  

<span data-ttu-id="f6fd1-168">Observará que presentamos una clase de autor y el lector en el ejemplo: ambas clases implementan la interfaz de IPerson.</span><span class="sxs-lookup"><span data-stu-id="f6fd1-168">You'll notice we are introducing a Author and Reader class into the example - both of these classes implement the IPerson interface.</span></span>  

``` csharp
public class Author : IPerson
{
    public int AuthorId { get; set; }
    public string Name { get; set; }
    public string Biography { get; set; }
}

public class Reader : IPerson
{
    public int ReaderId { get; set; }
    public string Name { get; set; }
    public string Username { get; set; }
}

public interface IPerson
{
    string Name { get; }
}
```  

<span data-ttu-id="f6fd1-169">Supongamos que tenemos los datos siguientes en la base de datos:</span><span class="sxs-lookup"><span data-stu-id="f6fd1-169">Let's assume we have the following data in the database:</span></span>

<span data-ttu-id="f6fd1-170">Blog de BlogId = 1 y nombre = 'Blog de ADO.NET'</span><span class="sxs-lookup"><span data-stu-id="f6fd1-170">Blog with BlogId = 1 and Name = 'ADO.NET Blog'</span></span>  
<span data-ttu-id="f6fd1-171">Blog de BlogId = 2 y el nombre = "Blog de Visual Studio"</span><span class="sxs-lookup"><span data-stu-id="f6fd1-171">Blog with BlogId = 2 and Name = 'The Visual Studio Blog'</span></span>  
<span data-ttu-id="f6fd1-172">Blog de BlogId = 3 y el nombre = 'Blog de .NET Framework'</span><span class="sxs-lookup"><span data-stu-id="f6fd1-172">Blog with BlogId = 3 and Name = '.NET Framework Blog'</span></span>  
<span data-ttu-id="f6fd1-173">Autor con AuthorId = 1 y nombre = 'Joe Calvo'</span><span class="sxs-lookup"><span data-stu-id="f6fd1-173">Author with AuthorId = 1 and Name = 'Joe Bloggs'</span></span>  
<span data-ttu-id="f6fd1-174">Lector con ReaderId = 1 y nombre = "John Doe"</span><span class="sxs-lookup"><span data-stu-id="f6fd1-174">Reader with ReaderId = 1 and Name = 'John Doe'</span></span>  

<span data-ttu-id="f6fd1-175">El resultado de ejecutar el código sería:</span><span class="sxs-lookup"><span data-stu-id="f6fd1-175">The output from running the code would be:</span></span>  

```  
All tracked entities:
Found entity of type Blog with state Modified
Found entity of type Blog with state Deleted
Found entity of type Blog with state Unchanged
Found entity of type Author with state Unchanged
Found entity of type Author with state Added
Found entity of type Reader with state Modified

All modified entities:
Found entity of type Blog with state Modified
Found entity of type Reader with state Modified

Tracked blogs:
Found Blog 1: The New ADO.NET Blog with original Name ADO.NET Blog
Found Blog 2: The Visual Studio Blog with original Name The Visual Studio Blog
Found Blog 3: .NET Framework Blog with original Name .NET Framework Blog

People:
Found Person John Doe
Found Person Joe Bloggs
Found Person Jane Doe
```  

<span data-ttu-id="f6fd1-176">Estos ejemplos muestran varios puntos:</span><span class="sxs-lookup"><span data-stu-id="f6fd1-176">These examples illustrate several points:</span></span>  

- <span data-ttu-id="f6fd1-177">Los métodos de entradas devuelven las entradas para las entidades en todos los Estados, incluida Deleted.</span><span class="sxs-lookup"><span data-stu-id="f6fd1-177">The Entries methods return entries for entities in all states, including Deleted.</span></span> <span data-ttu-id="f6fd1-178">Esta opción para comparar Local, que excluye elimina entidades.</span><span class="sxs-lookup"><span data-stu-id="f6fd1-178">Compare this to Local which excludes Deleted entities.</span></span>  
- <span data-ttu-id="f6fd1-179">Cuando se usa el método de entradas no genérica, se devuelven las entradas para todos los tipos de entidad.</span><span class="sxs-lookup"><span data-stu-id="f6fd1-179">Entries for all entity types are returned when the non-generic Entries method is used.</span></span> <span data-ttu-id="f6fd1-180">Cuando se usa el método genérico entradas sólo se devuelven las entradas para las entidades que son instancias del tipo genérico.</span><span class="sxs-lookup"><span data-stu-id="f6fd1-180">When the generic entries method is used entries are only returned for entities that are instances of the generic type.</span></span> <span data-ttu-id="f6fd1-181">Se usó anteriormente para obtener las entradas para todos los blogs.</span><span class="sxs-lookup"><span data-stu-id="f6fd1-181">This was used above to get entries for all blogs.</span></span> <span data-ttu-id="f6fd1-182">También se usó para obtener las entradas de todas las entidades que implementan IPerson.</span><span class="sxs-lookup"><span data-stu-id="f6fd1-182">It was also used to get entries for all entities that implement IPerson.</span></span> <span data-ttu-id="f6fd1-183">Esto demuestra que el tipo genérico no tiene un tipo de entidad real.</span><span class="sxs-lookup"><span data-stu-id="f6fd1-183">This demonstrates that the generic type does not have to be an actual entity type.</span></span>  
- <span data-ttu-id="f6fd1-184">Para filtrar los resultados devueltos se puede usar LINQ para objetos.</span><span class="sxs-lookup"><span data-stu-id="f6fd1-184">LINQ to Objects can be used to filter the results returned.</span></span> <span data-ttu-id="f6fd1-185">Se usó anteriormente para buscar las entidades de cualquier tipo, siempre y cuando se modifican.</span><span class="sxs-lookup"><span data-stu-id="f6fd1-185">This was used above to find entities of any type as long as they are modified.</span></span>  

<span data-ttu-id="f6fd1-186">Tenga en cuenta que las instancias de DbEntityEntry siempre contienen una entidad que no sea null.</span><span class="sxs-lookup"><span data-stu-id="f6fd1-186">Note that DbEntityEntry instances always contain a non-null Entity.</span></span> <span data-ttu-id="f6fd1-187">Entradas de código auxiliar y las entradas de relación no se representan como instancias de DbEntityEntry por lo que no es necesario para filtrar estos.</span><span class="sxs-lookup"><span data-stu-id="f6fd1-187">Relationship entries and stub entries are not represented as DbEntityEntry instances so there is no need to filter for these.</span></span>