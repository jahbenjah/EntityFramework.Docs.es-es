---
title: Tutorial de entidades - EF6 con seguimiento propio
author: divega
ms.date: 2016-10-23
ms.prod: entity-framework
ms.author: divega
ms.manager: avickers
ms.technology: entity-framework-6
ms.topic: article
ms.assetid: b21207c9-1d95-4aa3-ae05-bc5fe300dab0
caps.latest.revision: 3
ms.openlocfilehash: d07ae727ffba60a9296b34b9261acd99f7e8e3b6
ms.sourcegitcommit: f05e7b62584cf228f17390bb086a61d505712e1b
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/08/2018
ms.locfileid: "39122351"
---
# <a name="self-tracking-entities-walkthrough"></a><span data-ttu-id="5b74d-102">Tutorial de las entidades de seguimiento propio</span><span class="sxs-lookup"><span data-stu-id="5b74d-102">Self-Tracking Entities Walkthrough</span></span>
> [!IMPORTANT]
> <span data-ttu-id="5b74d-103">Ya no se recomienda usar la plantilla Entidades de autoseguimiento.</span><span class="sxs-lookup"><span data-stu-id="5b74d-103">We no longer recommend using the self-tracking-entities template.</span></span> <span data-ttu-id="5b74d-104">Solo sigue estando disponible para la compatibilidad con las aplicaciones existentes.</span><span class="sxs-lookup"><span data-stu-id="5b74d-104">It will only continue to be available to support existing applications.</span></span> <span data-ttu-id="5b74d-105">Si la aplicación necesita trabajar con gráficos desconectados de entidades, considere otras alternativas, como [Trackable Entities](http://trackableentities.github.io/), que es una tecnología similar a Entidades de autoseguimiento pero que la comunidad desarrolla de forma más activa, o escriba código personalizado mediante la API de seguimiento de cambios de bajo nivel.</span><span class="sxs-lookup"><span data-stu-id="5b74d-105">If your application requires working with disconnected graphs of entities, consider other alternatives such as [Trackable Entities](http://trackableentities.github.io/), which is a technology similar to Self-Tracking-Entities that is more actively developed by the community, or writing custom code using the low-level change tracking APIs.</span></span>

<span data-ttu-id="5b74d-106">En este tutorial se muestra el escenario en el que un servicio de Windows Communication Foundation (WCF) expone una operación que devuelve un gráfico de la entidad.</span><span class="sxs-lookup"><span data-stu-id="5b74d-106">This walkthrough demonstrates the scenario in which a Windows Communication Foundation (WCF) service exposes an operation that returns an entity graph.</span></span> <span data-ttu-id="5b74d-107">A continuación, una aplicación cliente manipula dicho gráfico y envía las modificaciones a una operación de servicio que valida y guarda las actualizaciones en una base de datos mediante Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="5b74d-107">Next, a client application manipulates that graph and submits the modifications to a service operation that validates and saves the updates to a database using Entity Framework.</span></span>

<span data-ttu-id="5b74d-108">Antes de completar este tutorial, asegúrese de leer el [entidades de seguimiento automático](index.md) página.</span><span class="sxs-lookup"><span data-stu-id="5b74d-108">Before completing this walkthrough make sure you read the [Self-Tracking Entities](index.md) page.</span></span>

<span data-ttu-id="5b74d-109">Este tutorial realiza las siguientes acciones:</span><span class="sxs-lookup"><span data-stu-id="5b74d-109">This walkthrough completes the following actions:</span></span>

-   <span data-ttu-id="5b74d-110">Crea una base de datos para tener acceso a.</span><span class="sxs-lookup"><span data-stu-id="5b74d-110">Creates a database to access.</span></span>
-   <span data-ttu-id="5b74d-111">Crea una biblioteca de clases que contiene el modelo.</span><span class="sxs-lookup"><span data-stu-id="5b74d-111">Creates a class library that contains the model.</span></span>
-   <span data-ttu-id="5b74d-112">Intercambios a la plantilla generador de entidades de seguimiento propio.</span><span class="sxs-lookup"><span data-stu-id="5b74d-112">Swaps to the Self-Tracking Entity Generator template.</span></span>
-   <span data-ttu-id="5b74d-113">Mueve las clases de entidad a un proyecto independiente.</span><span class="sxs-lookup"><span data-stu-id="5b74d-113">Moves the entity classes to a separate project.</span></span>
-   <span data-ttu-id="5b74d-114">Crea un servicio WCF que expone las operaciones para consultar y guardar las entidades.</span><span class="sxs-lookup"><span data-stu-id="5b74d-114">Creates a WCF service that exposes operations to query and save entities.</span></span>
-   <span data-ttu-id="5b74d-115">Crea aplicaciones (consola y WPF) que consumen el servicio de cliente.</span><span class="sxs-lookup"><span data-stu-id="5b74d-115">Creates client applications (Console and WPF) that consume the service.</span></span>

<span data-ttu-id="5b74d-116">Vamos a usar la primera base de datos en este tutorial, pero las mismas técnicas se aplican igualmente a Model First.</span><span class="sxs-lookup"><span data-stu-id="5b74d-116">We'll use Database First in this walkthrough but the same techniques apply equally to Model First.</span></span>

## <a name="pre-requisites"></a><span data-ttu-id="5b74d-117">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="5b74d-117">Pre-Requisites</span></span>

<span data-ttu-id="5b74d-118">Para completar este tutorial necesita una versión reciente de Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="5b74d-118">To complete this walkthrough you will need a recent version of Visual Studio.</span></span>

## <a name="create-a-database"></a><span data-ttu-id="5b74d-119">Crear una base de datos</span><span class="sxs-lookup"><span data-stu-id="5b74d-119">Create a Database</span></span>

<span data-ttu-id="5b74d-120">El servidor de base de datos que se instala con Visual Studio es diferente según la versión de Visual Studio que ha instalado:</span><span class="sxs-lookup"><span data-stu-id="5b74d-120">The database server that is installed with Visual Studio is different depending on the version of Visual Studio you have installed:</span></span>

-   <span data-ttu-id="5b74d-121">Si usa Visual Studio 2012, a continuación, se creará una base de datos LocalDB.</span><span class="sxs-lookup"><span data-stu-id="5b74d-121">If you are using Visual Studio 2012 then you'll be creating a LocalDB database.</span></span>
-   <span data-ttu-id="5b74d-122">Si usa Visual Studio 2010 se creará una base de datos de SQL Express.</span><span class="sxs-lookup"><span data-stu-id="5b74d-122">If you are using Visual Studio 2010 you'll be creating a SQL Express database.</span></span>

<span data-ttu-id="5b74d-123">Sigamos adelante y generar la base de datos.</span><span class="sxs-lookup"><span data-stu-id="5b74d-123">Let's go ahead and generate the database.</span></span>

-   <span data-ttu-id="5b74d-124">Apertura de Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5b74d-124">Open Visual Studio</span></span>
-   <span data-ttu-id="5b74d-125">**Vista -&gt; Explorador de servidores**</span><span class="sxs-lookup"><span data-stu-id="5b74d-125">**View -&gt; Server Explorer**</span></span>
-   <span data-ttu-id="5b74d-126">Haga clic con el botón derecho en **conexiones de datos -&gt; Agregar conexión...**</span><span class="sxs-lookup"><span data-stu-id="5b74d-126">Right click on **Data Connections -&gt; Add Connection…**</span></span>
-   <span data-ttu-id="5b74d-127">Si aún no lo ha conectado a una base de datos del explorador de servidores antes de que tendrá que seleccionar **Microsoft SQL Server** como origen de datos</span><span class="sxs-lookup"><span data-stu-id="5b74d-127">If you haven’t connected to a database from Server Explorer before you’ll need to select **Microsoft SQL Server** as the data source</span></span>
-   <span data-ttu-id="5b74d-128">Conectarse a LocalDB o Express de SQL, según lo que se ha instalado</span><span class="sxs-lookup"><span data-stu-id="5b74d-128">Connect to either LocalDB or SQL Express, depending on which one you have installed</span></span>
-   <span data-ttu-id="5b74d-129">Escriba **STESample** como el nombre de la base de datos</span><span class="sxs-lookup"><span data-stu-id="5b74d-129">Enter **STESample** as the database name</span></span>
-   <span data-ttu-id="5b74d-130">Seleccione **Aceptar** y se le solicitará si desea crear una nueva base de datos, seleccione **sí**</span><span class="sxs-lookup"><span data-stu-id="5b74d-130">Select **OK** and you will be asked if you want to create a new database, select **Yes**</span></span>
-   <span data-ttu-id="5b74d-131">La nueva base de datos aparecerán en el Explorador de servidores</span><span class="sxs-lookup"><span data-stu-id="5b74d-131">The new database will now appear in Server Explorer</span></span>
-   <span data-ttu-id="5b74d-132">Si usa Visual Studio 2012</span><span class="sxs-lookup"><span data-stu-id="5b74d-132">If you are using Visual Studio 2012</span></span>
    -   <span data-ttu-id="5b74d-133">Haga doble clic en la base de datos en el Explorador de servidores y seleccione **nueva consulta**</span><span class="sxs-lookup"><span data-stu-id="5b74d-133">Right-click on the database in Server Explorer and select **New Query**</span></span>
    -   <span data-ttu-id="5b74d-134">Copie el siguiente código SQL en la consulta nueva y, después, haga doble clic en la consulta y seleccione **Execute**</span><span class="sxs-lookup"><span data-stu-id="5b74d-134">Copy the following SQL into the new query, then right-click on the query and select **Execute**</span></span>
-   <span data-ttu-id="5b74d-135">Si usa Visual Studio 2010</span><span class="sxs-lookup"><span data-stu-id="5b74d-135">If you are using Visual Studio 2010</span></span>
    -   <span data-ttu-id="5b74d-136">Seleccione **datos -&gt; Transact SQL Editor -&gt; nueva conexión de consulta...**</span><span class="sxs-lookup"><span data-stu-id="5b74d-136">Select **Data -&gt; Transact SQL Editor -&gt; New Query Connection...**</span></span>
    -   <span data-ttu-id="5b74d-137">Escriba **.\\ SQLEXPRESS** como el nombre del servidor y haga clic en **Aceptar**</span><span class="sxs-lookup"><span data-stu-id="5b74d-137">Enter **.\\SQLEXPRESS** as the server name and click **OK**</span></span>
    -   <span data-ttu-id="5b74d-138">Seleccione el **STESample** de base de datos en la lista desplegable en la parte superior del editor de consultas</span><span class="sxs-lookup"><span data-stu-id="5b74d-138">Select the **STESample** database from the drop down at the top of the query editor</span></span>
    -   <span data-ttu-id="5b74d-139">Copie el siguiente código SQL en la consulta nueva y, después, haga doble clic en la consulta y seleccione **ejecutar SQL**</span><span class="sxs-lookup"><span data-stu-id="5b74d-139">Copy the following SQL into the new query, then right-click on the query and select **Execute SQL**</span></span>

``` SQL
    CREATE TABLE [dbo].[Blogs] (
        [BlogId] INT IDENTITY (1, 1) NOT NULL,
        [Name] NVARCHAR (200) NULL,
        [Url]  NVARCHAR (200) NULL,
        CONSTRAINT [PK_dbo.Blogs] PRIMARY KEY CLUSTERED ([BlogId] ASC)
    );

    CREATE TABLE [dbo].[Posts] (
        [PostId] INT IDENTITY (1, 1) NOT NULL,
        [Title] NVARCHAR (200) NULL,
        [Content] NTEXT NULL,
        [BlogId] INT NOT NULL,
        CONSTRAINT [PK_dbo.Posts] PRIMARY KEY CLUSTERED ([PostId] ASC),
        CONSTRAINT [FK_dbo.Posts_dbo.Blogs_BlogId] FOREIGN KEY ([BlogId]) REFERENCES [dbo].[Blogs] ([BlogId]) ON DELETE CASCADE
    );

    SET IDENTITY_INSERT [dbo].[Blogs] ON
    INSERT INTO [dbo].[Blogs] ([BlogId], [Name], [Url]) VALUES (1, N'ADO.NET Blog', N'blogs.msdn.com/adonet')
    SET IDENTITY_INSERT [dbo].[Blogs] OFF
    INSERT INTO [dbo].[Posts] ([Title], [Content], [BlogId]) VALUES (N'Intro to EF', N'Interesting stuff...', 1)
    INSERT INTO [dbo].[Posts] ([Title], [Content], [BlogId]) VALUES (N'What is New', N'More interesting stuff...', 1)
```

## <a name="create-the-model"></a><span data-ttu-id="5b74d-140">Crear el modelo</span><span class="sxs-lookup"><span data-stu-id="5b74d-140">Create the Model</span></span>

<span data-ttu-id="5b74d-141">En primer lugar, necesitamos poner el modelo en un proyecto de.</span><span class="sxs-lookup"><span data-stu-id="5b74d-141">First up, we need a project to put the model in.</span></span>

-   <span data-ttu-id="5b74d-142">**Archivo -&gt; nuevo:&gt; proyecto...**</span><span class="sxs-lookup"><span data-stu-id="5b74d-142">**File -&gt; New -&gt; Project...**</span></span>
-   <span data-ttu-id="5b74d-143">Seleccione **Visual C\#**  en el panel izquierdo y, a continuación, **biblioteca de clases**</span><span class="sxs-lookup"><span data-stu-id="5b74d-143">Select **Visual C\#** from the left pane and then **Class Library**</span></span>
-   <span data-ttu-id="5b74d-144">Escriba **STESample** como el nombre y haga clic en **Aceptar**</span><span class="sxs-lookup"><span data-stu-id="5b74d-144">Enter **STESample** as the name and click **OK**</span></span>

<span data-ttu-id="5b74d-145">Ahora vamos a crear un modelo simple en EF Designer para tener acceso a nuestra base de datos:</span><span class="sxs-lookup"><span data-stu-id="5b74d-145">Now we'll create a simple model in the EF Designer to access our database:</span></span>

-   <span data-ttu-id="5b74d-146">**Proyecto -&gt; Agregar nuevo elemento...**</span><span class="sxs-lookup"><span data-stu-id="5b74d-146">**Project -&gt; Add New Item...**</span></span>
-   <span data-ttu-id="5b74d-147">Seleccione **datos** en el panel izquierdo y, a continuación, **ADO.NET Entity Data Model**</span><span class="sxs-lookup"><span data-stu-id="5b74d-147">Select **Data** from the left pane and then **ADO.NET Entity Data Model**</span></span>
-   <span data-ttu-id="5b74d-148">Escriba **BloggingModel** como el nombre y haga clic en **Aceptar**</span><span class="sxs-lookup"><span data-stu-id="5b74d-148">Enter **BloggingModel** as the name and click **OK**</span></span>
-   <span data-ttu-id="5b74d-149">Seleccione **generar desde la base de datos** y haga clic en **siguiente**</span><span class="sxs-lookup"><span data-stu-id="5b74d-149">Select **Generate from database** and click **Next**</span></span>
-   <span data-ttu-id="5b74d-150">Escriba la información de conexión para la base de datos que creó en la sección anterior</span><span class="sxs-lookup"><span data-stu-id="5b74d-150">Enter the connection information for the database that you created in the previous section</span></span>
-   <span data-ttu-id="5b74d-151">Escriba **BloggingContext** como el nombre de la cadena de conexión y haga clic en **siguiente**</span><span class="sxs-lookup"><span data-stu-id="5b74d-151">Enter **BloggingContext** as the name for the connection string and click **Next**</span></span>
-   <span data-ttu-id="5b74d-152">Active la casilla junto a **tablas** y haga clic en **finalizar**</span><span class="sxs-lookup"><span data-stu-id="5b74d-152">Check the box next to **Tables** and click **Finish**</span></span>

## <a name="swap-to-ste-code-generation"></a><span data-ttu-id="5b74d-153">Cambiar a la generación de código de la compañía</span><span class="sxs-lookup"><span data-stu-id="5b74d-153">Swap to STE Code Generation</span></span>

<span data-ttu-id="5b74d-154">Ahora es necesario deshabilitar la generación de código predeterminado y el intercambio a entidades de seguimiento automático.</span><span class="sxs-lookup"><span data-stu-id="5b74d-154">Now we need to disable the default code generation and swap to Self-Tracking Entities.</span></span>

### <a name="if-you-are-using-visual-studio-2012"></a><span data-ttu-id="5b74d-155">Si usa Visual Studio 2012</span><span class="sxs-lookup"><span data-stu-id="5b74d-155">If you are using Visual Studio 2012</span></span>

-   <span data-ttu-id="5b74d-156">Expanda **BloggingModel.edmx** en **el Explorador de soluciones** y elimine el **BloggingModel.tt** y **BloggingModel.Context.tt** 
     *Esto deshabilitará la generación de código predeterminada*</span><span class="sxs-lookup"><span data-stu-id="5b74d-156">Expand **BloggingModel.edmx** in **Solution Explorer** and delete the **BloggingModel.tt** and **BloggingModel.Context.tt**
*This will disable the default code generation*</span></span>
-   <span data-ttu-id="5b74d-157">Haga clic en un área vacía del Diseñador de EF superficie y seleccione **Add Code Generation Item...**</span><span class="sxs-lookup"><span data-stu-id="5b74d-157">Right-click an empty area on the EF Designer surface and select **Add Code Generation Item...**</span></span>
-   <span data-ttu-id="5b74d-158">Seleccione **Online** desde el panel izquierdo y busque **STE generador**</span><span class="sxs-lookup"><span data-stu-id="5b74d-158">Select **Online** from the left pane and search for **STE Generator**</span></span>
-   <span data-ttu-id="5b74d-159">Seleccione el **STE generador para C\#**  plantilla, escriba **STETemplate** como el nombre y haga clic en **agregar**</span><span class="sxs-lookup"><span data-stu-id="5b74d-159">Select the **STE Generator for C\#** template, enter **STETemplate** as the name and click **Add**</span></span>
-   <span data-ttu-id="5b74d-160">El **STETemplate.tt** y **STETemplate.Context.tt** se agregan archivos anidados en el archivo BloggingModel.edmx</span><span class="sxs-lookup"><span data-stu-id="5b74d-160">The **STETemplate.tt** and **STETemplate.Context.tt** files are added nested under the BloggingModel.edmx file</span></span>

### <a name="if-you-are-using-visual-studio-2010"></a><span data-ttu-id="5b74d-161">Si usa Visual Studio 2010</span><span class="sxs-lookup"><span data-stu-id="5b74d-161">If you are using Visual Studio 2010</span></span>

-   <span data-ttu-id="5b74d-162">Haga clic en un área vacía del Diseñador de EF superficie y seleccione **Add Code Generation Item...**</span><span class="sxs-lookup"><span data-stu-id="5b74d-162">Right-click an empty area on the EF Designer surface and select **Add Code Generation Item...**</span></span>
-   <span data-ttu-id="5b74d-163">Seleccione **código** en el panel izquierdo y, a continuación, **generador de entidades de seguimiento propio de ADO.NET**</span><span class="sxs-lookup"><span data-stu-id="5b74d-163">Select **Code** from the left pane and then **ADO.NET Self-Tracking Entity Generator**</span></span>
-   <span data-ttu-id="5b74d-164">Escriba **STETemplate** como el nombre y haga clic en **agregar**</span><span class="sxs-lookup"><span data-stu-id="5b74d-164">Enter **STETemplate** as the name and click **Add**</span></span>
-   <span data-ttu-id="5b74d-165">El **STETemplate.tt** y **STETemplate.Context.tt** archivos se agregan directamente al proyecto</span><span class="sxs-lookup"><span data-stu-id="5b74d-165">The **STETemplate.tt** and **STETemplate.Context.tt** files are added directly to your project</span></span>

## <a name="move-entity-types-into-separate-project"></a><span data-ttu-id="5b74d-166">Mover los tipos de entidad en un proyecto independiente</span><span class="sxs-lookup"><span data-stu-id="5b74d-166">Move Entity Types into Separate Project</span></span>

<span data-ttu-id="5b74d-167">Para usar entidades de seguimiento automático nuestra aplicación cliente necesita acceso a las clases de entidad generado a partir de nuestro modelo.</span><span class="sxs-lookup"><span data-stu-id="5b74d-167">To use Self-Tracking Entities our client application needs access to the entity classes generated from our model.</span></span> <span data-ttu-id="5b74d-168">Porque no queremos exponer el modelo completo a la aplicación cliente, vamos a mover las clases de entidad en un proyecto independiente.</span><span class="sxs-lookup"><span data-stu-id="5b74d-168">Because we don't want to expose the whole model to the client application we're going to move the entity classes into a separate project.</span></span>

<span data-ttu-id="5b74d-169">El primer paso es detener la generación de clases de entidad en el proyecto existente:</span><span class="sxs-lookup"><span data-stu-id="5b74d-169">The first step is to stop generating entity classes in the existing project:</span></span>

-   <span data-ttu-id="5b74d-170">Haga doble clic en **STETemplate.tt** en **el Explorador de soluciones** y seleccione **propiedades**</span><span class="sxs-lookup"><span data-stu-id="5b74d-170">Right-click on **STETemplate.tt** in **Solution Explorer** and select **Properties**</span></span>
-   <span data-ttu-id="5b74d-171">En el **propiedades** ventana Borrar **TextTemplatingFileGenerator** desde el **CustomTool** propiedad</span><span class="sxs-lookup"><span data-stu-id="5b74d-171">In the **Properties** window clear **TextTemplatingFileGenerator** from the **CustomTool** property</span></span>
-   <span data-ttu-id="5b74d-172">Expanda **STETemplate.tt** en **el Explorador de soluciones** y elimine todos los archivos anidados debajo de él</span><span class="sxs-lookup"><span data-stu-id="5b74d-172">Expand **STETemplate.tt** in **Solution Explorer** and delete all files nested under it</span></span>

<span data-ttu-id="5b74d-173">A continuación, vamos a agregar un nuevo proyecto y generar las clases de entidad en él</span><span class="sxs-lookup"><span data-stu-id="5b74d-173">Next, we are going to add a new project and generate the entity classes in it</span></span>

-   <span data-ttu-id="5b74d-174">**Archivo -&gt; Add -&gt; proyecto...**</span><span class="sxs-lookup"><span data-stu-id="5b74d-174">**File -&gt; Add -&gt; Project...**</span></span>
-   <span data-ttu-id="5b74d-175">Seleccione **Visual C\#**  en el panel izquierdo y, a continuación, **biblioteca de clases**</span><span class="sxs-lookup"><span data-stu-id="5b74d-175">Select **Visual C\#** from the left pane and then **Class Library**</span></span>
-   <span data-ttu-id="5b74d-176">Escriba **STESample.Entities** como el nombre y haga clic en **Aceptar**</span><span class="sxs-lookup"><span data-stu-id="5b74d-176">Enter **STESample.Entities** as the name and click **OK**</span></span>
-   <span data-ttu-id="5b74d-177">**Proyecto -&gt; Agregar elemento existente...**</span><span class="sxs-lookup"><span data-stu-id="5b74d-177">**Project -&gt; Add Existing Item...**</span></span>
-   <span data-ttu-id="5b74d-178">Navegue hasta la **STESample** carpeta del proyecto</span><span class="sxs-lookup"><span data-stu-id="5b74d-178">Navigate to the **STESample** project folder</span></span>
-   <span data-ttu-id="5b74d-179">Seleccione esta opción para ver **todos los archivos (\*.\*)**</span><span class="sxs-lookup"><span data-stu-id="5b74d-179">Select to view **All Files (\*.\*)**</span></span>
-   <span data-ttu-id="5b74d-180">Seleccione el **STETemplate.tt** archivo</span><span class="sxs-lookup"><span data-stu-id="5b74d-180">Select the **STETemplate.tt** file</span></span>
-   <span data-ttu-id="5b74d-181">Haga clic en la flecha desplegable junto a la **agregar** y seleccione **agregar como vínculo**</span><span class="sxs-lookup"><span data-stu-id="5b74d-181">Click on the drop down arrow next to the **Add** button and select **Add As Link**</span></span>

    ![AddLinkedTemplate](~/ef6/media/addlinkedtemplate.png)

<span data-ttu-id="5b74d-183">También vamos a asegurarse de que se generan las clases de entidad en el mismo espacio de nombres como el contexto.</span><span class="sxs-lookup"><span data-stu-id="5b74d-183">We're also going to make sure the entity classes get generated in the same namespace as the context.</span></span> <span data-ttu-id="5b74d-184">Simplemente, esto reduce el número del uso de las instrucciones que se deba agregar a lo largo de nuestra aplicación.</span><span class="sxs-lookup"><span data-stu-id="5b74d-184">This just reduces the number of using statements we need to add throughout our application.</span></span>

-   <span data-ttu-id="5b74d-185">Haga doble clic en la que está vinculada **STETemplate.tt** en **el Explorador de soluciones** y seleccione **propiedades**</span><span class="sxs-lookup"><span data-stu-id="5b74d-185">Right-click on the linked **STETemplate.tt** in **Solution Explorer** and select **Properties**</span></span>
-   <span data-ttu-id="5b74d-186">En el **propiedades** ventana conjunto **Custom Tool Namespace** a **STESample**</span><span class="sxs-lookup"><span data-stu-id="5b74d-186">In the **Properties** window set **Custom Tool Namespace** to **STESample**</span></span>

<span data-ttu-id="5b74d-187">El código generado por la plantilla STE necesitará una referencia a **System.Runtime.Serialization** con el fin de compilar.</span><span class="sxs-lookup"><span data-stu-id="5b74d-187">The code generated by the STE template will need a reference to **System.Runtime.Serialization** in order to compile.</span></span> <span data-ttu-id="5b74d-188">Esta biblioteca es necesaria para WCF **DataContract** y **DataMember** atributos que se usan en los tipos de entidad serializables.</span><span class="sxs-lookup"><span data-stu-id="5b74d-188">This library is needed for the WCF **DataContract** and **DataMember** attributes that are used on the serializable entity types.</span></span>

-   <span data-ttu-id="5b74d-189">Haga clic con el botón derecho en el **STESample.Entities** proyecto **el Explorador de soluciones** y seleccione **Agregar referencia...**</span><span class="sxs-lookup"><span data-stu-id="5b74d-189">Right click on the **STESample.Entities** project in **Solution Explorer** and select **Add Reference...**</span></span>
    -   <span data-ttu-id="5b74d-190">En Visual Studio 2012: Active la casilla junto a **System.Runtime.Serialization** y haga clic en **Aceptar**</span><span class="sxs-lookup"><span data-stu-id="5b74d-190">In Visual Studio 2012 - check the box next to **System.Runtime.Serialization** and click **OK**</span></span>
    -   <span data-ttu-id="5b74d-191">En Visual Studio 2010 - seleccione **System.Runtime.Serialization** y haga clic en **Aceptar**</span><span class="sxs-lookup"><span data-stu-id="5b74d-191">In Visual Studio 2010 - select **System.Runtime.Serialization** and click **OK**</span></span>

<span data-ttu-id="5b74d-192">Por último, el proyecto con el contexto en necesitará una referencia a los tipos de entidad.</span><span class="sxs-lookup"><span data-stu-id="5b74d-192">Finally, the project with our context in it will need a reference to the entity types.</span></span>

-   <span data-ttu-id="5b74d-193">Haga clic con el botón derecho en el **STESample** proyecto **el Explorador de soluciones** y seleccione **Agregar referencia...**</span><span class="sxs-lookup"><span data-stu-id="5b74d-193">Right click on the **STESample** project in **Solution Explorer** and select **Add Reference...**</span></span>
    -   <span data-ttu-id="5b74d-194">En Visual Studio 2012: seleccione **solución** en el panel izquierdo, active la casilla junto a **STESample.Entities** y haga clic en **Aceptar**</span><span class="sxs-lookup"><span data-stu-id="5b74d-194">In Visual Studio 2012 - select **Solution** from the left pane, check the box next to **STESample.Entities** and click **OK**</span></span>
    -   <span data-ttu-id="5b74d-195">En Visual Studio 2010 - seleccione la **proyectos** ficha, seleccione **STESample.Entities** y haga clic en **Aceptar**</span><span class="sxs-lookup"><span data-stu-id="5b74d-195">In Visual Studio 2010 - select the **Projects** tab, select **STESample.Entities** and click **OK**</span></span>

>[!NOTE]
> <span data-ttu-id="5b74d-196">Otra opción para mover los tipos de entidad a un proyecto independiente es mover el archivo de plantilla, en lugar de vincularla desde su ubicación predeterminada.</span><span class="sxs-lookup"><span data-stu-id="5b74d-196">Another option for moving the entity types to a separate project is to move the template file, rather than linking it from its default location.</span></span> <span data-ttu-id="5b74d-197">Si lo hace, deberá actualizar el **inputFile** variable en la plantilla para proporcionar la ruta de acceso relativa al archivo edmx (en este ejemplo, sería **... \\BloggingModel.edmx**).</span><span class="sxs-lookup"><span data-stu-id="5b74d-197">If you do this, you will need to update the **inputFile** variable in the template to provide the relative path to the edmx file (in this example that would be **..\\BloggingModel.edmx**).</span></span>

## <a name="create-a-wcf-service"></a><span data-ttu-id="5b74d-198">Crear un servicio WCF</span><span class="sxs-lookup"><span data-stu-id="5b74d-198">Create a WCF Service</span></span>

<span data-ttu-id="5b74d-199">Ahora es momento de agregar un servicio WCF para exponer nuestros datos, comenzaremos por crear el proyecto.</span><span class="sxs-lookup"><span data-stu-id="5b74d-199">Now it's time to add a WCF Service to expose our data, we'll start by creating the project.</span></span>

-   <span data-ttu-id="5b74d-200">**Archivo -&gt; Add -&gt; proyecto...**</span><span class="sxs-lookup"><span data-stu-id="5b74d-200">**File -&gt; Add -&gt; Project...**</span></span>
-   <span data-ttu-id="5b74d-201">Seleccione **Visual C\#**  en el panel izquierdo y, a continuación, **aplicación de servicio WCF**</span><span class="sxs-lookup"><span data-stu-id="5b74d-201">Select **Visual C\#** from the left pane and then **WCF Service Application**</span></span>
-   <span data-ttu-id="5b74d-202">Escriba **STESample.Service** como el nombre y haga clic en **Aceptar**</span><span class="sxs-lookup"><span data-stu-id="5b74d-202">Enter **STESample.Service** as the name and click **OK**</span></span>
-   <span data-ttu-id="5b74d-203">Agregue una referencia a la **System.Data.Entity** ensamblado</span><span class="sxs-lookup"><span data-stu-id="5b74d-203">Add a reference to the **System.Data.Entity** assembly</span></span>
-   <span data-ttu-id="5b74d-204">Agregue una referencia a la **STESample** y **STESample.Entities** proyectos</span><span class="sxs-lookup"><span data-stu-id="5b74d-204">Add a reference to the **STESample** and **STESample.Entities** projects</span></span>

<span data-ttu-id="5b74d-205">Es necesario copiar la cadena de conexión de EF en este proyecto para que se encuentra en tiempo de ejecución.</span><span class="sxs-lookup"><span data-stu-id="5b74d-205">We need to copy the EF connection string to this project so that it is found at runtime.</span></span>

-   <span data-ttu-id="5b74d-206">Abra el **App.Config** de archivos para el ** STESample ** proyecto y copie el **connectionStrings** elemento</span><span class="sxs-lookup"><span data-stu-id="5b74d-206">Open the **App.Config** file for the **STESample **project and copy the **connectionStrings** element</span></span>
-   <span data-ttu-id="5b74d-207">Pegar la **connectionStrings** como un elemento secundario de la **configuración** elemento de la **Web.Config** de archivos en el **STESample.Service** proyecto</span><span class="sxs-lookup"><span data-stu-id="5b74d-207">Paste the **connectionStrings** element as a child element of the **configuration** element of the **Web.Config** file in the **STESample.Service** project</span></span>

<span data-ttu-id="5b74d-208">Ahora es momento de implementar el servicio real.</span><span class="sxs-lookup"><span data-stu-id="5b74d-208">Now it's time to implement the actual service.</span></span>

-   <span data-ttu-id="5b74d-209">Abra **IService1.cs** y reemplace el contenido con el código siguiente</span><span class="sxs-lookup"><span data-stu-id="5b74d-209">Open **IService1.cs** and replace the contents with the following code</span></span>

``` csharp
    using System.Collections.Generic;
    using System.ServiceModel;

    namespace STESample.Service
    {
        [ServiceContract]
        public interface IService1
        {
            [OperationContract]
            List<Blog> GetBlogs();

            [OperationContract]
            void UpdateBlog(Blog blog);
        }
    }
```

-   <span data-ttu-id="5b74d-210">Abra **Service1.svc** y reemplace el contenido con el código siguiente</span><span class="sxs-lookup"><span data-stu-id="5b74d-210">Open **Service1.svc** and replace the contents with the following code</span></span>

``` csharp
    using System;
    using System.Collections.Generic;
    using System.Data;
    using System.Linq;

    namespace STESample.Service
    {
        public class Service1 : IService1
        {
            /// <summary>
            /// Gets all the Blogs and related Posts.
            /// </summary>
            public List<Blog> GetBlogs()
            {
                using (BloggingContext context = new BloggingContext())
                {
                    return context.Blogs.Include("Posts").ToList();
                }
            }

            /// <summary>
            /// Updates Blog and its related Posts.
            /// </summary>
            public void UpdateBlog(Blog blog)
            {
                using (BloggingContext context = new BloggingContext())
                {
                    try
                    {
                        // TODO: Perform validation on the updated order before applying the changes.

                        // The ApplyChanges method examines the change tracking information
                        // contained in the graph of self-tracking entities to infer the set of operations
                        // that need to be performed to reflect the changes in the database.
                        context.Blogs.ApplyChanges(blog);
                        context.SaveChanges();

                    }
                    catch (UpdateException)
                    {
                        // To avoid propagating exception messages that contain sensitive data to the client tier
                        // calls to ApplyChanges and SaveChanges should be wrapped in exception handling code.
                        throw new InvalidOperationException("Failed to update. Try your request again.");
                    }
                }
            }        
        }
    }
```

## <a name="consume-the-service-from-a-console-application"></a><span data-ttu-id="5b74d-211">Utilizar el servicio de una aplicación de consola</span><span class="sxs-lookup"><span data-stu-id="5b74d-211">Consume the Service from a Console Application</span></span>

<span data-ttu-id="5b74d-212">Vamos a crear una aplicación de consola que usa nuestro servicio.</span><span class="sxs-lookup"><span data-stu-id="5b74d-212">Let's create a console application that uses our service.</span></span>

-   <span data-ttu-id="5b74d-213">**Archivo -&gt; nuevo:&gt; proyecto...**</span><span class="sxs-lookup"><span data-stu-id="5b74d-213">**File -&gt; New -&gt; Project...**</span></span>
-   <span data-ttu-id="5b74d-214">Seleccione **Visual C\#**  en el panel izquierdo y, a continuación, **aplicación de consola**</span><span class="sxs-lookup"><span data-stu-id="5b74d-214">Select **Visual C\#** from the left pane and then **Console Application**</span></span>
-   <span data-ttu-id="5b74d-215">Escriba **STESample.ConsoleTest** como el nombre y haga clic en **Aceptar**</span><span class="sxs-lookup"><span data-stu-id="5b74d-215">Enter **STESample.ConsoleTest** as the name and click **OK**</span></span>
-   <span data-ttu-id="5b74d-216">Agregue una referencia a la **STESample.Entities** proyecto</span><span class="sxs-lookup"><span data-stu-id="5b74d-216">Add a reference to the **STESample.Entities** project</span></span>

<span data-ttu-id="5b74d-217">Se necesita una referencia de servicio a nuestro servicio WCF</span><span class="sxs-lookup"><span data-stu-id="5b74d-217">We need a service reference to our WCF service</span></span>

-   <span data-ttu-id="5b74d-218">Haga clic en el **STESample.ConsoleTest** proyecto **el Explorador de soluciones** y seleccione **Agregar referencia de servicio...**</span><span class="sxs-lookup"><span data-stu-id="5b74d-218">Right-click the **STESample.ConsoleTest** project in **Solution Explorer** and select **Add Service Reference...**</span></span>
-   <span data-ttu-id="5b74d-219">Haga clic en **detectar**</span><span class="sxs-lookup"><span data-stu-id="5b74d-219">Click **Discover**</span></span>
-   <span data-ttu-id="5b74d-220">Escriba **BloggingService** como el espacio de nombres y haga clic en **Aceptar**</span><span class="sxs-lookup"><span data-stu-id="5b74d-220">Enter **BloggingService** as the namespace and click **OK**</span></span>

<span data-ttu-id="5b74d-221">Ahora podemos escribir algo de código para consumir el servicio.</span><span class="sxs-lookup"><span data-stu-id="5b74d-221">Now we can write some code to consume the service.</span></span>

-   <span data-ttu-id="5b74d-222">Abra **Program.cs** y reemplace el contenido con el código siguiente.</span><span class="sxs-lookup"><span data-stu-id="5b74d-222">Open **Program.cs** and replace the contents with the following code.</span></span>

``` csharp
    using STESample.ConsoleTest.BloggingService;
    using System;
    using System.Linq;

    namespace STESample.ConsoleTest
    {
        class Program
        {
            static void Main(string[] args)
            {
                // Print out the data before we change anything
                Console.WriteLine("Initial Data:");
                DisplayBlogsAndPosts();

                // Add a new Blog and some Posts
                AddBlogAndPost();
                Console.WriteLine("After Adding:");
                DisplayBlogsAndPosts();

                // Modify the Blog and one of its Posts
                UpdateBlogAndPost();
                Console.WriteLine("After Update:");
                DisplayBlogsAndPosts();

                // Delete the Blog and its Posts
                DeleteBlogAndPost();
                Console.WriteLine("After Delete:");
                DisplayBlogsAndPosts();

                Console.WriteLine("Press any key to exit...");
                Console.ReadKey();
            }

            static void DisplayBlogsAndPosts()
            {
                using (var service = new Service1Client())
                {
                    // Get all Blogs (and Posts) from the service
                    // and print them to the console
                    var blogs = service.GetBlogs();
                    foreach (var blog in blogs)
                    {
                        Console.WriteLine(blog.Name);
                        foreach (var post in blog.Posts)
                        {
                            Console.WriteLine(" - {0}", post.Title);
                        }
                    }
                }

                Console.WriteLine();
                Console.WriteLine();
            }

            static void AddBlogAndPost()
            {
                using (var service = new Service1Client())
                {
                    // Create a new Blog with a couple of Posts
                    var newBlog = new Blog
                    {
                        Name = "The New Blog",
                        Posts =
                        {
                            new Post { Title = "Welcome to the new blog"},
                            new Post { Title = "What's new on the new blog"}
                        }
                    };

                    // Save the changes using the service
                    service.UpdateBlog(newBlog);
                }
            }

            static void UpdateBlogAndPost()
            {
                using (var service = new Service1Client())
                {
                    // Get all the Blogs
                    var blogs = service.GetBlogs();

                    // Use LINQ to Objects to find The New Blog
                    var blog = blogs.First(b => b.Name == "The New Blog");

                    // Update the Blogs name
                    blog.Name = "The Not-So-New Blog";

                    // Update one of the related posts
                    blog.Posts.First().Content = "Some interesting content...";

                    // Save the changes using the service
                    service.UpdateBlog(blog);
                }
            }

            static void DeleteBlogAndPost()
            {
                using (var service = new Service1Client())
                {
                    // Get all the Blogs
                    var blogs = service.GetBlogs();

                    // Use LINQ to Objects to find The Not-So-New Blog
                    var blog = blogs.First(b => b.Name == "The Not-So-New Blog");

                    // Mark all related Posts for deletion
                    // We need to call ToList because each Post will be removed from the
                    // Posts collection when we call MarkAsDeleted
                    foreach (var post in blog.Posts.ToList())
                    {
                        post.MarkAsDeleted();
                    }

                    // Mark the Blog for deletion
                    blog.MarkAsDeleted();

                    // Save the changes using the service
                    service.UpdateBlog(blog);
                }
            }
        }
    }
```

<span data-ttu-id="5b74d-223">Ahora puede ejecutar la aplicación para verla en acción.</span><span class="sxs-lookup"><span data-stu-id="5b74d-223">You can now run the application to see it in action.</span></span>

-   <span data-ttu-id="5b74d-224">Haga clic en el **STESample.ConsoleTest** proyecto **el Explorador de soluciones** y seleccione **depurar -&gt; Iniciar nueva instancia**</span><span class="sxs-lookup"><span data-stu-id="5b74d-224">Right-click the **STESample.ConsoleTest** project in **Solution Explorer** and select **Debug -&gt; Start new instance**</span></span>

<span data-ttu-id="5b74d-225">Verá el siguiente resultado cuando se ejecuta la aplicación.</span><span class="sxs-lookup"><span data-stu-id="5b74d-225">You'll see the following output when the application executes.</span></span>

```
Initial Data:
ADO.NET Blog
- Intro to EF
- What is New

After Adding:
ADO.NET Blog
- Intro to EF
- What is New
The New Blog
- Welcome to the new blog
- What's new on the new blog

After Update:
ADO.NET Blog
- Intro to EF
- What is New
The Not-So-New Blog
- Welcome to the new blog
- What's new on the new blog

After Delete:
ADO.NET Blog
- Intro to EF
- What is New

Press any key to exit...
```

## <a name="consume-the-service-from-a-wpf-application"></a><span data-ttu-id="5b74d-226">Utilizar el servicio de una aplicación de WPF</span><span class="sxs-lookup"><span data-stu-id="5b74d-226">Consume the Service from a WPF Application</span></span>

<span data-ttu-id="5b74d-227">Vamos a crear una aplicación WPF que usa nuestro servicio.</span><span class="sxs-lookup"><span data-stu-id="5b74d-227">Let's create a WPF application that uses our service.</span></span>

-   <span data-ttu-id="5b74d-228">**Archivo -&gt; nuevo:&gt; proyecto...**</span><span class="sxs-lookup"><span data-stu-id="5b74d-228">**File -&gt; New -&gt; Project...**</span></span>
-   <span data-ttu-id="5b74d-229">Seleccione **Visual C\#**  en el panel izquierdo y, a continuación, **aplicación WPF**</span><span class="sxs-lookup"><span data-stu-id="5b74d-229">Select **Visual C\#** from the left pane and then **WPF Application**</span></span>
-   <span data-ttu-id="5b74d-230">Escriba **STESample.WPFTest** como el nombre y haga clic en **Aceptar**</span><span class="sxs-lookup"><span data-stu-id="5b74d-230">Enter **STESample.WPFTest** as the name and click **OK**</span></span>
-   <span data-ttu-id="5b74d-231">Agregue una referencia a la **STESample.Entities** proyecto</span><span class="sxs-lookup"><span data-stu-id="5b74d-231">Add a reference to the **STESample.Entities** project</span></span>

<span data-ttu-id="5b74d-232">Se necesita una referencia de servicio a nuestro servicio WCF</span><span class="sxs-lookup"><span data-stu-id="5b74d-232">We need a service reference to our WCF service</span></span>

-   <span data-ttu-id="5b74d-233">Haga clic en el **STESample.WPFTest** proyecto **el Explorador de soluciones** y seleccione **Agregar referencia de servicio...**</span><span class="sxs-lookup"><span data-stu-id="5b74d-233">Right-click the **STESample.WPFTest** project in **Solution Explorer** and select **Add Service Reference...**</span></span>
-   <span data-ttu-id="5b74d-234">Haga clic en **detectar**</span><span class="sxs-lookup"><span data-stu-id="5b74d-234">Click **Discover**</span></span>
-   <span data-ttu-id="5b74d-235">Escriba **BloggingService** como el espacio de nombres y haga clic en **Aceptar**</span><span class="sxs-lookup"><span data-stu-id="5b74d-235">Enter **BloggingService** as the namespace and click **OK**</span></span>

<span data-ttu-id="5b74d-236">Ahora podemos escribir algo de código para consumir el servicio.</span><span class="sxs-lookup"><span data-stu-id="5b74d-236">Now we can write some code to consume the service.</span></span>

-   <span data-ttu-id="5b74d-237">Abra **MainWindow.xaml** y reemplace el contenido con el código siguiente.</span><span class="sxs-lookup"><span data-stu-id="5b74d-237">Open **MainWindow.xaml** and replace the contents with the following code.</span></span>

``` xaml
    <Window
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
        xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
        xmlns:STESample="clr-namespace:STESample;assembly=STESample.Entities"
        mc:Ignorable="d" x:Class="STESample.WPFTest.MainWindow"
        Title="MainWindow" Height="350" Width="525" Loaded="Window_Loaded">

        <Window.Resources>
            <CollectionViewSource
                x:Key="blogViewSource"
                d:DesignSource="{d:DesignInstance {x:Type STESample:Blog}, CreateList=True}"/>
            <CollectionViewSource
                x:Key="blogPostsViewSource"
                Source="{Binding Posts, Source={StaticResource blogViewSource}}"/>
        </Window.Resources>

        <Grid DataContext="{StaticResource blogViewSource}">
            <DataGrid AutoGenerateColumns="False" EnableRowVirtualization="True"
                      ItemsSource="{Binding}" Margin="10,10,10,179">
                <DataGrid.Columns>
                    <DataGridTextColumn Binding="{Binding BlogId}" Header="Id" Width="Auto" IsReadOnly="True" />
                    <DataGridTextColumn Binding="{Binding Name}" Header="Name" Width="Auto"/>
                    <DataGridTextColumn Binding="{Binding Url}" Header="Url" Width="Auto"/>
                </DataGrid.Columns>
            </DataGrid>
            <DataGrid AutoGenerateColumns="False" EnableRowVirtualization="True"
                      ItemsSource="{Binding Source={StaticResource blogPostsViewSource}}" Margin="10,145,10,38">
                <DataGrid.Columns>
                    <DataGridTextColumn Binding="{Binding PostId}" Header="Id" Width="Auto"  IsReadOnly="True"/>
                    <DataGridTextColumn Binding="{Binding Title}" Header="Title" Width="Auto"/>
                    <DataGridTextColumn Binding="{Binding Content}" Header="Content" Width="Auto"/>
                </DataGrid.Columns>
            </DataGrid>
            <Button Width="68" Height="23" HorizontalAlignment="Right" VerticalAlignment="Bottom"
                    Margin="0,0,10,10" Click="buttonSave_Click">Save</Button>
        </Grid>
    </Window>
```

-   <span data-ttu-id="5b74d-238">Abra el código subyacente para MainWindow (**MainWindow.xaml.cs**) y reemplace el contenido con el código siguiente</span><span class="sxs-lookup"><span data-stu-id="5b74d-238">Open the code behind for MainWindow (**MainWindow.xaml.cs**) and replace the contents with the following code</span></span>

``` csharp
    using STESample.WPFTest.BloggingService;
    using System.Collections.Generic;
    using System.Linq;
    using System.Windows;
    using System.Windows.Data;

    namespace STESample.WPFTest
    {
        public partial class MainWindow : Window
        {
            public MainWindow()
            {
                InitializeComponent();
            }

            private void Window_Loaded(object sender, RoutedEventArgs e)
            {
                using (var service = new Service1Client())
                {
                    // Find the view source for Blogs and populate it with all Blogs (and related Posts)
                    // from the Service. The default editing functionality of WPF will allow the objects
                    // to be manipulated on the screen.
                    var blogsViewSource = (CollectionViewSource)this.FindResource("blogViewSource");
                    blogsViewSource.Source = service.GetBlogs().ToList();
                }
            }

            private void buttonSave_Click(object sender, RoutedEventArgs e)
            {
                using (var service = new Service1Client())
                {
                    // Get the blogs that are bound to the screen
                    var blogsViewSource = (CollectionViewSource)this.FindResource("blogViewSource");
                    var blogs = (List<Blog>)blogsViewSource.Source;

                    // Save all Blogs and related Posts
                    foreach (var blog in blogs)
                    {
                        service.UpdateBlog(blog);
                    }

                    // Re-query for data to get database-generated keys etc.
                    blogsViewSource.Source = service.GetBlogs().ToList();
                }
            }
        }
    }
```

<span data-ttu-id="5b74d-239">Ahora puede ejecutar la aplicación para verla en acción.</span><span class="sxs-lookup"><span data-stu-id="5b74d-239">You can now run the application to see it in action.</span></span>

-   <span data-ttu-id="5b74d-240">Haga clic en el **STESample.WPFTest** proyecto **el Explorador de soluciones** y seleccione **depurar -&gt; Iniciar nueva instancia**</span><span class="sxs-lookup"><span data-stu-id="5b74d-240">Right-click the **STESample.WPFTest** project in **Solution Explorer** and select **Debug -&gt; Start new instance**</span></span>
-   <span data-ttu-id="5b74d-241">Puede manipular los datos mediante la pantalla y guardarlo mediante el servicio del sistema con el **guardar** botón</span><span class="sxs-lookup"><span data-stu-id="5b74d-241">You can manipulate the data using the screen and save it via the service using the **Save** button</span></span>

![WPF](~/ef6/media/wpf.png)