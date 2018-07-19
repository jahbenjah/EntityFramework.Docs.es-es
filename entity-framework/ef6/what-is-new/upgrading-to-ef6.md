---
title: Actualizar a Entity Framework 6
author: divega
ms.date: 2016-10-23
ms.prod: entity-framework
ms.author: divega
ms.manager: avickers
ms.technology: entity-framework-6
ms.topic: article
ms.assetid: 29958ae5-85d3-4585-9ba6-550b8ec9393a
caps.latest.revision: 3
ms.openlocfilehash: d22f0d686e6b8e3922d37245386af7723bf08ba1
ms.sourcegitcommit: bdd06c9a591ba5e6d6a3ec046c80de98f598f3f3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/10/2018
ms.locfileid: "39122785"
---
# <a name="upgrading-to-entity-framework-6"></a><span data-ttu-id="60b3a-102">Actualizar a Entity Framework 6</span><span class="sxs-lookup"><span data-stu-id="60b3a-102">Upgrading to Entity Framework 6</span></span>

<span data-ttu-id="60b3a-103">En versiones anteriores de EF se dividió el código entre las bibliotecas de core (principalmente System.Data.Entity.dll) se incluye como parte de .NET Framework y las bibliotecas de (OOB) de fuera de banda (principalmente EntityFramework.dll) se incluye en un paquete de NuGet.</span><span class="sxs-lookup"><span data-stu-id="60b3a-103">In previous versions of EF the code was split between core libraries (primarily System.Data.Entity.dll) shipped as part of the .NET Framework and out-of-band (OOB) libraries (primarily EntityFramework.dll) shipped in a NuGet package.</span></span> <span data-ttu-id="60b3a-104">EF6 toma el código de las bibliotecas de core y se incorpora a las bibliotecas OOB.</span><span class="sxs-lookup"><span data-stu-id="60b3a-104">EF6 takes the code from the core libraries and incorporates it into the OOB libraries.</span></span> <span data-ttu-id="60b3a-105">Esto ha sido necesario para permitir que EF realizarse de código abierto y para que sea capaz de evolucionar a un ritmo diferente de .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="60b3a-105">This was necessary in order to allow EF to be made open source and for it to be able to evolve at a different pace from .NET Framework.</span></span> <span data-ttu-id="60b3a-106">La consecuencia de esto es que las aplicaciones deben volver a generarse con los tipos que se ha movido.</span><span class="sxs-lookup"><span data-stu-id="60b3a-106">The consequence of this is that applications will need to be rebuilt against the moved types.</span></span>

<span data-ttu-id="60b3a-107">Esto debe ser sencilla para las aplicaciones que hacen uso de DbContext como enviadas en EF 4.1 y versiones posteriores.</span><span class="sxs-lookup"><span data-stu-id="60b3a-107">This should be straightforward for applications that make use of DbContext as shipped in EF 4.1 and later.</span></span> <span data-ttu-id="60b3a-108">Se necesita para las aplicaciones que hacen uso de ObjectContext un poco más trabajo, pero todavía no es difícil de hacer.</span><span class="sxs-lookup"><span data-stu-id="60b3a-108">A little more work is required for applications that make use of ObjectContext but it still isn’t hard to do.</span></span>

<span data-ttu-id="60b3a-109">Le presentamos una lista de comprobación de las cosas que debe hacer para actualizar una aplicación existente a EF6.</span><span class="sxs-lookup"><span data-stu-id="60b3a-109">Here is a checklist of the things you need to do to upgrade an existing application to EF6.</span></span>

## <a name="1-install-the-ef6-nuget-package"></a><span data-ttu-id="60b3a-110">1. Instale el paquete NuGet de EF6</span><span class="sxs-lookup"><span data-stu-id="60b3a-110">1. Install the EF6 NuGet package</span></span>

<span data-ttu-id="60b3a-111">Deberá actualizar a la nueva Entity Framework 6 en tiempo de ejecución.</span><span class="sxs-lookup"><span data-stu-id="60b3a-111">You need to upgrade to the new Entity Framework 6 runtime.</span></span>

1. <span data-ttu-id="60b3a-112">Haga doble clic en el proyecto y seleccione **administrar paquetes NuGet...**</span><span class="sxs-lookup"><span data-stu-id="60b3a-112">Right-click on your project and select **Manage NuGet Packages...**</span></span>  
2. <span data-ttu-id="60b3a-113">En el **Online** ficha, seleccione **EntityFramework** y haga clic en **instalar**</span><span class="sxs-lookup"><span data-stu-id="60b3a-113">Under the **Online** tab select **EntityFramework** and click **Install**</span></span>  
   > [!NOTE]
   > <span data-ttu-id="60b3a-114">Si una versión anterior de EntityFramework NuGet se instaló el paquete se actualizará a EF6.</span><span class="sxs-lookup"><span data-stu-id="60b3a-114">If a previous version of the EntityFramework NuGet package was installed this will upgrade it to EF6.</span></span>

<span data-ttu-id="60b3a-115">Como alternativa, puede ejecutar el siguiente comando desde la consola de administrador de paquetes:</span><span class="sxs-lookup"><span data-stu-id="60b3a-115">Alternatively, you can run the following command from Package Manager Console:</span></span>

``` powershell
Install-Package EntityFramework
```

## <a name="2-ensure-that-assembly-references-to-systemdataentitydll-are-removed"></a><span data-ttu-id="60b3a-116">2. Asegúrese de que se han quitado las referencias de ensamblado a System.Data.Entity.dll</span><span class="sxs-lookup"><span data-stu-id="60b3a-116">2. Ensure that assembly references to System.Data.Entity.dll are removed</span></span>

<span data-ttu-id="60b3a-117">Instalar el paquete NuGet de EF6 automáticamente debe quitar las referencias a System.Data.Entity desde el proyecto para usted.</span><span class="sxs-lookup"><span data-stu-id="60b3a-117">Installing the EF6 NuGet package should automatically remove any references to System.Data.Entity from your project for you.</span></span>

## <a name="3-swap-any-ef-designer-edmx-models-to-use-ef-6x-code-generation"></a><span data-ttu-id="60b3a-118">3. Intercambiar los modelos de EF Designer (EDMX) para usar la generación de código EF 6.x</span><span class="sxs-lookup"><span data-stu-id="60b3a-118">3. Swap any EF Designer (EDMX) models to use EF 6.x code generation</span></span>

<span data-ttu-id="60b3a-119">Si tiene cualquier modelo creado con el Diseñador de EF, deberá actualizar las plantillas de generación de código para generar código compatible con EF6.</span><span class="sxs-lookup"><span data-stu-id="60b3a-119">If you have any models created with the EF Designer, you will need to update the code generation templates to generate EF6 compatible code.</span></span>

> [!NOTE]
> <span data-ttu-id="60b3a-120">Actualmente hay disponibles para Visual Studio 2012 y 2013 solo plantillas de generador de DbContext EF 6.x.</span><span class="sxs-lookup"><span data-stu-id="60b3a-120">There are currently only EF 6.x DbContext Generator templates available for Visual Studio 2012 and 2013.</span></span>

1. <span data-ttu-id="60b3a-121">Eliminar plantillas de generación de código existentes.</span><span class="sxs-lookup"><span data-stu-id="60b3a-121">Delete existing code-generation templates.</span></span> <span data-ttu-id="60b3a-122">Estos archivos se suele denominar  **\<edmx_file_name\>.tt** y  **\<edmx_file_name\>. Context.tt** y estar anidado en el archivo edmx en el Explorador de soluciones.</span><span class="sxs-lookup"><span data-stu-id="60b3a-122">These files will typically be named **\<edmx_file_name\>.tt** and **\<edmx_file_name\>.Context.tt** and be nested under your edmx file in Solution Explorer.</span></span> <span data-ttu-id="60b3a-123">Puede seleccionar las plantillas en el Explorador de soluciones y presione la **SUPR** clave para eliminarlos.</span><span class="sxs-lookup"><span data-stu-id="60b3a-123">You can select the templates in Solution Explorer and press the **Del** key to delete them.</span></span>  
   > [!NOTE]
   > <span data-ttu-id="60b3a-124">En proyectos de sitios Web las plantillas no se anidado en el archivo edmx, pero aparece junto a ella en el Explorador de soluciones.</span><span class="sxs-lookup"><span data-stu-id="60b3a-124">In Web Site projects the templates will not be nested under your edmx file, but listed alongside it in Solution Explorer.</span></span>  

   > [!NOTE]
   > <span data-ttu-id="60b3a-125">En los proyectos de VB.NET deberá habilitar "Mostrar todos los archivos' poder ver los archivos de plantilla anidada.</span><span class="sxs-lookup"><span data-stu-id="60b3a-125">In VB.NET projects you will need to enable 'Show All Files' to be able to see the nested template files.</span></span>
2. <span data-ttu-id="60b3a-126">Agregue la plantilla de generación de código de adecuada EF 6.x.</span><span class="sxs-lookup"><span data-stu-id="60b3a-126">Add the appropriate EF 6.x code generation template.</span></span> <span data-ttu-id="60b3a-127">Abra el modelo de EF Designer, haga doble clic en la superficie de diseño y seleccione **Add Code Generation Item...**</span><span class="sxs-lookup"><span data-stu-id="60b3a-127">Open your model in the EF Designer, right-click on the design surface and select **Add Code Generation Item...**</span></span>
    - <span data-ttu-id="60b3a-128">Si está usando la API de DbContext (recomendado), a continuación, **EF 6.x generador de DbContext** estará disponible en el **datos** ficha.</span><span class="sxs-lookup"><span data-stu-id="60b3a-128">If you are using the DbContext API (recommended) then **EF 6.x DbContext Generator** will be available under the **Data** tab.</span></span>  
      > [!NOTE]
      > <span data-ttu-id="60b3a-129">Si utiliza Visual Studio 2012, deberá instalar las herramientas de EF 6 para que esta plantilla.</span><span class="sxs-lookup"><span data-stu-id="60b3a-129">If you are using Visual Studio 2012, you will need to install the EF 6 Tools to have this template.</span></span> <span data-ttu-id="60b3a-130">Consulte [obtener Entity Framework](~/ef6/fundamentals/install.md) para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="60b3a-130">See [Get Entity Framework](~/ef6/fundamentals/install.md) for details.</span></span>  

    - <span data-ttu-id="60b3a-131">Si está utilizando la API de ObjectContext, a continuación, deberá seleccionar la **Online** pestaña y busque **EF 6.x generador de EntityObject**.</span><span class="sxs-lookup"><span data-stu-id="60b3a-131">If you are using the ObjectContext API then you will need to select the **Online** tab and search for **EF 6.x EntityObject Generator**.</span></span>  
3. <span data-ttu-id="60b3a-132">Si aplica las personalizaciones a las plantillas de generación de código que deberá volver a aplicarlos a las plantillas actualizadas.</span><span class="sxs-lookup"><span data-stu-id="60b3a-132">If you applied any customizations to the code generation templates you will need to re-apply them to the updated templates.</span></span>

## <a name="4-update-namespaces-for-any-core-ef-types-being-used"></a><span data-ttu-id="60b3a-133">4. Actualice los espacios de nombres para cualquier tipo EF core que se va a usar</span><span class="sxs-lookup"><span data-stu-id="60b3a-133">4. Update namespaces for any core EF types being used</span></span>

<span data-ttu-id="60b3a-134">No han cambiado los espacios de nombres para tipos de DbContext y Code First.</span><span class="sxs-lookup"><span data-stu-id="60b3a-134">The namespaces for DbContext and Code First types have not changed.</span></span> <span data-ttu-id="60b3a-135">Esto significa que para muchas aplicaciones que usan EF 4.1 o posterior no debe cambiar nada.</span><span class="sxs-lookup"><span data-stu-id="60b3a-135">This means for many applications that use EF 4.1 or later you will not need to change anything.</span></span>

<span data-ttu-id="60b3a-136">Tipos como ObjectContext que anteriormente se encontraban en System.Data.Entity.dll se movieron a nuevos espacios de nombres.</span><span class="sxs-lookup"><span data-stu-id="60b3a-136">Types like ObjectContext that were previously in System.Data.Entity.dll have been moved to new namespaces.</span></span> <span data-ttu-id="60b3a-137">Esto significa que deba actualizar su *mediante* o *importación* directivas para la compilación EF6.</span><span class="sxs-lookup"><span data-stu-id="60b3a-137">This means you may need to update your *using* or *Import* directives to build against EF6.</span></span>

<span data-ttu-id="60b3a-138">La regla general para los cambios del espacio de nombres es que cualquier tipo de System.Data.* se mueve al System.Data.Entity.Core.*.</span><span class="sxs-lookup"><span data-stu-id="60b3a-138">The general rule for namespace changes is that any type in System.Data.* is moved to System.Data.Entity.Core.*.</span></span> <span data-ttu-id="60b3a-139">En otras palabras, sólo tiene que insertar **Entity.Core.**</span><span class="sxs-lookup"><span data-stu-id="60b3a-139">In other words, just insert **Entity.Core.**</span></span> <span data-ttu-id="60b3a-140">Después de System.Data.</span><span class="sxs-lookup"><span data-stu-id="60b3a-140">after System.Data.</span></span> <span data-ttu-id="60b3a-141">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="60b3a-141">For example:</span></span>

- <span data-ttu-id="60b3a-142">System.Data.EntityException = > System.Data. **Entity.Core.** EntityException</span><span class="sxs-lookup"><span data-stu-id="60b3a-142">System.Data.EntityException => System.Data.**Entity.Core.** EntityException</span></span>  
- <span data-ttu-id="60b3a-143">System.Data.Objects.ObjectContext = > System.Data. **Entity.Core.** Objects.ObjectContext</span><span class="sxs-lookup"><span data-stu-id="60b3a-143">System.Data.Objects.ObjectContext => System.Data.**Entity.Core.** Objects.ObjectContext</span></span>  
- <span data-ttu-id="60b3a-144">System.Data.Objects.DataClasses.RelationshipManager = > System.Data. **Entity.Core.** Objects.DataClasses.RelationshipManager</span><span class="sxs-lookup"><span data-stu-id="60b3a-144">System.Data.Objects.DataClasses.RelationshipManager => System.Data.**Entity.Core.** Objects.DataClasses.RelationshipManager</span></span>  

<span data-ttu-id="60b3a-145">Estos tipos están en el *Core* espacios de nombres porque no se usan directamente para la mayoría de las aplicaciones basadas en DbContext.</span><span class="sxs-lookup"><span data-stu-id="60b3a-145">These types are in the *Core* namespaces because they are not used directly for most DbContext-based applications.</span></span> <span data-ttu-id="60b3a-146">Algunos tipos que formaban parte de System.Data.Entity.dll todavía se utilizan con frecuencia y directamente para las aplicaciones basadas en DbContext y lo que no se han movido los *Core* espacios de nombres.</span><span class="sxs-lookup"><span data-stu-id="60b3a-146">Some types that were part of System.Data.Entity.dll are still used commonly and directly for DbContext-based applications and so have not been moved into the *Core* namespaces.</span></span> <span data-ttu-id="60b3a-147">Estos son:</span><span class="sxs-lookup"><span data-stu-id="60b3a-147">These are:</span></span>

- <span data-ttu-id="60b3a-148">System.Data.EntityState = > System.Data. **Entidad.** EntityState</span><span class="sxs-lookup"><span data-stu-id="60b3a-148">System.Data.EntityState => System.Data.**Entity.** EntityState</span></span>  
- <span data-ttu-id="60b3a-149">System.Data.Objects.DataClasses.EdmFunctionAttribute = > System.Data. **Entity.DbFunctionAttribute**</span><span class="sxs-lookup"><span data-stu-id="60b3a-149">System.Data.Objects.DataClasses.EdmFunctionAttribute => System.Data.**Entity.DbFunctionAttribute**</span></span>  
  > [!NOTE]
  > <span data-ttu-id="60b3a-150">Esta clase ha cambiado de nombre; una clase con el nombre antiguo todavía existe y funciona, pero ahora marcado como obsoleto.</span><span class="sxs-lookup"><span data-stu-id="60b3a-150">This class has been renamed; a class with the old name still exists and works, but it now marked as obsolete.</span></span>  
- <span data-ttu-id="60b3a-151">System.Data.Objects.EntityFunctions = > System.Data. **Entity.DbFunctions**</span><span class="sxs-lookup"><span data-stu-id="60b3a-151">System.Data.Objects.EntityFunctions => System.Data.**Entity.DbFunctions**</span></span>  
  > [!NOTE]
  > <span data-ttu-id="60b3a-152">Esta clase ha cambiado de nombre; una clase con el nombre antiguo todavía existe y funciona, pero ahora marcado como obsoleto.)</span><span class="sxs-lookup"><span data-stu-id="60b3a-152">This class has been renamed; a class with the old name still exists and works, but it now marked as obsolete.)</span></span>  
- <span data-ttu-id="60b3a-153">Clases espaciales (por ejemplo, DbGeography, DbGeometry) han movido desde System.Data.Spatial = > System.Data. **Entidad.** Espacial</span><span class="sxs-lookup"><span data-stu-id="60b3a-153">Spatial classes (for example, DbGeography, DbGeometry) have moved from System.Data.Spatial => System.Data.**Entity.** Spatial</span></span>

> [!NOTE]
> <span data-ttu-id="60b3a-154">Algunos tipos en el espacio de nombres System.Data se encuentran en System.Data.dll, que no es un ensamblado EF.</span><span class="sxs-lookup"><span data-stu-id="60b3a-154">Some types in the System.Data namespace are in System.Data.dll which is not an EF assembly.</span></span> <span data-ttu-id="60b3a-155">Estos tipos no se han movido y por lo que sus espacios de nombres permanecen sin cambios.</span><span class="sxs-lookup"><span data-stu-id="60b3a-155">These types have not moved and so their namespaces remain unchanged.</span></span>