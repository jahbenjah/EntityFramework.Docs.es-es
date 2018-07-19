---
title: Las pruebas con sus propio dobles de pruebas - EF6
author: divega
ms.date: 2016-10-23
ms.prod: entity-framework
ms.author: divega
ms.manager: avickers
ms.technology: entity-framework-6
ms.topic: article
ms.assetid: 16a8b7c0-2d23-47f4-9cc0-e2eb2e738ca3
caps.latest.revision: 4
ms.openlocfilehash: 4e2511f92f9bb034ab468dd030ef238e325ce7c0
ms.sourcegitcommit: 390f3a37bc55105ed7cc5b0e0925b7f9c9e80ba6
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/09/2018
ms.locfileid: "39122568"
---
# <a name="testing-with-your-own-test-doubles"></a><span data-ttu-id="06392-102">Las pruebas con sus propio dobles de pruebas</span><span class="sxs-lookup"><span data-stu-id="06392-102">Testing with your own test doubles</span></span>
> [!NOTE]
> <span data-ttu-id="06392-103">**Solo EF6 y versiones posteriores**: las características, las API, etc. que se tratan en esta página se han incluido a partir de Entity Framework 6.</span><span class="sxs-lookup"><span data-stu-id="06392-103">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="06392-104">Si usa una versión anterior, no se aplica parte o la totalidad de la información.</span><span class="sxs-lookup"><span data-stu-id="06392-104">If you are using an earlier version, some or all of the information does not apply.</span></span>  

<span data-ttu-id="06392-105">Al escribir pruebas para la aplicación suele ser deseable para evitar llegar a la base de datos.</span><span class="sxs-lookup"><span data-stu-id="06392-105">When writing tests for your application it is often desirable to avoid hitting the database.</span></span>  <span data-ttu-id="06392-106">Entity Framework le permite conseguir esto mediante la creación de un contexto – con el comportamiento definido por las pruebas, que hace uso de datos en memoria.</span><span class="sxs-lookup"><span data-stu-id="06392-106">Entity Framework allows you to achieve this by creating a context – with behavior defined by your tests – that makes use of in-memory data.</span></span>  

## <a name="options-for-creating-test-doubles"></a><span data-ttu-id="06392-107">Opciones para crear los dobles de pruebas</span><span class="sxs-lookup"><span data-stu-id="06392-107">Options for creating test doubles</span></span>  

<span data-ttu-id="06392-108">Existen dos enfoques diferentes que pueden usarse para crear una versión en memoria de su contexto.</span><span class="sxs-lookup"><span data-stu-id="06392-108">There are two different approaches that can be used to create an in-memory version of your context.</span></span>  

- <span data-ttu-id="06392-109">**Crear sus propio dobles de pruebas** – este enfoque implica escribir su propia implementación en memoria de su contexto y DbSets.</span><span class="sxs-lookup"><span data-stu-id="06392-109">**Create your own test doubles** – This approach involves writing your own in-memory implementation of your context and DbSets.</span></span> <span data-ttu-id="06392-110">Esto le ofrece un gran control sobre cómo se comportan las clases, pero pueden implicar escribir y poseer una cantidad razonable de código.</span><span class="sxs-lookup"><span data-stu-id="06392-110">This gives you a lot of control over how the classes behave but can involve writing and owning a reasonable amount of code.</span></span>  
- <span data-ttu-id="06392-111">**Usar un marco de simulación para crear los dobles de pruebas** : mediante un marco de simulación (como Moq) puede tener las implementaciones en memoria que contexto y los conjuntos creados dinámicamente en tiempo de ejecución para usted.</span><span class="sxs-lookup"><span data-stu-id="06392-111">**Use a mocking framework to create test doubles** – Using a mocking framework (such as Moq) you can have the in-memory implementations of you context and sets created dynamically at runtime for you.</span></span>  

<span data-ttu-id="06392-112">En este artículo tratará la creación de su propia prueba doble.</span><span class="sxs-lookup"><span data-stu-id="06392-112">This article will deal with creating your own test double.</span></span> <span data-ttu-id="06392-113">Para obtener información sobre el uso de un marco de simulación vea [las pruebas con un marco de simulación](mocking.md).</span><span class="sxs-lookup"><span data-stu-id="06392-113">For information on using a mocking framework see [Testing with a Mocking Framework](mocking.md).</span></span>  

## <a name="testing-with-pre-ef6-versions"></a><span data-ttu-id="06392-114">Las pruebas con las versiones anteriores a EF6</span><span class="sxs-lookup"><span data-stu-id="06392-114">Testing with pre-EF6 versions</span></span>  

<span data-ttu-id="06392-115">El código mostrado en este artículo es compatible con EF6.</span><span class="sxs-lookup"><span data-stu-id="06392-115">The code shown in this article is compatible with EF6.</span></span> <span data-ttu-id="06392-116">Para las pruebas con EF5 y una versión anterior, consulte [las pruebas con un contexto de imitar](http://romiller.com/2012/02/14/testing-with-a-fake-dbcontext/).</span><span class="sxs-lookup"><span data-stu-id="06392-116">For testing with EF5 and earlier version see [Testing with a Fake Context](http://romiller.com/2012/02/14/testing-with-a-fake-dbcontext/).</span></span>  

## <a name="limitations-of-ef-in-memory-test-doubles"></a><span data-ttu-id="06392-117">Limitaciones de EF en memoria de dobles de pruebas</span><span class="sxs-lookup"><span data-stu-id="06392-117">Limitations of EF in-memory test doubles</span></span>  

<span data-ttu-id="06392-118">Dobles de pruebas en memoria pueden ser una buena forma de proporcionar cobertura de nivel de bits de la aplicación que usan EF de prueba unitaria.</span><span class="sxs-lookup"><span data-stu-id="06392-118">In-memory test doubles can be a good way to provide unit test level coverage of bits of your application that use EF.</span></span> <span data-ttu-id="06392-119">Sin embargo, al hacerlo se mediante LINQ to Objects para ejecutar consultas en datos en memoria.</span><span class="sxs-lookup"><span data-stu-id="06392-119">However, when doing this you are using LINQ to Objects to execute queries against in-memory data.</span></span> <span data-ttu-id="06392-120">Esto puede provocar un comportamiento diferente que el proveedor LINQ de EF (LINQ to Entities) para traducir consultas en SQL que se ejecuta en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="06392-120">This can result in different behavior than using EF’s LINQ provider (LINQ to Entities) to translate queries into SQL that is run against your database.</span></span>  

<span data-ttu-id="06392-121">Un ejemplo de esta diferencia está cargando datos relacionados.</span><span class="sxs-lookup"><span data-stu-id="06392-121">One example of such a difference is loading related data.</span></span> <span data-ttu-id="06392-122">Si crea una serie de Blogs de publicaciones que cada relacionadas, a continuación, cuando se usan los datos en memoria siempre se cargarán esas entradas para cada Blog.</span><span class="sxs-lookup"><span data-stu-id="06392-122">If you create a series of Blogs that each have related Posts, then when using in-memory data the related Posts will always be loaded for each Blog.</span></span> <span data-ttu-id="06392-123">Sin embargo, cuando se ejecuta en una base de datos solo se cargará los datos si usa el método Include.</span><span class="sxs-lookup"><span data-stu-id="06392-123">However, when running against a database the data will only be loaded if you use the Include method.</span></span>  

<span data-ttu-id="06392-124">Por este motivo, se recomienda incluir siempre cierto nivel de pruebas to-end (además de las pruebas unitarias) para asegurarse de la aplicación funciona correctamente en una base de datos.</span><span class="sxs-lookup"><span data-stu-id="06392-124">For this reason, it is recommended to always include some level of end-to-end testing (in addition to your unit tests) to ensure your application works correctly against a database.</span></span>  

## <a name="following-along-with-this-article"></a><span data-ttu-id="06392-125">Siguiendo con este artículo.</span><span class="sxs-lookup"><span data-stu-id="06392-125">Following along with this article</span></span>  

<span data-ttu-id="06392-126">Este artículo proporcionan listas de código completo que se pueden copiar en Visual Studio para seguir el tutorial si lo desea.</span><span class="sxs-lookup"><span data-stu-id="06392-126">This article gives complete code listings that you can copy into Visual Studio to follow along if you wish.</span></span> <span data-ttu-id="06392-127">Es más fácil crear un **proyecto de prueba unitaria** y será necesario al destino **.NET Framework 4.5** para completar las secciones que se usa async.</span><span class="sxs-lookup"><span data-stu-id="06392-127">It's easiest to create a **Unit Test Project** and you will need to target **.NET Framework 4.5** to complete the sections that use async.</span></span>  

## <a name="creating-a-context-interface"></a><span data-ttu-id="06392-128">Creación de una interfaz de contexto</span><span class="sxs-lookup"><span data-stu-id="06392-128">Creating a context interface</span></span>  

<span data-ttu-id="06392-129">Vamos a ver en las pruebas de un servicio que hace uso de EF modelo.</span><span class="sxs-lookup"><span data-stu-id="06392-129">We're going to look at testing a service that makes use of an EF model.</span></span> <span data-ttu-id="06392-130">Para poder reemplazar el contexto de EF con una versión en memoria para las pruebas, definiremos una interfaz que nuestro contexto EF (y su doble de memoria) seguirán imeplement.</span><span class="sxs-lookup"><span data-stu-id="06392-130">In order to be able to replace our EF context with an in-memory version for testing, we'll define an interface that our EF context (and it's in-memory double) will imeplement.</span></span>  

<span data-ttu-id="06392-131">Vamos a probar el servicio de consultar y modificar datos mediante las propiedades DbSet de nuestro contexto y también llamar a SaveChanges para insertar los cambios en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="06392-131">The service we are going to test will query and modify data using the DbSet properties of our context and also call SaveChanges to push changes to the database.</span></span> <span data-ttu-id="06392-132">Por lo que hemos incluido a estos miembros en la interfaz.</span><span class="sxs-lookup"><span data-stu-id="06392-132">So we're including these members on the interface.</span></span>  

``` csharp
using System.Data.Entity;

namespace TestingDemo
{
    public interface IBloggingContext
    {
        DbSet<Blog> Blogs { get; }
        DbSet<Post> Posts { get; }
        int SaveChanges();
    }
}
```  

## <a name="the-ef-model"></a><span data-ttu-id="06392-133">El modelo de EF</span><span class="sxs-lookup"><span data-stu-id="06392-133">The EF model</span></span>  

<span data-ttu-id="06392-134">El servicio, vamos a probar hace uso de EF modelo formado por el BloggingContext y las clases de Blog y Post.</span><span class="sxs-lookup"><span data-stu-id="06392-134">The service we're going to test makes use of an EF model made up of the BloggingContext and the Blog and Post classes.</span></span> <span data-ttu-id="06392-135">Este código puede haber sido generado por el Diseñador de EF o ser un modelo de Code First.</span><span class="sxs-lookup"><span data-stu-id="06392-135">This code may have been generated by the EF Designer or be a Code First model.</span></span>  

``` csharp
using System.Collections.Generic;
using System.Data.Entity;

namespace TestingDemo
{
    public class BloggingContext : DbContext, IBloggingContext
    {
        public DbSet<Blog> Blogs { get; set; }
        public DbSet<Post> Posts { get; set; }
    }

    public class Blog
    {
        public int BlogId { get; set; }
        public string Name { get; set; }
        public string Url { get; set; }

        public virtual List<Post> Posts { get; set; }
    }

    public class Post
    {
        public int PostId { get; set; }
        public string Title { get; set; }
        public string Content { get; set; }

        public int BlogId { get; set; }
        public virtual Blog Blog { get; set; }
    }
}
```  

### <a name="implementing-the-context-interface-with-the-ef-designer"></a><span data-ttu-id="06392-136">Implementa la interfaz de contexto con EF Designer</span><span class="sxs-lookup"><span data-stu-id="06392-136">Implementing the context interface with the EF Designer</span></span>  

<span data-ttu-id="06392-137">Tenga en cuenta que nuestro contexto implementa la interfaz IBloggingContext.</span><span class="sxs-lookup"><span data-stu-id="06392-137">Note that our context implements the IBloggingContext interface.</span></span>  

<span data-ttu-id="06392-138">Si utiliza Code First, a continuación, puede editar su contexto directamente para implementar la interfaz.</span><span class="sxs-lookup"><span data-stu-id="06392-138">If you are using Code First then you can edit your context directly to implement the interface.</span></span> <span data-ttu-id="06392-139">Si está utilizando el Diseñador de EF, a continuación, deberá editar la plantilla T4 que genera el contexto.</span><span class="sxs-lookup"><span data-stu-id="06392-139">If you are using the EF Designer then you’ll need to edit the T4 template that generates your context.</span></span> <span data-ttu-id="06392-140">Abra el \<model_name\>. Archivo Context.tt que está anidado bajo el archivo edmx, busque el siguiente fragmento de código y agregue en la interfaz tal como se muestra.</span><span class="sxs-lookup"><span data-stu-id="06392-140">Open up the \<model_name\>.Context.tt file that is nested under you edmx file, find the following fragment of code and add in the interface as shown.</span></span>  

``` csharp  
<#=Accessibility.ForType(container)#> partial class <#=code.Escape(container)#> : DbContext, IBloggingContext
```  

## <a name="service-to-be-tested"></a><span data-ttu-id="06392-141">Servicio va a probar</span><span class="sxs-lookup"><span data-stu-id="06392-141">Service to be tested</span></span>  

<span data-ttu-id="06392-142">Para mostrar la prueba de dobles de pruebas en memoria que vamos a escribir un par de pruebas para un BlogService.</span><span class="sxs-lookup"><span data-stu-id="06392-142">To demonstrate testing with in-memory test doubles we are going to be writing a couple of tests for a BlogService.</span></span> <span data-ttu-id="06392-143">El servicio es capaz de crear nuevos blogs (AddBlog) y devolver todos los Blogs ordenados por nombre (GetAllBlogs).</span><span class="sxs-lookup"><span data-stu-id="06392-143">The service is capable of creating new blogs (AddBlog) and returning all Blogs ordered by name (GetAllBlogs).</span></span> <span data-ttu-id="06392-144">Además de GetAllBlogs, también hemos proporcionado un método que obtendrá de forma asincrónica todos los blogs, ordenados por nombre (GetAllBlogsAsync).</span><span class="sxs-lookup"><span data-stu-id="06392-144">In addition to GetAllBlogs, we’ve also provided a method that will asynchronously get all blogs ordered by name (GetAllBlogsAsync).</span></span>  

``` csharp
using System.Collections.Generic;
using System.Data.Entity;
using System.Linq;
using System.Threading.Tasks;

namespace TestingDemo
{
    public class BlogService
    {
        private IBloggingContext _context;

        public BlogService(IBloggingContext context)
        {
            _context = context;
        }

        public Blog AddBlog(string name, string url)
        {
            var blog = new Blog { Name = name, Url = url };
            _context.Blogs.Add(blog);
            _context.SaveChanges();

            return blog;
        }

        public List<Blog> GetAllBlogs()
        {
            var query = from b in _context.Blogs
                        orderby b.Name
                        select b;

            return query.ToList();
        }

        public async Task<List<Blog>> GetAllBlogsAsync()
        {
            var query = from b in _context.Blogs
                        orderby b.Name
                        select b;

            return await query.ToListAsync();
        }
    }
}
```  

<span data-ttu-id="06392-145"><a name="creating-the-in-memory-test-doubles"/> ## Duplica crear la prueba en memoria</span><span class="sxs-lookup"><span data-stu-id="06392-145"><a name="creating-the-in-memory-test-doubles"/> ## Creating the in-memory test doubles</span></span>  

<span data-ttu-id="06392-146">Ahora que tenemos el modelo de EF real y el servicio que puede usarlo, es momento de crear la prueba en memoria dobles que podemos usar para realizar pruebas.</span><span class="sxs-lookup"><span data-stu-id="06392-146">Now that we have the real EF model and the service that can use it, it's time to create the in-memory test double that we can use for testing.</span></span> <span data-ttu-id="06392-147">Hemos creado una prueba TestContext dobles para nuestro contexto.</span><span class="sxs-lookup"><span data-stu-id="06392-147">We've created a TestContext test double for our context.</span></span> <span data-ttu-id="06392-148">Llegamos al elegir el comportamiento que queremos para admitir las pruebas de dobles de pruebas se va a ejecutar.</span><span class="sxs-lookup"><span data-stu-id="06392-148">In test doubles we get to choose the behavior we want in order to support the tests we are going to run.</span></span> <span data-ttu-id="06392-149">En este ejemplo nos estamos captura solo el número de veces que se llama a SaveChanges, pero puede incluir la lógica que se necesita para comprobar el escenario que se está probando.</span><span class="sxs-lookup"><span data-stu-id="06392-149">In this example we're just capturing the number of times SaveChanges is called, but you can include whatever logic is needed to verify the scenario you are testing.</span></span>  

<span data-ttu-id="06392-150">También hemos creado un TestDbSet que proporciona una implementación en memoria de DbSet.</span><span class="sxs-lookup"><span data-stu-id="06392-150">We've also created a TestDbSet that provides an in-memory implementation of DbSet.</span></span> <span data-ttu-id="06392-151">Hemos proporcionado una implementación completa para todos los métodos en DbSet (excepto para buscar), pero solo tiene que implementar a los miembros que se va a usar el escenario de prueba.</span><span class="sxs-lookup"><span data-stu-id="06392-151">We've provided a complete implemention for all the methods on DbSet (except for Find), but you only need to implement the members that your test scenario will use.</span></span>  

<span data-ttu-id="06392-152">TestDbSet hace uso de otras clases de infraestructura que hemos incluido para asegurarse de que se pueden procesar las consultas asincrónicas.</span><span class="sxs-lookup"><span data-stu-id="06392-152">TestDbSet makes use of some other infrastructure classes that we've included to ensure that async queries can be processed.</span></span>  

``` csharp
using System;
using System.Collections.Generic;
using System.Collections.ObjectModel;
using System.Data.Entity;
using System.Data.Entity.Infrastructure;
using System.Linq;
using System.Linq.Expressions;
using System.Threading;
using System.Threading.Tasks;

namespace TestingDemo
{
    public class TestContext : IBloggingContext
    {
        public TestContext()
        {
            this.Blogs = new TestDbSet<Blog>();
            this.Posts = new TestDbSet<Post>();
        }

        public DbSet<Blog> Blogs { get; set; }
        public DbSet<Post> Posts { get; set; }
        public int SaveChangesCount { get; private set; }
        public int SaveChanges()
        {
            this.SaveChangesCount++;
            return 1;
        }
    }

    public class TestDbSet<TEntity> : DbSet<TEntity>, IQueryable, IEnumerable<TEntity>, IDbAsyncEnumerable<TEntity>
        where TEntity : class
    {
        ObservableCollection<TEntity> _data;
        IQueryable _query;

        public TestDbSet()
        {
            _data = new ObservableCollection<TEntity>();
            _query = _data.AsQueryable();
        }

        public override TEntity Add(TEntity item)
        {
            _data.Add(item);
            return item;
        }

        public override TEntity Remove(TEntity item)
        {
            _data.Remove(item);
            return item;
        }

        public override TEntity Attach(TEntity item)
        {
            _data.Add(item);
            return item;
        }

        public override TEntity Create()
        {
            return Activator.CreateInstance<TEntity>();
        }

        public override TDerivedEntity Create<TDerivedEntity>()
        {
            return Activator.CreateInstance<TDerivedEntity>();
        }

        public override ObservableCollection<TEntity> Local
        {
            get { return _data; }
        }

        Type IQueryable.ElementType
        {
            get { return _query.ElementType; }
        }

        Expression IQueryable.Expression
        {
            get { return _query.Expression; }
        }

        IQueryProvider IQueryable.Provider
        {
            get { return new TestDbAsyncQueryProvider<TEntity>(_query.Provider); }
        }

        System.Collections.IEnumerator System.Collections.IEnumerable.GetEnumerator()
        {
            return _data.GetEnumerator();
        }

        IEnumerator<TEntity> IEnumerable<TEntity>.GetEnumerator()
        {
            return _data.GetEnumerator();
        }

        IDbAsyncEnumerator<TEntity> IDbAsyncEnumerable<TEntity>.GetAsyncEnumerator()
        {
            return new TestDbAsyncEnumerator<TEntity>(_data.GetEnumerator());
        }
    }

    internal class TestDbAsyncQueryProvider<TEntity> : IDbAsyncQueryProvider
    {
        private readonly IQueryProvider _inner;

        internal TestDbAsyncQueryProvider(IQueryProvider inner)
        {
            _inner = inner;
        }

        public IQueryable CreateQuery(Expression expression)
        {
            return new TestDbAsyncEnumerable<TEntity>(expression);
        }

        public IQueryable<TElement> CreateQuery<TElement>(Expression expression)
        {
            return new TestDbAsyncEnumerable<TElement>(expression);
        }

        public object Execute(Expression expression)
        {
            return _inner.Execute(expression);
        }

        public TResult Execute<TResult>(Expression expression)
        {
            return _inner.Execute<TResult>(expression);
        }

        public Task<object> ExecuteAsync(Expression expression, CancellationToken cancellationToken)
        {
            return Task.FromResult(Execute(expression));
        }

        public Task<TResult> ExecuteAsync<TResult>(Expression expression, CancellationToken cancellationToken)
        {
            return Task.FromResult(Execute<TResult>(expression));
        }
    }

    internal class TestDbAsyncEnumerable<T> : EnumerableQuery<T>, IDbAsyncEnumerable<T>, IQueryable<T>
    {
        public TestDbAsyncEnumerable(IEnumerable<T> enumerable)
            : base(enumerable)
        { }

        public TestDbAsyncEnumerable(Expression expression)
            : base(expression)
        { }

        public IDbAsyncEnumerator<T> GetAsyncEnumerator()
        {
            return new TestDbAsyncEnumerator<T>(this.AsEnumerable().GetEnumerator());
        }

        IDbAsyncEnumerator IDbAsyncEnumerable.GetAsyncEnumerator()
        {
            return GetAsyncEnumerator();
        }

        IQueryProvider IQueryable.Provider
        {
            get { return new TestDbAsyncQueryProvider<T>(this); }
        }
    }

    internal class TestDbAsyncEnumerator<T> : IDbAsyncEnumerator<T>
    {
        private readonly IEnumerator<T> _inner;

        public TestDbAsyncEnumerator(IEnumerator<T> inner)
        {
            _inner = inner;
        }

        public void Dispose()
        {
            _inner.Dispose();
        }

        public Task<bool> MoveNextAsync(CancellationToken cancellationToken)
        {
            return Task.FromResult(_inner.MoveNext());
        }

        public T Current
        {
            get { return _inner.Current; }
        }

        object IDbAsyncEnumerator.Current
        {
            get { return Current; }
        }
    }
}
```  

### <a name="implementing-find"></a><span data-ttu-id="06392-153">Implementación de búsqueda</span><span class="sxs-lookup"><span data-stu-id="06392-153">Implementing Find</span></span>  

<span data-ttu-id="06392-154">El método Find es difícil de implementar de forma genérica.</span><span class="sxs-lookup"><span data-stu-id="06392-154">The Find method is difficult to implement in a generic fashion.</span></span> <span data-ttu-id="06392-155">Si necesita probar código que hace uso del método Find es más fácil crear una prueba de DbSet para cada uno de los tipos de entidad que necesitan compatibilidad con buscar.</span><span class="sxs-lookup"><span data-stu-id="06392-155">If you need to test code that makes use of the Find method it is easiest to create a test DbSet for each of the entity types that need to support find.</span></span> <span data-ttu-id="06392-156">A continuación, puede escribir lógica para buscar ese tipo de entidad, como se muestra a continuación.</span><span class="sxs-lookup"><span data-stu-id="06392-156">You can then write logic to find that particular type of entity, as shown below.</span></span>  

``` csharp
using System.Linq;

namespace TestingDemo
{
    class TestBlogDbSet : TestDbSet<Blog>
    {
        public override Blog Find(params object[] keyValues)
        {
            var id = (int)keyValues.Single();
            return this.SingleOrDefault(b => b.BlogId == id);
        }
    }
}
```  

## <a name="writing-some-tests"></a><span data-ttu-id="06392-157">Escritura de pruebas</span><span class="sxs-lookup"><span data-stu-id="06392-157">Writing some tests</span></span>  

<span data-ttu-id="06392-158">Eso es todo lo que necesitamos hacer para comenzar a probar.</span><span class="sxs-lookup"><span data-stu-id="06392-158">That’s all we need to do to start testing.</span></span> <span data-ttu-id="06392-159">La siguiente prueba crea un TestContext y, a continuación, un servicio basado en este contexto.</span><span class="sxs-lookup"><span data-stu-id="06392-159">The following test creates a TestContext and then a service based on this context.</span></span> <span data-ttu-id="06392-160">El servicio, a continuación, se usa para crear un nuevo blog: mediante el método AddBlog.</span><span class="sxs-lookup"><span data-stu-id="06392-160">The service is then used to create a new blog – using the AddBlog method.</span></span> <span data-ttu-id="06392-161">Por último, la prueba comprueba que el servicio agrega un nuevo Blog a la propiedad de Blogs del contexto y llama a SaveChanges en el contexto.</span><span class="sxs-lookup"><span data-stu-id="06392-161">Finally, the test verifies that the service added a new Blog to the context's Blogs property and called SaveChanges on the context.</span></span>  

<span data-ttu-id="06392-162">Esto es solo un ejemplo de los tipos de cosas que puede probar con un doble de pruebas en memoria y se puede ajustar la lógica de las dobles de pruebas y la comprobación para satisfacer sus necesidades.</span><span class="sxs-lookup"><span data-stu-id="06392-162">This is just an example of the types of things you can test with an in-memory test double and you can adjust the logic of the test doubles and the verification to meet your requirements.</span></span>  

``` csharp
using Microsoft.VisualStudio.TestTools.UnitTesting;
using System.Linq;

namespace TestingDemo
{
    [TestClass]
    public class NonQueryTests
    {
        [TestMethod]
        public void CreateBlog_saves_a_blog_via_context()
        {
            var context = new TestContext();

            var service = new BlogService(context);
            service.AddBlog("ADO.NET Blog", "http://blogs.msdn.com/adonet");

            Assert.AreEqual(1, context.Blogs.Count());
            Assert.AreEqual("ADO.NET Blog", context.Blogs.Single().Name);
            Assert.AreEqual("http://blogs.msdn.com/adonet", context.Blogs.Single().Url);
            Assert.AreEqual(1, context.SaveChangesCount);
        }
    }
}
```  

<span data-ttu-id="06392-163">Este es otro ejemplo de una prueba - en este momento uno que realiza una consulta.</span><span class="sxs-lookup"><span data-stu-id="06392-163">Here is another example of a test - this time one that performs a query.</span></span> <span data-ttu-id="06392-164">La prueba se inicia mediante la creación de un contexto de prueba con algunos datos en su propiedad de Blog: tenga en cuenta que los datos no están en orden alfabético.</span><span class="sxs-lookup"><span data-stu-id="06392-164">The test starts by creating a test context with some data in its Blog property - note that the data is not in alphabetical order.</span></span> <span data-ttu-id="06392-165">A continuación, podemos crear un BlogService según el contexto de prueba y asegúrese de que los datos, obtenemos desde GetAllBlogs se ordenan por nombre.</span><span class="sxs-lookup"><span data-stu-id="06392-165">We can then create a BlogService based on our test context and ensure that the data we get back from GetAllBlogs is ordered by name.</span></span>  

``` csharp
using Microsoft.VisualStudio.TestTools.UnitTesting;

namespace TestingDemo
{
    [TestClass]
    public class QueryTests
    {
        [TestMethod]
        public void GetAllBlogs_orders_by_name()
        {
            var context = new TestContext();
            context.Blogs.Add(new Blog { Name = "BBB" });
            context.Blogs.Add(new Blog { Name = "ZZZ" });
            context.Blogs.Add(new Blog { Name = "AAA" });

            var service = new BlogService(context);
            var blogs = service.GetAllBlogs();

            Assert.AreEqual(3, blogs.Count);
            Assert.AreEqual("AAA", blogs[0].Name);
            Assert.AreEqual("BBB", blogs[1].Name);
            Assert.AreEqual("ZZZ", blogs[2].Name);
        }
    }
}
```  

<span data-ttu-id="06392-166">Por último, vamos a escribir una prueba más que usa el método asincrónico para asegurarse de que se incluyen en la infraestructura de async [TestDbSet](#creating-the-in-memory-test-doubles) está trabajando.</span><span class="sxs-lookup"><span data-stu-id="06392-166">Finally, we'll write one more test that uses our async method to ensure that the async infrastructure we included in [TestDbSet](#creating-the-in-memory-test-doubles) is working.</span></span>  

``` csharp
using Microsoft.VisualStudio.TestTools.UnitTesting;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;

namespace TestingDemo
{
    [TestClass]
    public class AsyncQueryTests
    {
        [TestMethod]
        public async Task GetAllBlogsAsync_orders_by_name()
        {
            var context = new TestContext();
            context.Blogs.Add(new Blog { Name = "BBB" });
            context.Blogs.Add(new Blog { Name = "ZZZ" });
            context.Blogs.Add(new Blog { Name = "AAA" });

            var service = new BlogService(context);
            var blogs = await service.GetAllBlogsAsync();

            Assert.AreEqual(3, blogs.Count);
            Assert.AreEqual("AAA", blogs[0].Name);
            Assert.AreEqual("BBB", blogs[1].Name);
            Assert.AreEqual("ZZZ", blogs[2].Name);
        }
    }
}
```  