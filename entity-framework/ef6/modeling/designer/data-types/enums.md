---
title: Compatibilidad de enumeraciones - EF Designer - EF6
author: divega
ms.date: 2016-10-23
ms.prod: entity-framework
ms.author: divega
ms.manager: avickers
ms.technology: entity-framework-6
ms.topic: article
ms.assetid: c6ae6d8f-1ace-47db-ad47-b1718f1ba082
caps.latest.revision: 3
ms.openlocfilehash: cbf9b01fcbe21274ff3644c6ae6bc8fdfd338e3b
ms.sourcegitcommit: 390f3a37bc55105ed7cc5b0e0925b7f9c9e80ba6
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/09/2018
ms.locfileid: "39122584"
---
# <a name="enum-support---ef-designer"></a><span data-ttu-id="98187-102">Compatibilidad con enum - EF Designer</span><span class="sxs-lookup"><span data-stu-id="98187-102">Enum Support - EF Designer</span></span>
> [!NOTE]
> <span data-ttu-id="98187-103">**EF5 y versiones posteriores solo** -las características, las API, etc. se describe en esta página se introdujeron en Entity Framework 5.</span><span class="sxs-lookup"><span data-stu-id="98187-103">**EF5 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 5.</span></span> <span data-ttu-id="98187-104">Si usa una versión anterior, no se aplica parte o la totalidad de la información.</span><span class="sxs-lookup"><span data-stu-id="98187-104">If you are using an earlier version, some or all of the information does not apply.</span></span>

<span data-ttu-id="98187-105">En este tutorial de vídeo y paso a paso muestra cómo usar los tipos de enumeración con Entity Framework Designer.</span><span class="sxs-lookup"><span data-stu-id="98187-105">This video and step-by-step walkthrough shows how to use enum types with the Entity Framework Designer.</span></span> <span data-ttu-id="98187-106">También se muestra cómo usar las enumeraciones en una consulta LINQ.</span><span class="sxs-lookup"><span data-stu-id="98187-106">It also demonstrates how to use enums in a LINQ query.</span></span>

<span data-ttu-id="98187-107">En este tutorial usará Model First para crear una nueva base de datos, pero también se puede usar el Diseñador de EF con el [Database First](~/ef6/modeling/designer/workflows/database-first.md) flujo de trabajo para asignar a una base de datos existente.</span><span class="sxs-lookup"><span data-stu-id="98187-107">This walkthrough will use Model First to create a new database, but the EF Designer can also be used with the [Database First](~/ef6/modeling/designer/workflows/database-first.md) workflow to map to an existing database.</span></span>

<span data-ttu-id="98187-108">Compatibilidad de la enumeración se introdujo en Entity Framework 5.</span><span class="sxs-lookup"><span data-stu-id="98187-108">Enum support was introduced in Entity Framework 5.</span></span> <span data-ttu-id="98187-109">Para usar las nuevas características como enumeraciones, tipos de datos espaciales y funciones con valores de tabla, debe tener como destino .NET Framework 4.5.</span><span class="sxs-lookup"><span data-stu-id="98187-109">To use the new features like enums, spatial data types, and table-valued functions, you must target .NET Framework 4.5.</span></span> <span data-ttu-id="98187-110">Visual Studio 2012 tiene como destino .NET 4.5 de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="98187-110">Visual Studio 2012 targets .NET 4.5 by default.</span></span>

<span data-ttu-id="98187-111">En Entity Framework, una enumeración puede tener los siguientes tipos subyacentes: **bytes**, **Int16**, **Int32**, **Int64** , o **SByte**.</span><span class="sxs-lookup"><span data-stu-id="98187-111">In Entity Framework, an enumeration can have the following underlying types: **Byte**, **Int16**, **Int32**, **Int64** , or **SByte**.</span></span>

## <a name="watch-the-video"></a><span data-ttu-id="98187-112">Vea el vídeo</span><span class="sxs-lookup"><span data-stu-id="98187-112">Watch the Video</span></span>
<span data-ttu-id="98187-113">Este vídeo muestra cómo usar los tipos de enumeración con Entity Framework Designer.</span><span class="sxs-lookup"><span data-stu-id="98187-113">This video shows how to use enum types with the Entity Framework Designer.</span></span> <span data-ttu-id="98187-114">También se muestra cómo usar las enumeraciones en una consulta LINQ.</span><span class="sxs-lookup"><span data-stu-id="98187-114">It also demonstrates how to use enums in a LINQ query.</span></span>

<span data-ttu-id="98187-115">**Presentado por**: Julia Kornich</span><span class="sxs-lookup"><span data-stu-id="98187-115">**Presented By**: Julia Kornich</span></span>

<span data-ttu-id="98187-116">**Vídeo**: [WMV](http://download.microsoft.com/download/0/7/A/07ADECC9-7893-415D-9F20-8B97D46A37EC/HDI-ITPro-MSDN-winvideo-enumwithdesiger.wmv) | [MP4](http://download.microsoft.com/download/0/7/A/07ADECC9-7893-415D-9F20-8B97D46A37EC/HDI-ITPro-MSDN-mp4video-enumwithdesiger.m4v) | [WMV (ZIP)](http://download.microsoft.com/download/0/7/A/07ADECC9-7893-415D-9F20-8B97D46A37EC/HDI-ITPro-MSDN-winvideo-enumwithdesiger.zip)</span><span class="sxs-lookup"><span data-stu-id="98187-116">**Video**: [WMV](http://download.microsoft.com/download/0/7/A/07ADECC9-7893-415D-9F20-8B97D46A37EC/HDI-ITPro-MSDN-winvideo-enumwithdesiger.wmv) | [MP4](http://download.microsoft.com/download/0/7/A/07ADECC9-7893-415D-9F20-8B97D46A37EC/HDI-ITPro-MSDN-mp4video-enumwithdesiger.m4v) | [WMV (ZIP)](http://download.microsoft.com/download/0/7/A/07ADECC9-7893-415D-9F20-8B97D46A37EC/HDI-ITPro-MSDN-winvideo-enumwithdesiger.zip)</span></span>

## <a name="pre-requisites"></a><span data-ttu-id="98187-117">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="98187-117">Pre-Requisites</span></span>

<span data-ttu-id="98187-118">Necesitará tener Visual Studio 2012, Ultimate, Premium, Professional o Web Express edition instalado para completar este tutorial.</span><span class="sxs-lookup"><span data-stu-id="98187-118">You will need to have Visual Studio 2012, Ultimate, Premium, Professional, or Web Express edition installed to complete this walkthrough.</span></span>

## <a name="set-up-the-project"></a><span data-ttu-id="98187-119">Configurar el proyecto</span><span class="sxs-lookup"><span data-stu-id="98187-119">Set up the Project</span></span>

1.  <span data-ttu-id="98187-120">Abra Visual Studio 2012</span><span class="sxs-lookup"><span data-stu-id="98187-120">Open Visual Studio 2012</span></span>
2.  <span data-ttu-id="98187-121">En el **archivo** menú, elija **New**y, a continuación, haga clic en **proyecto**</span><span class="sxs-lookup"><span data-stu-id="98187-121">On the **File** menu, point to **New**, and then click **Project**</span></span>
3.  <span data-ttu-id="98187-122">En el panel izquierdo, haga clic en **Visual C\#** y, a continuación, seleccione el **consola** plantilla</span><span class="sxs-lookup"><span data-stu-id="98187-122">In the left pane, click **Visual C\#**, and then select the **Console** template</span></span>
4.  <span data-ttu-id="98187-123">Escriba **EnumEFDesigner** como el nombre del proyecto y haga clic en **Aceptar**</span><span class="sxs-lookup"><span data-stu-id="98187-123">Enter **EnumEFDesigner** as the name of the project and click **OK**</span></span>

## <a name="create-a-new-model-using-the-ef-designer"></a><span data-ttu-id="98187-124">Crear un nuevo modelo con EF Designer</span><span class="sxs-lookup"><span data-stu-id="98187-124">Create a New Model using the EF Designer</span></span>

1.  <span data-ttu-id="98187-125">Haga clic en el nombre del proyecto en el Explorador de soluciones, seleccione **agregar**y, a continuación, haga clic en **nuevo elemento**</span><span class="sxs-lookup"><span data-stu-id="98187-125">Right-click the project name in Solution Explorer, point to **Add**, and then click **New Item**</span></span>
2.  <span data-ttu-id="98187-126">Seleccione **datos** desde el menú de la izquierda y seleccione **ADO.NET Entity Data Model** en el panel de plantillas</span><span class="sxs-lookup"><span data-stu-id="98187-126">Select **Data** from the left menu and then select **ADO.NET Entity Data Model** in the Templates pane</span></span>
3.  <span data-ttu-id="98187-127">Escriba **EnumTestModel.edmx** para el nombre de archivo y, a continuación, haga clic en **agregar**</span><span class="sxs-lookup"><span data-stu-id="98187-127">Enter **EnumTestModel.edmx** for the file name, and then click **Add**</span></span>
4.  <span data-ttu-id="98187-128">En la página del Asistente para Entity Data Model, seleccione **modelo vacío** en el cuadro de diálogo Elegir contenido del modelo</span><span class="sxs-lookup"><span data-stu-id="98187-128">On the Entity Data Model Wizard page, select **Empty Model** in the Choose Model Contents dialog box</span></span>
5.  <span data-ttu-id="98187-129">Haga clic en **finalizar**</span><span class="sxs-lookup"><span data-stu-id="98187-129">Click **Finish**</span></span>

<span data-ttu-id="98187-130">Entity Designer, que proporciona una superficie de diseño para modificar el modelo, se muestra.</span><span class="sxs-lookup"><span data-stu-id="98187-130">The Entity Designer, which provides a design surface for editing your model, is displayed.</span></span>

<span data-ttu-id="98187-131">El asistente realiza las siguientes acciones:</span><span class="sxs-lookup"><span data-stu-id="98187-131">The wizard performs the following actions:</span></span>

-   <span data-ttu-id="98187-132">Genera el archivo EnumTestModel.edmx que define el modelo conceptual, el modelo de almacenamiento y la asignación entre ellos.</span><span class="sxs-lookup"><span data-stu-id="98187-132">Generates the EnumTestModel.edmx file that defines the conceptual model, the storage model, and the mapping between them.</span></span> <span data-ttu-id="98187-133">Establece la propiedad Metadata Artifact Processing del archivo .edmx para insertar en el ensamblado de salida para que los archivos de metadatos generados se incrustan en el ensamblado.</span><span class="sxs-lookup"><span data-stu-id="98187-133">Sets the Metadata Artifact Processing property of the .edmx file to Embed in Output Assembly so the generated metadata files get embedded into the assembly.</span></span>
-   <span data-ttu-id="98187-134">Agrega una referencia a los ensamblados siguientes: Entity Framework, System.ComponentModel.DataAnnotations y System.Data.Entity.</span><span class="sxs-lookup"><span data-stu-id="98187-134">Adds a reference to the following assemblies: EntityFramework, System.ComponentModel.DataAnnotations, and System.Data.Entity.</span></span>
-   <span data-ttu-id="98187-135">Crea los archivos EnumTestModel.tt y EnumTestModel.Context.tt y los agrega en el archivo .edmx.</span><span class="sxs-lookup"><span data-stu-id="98187-135">Creates EnumTestModel.tt and EnumTestModel.Context.tt files and adds them under the .edmx file.</span></span> <span data-ttu-id="98187-136">Estos archivos de plantilla T4 generan el código que define el tipo de DbContext derivado y tipos POCO que se asignan a las entidades en el modelo de edmx.</span><span class="sxs-lookup"><span data-stu-id="98187-136">These T4 template files generate the code that defines the DbContext derived type and POCO types that map to the entities in the .edmx model.</span></span>

## <a name="add-a-new-entity-type"></a><span data-ttu-id="98187-137">Agregar un nuevo tipo de entidad</span><span class="sxs-lookup"><span data-stu-id="98187-137">Add a New Entity Type</span></span>

1.  <span data-ttu-id="98187-138">Haga clic en un área vacía de la superficie de diseño, seleccione **Add -&gt; entidad**, aparece el cuadro de diálogo New Entity</span><span class="sxs-lookup"><span data-stu-id="98187-138">Right-click an empty area of the design surface, select **Add -&gt; Entity**, the New Entity dialog box appears</span></span>
2.  <span data-ttu-id="98187-139">Especificar **departamento** para el tipo de nombre y especificar **DepartmentID** para el nombre de propiedad de clave, deje el tipo como **Int32**</span><span class="sxs-lookup"><span data-stu-id="98187-139">Specify **Department** for the type name and specify **DepartmentID** for the key property name, leave the type as **Int32**</span></span>
3.  <span data-ttu-id="98187-140">Haga clic en **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="98187-140">Click **OK**</span></span>
4.  <span data-ttu-id="98187-141">Haga clic en la entidad y seleccione **Add New -&gt; propiedad escalar**</span><span class="sxs-lookup"><span data-stu-id="98187-141">Right-click the entity and select **Add New -&gt; Scalar Property**</span></span>
5.  <span data-ttu-id="98187-142">Cambiar el nombre de la nueva propiedad a **nombre**</span><span class="sxs-lookup"><span data-stu-id="98187-142">Rename the new property to **Name**</span></span>
6.  <span data-ttu-id="98187-143">Cambiar el tipo de la nueva propiedad a **Int32** (de forma predeterminada, la nueva propiedad es de tipo cadena) para cambiar el tipo, abra la ventana Propiedades y cambie la propiedad de tipo a **Int32**</span><span class="sxs-lookup"><span data-stu-id="98187-143">Change the type of the new property to **Int32** (by default, the new property is of String type) To change the type, open the Properties window and change the Type property to **Int32**</span></span>
7.  <span data-ttu-id="98187-144">Agregar otra propiedad escalar y cambie su nombre a **presupuesto**, cambie el tipo a **Decimal**</span><span class="sxs-lookup"><span data-stu-id="98187-144">Add another scalar property and rename it to **Budget**, change the type to **Decimal**</span></span>

## <a name="add-an-enum-type"></a><span data-ttu-id="98187-145">Agregar un tipo de enumeración</span><span class="sxs-lookup"><span data-stu-id="98187-145">Add an Enum Type</span></span>

1.  <span data-ttu-id="98187-146">En Entity Framework Designer, haga clic en la propiedad Name, seleccione **convertir para una enumeración**</span><span class="sxs-lookup"><span data-stu-id="98187-146">In the Entity Framework Designer, right-click the Name property, select **Convert to enum**</span></span>

    ![ConvertToEnum](~/ef6/media/converttoenum.png)

2.  <span data-ttu-id="98187-148">En el **agregar Enum** tipo de cuadro de diálogo **DepartmentNames** para el nombre del tipo Enum, cambie el tipo subyacente a **Int32**, y, a continuación, agregue los siguientes miembros para el tipo: inglés, Matemáticas y ahorro</span><span class="sxs-lookup"><span data-stu-id="98187-148">In the **Add Enum** dialog box type **DepartmentNames** for the Enum Type Name, change the Underlying Type to **Int32**, and then add the following members to the type: English, Math, and Economics</span></span>

    ![AddEnumType](~/ef6/media/addenumtype.png)

3.  <span data-ttu-id="98187-150">Presione **Aceptar**</span><span class="sxs-lookup"><span data-stu-id="98187-150">Press **OK**</span></span>
4.  <span data-ttu-id="98187-151">Guardar el modelo y compile el proyecto</span><span class="sxs-lookup"><span data-stu-id="98187-151">Save the model and build the project</span></span>
    > [!NOTE]
    > <span data-ttu-id="98187-152">Cuando se compila, las advertencias sobre sin asignar entidades y asociaciones pueden aparecer en la lista de errores.</span><span class="sxs-lookup"><span data-stu-id="98187-152">When you build, warnings about unmapped entities and associations may appear in the Error List.</span></span> <span data-ttu-id="98187-153">Puede omitir estas advertencias porque una vez que se elija Generar la base de datos del modelo, los errores desaparecerán.</span><span class="sxs-lookup"><span data-stu-id="98187-153">You can ignore these warnings because after we choose to generate the database from the model, the errors will go away.</span></span>

<span data-ttu-id="98187-154">Si observa la ventana Propiedades, observará que el tipo de la propiedad Name se cambiara a **DepartmentNames** y el tipo de enumeración recién agregada se agregó a la lista de tipos.</span><span class="sxs-lookup"><span data-stu-id="98187-154">If you look at the Properties window, you will notice that the type of the Name property was changed to **DepartmentNames** and the newly added enum type was added to the list of types.</span></span>

<span data-ttu-id="98187-155">Si cambia a la ventana del explorador de modelos, verá que el tipo también se ha agregado al nodo de tipos de enumeración.</span><span class="sxs-lookup"><span data-stu-id="98187-155">If you switch to the Model Browser window, you will see that the type was also added to the Enum Types node.</span></span>

![ModelBrowser](~/ef6/media/modelbrowser.png)

>[!NOTE]
> <span data-ttu-id="98187-157">También puede agregar nuevos tipos de enumeración desde esta ventana, haga clic en el botón secundario del mouse y seleccione **Agregar tipo de enumeración**.</span><span class="sxs-lookup"><span data-stu-id="98187-157">You can also add new enum types from this window by clicking the right mouse button and selecting **Add Enum Type**.</span></span> <span data-ttu-id="98187-158">Una vez creado el tipo aparecerá en la lista de tipos y podrá asociar a una propiedad</span><span class="sxs-lookup"><span data-stu-id="98187-158">Once the type is created it will appear in the list of types and you would be able to associate with a property</span></span>

## <a name="generate-database-from-model"></a><span data-ttu-id="98187-159">Generar la base de datos de modelo</span><span class="sxs-lookup"><span data-stu-id="98187-159">Generate Database from Model</span></span>

<span data-ttu-id="98187-160">Ahora podemos generar una base de datos que se basa en el modelo.</span><span class="sxs-lookup"><span data-stu-id="98187-160">Now we can generate a database that is based on the model.</span></span>

1.  <span data-ttu-id="98187-161">Haga clic en un espacio vacío en el Entity Designer superficie y seleccione **generar base de datos de modelo**</span><span class="sxs-lookup"><span data-stu-id="98187-161">Right-click an empty space on the Entity Designer surface and select **Generate Database from Model**</span></span>
2.  <span data-ttu-id="98187-162">Haga clic en mostrará la elija el cuadro de diálogo de conexión de datos del Asistente para generar base de datos es el **nueva conexión** botón especifique **(localdb)\\mssqllocaldb** para el nombre del servidor y  **EnumTest** para la base de datos y haga clic en **Aceptar**</span><span class="sxs-lookup"><span data-stu-id="98187-162">The Choose Your Data Connection Dialog Box of the Generate Database Wizard is displayed Click the **New Connection** button Specify **(localdb)\\mssqllocaldb** for the server name and **EnumTest** for the database and click **OK**</span></span>
3.  <span data-ttu-id="98187-163">Se abrirá, haga clic en un cuadro de diálogo que le pregunta si desea crear una nueva base de datos **Sí**.</span><span class="sxs-lookup"><span data-stu-id="98187-163">A dialog asking if you want to create a new database will pop up, click **Yes**.</span></span>
4.  <span data-ttu-id="98187-164">Haga clic en **siguiente** y Asistente para crear base de datos genera el lenguaje de definición de datos (DDL) para crear una base de datos de la DDL generada se muestra en el resumen y una nota de cuadro de diálogo Configuración, el DDL no contiene una definición para un tabla que se asigna al tipo de enumeración</span><span class="sxs-lookup"><span data-stu-id="98187-164">Click **Next** and the Create Database Wizard generates data definition language (DDL) for creating a database The generated DDL is displayed in the Summary and Settings Dialog Box Note, that the DDL does not contain a definition for a table that maps to the enumeration type</span></span>
5.  <span data-ttu-id="98187-165">Haga clic en **finalizar** no ejecuta el script DDL al hacer clic en Finalizar.</span><span class="sxs-lookup"><span data-stu-id="98187-165">Click **Finish** Clicking Finish does not execute the DDL script.</span></span>
6.  <span data-ttu-id="98187-166">El Asistente para crear base de datos hace lo siguiente: abre el **EnumTest.edmx.sql** en T-SQL, Editor genera las secciones de esquema y asignación de almacén de EDMX archivo agrega información de la cadena de conexión al archivo App.config</span><span class="sxs-lookup"><span data-stu-id="98187-166">The Create Database Wizard does the following: Opens the **EnumTest.edmx.sql** in T-SQL Editor Generates the store schema and mapping sections of the EDMX file Adds connection string information to the App.config file</span></span>
7.  <span data-ttu-id="98187-167">Haga clic en el botón secundario del mouse en el Editor de Transact-SQL y seleccione **Execute** aparece el diálogo Conectar a servidor, escriba la información de conexión del paso 2 y haga clic en **Connect**</span><span class="sxs-lookup"><span data-stu-id="98187-167">Click the right mouse button in T-SQL Editor and select **Execute** The Connect to Server dialog appears, enter the connection information from step 2 and click **Connect**</span></span>
8.  <span data-ttu-id="98187-168">Para ver el esquema generado, haga doble clic en el nombre de base de datos en el Explorador de objetos de SQL Server y seleccione **actualizar**</span><span class="sxs-lookup"><span data-stu-id="98187-168">To view the generated schema, right-click on the database name in SQL Server Object Explorer and select **Refresh**</span></span>

## <a name="persist-and-retrieve-data"></a><span data-ttu-id="98187-169">Conservar y recuperar datos</span><span class="sxs-lookup"><span data-stu-id="98187-169">Persist and Retrieve Data</span></span>

<span data-ttu-id="98187-170">Abra el archivo Program.cs, donde se define el método Main.</span><span class="sxs-lookup"><span data-stu-id="98187-170">Open the Program.cs file where the Main method is defined.</span></span> <span data-ttu-id="98187-171">Agregue el código siguiente en la función Main.</span><span class="sxs-lookup"><span data-stu-id="98187-171">Add the following code into the Main function.</span></span> <span data-ttu-id="98187-172">El código agrega un nuevo objeto de departamento en el contexto.</span><span class="sxs-lookup"><span data-stu-id="98187-172">The code adds a new Department object to the context.</span></span> <span data-ttu-id="98187-173">A continuación, guarda los datos.</span><span class="sxs-lookup"><span data-stu-id="98187-173">It then saves the data.</span></span> <span data-ttu-id="98187-174">El código también ejecuta una consulta LINQ que devuelve un departamento donde el nombre es DepartmentNames.English.</span><span class="sxs-lookup"><span data-stu-id="98187-174">The code also executes a LINQ query that returns a Department where the name is DepartmentNames.English.</span></span>

``` csharp
using (var context = new EnumTestModelContainer())
{
    context.Departments.Add(new Department{ Name = DepartmentNames.English });

    context.SaveChanges();

    var department = (from d in context.Departments
                        where d.Name == DepartmentNames.English
                        select d).FirstOrDefault();

    Console.WriteLine(
        "DepartmentID: {0} and Name: {1}",
        department.DepartmentID,  
        department.Name);
}
```

<span data-ttu-id="98187-175">Compile y ejecute la aplicación.</span><span class="sxs-lookup"><span data-stu-id="98187-175">Compile and run the application.</span></span> <span data-ttu-id="98187-176">El programa produce el siguiente resultado:</span><span class="sxs-lookup"><span data-stu-id="98187-176">The program produces the following output:</span></span>

```
DepartmentID: 1 Name: English
```

<span data-ttu-id="98187-177">Para ver los datos en la base de datos, haga doble clic en el nombre de base de datos en el Explorador de objetos de SQL Server y seleccione **actualizar**.</span><span class="sxs-lookup"><span data-stu-id="98187-177">To view data in the database, right-click on the database name in SQL Server Object Explorer and select **Refresh**.</span></span> <span data-ttu-id="98187-178">A continuación, haga clic en el botón secundario del mouse en la tabla y seleccione **ver datos**.</span><span class="sxs-lookup"><span data-stu-id="98187-178">Then, click the right mouse button on the table and select **View Data**.</span></span>

## <a name="summary"></a><span data-ttu-id="98187-179">Resumen</span><span class="sxs-lookup"><span data-stu-id="98187-179">Summary</span></span>

<span data-ttu-id="98187-180">En este tutorial explicamos cómo asignar tipos de enumeración mediante el Diseñador de Entity Framework y cómo usar las enumeraciones en el código.</span><span class="sxs-lookup"><span data-stu-id="98187-180">In this walkthrough we looked at how to map enum types using the Entity Framework Designer and how to use enums in code.</span></span> 