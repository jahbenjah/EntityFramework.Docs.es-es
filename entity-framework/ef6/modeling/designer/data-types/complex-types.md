---
title: Tipos complejos - EF Designer - EF6
author: divega
ms.date: 2016-10-23
ms.prod: entity-framework
ms.author: divega
ms.manager: avickers
ms.technology: entity-framework-6
ms.topic: article
ms.assetid: 9a8228ef-acfd-4575-860d-769d2c0e18a1
caps.latest.revision: 3
ms.openlocfilehash: 6d0744921085afdafa35d137d276c54738cdd465
ms.sourcegitcommit: f05e7b62584cf228f17390bb086a61d505712e1b
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/08/2018
ms.locfileid: "39122279"
---
# <a name="complex-types---ef-designer"></a><span data-ttu-id="0321a-102">Tipos complejos - EF Designer</span><span class="sxs-lookup"><span data-stu-id="0321a-102">Complex Types - EF Designer</span></span>
<span data-ttu-id="0321a-103">En este tema se muestra cómo asignar tipos complejos con Entity Framework Designer (Diseñador de EF) y cómo consultar entidades que contienen propiedades de tipo complejo.</span><span class="sxs-lookup"><span data-stu-id="0321a-103">This topic shows how to map complex types with the Entity Framework Designer (EF Designer) and how to query for entities that contain properties of complex type.</span></span>

<span data-ttu-id="0321a-104">La siguiente imagen muestra las ventanas principales que se usan al trabajar con EF Designer.</span><span class="sxs-lookup"><span data-stu-id="0321a-104">The following image shows the main windows that are used when working with the EF Designer.</span></span>

![EFDesigner](~/ef6/media/efdesigner.png)

> [!NOTE]
> <span data-ttu-id="0321a-106">Cuando se compila el modelo conceptual, las advertencias sobre sin asignar entidades y asociaciones pueden aparecer en la lista de errores.</span><span class="sxs-lookup"><span data-stu-id="0321a-106">When you build the conceptual model, warnings about unmapped entities and associations may appear in the Error List.</span></span> <span data-ttu-id="0321a-107">Puede omitir estas advertencias porque después de elegir generar la base de datos del modelo, los errores desaparecerán.</span><span class="sxs-lookup"><span data-stu-id="0321a-107">You can ignore these warnings because after you choose to generate the database from the model, the errors will go away.</span></span>

## <a name="what-is-a-complex-type"></a><span data-ttu-id="0321a-108">¿Qué es un tipo complejo</span><span class="sxs-lookup"><span data-stu-id="0321a-108">What is a Complex Type</span></span>

<span data-ttu-id="0321a-109">Los tipos complejos son propiedades no escalares de tipos de entidad que permiten organizar las propiedades escalares dentro de las entidades.</span><span class="sxs-lookup"><span data-stu-id="0321a-109">Complex types are non-scalar properties of entity types that enable scalar properties to be organized within entities.</span></span> <span data-ttu-id="0321a-110">Al igual que las entidades, los tipos complejos están compuestos de propiedades escalares u otras propiedades de tipo complejo.</span><span class="sxs-lookup"><span data-stu-id="0321a-110">Like entities, complex types consist of scalar properties or other complex type properties.</span></span>

<span data-ttu-id="0321a-111">Cuando se trabaja con objetos que representan tipos complejos, tenga en cuenta lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="0321a-111">When you work with objects that represent complex types, be aware of the following:</span></span>

-   <span data-ttu-id="0321a-112">Tipos complejos no tienen claves y, por tanto, no pueden existir de forma independiente.</span><span class="sxs-lookup"><span data-stu-id="0321a-112">Complex types do not have keys and therefore cannot exist independently.</span></span> <span data-ttu-id="0321a-113">Los tipos complejos solo pueden existir como propiedades de tipos de entidad u otros tipos complejos.</span><span class="sxs-lookup"><span data-stu-id="0321a-113">Complex types can only exist as properties of entity types or other complex types.</span></span>
-   <span data-ttu-id="0321a-114">Tipos complejos no pueden participar en las asociaciones y no pueden contener las propiedades de navegación.</span><span class="sxs-lookup"><span data-stu-id="0321a-114">Complex types cannot participate in associations and cannot contain navigation properties.</span></span>
-   <span data-ttu-id="0321a-115">Propiedades de tipo complejo no pueden ser **null**.</span><span class="sxs-lookup"><span data-stu-id="0321a-115">Complex type properties cannot be **null**.</span></span> <span data-ttu-id="0321a-116">Un ** InvalidOperationException ** se produce cuando **DbContext.SaveChanges** se denomina y se encuentra un objeto complejo null.</span><span class="sxs-lookup"><span data-stu-id="0321a-116">An **InvalidOperationException **occurs when **DbContext.SaveChanges** is called and a null complex object is encountered.</span></span> <span data-ttu-id="0321a-117">Las propiedades escalares de objetos complejos pueden ser **null**.</span><span class="sxs-lookup"><span data-stu-id="0321a-117">Scalar properties of complex objects can be **null**.</span></span>
-   <span data-ttu-id="0321a-118">Los tipos complejos no pueden heredar de otros tipos complejos.</span><span class="sxs-lookup"><span data-stu-id="0321a-118">Complex types cannot inherit from other complex types.</span></span>
-   <span data-ttu-id="0321a-119">Debe definir el tipo complejo como un **clase**.</span><span class="sxs-lookup"><span data-stu-id="0321a-119">You must define the complex type as a **class**.</span></span> 
-   <span data-ttu-id="0321a-120">EF detecta los cambios en los miembros en un objeto de tipo complejo cuando **DbContext.DetectChanges** se llama.</span><span class="sxs-lookup"><span data-stu-id="0321a-120">EF detects changes to members on a complex type object when **DbContext.DetectChanges** is called.</span></span> <span data-ttu-id="0321a-121">Entity Framework llama **DetectChanges** automáticamente cuando se llama a los siguientes miembros: **DbSet.Find**, **DbSet.Local**, **DbSet.Remove**, **DbSet.Add**, **DbSet.Attach**, **DbContext.SaveChanges**, **DbContext.GetValidationErrors**, **DbContext.Entry**, **DbChangeTracker.Entries**.</span><span class="sxs-lookup"><span data-stu-id="0321a-121">Entity Framework calls **DetectChanges** automatically when the following members are called: **DbSet.Find**, **DbSet.Local**, **DbSet.Remove**, **DbSet.Add**, **DbSet.Attach**, **DbContext.SaveChanges**, **DbContext.GetValidationErrors**, **DbContext.Entry**, **DbChangeTracker.Entries**.</span></span>

## <a name="refactor-an-entitys-properties-into-new-complex-type"></a><span data-ttu-id="0321a-122">Refactorizar propiedades de la entidad en el nuevo tipo complejo</span><span class="sxs-lookup"><span data-stu-id="0321a-122">Refactor an Entity’s Properties into New Complex Type</span></span>

<span data-ttu-id="0321a-123">Si ya tiene una entidad en el modelo conceptual que se desea refactorizar algunas de las propiedades en una propiedad de tipo complejo.</span><span class="sxs-lookup"><span data-stu-id="0321a-123">If you already have an entity in your conceptual model you may want to refactor some of the properties into a complex type property.</span></span>

<span data-ttu-id="0321a-124">En la superficie del diseñador, seleccione uno o más propiedades (excepto las propiedades de navegación) de una entidad, a continuación, haga clic en y seleccione **refactorizar -&gt; mover al nuevo tipo complejo**.</span><span class="sxs-lookup"><span data-stu-id="0321a-124">On the designer surface, select one or more properties (excluding navigation properties) of an entity, then right-click and select **Refactor -&gt; Move to New Complex Type**.</span></span>

![Refactorizar](~/ef6/media/refactor.png)

<span data-ttu-id="0321a-126">Se agrega un nuevo tipo complejo con las propiedades seleccionadas a la **Explorador de modelos**.</span><span class="sxs-lookup"><span data-stu-id="0321a-126">A new complex type with the selected properties is added to the **Model Browser**.</span></span> <span data-ttu-id="0321a-127">Se asigna un nombre predeterminado al tipo complejo.</span><span class="sxs-lookup"><span data-stu-id="0321a-127">The complex type is given a default name.</span></span>

<span data-ttu-id="0321a-128">Una propiedad compleja del tipo que se acaba de crear reemplaza las propiedades seleccionadas.</span><span class="sxs-lookup"><span data-stu-id="0321a-128">A complex property of the newly created type replaces the selected properties.</span></span> <span data-ttu-id="0321a-129">Se conservan todas las asignaciones de propiedades.</span><span class="sxs-lookup"><span data-stu-id="0321a-129">All property mappings are preserved.</span></span>

![Refactor2](~/ef6/media/refactor2.png)

## <a name="create-a-new-complex-type"></a><span data-ttu-id="0321a-131">Crear un nuevo tipo complejo</span><span class="sxs-lookup"><span data-stu-id="0321a-131">Create a New Complex Type</span></span>

<span data-ttu-id="0321a-132">También puede crear un nuevo tipo complejo que no contiene las propiedades de una entidad existente.</span><span class="sxs-lookup"><span data-stu-id="0321a-132">You can also create a new complex type that does not contain properties of an existing entity.</span></span>

<span data-ttu-id="0321a-133">Haga clic en el **tipos complejos** carpeta en el Explorador de modelos, apunte a **tipo complejo de AddNew...** .</span><span class="sxs-lookup"><span data-stu-id="0321a-133">Right-click the **Complex Types** folder in the Model Browser, point to **AddNew Complex Type…**.</span></span> <span data-ttu-id="0321a-134">Como alternativa, puede seleccionar la **tipos complejos** carpeta y presione la **insertar** en el teclado.</span><span class="sxs-lookup"><span data-stu-id="0321a-134">Alternatively, you can select the **Complex Types** folder and press the **Insert** key on your keyboard.</span></span>

![AddNewComplextype](~/ef6/media/addnewcomplextype.png)

<span data-ttu-id="0321a-136">Un nuevo tipo complejo se agrega a la carpeta con un nombre predeterminado.</span><span class="sxs-lookup"><span data-stu-id="0321a-136">A new complex type is added to the folder with a default name.</span></span> <span data-ttu-id="0321a-137">Ahora puede agregar propiedades al tipo.</span><span class="sxs-lookup"><span data-stu-id="0321a-137">You can now add properties to the type.</span></span>

## <a name="add-properties-to-a-complex-type"></a><span data-ttu-id="0321a-138">Agregar propiedades a un tipo complejo</span><span class="sxs-lookup"><span data-stu-id="0321a-138">Add Properties to a Complex Type</span></span>

<span data-ttu-id="0321a-139">Las propiedades de un tipo complejo pueden ser tipos escalares o los tipos complejos existentes.</span><span class="sxs-lookup"><span data-stu-id="0321a-139">Properties of a complex type can be scalar types or existing complex types.</span></span> <span data-ttu-id="0321a-140">Sin embargo, las propiedades de tipo complejo no pueden tener las referencias circulares.</span><span class="sxs-lookup"><span data-stu-id="0321a-140">However, complex type properties cannot have circular references.</span></span> <span data-ttu-id="0321a-141">Por ejemplo, un tipo complejo **OnsiteCourseDetails** no puede tener una propiedad de tipo complejo **OnsiteCourseDetails**.</span><span class="sxs-lookup"><span data-stu-id="0321a-141">For example, a complex type **OnsiteCourseDetails** cannot have a property of complex type **OnsiteCourseDetails**.</span></span>

<span data-ttu-id="0321a-142">Puede agregar una propiedad a un tipo complejo de cualquiera de las formas enumeradas a continuación.</span><span class="sxs-lookup"><span data-stu-id="0321a-142">You can add a property to a complex type in any of the ways listed below.</span></span>

-   <span data-ttu-id="0321a-143">Haga clic en un tipo complejo en el Explorador de modelos, seleccione **agregar**, a continuación, elija **propiedad escalar** o **propiedad compleja**, a continuación, seleccione el tipo de propiedad deseado.</span><span class="sxs-lookup"><span data-stu-id="0321a-143">Right-click a complex type in the Model Browser, point to **Add**, then point to **Scalar Property** or **Complex Property**, then select the desired property type.</span></span> <span data-ttu-id="0321a-144">Como alternativa, puede seleccionar un tipo complejo y, a continuación, presione el **insertar** en el teclado.</span><span class="sxs-lookup"><span data-stu-id="0321a-144">Alternatively, you can select a complex type and then press the **Insert** key on your keyboard.</span></span>  

    ![AddPropertiestoComplexType](~/ef6/media/addpropertiestocomplextype.png)

    <span data-ttu-id="0321a-146">Se agrega una nueva propiedad al tipo complejo con un nombre predeterminado.</span><span class="sxs-lookup"><span data-stu-id="0321a-146">A new property is added to the complex type with a default name.</span></span>

- <span data-ttu-id="0321a-147">OR:</span><span class="sxs-lookup"><span data-stu-id="0321a-147">OR -</span></span>

-   <span data-ttu-id="0321a-148">Haga clic en una propiedad de entidad en el **EF Designer** superficie y seleccione **copia**, a continuación, haga clic en el tipo complejo en el **Explorador de modelos** y seleccione  **Pegar**.</span><span class="sxs-lookup"><span data-stu-id="0321a-148">Right-click an entity property on the **EF  Designer** surface and select **Copy**, then right-click the complex type in the **Model Browser** and select **Paste**.</span></span>

## <a name="rename-a-complex-type"></a><span data-ttu-id="0321a-149">Cambiar el nombre de un tipo complejo</span><span class="sxs-lookup"><span data-stu-id="0321a-149">Rename a Complex Type</span></span>

<span data-ttu-id="0321a-150">Al cambiar el nombre de un tipo complejo, todas las referencias al tipo se actualizan en el proyecto.</span><span class="sxs-lookup"><span data-stu-id="0321a-150">When you rename a complex type, all references to the type are updated throughout the project.</span></span>

-   <span data-ttu-id="0321a-151">Lentamente, haga doble clic en un tipo complejo en el **Explorador de modelos**.</span><span class="sxs-lookup"><span data-stu-id="0321a-151">Slowly double-click a complex type in the **Model Browser**.</span></span>
    <span data-ttu-id="0321a-152">El nombre se seleccionará y se abrirá en modo de edición.</span><span class="sxs-lookup"><span data-stu-id="0321a-152">The name will be selected and in edit mode.</span></span>

- <span data-ttu-id="0321a-153">OR:</span><span class="sxs-lookup"><span data-stu-id="0321a-153">OR -</span></span>

-   <span data-ttu-id="0321a-154">Haga clic en un tipo complejo en el **Explorador de modelos** y seleccione **cambiar el nombre**.</span><span class="sxs-lookup"><span data-stu-id="0321a-154">Right-click a complex type in the **Model Browser** and select **Rename**.</span></span>

- <span data-ttu-id="0321a-155">OR:</span><span class="sxs-lookup"><span data-stu-id="0321a-155">OR -</span></span>

-   <span data-ttu-id="0321a-156">Seleccione un tipo complejo en el Explorador de modelos y presione la tecla F2.</span><span class="sxs-lookup"><span data-stu-id="0321a-156">Select a complex type in the Model Browser and press the F2 key.</span></span>

- <span data-ttu-id="0321a-157">OR:</span><span class="sxs-lookup"><span data-stu-id="0321a-157">OR -</span></span>

-   <span data-ttu-id="0321a-158">Haga clic en un tipo complejo en el **Explorador de modelos** y seleccione **propiedades**.</span><span class="sxs-lookup"><span data-stu-id="0321a-158">Right-click a complex type in the **Model Browser** and select **Properties**.</span></span> <span data-ttu-id="0321a-159">Edite el nombre de la **propiedades** ventana.</span><span class="sxs-lookup"><span data-stu-id="0321a-159">Edit the name in the **Properties** window.</span></span>

## <a name="add-an-existing-complex-type-to-an-entity-and-map-its-properties-to-table-columns"></a><span data-ttu-id="0321a-160">Agregar un tipo complejo existente a una entidad y sus propiedades se asignan a columnas de tabla</span><span class="sxs-lookup"><span data-stu-id="0321a-160">Add an Existing Complex Type to an Entity and Map its Properties to Table Columns</span></span>

1.  <span data-ttu-id="0321a-161">Haga clic en una entidad, seleccione **Agregar nuevo**y seleccione **propiedad compleja**.</span><span class="sxs-lookup"><span data-stu-id="0321a-161">Right-click an entity, point to **Add New**, and select **Complex Property**.</span></span>
    <span data-ttu-id="0321a-162">Se agrega a la entidad una propiedad de tipo complejo con un nombre predeterminado.</span><span class="sxs-lookup"><span data-stu-id="0321a-162">A complex type property with a default name is added to the entity.</span></span> <span data-ttu-id="0321a-163">Se asigna un tipo predeterminado (que se elige entre los tipos complejos existentes) a la propiedad.</span><span class="sxs-lookup"><span data-stu-id="0321a-163">A default type (chosen from the existing complex types) is assigned to the property.</span></span>
2.  <span data-ttu-id="0321a-164">Asigne el tipo deseado a la propiedad en el **propiedades** ventana.</span><span class="sxs-lookup"><span data-stu-id="0321a-164">Assign the desired type to the property in the **Properties** window.</span></span>
    <span data-ttu-id="0321a-165">Después de agregar una propiedad de tipo complejo a una entidad, debe asignar sus propiedades a las columnas de una tabla.</span><span class="sxs-lookup"><span data-stu-id="0321a-165">After adding a complex type property to an entity, you must map its properties to table columns.</span></span>
3.  <span data-ttu-id="0321a-166">Haga clic en un tipo de entidad en la superficie de diseño o en el **Explorador de modelos** y seleccione **asignaciones de tabla**.</span><span class="sxs-lookup"><span data-stu-id="0321a-166">Right-click an entity type on the design surface or in the **Model Browser** and select **Table Mappings**.</span></span>
    <span data-ttu-id="0321a-167">Las asignaciones de tabla se muestran en el **detalles de Mapping** ventana.</span><span class="sxs-lookup"><span data-stu-id="0321a-167">The table mappings are displayed in the **Mapping Details** window.</span></span>
4.  <span data-ttu-id="0321a-168">Expanda el **se asigna a &lt;nombre de la tabla&gt;**  nodo.</span><span class="sxs-lookup"><span data-stu-id="0321a-168">Expand the **Maps to &lt;Table Name&gt;** node.</span></span>
    <span data-ttu-id="0321a-169">Un **asignaciones de columnas** aparece del nodo.</span><span class="sxs-lookup"><span data-stu-id="0321a-169">A **Column Mappings** node appears.</span></span>
5.  <span data-ttu-id="0321a-170">Expanda el **asignaciones de columnas** nodo.</span><span class="sxs-lookup"><span data-stu-id="0321a-170">Expand the **Column Mappings** node.</span></span>
    <span data-ttu-id="0321a-171">Aparece una lista de todas las columnas de la tabla.</span><span class="sxs-lookup"><span data-stu-id="0321a-171">A list of all the columns in the table appears.</span></span> <span data-ttu-id="0321a-172">Las propiedades predeterminadas (si existe) para que se asignan las columnas se muestran bajo el **valor/propiedad** encabezado.</span><span class="sxs-lookup"><span data-stu-id="0321a-172">The default properties (if any) to which the columns map are listed under the **Value/Property** heading.</span></span>
6.  <span data-ttu-id="0321a-173">Seleccione la columna que desea asignar y, a continuación, haga clic en el correspondiente **valor/propiedad** campo.</span><span class="sxs-lookup"><span data-stu-id="0321a-173">Select the column you want to map, and then right-click the corresponding **Value/Property** field.</span></span>
    <span data-ttu-id="0321a-174">Se muestra una lista desplegable de todas las propiedades escalares.</span><span class="sxs-lookup"><span data-stu-id="0321a-174">A drop-down list of all the scalar properties is displayed.</span></span>
7.  <span data-ttu-id="0321a-175">Seleccione la propiedad apropiada.</span><span class="sxs-lookup"><span data-stu-id="0321a-175">Select the appropriate property.</span></span>

    ![MapComplexType](~/ef6/media/mapcomplextype.png)

8.  <span data-ttu-id="0321a-177">Repita los pasos 6 y 7 para cada columna de tabla.</span><span class="sxs-lookup"><span data-stu-id="0321a-177">Repeat steps 6 and 7 for each table column.</span></span>

>[!NOTE]
> <span data-ttu-id="0321a-178">Para eliminar una asignación de columna, seleccione la columna que desea asignar y, a continuación, haga clic en el **valor/propiedad** campo.</span><span class="sxs-lookup"><span data-stu-id="0321a-178">To delete a column mapping, select the column that you want to map, and then click the **Value/Property** field.</span></span> <span data-ttu-id="0321a-179">A continuación, seleccione **eliminar** en la lista desplegable.</span><span class="sxs-lookup"><span data-stu-id="0321a-179">Then, select **Delete** from the drop-down list.</span></span>

## <a name="map-a-function-import-to-a-complex-type"></a><span data-ttu-id="0321a-180">Asignar una importación de función a un tipo complejo</span><span class="sxs-lookup"><span data-stu-id="0321a-180">Map a Function Import to a Complex Type</span></span>

<span data-ttu-id="0321a-181">Las importaciones de función se basan en procedimientos almacenados.</span><span class="sxs-lookup"><span data-stu-id="0321a-181">Function imports are based on stored procedures.</span></span> <span data-ttu-id="0321a-182">Para asignar una importación de función a un tipo complejo, las columnas devueltas por el procedimiento almacenado correspondiente deben coincidir con el número de propiedades del tipo complejo y deben tener tipos de almacenamiento compatibles con los tipos de propiedad.</span><span class="sxs-lookup"><span data-stu-id="0321a-182">To map a function import to a complex type, the columns returned by the corresponding stored procedure must match the properties of the complex type in number and must have storage types that are compatible with the property types.</span></span>

-   <span data-ttu-id="0321a-183">Haga doble clic en una función importada que desea asignar a un tipo complejo.</span><span class="sxs-lookup"><span data-stu-id="0321a-183">Double-click on an imported function that you want to map to a complex type.</span></span>

    ![FunctionImports](~/ef6/media/functionimports.png)

-   <span data-ttu-id="0321a-185">Rellene los valores para la nueva importación de función de la siguiente forma:</span><span class="sxs-lookup"><span data-stu-id="0321a-185">Fill in the settings for the new function import, as follows:</span></span>
    -   <span data-ttu-id="0321a-186">Especifique el procedimiento almacenado para el que está creando una importación de función en el **nombre de procedimiento almacenado** campo.</span><span class="sxs-lookup"><span data-stu-id="0321a-186">Specify the stored procedure for which you are creating a function import in the **Stored Procedure Name** field.</span></span> <span data-ttu-id="0321a-187">El campo es una lista desplegable que muestra todos los procedimientos almacenados del modelo de almacenamiento.</span><span class="sxs-lookup"><span data-stu-id="0321a-187">This field is a drop-down list that displays all the stored procedures in the storage model.</span></span>
    -   <span data-ttu-id="0321a-188">Especifique el nombre de la importación de función en el **nombre de Function Import** campo.</span><span class="sxs-lookup"><span data-stu-id="0321a-188">Specify the name of the function import in the **Function Import Name** field.</span></span>
    -   <span data-ttu-id="0321a-189">Seleccione **complejos** como el valor devuelto escriba y, a continuación, especifique el tipo devuelto complejo específico eligiendo el tipo adecuado de la lista desplegable.</span><span class="sxs-lookup"><span data-stu-id="0321a-189">Select **Complex** as the return type and then specify the specific complex return type by choosing the appropriate type from the drop-down list.</span></span>

        ![EditFunctionImport](~/ef6/media/editfunctionimport.png)

-   <span data-ttu-id="0321a-191">Haga clic en **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="0321a-191">Click **OK**.</span></span>
    <span data-ttu-id="0321a-192">Se crea la entrada de importación de función en el modelo conceptual.</span><span class="sxs-lookup"><span data-stu-id="0321a-192">The function import entry is created in the conceptual model.</span></span>

### <a name="customize-column-mapping-for-function-import"></a><span data-ttu-id="0321a-193">Personalizar para la importación de función de la asignación de columnas</span><span class="sxs-lookup"><span data-stu-id="0321a-193">Customize Column Mapping for Function Import</span></span>

-   <span data-ttu-id="0321a-194">Haga clic en la importación de función en el Explorador de modelos y seleccione **asignación de importación de función**.</span><span class="sxs-lookup"><span data-stu-id="0321a-194">Right-click the function import in the Model Browser and select **Function Import Mapping**.</span></span>
    <span data-ttu-id="0321a-195">El **detalles de Mapping** ventana aparece y muestra la asignación predeterminada para la importación de función.</span><span class="sxs-lookup"><span data-stu-id="0321a-195">The **Mapping Details** window appears and shows the default mapping for the function import.</span></span> <span data-ttu-id="0321a-196">Las flechas indican las asignaciones entre los valores de columna y de propiedad.</span><span class="sxs-lookup"><span data-stu-id="0321a-196">Arrows indicate the mappings between column values and property values.</span></span> <span data-ttu-id="0321a-197">De forma predeterminada, se presupone que los nombres de columna son los mismos que los nombres de propiedad del tipo complejo.</span><span class="sxs-lookup"><span data-stu-id="0321a-197">By default, the column names are assumed to be the same as the complex type's property names.</span></span> <span data-ttu-id="0321a-198">Los nombres de columna predeterminados aparecen en texto gris.</span><span class="sxs-lookup"><span data-stu-id="0321a-198">The default column names appear in gray text.</span></span>
-   <span data-ttu-id="0321a-199">Si es necesario, cambie los nombres de columna para que coincidan con los nombres de columna devueltos por el procedimiento almacenado que corresponden a la importación de función.</span><span class="sxs-lookup"><span data-stu-id="0321a-199">If necessary, change the column names to match the column names that are returned by the stored procedure that corresponds to the function import.</span></span>

## <a name="delete-a-complex-type"></a><span data-ttu-id="0321a-200">Eliminar un tipo complejo</span><span class="sxs-lookup"><span data-stu-id="0321a-200">Delete a Complex Type</span></span>

<span data-ttu-id="0321a-201">Al eliminar un tipo complejo, se elimina el tipo del modelo conceptual, así como las asignaciones para todas las instancias del tipo.</span><span class="sxs-lookup"><span data-stu-id="0321a-201">When you delete a complex type, the type is deleted from the conceptual model and mappings for all instances of the type are deleted.</span></span> <span data-ttu-id="0321a-202">Sin embargo, no se actualizan las referencias al tipo.</span><span class="sxs-lookup"><span data-stu-id="0321a-202">However, references to the type are not updated.</span></span> <span data-ttu-id="0321a-203">Por ejemplo, si una entidad tiene una propiedad de tipo complejo de tipo ComplexType1 y ComplexType1 se elimina en el **Explorador de modelos**, no se actualiza la propiedad de entidad correspondiente.</span><span class="sxs-lookup"><span data-stu-id="0321a-203">For example, if an entity has a complex type property of type ComplexType1 and ComplexType1 is deleted in the **Model Browser**, the corresponding entity property is not updated.</span></span> <span data-ttu-id="0321a-204">El modelo no se validará porque contiene una entidad que hace referencia a un tipo complejo eliminado.</span><span class="sxs-lookup"><span data-stu-id="0321a-204">The model will not validate  because it contains an entity that references a deleted complex type.</span></span> <span data-ttu-id="0321a-205">Puede actualizar o eliminar referencias a los tipos complejos eliminados utilizando Entity Designer.</span><span class="sxs-lookup"><span data-stu-id="0321a-205">You can update or delete references to deleted complex types by using the Entity Designer.</span></span>

-   <span data-ttu-id="0321a-206">Haga clic en un tipo complejo en el Explorador de modelos y seleccione **eliminar**.</span><span class="sxs-lookup"><span data-stu-id="0321a-206">Right-click a complex type in the Model Browser and select **Delete**.</span></span>

- <span data-ttu-id="0321a-207">OR:</span><span class="sxs-lookup"><span data-stu-id="0321a-207">OR -</span></span>

-   <span data-ttu-id="0321a-208">Seleccione un tipo complejo en el Explorador de modelos y presione la tecla Suprimir del teclado.</span><span class="sxs-lookup"><span data-stu-id="0321a-208">Select a complex type in the Model Browser and press the Delete key on your keyboard.</span></span>

## <a name="query-for-entities-containing-properties-of-complex-type"></a><span data-ttu-id="0321a-209">Consulta de entidades que contiene las propiedades de tipo complejo</span><span class="sxs-lookup"><span data-stu-id="0321a-209">Query for Entities Containing Properties of Complex Type</span></span>

<span data-ttu-id="0321a-210">El código siguiente muestra cómo ejecutar una consulta que devuelve una colección de entidad en objetos de tipo que contienen una propiedad de tipo complejo.</span><span class="sxs-lookup"><span data-stu-id="0321a-210">The following code shows how to execute a query that returns a collection of entity type objects that contain a complex type property.</span></span>

``` csharp
    using (SchoolEntities context = new SchoolEntities())
    {
        var courses =
            from c in context.OnsiteCourses
            order by c.Details.Time
            select c;

        foreach (var c in courses)
        {
            Console.WriteLine("Time: " + c.Details.Time);
            Console.WriteLine("Days: " + c.Details.Days);
            Console.WriteLine("Location: " + c.Details.Location);
        }
    }
```