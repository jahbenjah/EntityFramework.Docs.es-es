---
title: Datos espaciales - EF Core
author: bricelam
ms.author: bricelam
ms.date: 11/01/2018
ms.assetid: 2BDE29FC-4161-41A0-841E-69F51CCD9341
uid: core/modeling/spatial
ms.openlocfilehash: cf488c6b7d94ca19018efe1c23ff410fe7eb594b
ms.sourcegitcommit: 81c53ac43d8f15b900f117294ec71dc49fe028fa
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/16/2018
ms.locfileid: "51817915"
---
# <a name="spatial-data"></a><span data-ttu-id="e6f9d-102">Datos espaciales</span><span class="sxs-lookup"><span data-stu-id="e6f9d-102">Spatial Data</span></span>

> [!NOTE]
> <span data-ttu-id="e6f9d-103">Esta característica es nueva en EF Core 2.2.</span><span class="sxs-lookup"><span data-stu-id="e6f9d-103">This feature is new in EF Core 2.2.</span></span>

<span data-ttu-id="e6f9d-104">Los datos espaciales representan la ubicación física y la forma de objetos.</span><span class="sxs-lookup"><span data-stu-id="e6f9d-104">Spatial data represents the physical location and the shape of objects.</span></span> <span data-ttu-id="e6f9d-105">Muchas bases de datos proporcionan compatibilidad para este tipo de datos, por lo que se pueden indexar y consultar junto con otros datos.</span><span class="sxs-lookup"><span data-stu-id="e6f9d-105">Many databases provide support for this type of data so it can be indexed and queried alongside other data.</span></span> <span data-ttu-id="e6f9d-106">Escenarios habituales incluyen consultar los objetos dentro de una distancia especificada desde una ubicación, o seleccione el objeto cuyo borde contiene una ubicación determinada.</span><span class="sxs-lookup"><span data-stu-id="e6f9d-106">Common scenarios include querying for objects within a given distance from a location, or selecting the object whose border contains a given location.</span></span> <span data-ttu-id="e6f9d-107">EF Core admite la asignación a tipos de datos espaciales mediante la [NetTopologySuite](https://github.com/NetTopologySuite/NetTopologySuite) biblioteca espacial.</span><span class="sxs-lookup"><span data-stu-id="e6f9d-107">EF Core supports mapping to spatial data types using the [NetTopologySuite](https://github.com/NetTopologySuite/NetTopologySuite) spatial library.</span></span>

## <a name="installing"></a><span data-ttu-id="e6f9d-108">Instalación</span><span class="sxs-lookup"><span data-stu-id="e6f9d-108">Installing</span></span>

<span data-ttu-id="e6f9d-109">Para usar datos espaciales con EF Core, deberá instalar el paquete NuGet auxiliar adecuado.</span><span class="sxs-lookup"><span data-stu-id="e6f9d-109">In order to use spatial data with EF Core, you need to install the appropriate supporting NuGet package.</span></span> <span data-ttu-id="e6f9d-110">Qué paquetes necesita instalar depende del proveedor que está utilizando.</span><span class="sxs-lookup"><span data-stu-id="e6f9d-110">Which package you need to install depends on the provider you're using.</span></span>

<span data-ttu-id="e6f9d-111">Proveedor de EF Core</span><span class="sxs-lookup"><span data-stu-id="e6f9d-111">EF Core Provider</span></span>                        | <span data-ttu-id="e6f9d-112">Paquete de NuGet espacial</span><span class="sxs-lookup"><span data-stu-id="e6f9d-112">Spatial NuGet Package</span></span>
--------------------------------------- | ---------------------
<span data-ttu-id="e6f9d-113">Microsoft.EntityFrameworkCore.SqlServer</span><span class="sxs-lookup"><span data-stu-id="e6f9d-113">Microsoft.EntityFrameworkCore.SqlServer</span></span> | [<span data-ttu-id="e6f9d-114">Microsoft.EntityFrameworkCore.SqlServer.NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="e6f9d-114">Microsoft.EntityFrameworkCore.SqlServer.NetTopologySuite</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer.NetTopologySuite)
<span data-ttu-id="e6f9d-115">Microsoft.EntityFrameworkCore.Sqlite</span><span class="sxs-lookup"><span data-stu-id="e6f9d-115">Microsoft.EntityFrameworkCore.Sqlite</span></span>    | [<span data-ttu-id="e6f9d-116">Microsoft.EntityFrameworkCore.Sqlite.NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="e6f9d-116">Microsoft.EntityFrameworkCore.Sqlite.NetTopologySuite</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite.NetTopologySuite)
<span data-ttu-id="e6f9d-117">Microsoft.EntityFrameworkCore.InMemory</span><span class="sxs-lookup"><span data-stu-id="e6f9d-117">Microsoft.EntityFrameworkCore.InMemory</span></span>  | [<span data-ttu-id="e6f9d-118">NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="e6f9d-118">NetTopologySuite</span></span>](https://www.nuget.org/packages/NetTopologySuite)
<span data-ttu-id="e6f9d-119">Npgsql.EntityFrameworkCore.PostgreSQL</span><span class="sxs-lookup"><span data-stu-id="e6f9d-119">Npgsql.EntityFrameworkCore.PostgreSQL</span></span>   | [<span data-ttu-id="e6f9d-120">Npgsql.EntityFrameworkCore.PostgreSQL.NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="e6f9d-120">Npgsql.EntityFrameworkCore.PostgreSQL.NetTopologySuite</span></span>](https://www.nuget.org/packages/Npgsql.EntityFrameworkCore.PostgreSQL.NetTopologySuite)

## <a name="reverse-engineering"></a><span data-ttu-id="e6f9d-121">Ingeniería inversa</span><span class="sxs-lookup"><span data-stu-id="e6f9d-121">Reverse engineering</span></span>

<span data-ttu-id="e6f9d-122">Paquetes de NuGet espacial también habilitar [ingeniería inversa](../managing-schemas/scaffolding.md) modelos con propiedades espaciales, pero se deben instalar el paquete ***antes*** ejecutando `Scaffold-DbContext` o `dotnet ef dbcontext scaffold`.</span><span class="sxs-lookup"><span data-stu-id="e6f9d-122">The spatial NuGet packages also enable [reverse engineering](../managing-schemas/scaffolding.md) models with spatial properties, but you need to install the package ***before*** running `Scaffold-DbContext` or `dotnet ef dbcontext scaffold`.</span></span> <span data-ttu-id="e6f9d-123">Si no lo hace, recibirá advertencias acerca de cómo no buscar las asignaciones de tipos para las columnas y las columnas se omitirán.</span><span class="sxs-lookup"><span data-stu-id="e6f9d-123">If you don't, you'll receive warnings about not finding type mappings for the columns and the columns will be skipped.</span></span>

## <a name="nettopologysuite-nts"></a><span data-ttu-id="e6f9d-124">NetTopologySuite (NTS)</span><span class="sxs-lookup"><span data-stu-id="e6f9d-124">NetTopologySuite (NTS)</span></span>

<span data-ttu-id="e6f9d-125">NetTopologySuite es una biblioteca espacial para. NET.</span><span class="sxs-lookup"><span data-stu-id="e6f9d-125">NetTopologySuite is a spatial library for .NET.</span></span> <span data-ttu-id="e6f9d-126">EF Core permite la asignación de datos espaciales tipos en la base de datos mediante el uso de tipos de interrupción en el modelo.</span><span class="sxs-lookup"><span data-stu-id="e6f9d-126">EF Core enables mapping to spatial data types in the database by using NTS types in your model.</span></span>

<span data-ttu-id="e6f9d-127">Para habilitar la asignación de tipos espaciales a través de la interrupción, llame al método UseNetTopologySuite en el generador de opciones de DbContext del proveedor.</span><span class="sxs-lookup"><span data-stu-id="e6f9d-127">To enable mapping to spatial types via NTS, call the UseNetTopologySuite method on the provider's DbContext options builder.</span></span> <span data-ttu-id="e6f9d-128">Por ejemplo, con SQL Server podría llamarlo similar al siguiente.</span><span class="sxs-lookup"><span data-stu-id="e6f9d-128">For example, with SQL Server you'd call it like this.</span></span>

``` csharp
optionsBuilder.UseSqlServer(
    @"Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=WideWorldImporters",
    x => x.UseNetTopologySuite());
```

<span data-ttu-id="e6f9d-129">Hay varios tipos de datos espaciales.</span><span class="sxs-lookup"><span data-stu-id="e6f9d-129">There are several spatial data types.</span></span> <span data-ttu-id="e6f9d-130">Qué tipo se utiliza depende de los tipos de formas que desee permitir.</span><span class="sxs-lookup"><span data-stu-id="e6f9d-130">Which type you use depends on the types of shapes you want to allow.</span></span> <span data-ttu-id="e6f9d-131">Aquí es la jerarquía de tipos de interrupción que puede usar para las propiedades del modelo.</span><span class="sxs-lookup"><span data-stu-id="e6f9d-131">Here is the hierarchy of NTS types that you can use for properties in your model.</span></span> <span data-ttu-id="e6f9d-132">Que se encuentren dentro de la `NetTopologySuite.Geometries` espacio de nombres.</span><span class="sxs-lookup"><span data-stu-id="e6f9d-132">They're located within the `NetTopologySuite.Geometries` namespace.</span></span> <span data-ttu-id="e6f9d-133">Las interfaces correspondientes en el paquete GeoAPI (`GeoAPI.Geometries` espacio de nombres) también se puede usar.</span><span class="sxs-lookup"><span data-stu-id="e6f9d-133">Corresponding interfaces in the GeoAPI package (`GeoAPI.Geometries` namespace) can also be used.</span></span>

* <span data-ttu-id="e6f9d-134">geometría</span><span class="sxs-lookup"><span data-stu-id="e6f9d-134">Geometry</span></span>
  * <span data-ttu-id="e6f9d-135">Punto</span><span class="sxs-lookup"><span data-stu-id="e6f9d-135">Point</span></span>
  * <span data-ttu-id="e6f9d-136">LineString</span><span class="sxs-lookup"><span data-stu-id="e6f9d-136">LineString</span></span>
  * <span data-ttu-id="e6f9d-137">Polígono</span><span class="sxs-lookup"><span data-stu-id="e6f9d-137">Polygon</span></span>
  * <span data-ttu-id="e6f9d-138">GeometryCollection</span><span class="sxs-lookup"><span data-stu-id="e6f9d-138">GeometryCollection</span></span>
    * <span data-ttu-id="e6f9d-139">MultiPoint</span><span class="sxs-lookup"><span data-stu-id="e6f9d-139">MultiPoint</span></span>
    * <span data-ttu-id="e6f9d-140">MultiLineString</span><span class="sxs-lookup"><span data-stu-id="e6f9d-140">MultiLineString</span></span>
    * <span data-ttu-id="e6f9d-141">MultiPolygon</span><span class="sxs-lookup"><span data-stu-id="e6f9d-141">MultiPolygon</span></span>

> [!WARNING]
> <span data-ttu-id="e6f9d-142">CircularString, CompoundCurve y CurePolygon no son compatibles con interrupción.</span><span class="sxs-lookup"><span data-stu-id="e6f9d-142">CircularString, CompoundCurve, and CurePolygon aren't supported by NTS.</span></span>

<span data-ttu-id="e6f9d-143">El tipo de geometría base permite que cualquier tipo de forma que se especifique la propiedad.</span><span class="sxs-lookup"><span data-stu-id="e6f9d-143">Using the base Geometry type allows any type of shape to be specified by the property.</span></span>

<span data-ttu-id="e6f9d-144">Las clases de entidad siguiente podrían usarse para asignar a tablas en el [base de datos de ejemplo de Wide World Importers](http://go.microsoft.com/fwlink/?LinkID=800630).</span><span class="sxs-lookup"><span data-stu-id="e6f9d-144">The following entity classes could be used to map to tables in the [Wide World Importers sample database](http://go.microsoft.com/fwlink/?LinkID=800630).</span></span>

``` csharp
[Table("Cities", Schema = "Application"))]
class City
{
    public int CityID { get; set; }

    public string CityName { get; set; }

    public IPoint Location { get; set; }
}

[Table("Countries", Schema = "Application"))]
class Country
{
    public int CountryID { get; set; }

    public string CountryName { get; set; }

    // Database includes both Polygon and MultiPolygon values
    public IGeometry Border { get; set; }
}
```

### <a name="creating-values"></a><span data-ttu-id="e6f9d-145">Creación de valores</span><span class="sxs-lookup"><span data-stu-id="e6f9d-145">Creating values</span></span>

<span data-ttu-id="e6f9d-146">Puede usar constructores para crear objetos de geometría; Sin embargo, NTS recomienda usar una fábrica de geometría en su lugar.</span><span class="sxs-lookup"><span data-stu-id="e6f9d-146">You can use constructors to create geometry objects; however, NTS recommends using a geometry factory instead.</span></span> <span data-ttu-id="e6f9d-147">Esto le permite especificar un valor predeterminado SRID (el sistema de referencia espacial utilizado por las coordenadas) y le ofrece control sobre las cosas más avanzadas, como el modelo de precisión (que se usa durante los cálculos) y la secuencia de coordenadas (determina qué coordenadas--dimensiones y las medidas, están disponibles).</span><span class="sxs-lookup"><span data-stu-id="e6f9d-147">This lets you specify a default SRID (the spatial reference system used by the coordinates) and gives you control over more advanced things like the precision model (used during calculations) and the coordinate sequence (determines which ordinates--dimensions and measures--are available).</span></span>

``` csharp
var geometryFactory = NtsGeometryServices.Instance.CreateGeometryFactory(srid: 4326);
var currentLocation = geometryFactory.CreatePoint(-122.121512, 47.6739882);
```

> [!NOTE]
> <span data-ttu-id="e6f9d-148">4326 hace referencia a WGS 84, un estándar que se usa en GPS y otros sistemas geográficas.</span><span class="sxs-lookup"><span data-stu-id="e6f9d-148">4326 refers to WGS 84, a standard used in GPS and other geographic systems.</span></span>

### <a name="longitude-and-latitude"></a><span data-ttu-id="e6f9d-149">Longitud y latitud</span><span class="sxs-lookup"><span data-stu-id="e6f9d-149">Longitude and Latitude</span></span>

<span data-ttu-id="e6f9d-150">Coordenadas de NTS están en términos de valores X e Y.</span><span class="sxs-lookup"><span data-stu-id="e6f9d-150">Coordinates in NTS are in terms of X and Y values.</span></span> <span data-ttu-id="e6f9d-151">Para representar la longitud y latitud, utilice X para latitud e Y para latitude.</span><span class="sxs-lookup"><span data-stu-id="e6f9d-151">To represent longitude and latitude, use X for longitude and Y for latitude.</span></span> <span data-ttu-id="e6f9d-152">Tenga en cuenta que esto es **hacia atrás** desde el `latitude, longitude` formato en el que podría ver estos valores.</span><span class="sxs-lookup"><span data-stu-id="e6f9d-152">Note that this is **backwards** from the `latitude, longitude` format in which you typically see these values.</span></span>

### <a name="srid-ignored-during-client-operations"></a><span data-ttu-id="e6f9d-153">SRID omite durante las operaciones de cliente</span><span class="sxs-lookup"><span data-stu-id="e6f9d-153">SRID Ignored during client operations</span></span>

<span data-ttu-id="e6f9d-154">NTS omite los valores SRID durante las operaciones.</span><span class="sxs-lookup"><span data-stu-id="e6f9d-154">NTS ignores SRID values during operations.</span></span> <span data-ttu-id="e6f9d-155">Se supone que un sistema de coordenadas plano.</span><span class="sxs-lookup"><span data-stu-id="e6f9d-155">It assumes a planar coordinate system.</span></span> <span data-ttu-id="e6f9d-156">Esto significa que si especifica las coordenadas en términos de longitud y latitud, algunos valores evaluado como cliente, como distancia, longitud y área estarán en grados, no los medidores.</span><span class="sxs-lookup"><span data-stu-id="e6f9d-156">This means that if you specify coordinates in terms of longitude and latitude, some client-evaluated values like distance, length, and area will be in degrees, not meters.</span></span> <span data-ttu-id="e6f9d-157">Para los valores más significativos, primero necesita proyectar las coordenadas a otro sistema de coordenadas mediante una biblioteca como [ProjNet4GeoAPI](https://github.com/NetTopologySuite/ProjNet4GeoAPI) antes de calcular estos valores.</span><span class="sxs-lookup"><span data-stu-id="e6f9d-157">For more meaningful values, you first need to project the coordinates to another coordinate system using a library like [ProjNet4GeoAPI](https://github.com/NetTopologySuite/ProjNet4GeoAPI) before calculating these values.</span></span>

<span data-ttu-id="e6f9d-158">Si hay una operación evaluado como EF Core a través de SQL server, la unidad del resultado se determinará la base de datos.</span><span class="sxs-lookup"><span data-stu-id="e6f9d-158">If an operation is server-evaluated by EF Core via SQL, the result's unit will be determined by the database.</span></span>

<span data-ttu-id="e6f9d-159">Este es un ejemplo del uso de ProjNet4GeoAPI para calcular la distancia entre dos ciudades.</span><span class="sxs-lookup"><span data-stu-id="e6f9d-159">Here is an example of using ProjNet4GeoAPI to calculate the distance between two cities.</span></span>

``` csharp
static class GeometryExtensions
{
    static readonly IGeometryServices _geometryServices = NtsGeometryServices.Instance;
    static readonly ICoordinateSystemServices _coordinateSystemServices
        = new CoordinateSystemServices(
            new CoordinateSystemFactory(),
            new CoordinateTransformationFactory(),
            new Dictionary<int, string>
            {
                // Coordinate systems:

                // (3857 and 4326 included automatically)

                // This coordinate system covers the area of our data.
                // Different data requires a different coordinate system.
                [2855] =
                @"
                    PROJCS[""NAD83(HARN) / Washington North"",
                        GEOGCS[""NAD83(HARN)"",
                            DATUM[""NAD83_High_Accuracy_Regional_Network"",
                                SPHEROID[""GRS 1980"",6378137,298.257222101,
                                    AUTHORITY[""EPSG"",""7019""]],
                                AUTHORITY[""EPSG"",""6152""]],
                            PRIMEM[""Greenwich"",0,
                                AUTHORITY[""EPSG"",""8901""]],
                            UNIT[""degree"",0.01745329251994328,
                                AUTHORITY[""EPSG"",""9122""]],
                            AUTHORITY[""EPSG"",""4152""]],
                        PROJECTION[""Lambert_Conformal_Conic_2SP""],
                        PARAMETER[""standard_parallel_1"",48.73333333333333],
                        PARAMETER[""standard_parallel_2"",47.5],
                        PARAMETER[""latitude_of_origin"",47],
                        PARAMETER[""central_meridian"",-120.8333333333333],
                        PARAMETER[""false_easting"",500000],
                        PARAMETER[""false_northing"",0],
                        UNIT[""metre"",1,
                            AUTHORITY[""EPSG"",""9001""]],
                        AUTHORITY[""EPSG"",""2855""]]
                "
            });

    public static IGeometry ProjectTo(this IGeometry geometry, int srid)
    {
        var geometryFactory = _geometryServices.CreateGeometryFactory(srid);
        var transformation = _coordinateSystemServices.CreateTransformation(geometry.SRID, srid);

        return GeometryTransform.TransformGeometry(
            geometryFactory,
            geometry,
            transformation.MathTransform);
    }
}
```

``` csharp
var seattle = new Point(-122.333056, 47.609722) { SRID = 4326 };
var redmond = new Point(-122.123889, 47.669444) { SRID = 4326 };

var distance = seattle.ProjectTo(2855).Distance(redmond.ProjectTo(2855));
```

## <a name="querying-data"></a><span data-ttu-id="e6f9d-160">Consulta de datos</span><span class="sxs-lookup"><span data-stu-id="e6f9d-160">Querying Data</span></span>

<span data-ttu-id="e6f9d-161">En LINQ, los métodos de interrupción y las propiedades disponibles como funciones de base de datos se traducirá a SQL.</span><span class="sxs-lookup"><span data-stu-id="e6f9d-161">In LINQ, the NTS methods and properties available as database functions will be translated to SQL.</span></span> <span data-ttu-id="e6f9d-162">Por ejemplo, los métodos de distancia y Contains se traducen en las consultas siguientes.</span><span class="sxs-lookup"><span data-stu-id="e6f9d-162">For example, the Distance and Contains methods are translated in the following queries.</span></span> <span data-ttu-id="e6f9d-163">La tabla al final de este artículo muestran los miembros que son compatibles con varios proveedores de EF Core.</span><span class="sxs-lookup"><span data-stu-id="e6f9d-163">The table at the end of this article shows which members are supported by various EF Core providers.</span></span>

``` csharp
var nearestCity = db.Cities
    .OrderBy(c => c.Location.Distance(currentLocation))
    .FirstOrDefault();

var currentCountry = db.Countries
    .FirstOrDefault(c => c.Border.Contains(currentLocation));
```

## <a name="sql-server"></a><span data-ttu-id="e6f9d-164">SQL Server</span><span class="sxs-lookup"><span data-stu-id="e6f9d-164">SQL Server</span></span>

<span data-ttu-id="e6f9d-165">Si usa SQL Server, hay algunos aspectos adicionales que debe tener en cuenta.</span><span class="sxs-lookup"><span data-stu-id="e6f9d-165">If you're using SQL Server, there are some additional things you should be aware of.</span></span>

### <a name="geography-or-geometry"></a><span data-ttu-id="e6f9d-166">Geografía o geometría</span><span class="sxs-lookup"><span data-stu-id="e6f9d-166">Geography or geometry</span></span>

<span data-ttu-id="e6f9d-167">De forma predeterminada, las propiedades espaciales se asignan a `geography` columnas en SQL Server.</span><span class="sxs-lookup"><span data-stu-id="e6f9d-167">By default, spatial properties are mapped to `geography` columns in SQL Server.</span></span> <span data-ttu-id="e6f9d-168">Para usar `geometry`, [configurar el tipo de columna](xref:core/modeling/relational/data-types) en el modelo.</span><span class="sxs-lookup"><span data-stu-id="e6f9d-168">To use `geometry`, [configure the column type](xref:core/modeling/relational/data-types) in your model.</span></span>

### <a name="geography-polygon-rings"></a><span data-ttu-id="e6f9d-169">Anillos de polígono de geografía</span><span class="sxs-lookup"><span data-stu-id="e6f9d-169">Geography polygon rings</span></span>

<span data-ttu-id="e6f9d-170">Cuando se usa el `geography` tipo de columna, SQL Server impone requisitos adicionales en el anillo exterior (o el shell) e interiores anillos (ni marcadores).</span><span class="sxs-lookup"><span data-stu-id="e6f9d-170">When using the `geography` column type, SQL Server imposes additional requirements on the exterior ring (or shell) and interior rings (or holes).</span></span> <span data-ttu-id="e6f9d-171">El anillo exterior debe ser orientado a la izquierda y el interior a la derecha los anillos.</span><span class="sxs-lookup"><span data-stu-id="e6f9d-171">The exterior ring must be oriented counterclockwise and the interior rings clockwise.</span></span> <span data-ttu-id="e6f9d-172">NTS Esto valida antes de enviar valores a la base de datos.</span><span class="sxs-lookup"><span data-stu-id="e6f9d-172">NTS validates this before sending values to the database.</span></span>

### <a name="fullglobe"></a><span data-ttu-id="e6f9d-173">FullGlobe</span><span class="sxs-lookup"><span data-stu-id="e6f9d-173">FullGlobe</span></span>

<span data-ttu-id="e6f9d-174">SQL Server tiene un tipo de geometría no estándar para representar el globo terráqueo cuando se usa el `geography` tipo de columna.</span><span class="sxs-lookup"><span data-stu-id="e6f9d-174">SQL Server has a non-standard geometry type to represent the full globe when using the `geography` column type.</span></span> <span data-ttu-id="e6f9d-175">También tiene una forma de representar los polígonos basándose en el globo terráqueo (sin un anillo exterior).</span><span class="sxs-lookup"><span data-stu-id="e6f9d-175">It also has a way to represent polygons based on the full globe (without an exterior ring).</span></span> <span data-ttu-id="e6f9d-176">Ninguna de ellas son compatibles con interrupción.</span><span class="sxs-lookup"><span data-stu-id="e6f9d-176">Neither of these are supported by NTS.</span></span>

> [!WARNING]
> <span data-ttu-id="e6f9d-177">NTS no admiten FullGlobe y polígonos basados en él.</span><span class="sxs-lookup"><span data-stu-id="e6f9d-177">FullGlobe and polygons based on it aren't supported by NTS.</span></span>

## <a name="sqlite"></a><span data-ttu-id="e6f9d-178">SQLite</span><span class="sxs-lookup"><span data-stu-id="e6f9d-178">SQLite</span></span>

<span data-ttu-id="e6f9d-179">Esta es información adicional para los usuarios de SQLite.</span><span class="sxs-lookup"><span data-stu-id="e6f9d-179">Here is some additional information for those using SQLite.</span></span>

### <a name="installing-spatialite"></a><span data-ttu-id="e6f9d-180">Instalar SpatiaLite</span><span class="sxs-lookup"><span data-stu-id="e6f9d-180">Installing SpatiaLite</span></span>

<span data-ttu-id="e6f9d-181">En Windows, la biblioteca nativa mod_spatialite se distribuye como una dependencia del paquete NuGet.</span><span class="sxs-lookup"><span data-stu-id="e6f9d-181">On Windows, the native mod_spatialite library is distributed as a NuGet package dependency.</span></span> <span data-ttu-id="e6f9d-182">Otras plataformas necesitan instalarlo por separado.</span><span class="sxs-lookup"><span data-stu-id="e6f9d-182">Other platforms need to install it separately.</span></span> <span data-ttu-id="e6f9d-183">Esto se suele realizar mediante un administrador de paquetes de software.</span><span class="sxs-lookup"><span data-stu-id="e6f9d-183">This is typically done using a software package manager.</span></span> <span data-ttu-id="e6f9d-184">Por ejemplo, puede usar APT en Ubuntu y Homebrew en MacOS.</span><span class="sxs-lookup"><span data-stu-id="e6f9d-184">For example, you can use APT on Ubuntu and Homebrew on MacOS.</span></span>

``` sh
# Ubuntu
apt-get install libsqlite3-mod-spatialite

# macOS
brew install libspatialite
```

### <a name="configuring-srid"></a><span data-ttu-id="e6f9d-185">Configurar SRID</span><span class="sxs-lookup"><span data-stu-id="e6f9d-185">Configuring SRID</span></span>

<span data-ttu-id="e6f9d-186">En SpatiaLite, las columnas deben especificar un SRID por columna.</span><span class="sxs-lookup"><span data-stu-id="e6f9d-186">In SpatiaLite, columns need to specify an SRID per column.</span></span> <span data-ttu-id="e6f9d-187">El valor predeterminado SRID es `0`.</span><span class="sxs-lookup"><span data-stu-id="e6f9d-187">The default SRID is `0`.</span></span> <span data-ttu-id="e6f9d-188">Especifique un SRID diferentes mediante el método ForSqliteHasSrid.</span><span class="sxs-lookup"><span data-stu-id="e6f9d-188">Specify a different SRID using the ForSqliteHasSrid method.</span></span>

``` csharp
modelBuilder.Entity<City>().Property(c => c.Location)
    .ForSqliteHasSrid(4326);
```

### <a name="dimension"></a><span data-ttu-id="e6f9d-189">Dimensión</span><span class="sxs-lookup"><span data-stu-id="e6f9d-189">Dimension</span></span>

<span data-ttu-id="e6f9d-190">Al igual que los SRID, dimensión de una columna (o coordenadas) también se especifica como parte de la columna.</span><span class="sxs-lookup"><span data-stu-id="e6f9d-190">Similar to SRID, a column's dimension (or ordinates) is also specified as part of the column.</span></span> <span data-ttu-id="e6f9d-191">Las coordenadas de forma predeterminada son X y Y. Habilitar adicionales ordenadas (Z y M) mediante el método ForSqliteHasDimension.</span><span class="sxs-lookup"><span data-stu-id="e6f9d-191">The default ordinates are X and Y. Enable additional ordinates (Z and M) using the ForSqliteHasDimension method.</span></span>

``` csharp
modelBuilder.Entity<City>().Property(c => c.Location)
    .ForSqliteHasDimension(Ordinates.XYZ);
```

## <a name="translated-operations"></a><span data-ttu-id="e6f9d-192">Operaciones traducidas</span><span class="sxs-lookup"><span data-stu-id="e6f9d-192">Translated Operations</span></span>

<span data-ttu-id="e6f9d-193">Esta tabla muestra qué miembros NTS se traducen en SQL por cada proveedor de EF Core.</span><span class="sxs-lookup"><span data-stu-id="e6f9d-193">This table shows which NTS members are translated into SQL by each EF Core provider.</span></span>

<span data-ttu-id="e6f9d-194">NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="e6f9d-194">NetTopologySuite</span></span> | <span data-ttu-id="e6f9d-195">SQL Server (geometry)</span><span class="sxs-lookup"><span data-stu-id="e6f9d-195">SQL Server (geometry)</span></span> | <span data-ttu-id="e6f9d-196">SQL Server (geography)</span><span class="sxs-lookup"><span data-stu-id="e6f9d-196">SQL Server (geography)</span></span> | <span data-ttu-id="e6f9d-197">SQLite</span><span class="sxs-lookup"><span data-stu-id="e6f9d-197">SQLite</span></span> | <span data-ttu-id="e6f9d-198">Npgsql</span><span class="sxs-lookup"><span data-stu-id="e6f9d-198">Npgsql</span></span>
--- |:---:|:---:|:---:|:---:
<span data-ttu-id="e6f9d-199">Geometry.Area</span><span class="sxs-lookup"><span data-stu-id="e6f9d-199">Geometry.Area</span></span> | <span data-ttu-id="e6f9d-200">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-200">✔</span></span> | <span data-ttu-id="e6f9d-201">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-201">✔</span></span> | <span data-ttu-id="e6f9d-202">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-202">✔</span></span> | <span data-ttu-id="e6f9d-203">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-203">✔</span></span>
<span data-ttu-id="e6f9d-204">Geometry.AsBinary()</span><span class="sxs-lookup"><span data-stu-id="e6f9d-204">Geometry.AsBinary()</span></span> | <span data-ttu-id="e6f9d-205">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-205">✔</span></span> | <span data-ttu-id="e6f9d-206">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-206">✔</span></span> | <span data-ttu-id="e6f9d-207">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-207">✔</span></span> | <span data-ttu-id="e6f9d-208">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-208">✔</span></span>
<span data-ttu-id="e6f9d-209">Geometry.AsText()</span><span class="sxs-lookup"><span data-stu-id="e6f9d-209">Geometry.AsText()</span></span> | <span data-ttu-id="e6f9d-210">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-210">✔</span></span> | <span data-ttu-id="e6f9d-211">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-211">✔</span></span> | <span data-ttu-id="e6f9d-212">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-212">✔</span></span> | <span data-ttu-id="e6f9d-213">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-213">✔</span></span>
<span data-ttu-id="e6f9d-214">Geometry.Boundary</span><span class="sxs-lookup"><span data-stu-id="e6f9d-214">Geometry.Boundary</span></span> | <span data-ttu-id="e6f9d-215">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-215">✔</span></span> | | <span data-ttu-id="e6f9d-216">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-216">✔</span></span> | <span data-ttu-id="e6f9d-217">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-217">✔</span></span>
<span data-ttu-id="e6f9d-218">Geometry.Buffer(double)</span><span class="sxs-lookup"><span data-stu-id="e6f9d-218">Geometry.Buffer(double)</span></span> | <span data-ttu-id="e6f9d-219">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-219">✔</span></span> | <span data-ttu-id="e6f9d-220">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-220">✔</span></span> | <span data-ttu-id="e6f9d-221">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-221">✔</span></span> | <span data-ttu-id="e6f9d-222">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-222">✔</span></span>
<span data-ttu-id="e6f9d-223">Geometry.Buffer (double, int)</span><span class="sxs-lookup"><span data-stu-id="e6f9d-223">Geometry.Buffer(double, int)</span></span> | | | <span data-ttu-id="e6f9d-224">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-224">✔</span></span>
<span data-ttu-id="e6f9d-225">Geometry.Centroid</span><span class="sxs-lookup"><span data-stu-id="e6f9d-225">Geometry.Centroid</span></span> | <span data-ttu-id="e6f9d-226">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-226">✔</span></span> | | <span data-ttu-id="e6f9d-227">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-227">✔</span></span> | <span data-ttu-id="e6f9d-228">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-228">✔</span></span>
<span data-ttu-id="e6f9d-229">Geometry.Contains(Geometry)</span><span class="sxs-lookup"><span data-stu-id="e6f9d-229">Geometry.Contains(Geometry)</span></span> | <span data-ttu-id="e6f9d-230">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-230">✔</span></span> | <span data-ttu-id="e6f9d-231">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-231">✔</span></span> | <span data-ttu-id="e6f9d-232">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-232">✔</span></span> | <span data-ttu-id="e6f9d-233">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-233">✔</span></span>
<span data-ttu-id="e6f9d-234">Geometry.ConvexHull()</span><span class="sxs-lookup"><span data-stu-id="e6f9d-234">Geometry.ConvexHull()</span></span> | <span data-ttu-id="e6f9d-235">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-235">✔</span></span> | <span data-ttu-id="e6f9d-236">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-236">✔</span></span> | <span data-ttu-id="e6f9d-237">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-237">✔</span></span> | <span data-ttu-id="e6f9d-238">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-238">✔</span></span>
<span data-ttu-id="e6f9d-239">Geometry.CoveredBy(Geometry)</span><span class="sxs-lookup"><span data-stu-id="e6f9d-239">Geometry.CoveredBy(Geometry)</span></span> | | | <span data-ttu-id="e6f9d-240">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-240">✔</span></span> | <span data-ttu-id="e6f9d-241">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-241">✔</span></span>
<span data-ttu-id="e6f9d-242">Geometry.Covers(Geometry)</span><span class="sxs-lookup"><span data-stu-id="e6f9d-242">Geometry.Covers(Geometry)</span></span> | | | <span data-ttu-id="e6f9d-243">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-243">✔</span></span> | <span data-ttu-id="e6f9d-244">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-244">✔</span></span>
<span data-ttu-id="e6f9d-245">Geometry.Crosses(Geometry)</span><span class="sxs-lookup"><span data-stu-id="e6f9d-245">Geometry.Crosses(Geometry)</span></span> | <span data-ttu-id="e6f9d-246">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-246">✔</span></span> | | <span data-ttu-id="e6f9d-247">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-247">✔</span></span> | <span data-ttu-id="e6f9d-248">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-248">✔</span></span>
<span data-ttu-id="e6f9d-249">Geometry.Difference(Geometry)</span><span class="sxs-lookup"><span data-stu-id="e6f9d-249">Geometry.Difference(Geometry)</span></span> | <span data-ttu-id="e6f9d-250">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-250">✔</span></span> | <span data-ttu-id="e6f9d-251">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-251">✔</span></span> | <span data-ttu-id="e6f9d-252">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-252">✔</span></span> | <span data-ttu-id="e6f9d-253">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-253">✔</span></span>
<span data-ttu-id="e6f9d-254">Geometry.Dimension</span><span class="sxs-lookup"><span data-stu-id="e6f9d-254">Geometry.Dimension</span></span> | <span data-ttu-id="e6f9d-255">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-255">✔</span></span> | <span data-ttu-id="e6f9d-256">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-256">✔</span></span> | <span data-ttu-id="e6f9d-257">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-257">✔</span></span> | <span data-ttu-id="e6f9d-258">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-258">✔</span></span>
<span data-ttu-id="e6f9d-259">Geometry.Disjoint(Geometry)</span><span class="sxs-lookup"><span data-stu-id="e6f9d-259">Geometry.Disjoint(Geometry)</span></span> | <span data-ttu-id="e6f9d-260">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-260">✔</span></span> | <span data-ttu-id="e6f9d-261">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-261">✔</span></span> | <span data-ttu-id="e6f9d-262">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-262">✔</span></span> | <span data-ttu-id="e6f9d-263">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-263">✔</span></span>
<span data-ttu-id="e6f9d-264">Geometry.Distance(Geometry)</span><span class="sxs-lookup"><span data-stu-id="e6f9d-264">Geometry.Distance(Geometry)</span></span> | <span data-ttu-id="e6f9d-265">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-265">✔</span></span> | <span data-ttu-id="e6f9d-266">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-266">✔</span></span> | <span data-ttu-id="e6f9d-267">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-267">✔</span></span> | <span data-ttu-id="e6f9d-268">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-268">✔</span></span>
<span data-ttu-id="e6f9d-269">Geometry.Envelope</span><span class="sxs-lookup"><span data-stu-id="e6f9d-269">Geometry.Envelope</span></span> | <span data-ttu-id="e6f9d-270">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-270">✔</span></span> | | <span data-ttu-id="e6f9d-271">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-271">✔</span></span> | <span data-ttu-id="e6f9d-272">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-272">✔</span></span>
<span data-ttu-id="e6f9d-273">Geometry.EqualsExact(Geometry)</span><span class="sxs-lookup"><span data-stu-id="e6f9d-273">Geometry.EqualsExact(Geometry)</span></span> | | | | <span data-ttu-id="e6f9d-274">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-274">✔</span></span>
<span data-ttu-id="e6f9d-275">Geometry.EqualsTopologically(Geometry)</span><span class="sxs-lookup"><span data-stu-id="e6f9d-275">Geometry.EqualsTopologically(Geometry)</span></span> | <span data-ttu-id="e6f9d-276">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-276">✔</span></span> | <span data-ttu-id="e6f9d-277">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-277">✔</span></span> | <span data-ttu-id="e6f9d-278">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-278">✔</span></span> | <span data-ttu-id="e6f9d-279">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-279">✔</span></span>
<span data-ttu-id="e6f9d-280">Geometry.GeometryType</span><span class="sxs-lookup"><span data-stu-id="e6f9d-280">Geometry.GeometryType</span></span> | <span data-ttu-id="e6f9d-281">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-281">✔</span></span> | <span data-ttu-id="e6f9d-282">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-282">✔</span></span> | <span data-ttu-id="e6f9d-283">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-283">✔</span></span> | <span data-ttu-id="e6f9d-284">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-284">✔</span></span>
<span data-ttu-id="e6f9d-285">Geometry.GetGeometryN(int)</span><span class="sxs-lookup"><span data-stu-id="e6f9d-285">Geometry.GetGeometryN(int)</span></span> | <span data-ttu-id="e6f9d-286">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-286">✔</span></span> | | <span data-ttu-id="e6f9d-287">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-287">✔</span></span> | <span data-ttu-id="e6f9d-288">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-288">✔</span></span>
<span data-ttu-id="e6f9d-289">Geometry.InteriorPoint</span><span class="sxs-lookup"><span data-stu-id="e6f9d-289">Geometry.InteriorPoint</span></span> | <span data-ttu-id="e6f9d-290">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-290">✔</span></span> | | <span data-ttu-id="e6f9d-291">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-291">✔</span></span>
<span data-ttu-id="e6f9d-292">Geometry.Intersection(Geometry)</span><span class="sxs-lookup"><span data-stu-id="e6f9d-292">Geometry.Intersection(Geometry)</span></span> | <span data-ttu-id="e6f9d-293">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-293">✔</span></span> | <span data-ttu-id="e6f9d-294">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-294">✔</span></span> | <span data-ttu-id="e6f9d-295">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-295">✔</span></span> | <span data-ttu-id="e6f9d-296">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-296">✔</span></span>
<span data-ttu-id="e6f9d-297">Geometry.Intersects(Geometry)</span><span class="sxs-lookup"><span data-stu-id="e6f9d-297">Geometry.Intersects(Geometry)</span></span> | <span data-ttu-id="e6f9d-298">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-298">✔</span></span> | <span data-ttu-id="e6f9d-299">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-299">✔</span></span> | <span data-ttu-id="e6f9d-300">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-300">✔</span></span> | <span data-ttu-id="e6f9d-301">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-301">✔</span></span>
<span data-ttu-id="e6f9d-302">Geometry.IsEmpty</span><span class="sxs-lookup"><span data-stu-id="e6f9d-302">Geometry.IsEmpty</span></span> | <span data-ttu-id="e6f9d-303">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-303">✔</span></span> | <span data-ttu-id="e6f9d-304">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-304">✔</span></span> | <span data-ttu-id="e6f9d-305">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-305">✔</span></span> | <span data-ttu-id="e6f9d-306">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-306">✔</span></span>
<span data-ttu-id="e6f9d-307">Geometry.IsSimple</span><span class="sxs-lookup"><span data-stu-id="e6f9d-307">Geometry.IsSimple</span></span> | <span data-ttu-id="e6f9d-308">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-308">✔</span></span> | | <span data-ttu-id="e6f9d-309">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-309">✔</span></span> | <span data-ttu-id="e6f9d-310">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-310">✔</span></span>
<span data-ttu-id="e6f9d-311">Geometry.IsValid</span><span class="sxs-lookup"><span data-stu-id="e6f9d-311">Geometry.IsValid</span></span> | <span data-ttu-id="e6f9d-312">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-312">✔</span></span> | <span data-ttu-id="e6f9d-313">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-313">✔</span></span> | <span data-ttu-id="e6f9d-314">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-314">✔</span></span> | <span data-ttu-id="e6f9d-315">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-315">✔</span></span>
<span data-ttu-id="e6f9d-316">Geometry.IsWithinDistance (Geometry, double)</span><span class="sxs-lookup"><span data-stu-id="e6f9d-316">Geometry.IsWithinDistance(Geometry, double)</span></span> | <span data-ttu-id="e6f9d-317">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-317">✔</span></span> | | <span data-ttu-id="e6f9d-318">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-318">✔</span></span>
<span data-ttu-id="e6f9d-319">Geometry.Length</span><span class="sxs-lookup"><span data-stu-id="e6f9d-319">Geometry.Length</span></span> | <span data-ttu-id="e6f9d-320">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-320">✔</span></span> | <span data-ttu-id="e6f9d-321">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-321">✔</span></span> | <span data-ttu-id="e6f9d-322">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-322">✔</span></span> | <span data-ttu-id="e6f9d-323">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-323">✔</span></span>
<span data-ttu-id="e6f9d-324">Geometry.NumGeometries</span><span class="sxs-lookup"><span data-stu-id="e6f9d-324">Geometry.NumGeometries</span></span> | <span data-ttu-id="e6f9d-325">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-325">✔</span></span> | <span data-ttu-id="e6f9d-326">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-326">✔</span></span> | <span data-ttu-id="e6f9d-327">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-327">✔</span></span> | <span data-ttu-id="e6f9d-328">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-328">✔</span></span>
<span data-ttu-id="e6f9d-329">Geometry.NumPoints</span><span class="sxs-lookup"><span data-stu-id="e6f9d-329">Geometry.NumPoints</span></span> | <span data-ttu-id="e6f9d-330">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-330">✔</span></span> | <span data-ttu-id="e6f9d-331">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-331">✔</span></span> | <span data-ttu-id="e6f9d-332">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-332">✔</span></span> | <span data-ttu-id="e6f9d-333">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-333">✔</span></span>
<span data-ttu-id="e6f9d-334">Geometry.OgcGeometryType</span><span class="sxs-lookup"><span data-stu-id="e6f9d-334">Geometry.OgcGeometryType</span></span> | <span data-ttu-id="e6f9d-335">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-335">✔</span></span> | <span data-ttu-id="e6f9d-336">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-336">✔</span></span> | <span data-ttu-id="e6f9d-337">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-337">✔</span></span>
<span data-ttu-id="e6f9d-338">Geometry.Overlaps(Geometry)</span><span class="sxs-lookup"><span data-stu-id="e6f9d-338">Geometry.Overlaps(Geometry)</span></span> | <span data-ttu-id="e6f9d-339">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-339">✔</span></span> | <span data-ttu-id="e6f9d-340">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-340">✔</span></span> | <span data-ttu-id="e6f9d-341">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-341">✔</span></span> | <span data-ttu-id="e6f9d-342">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-342">✔</span></span>
<span data-ttu-id="e6f9d-343">Geometry.PointOnSurface</span><span class="sxs-lookup"><span data-stu-id="e6f9d-343">Geometry.PointOnSurface</span></span> | <span data-ttu-id="e6f9d-344">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-344">✔</span></span> | | <span data-ttu-id="e6f9d-345">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-345">✔</span></span> | <span data-ttu-id="e6f9d-346">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-346">✔</span></span>
<span data-ttu-id="e6f9d-347">Geometry.Relate (Geometry, cadena)</span><span class="sxs-lookup"><span data-stu-id="e6f9d-347">Geometry.Relate(Geometry, string)</span></span> | <span data-ttu-id="e6f9d-348">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-348">✔</span></span> | | <span data-ttu-id="e6f9d-349">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-349">✔</span></span> | <span data-ttu-id="e6f9d-350">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-350">✔</span></span>
<span data-ttu-id="e6f9d-351">Geometry.Reverse()</span><span class="sxs-lookup"><span data-stu-id="e6f9d-351">Geometry.Reverse()</span></span> | | | <span data-ttu-id="e6f9d-352">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-352">✔</span></span> | <span data-ttu-id="e6f9d-353">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-353">✔</span></span>
<span data-ttu-id="e6f9d-354">Geometry.SRID</span><span class="sxs-lookup"><span data-stu-id="e6f9d-354">Geometry.SRID</span></span> | <span data-ttu-id="e6f9d-355">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-355">✔</span></span> | <span data-ttu-id="e6f9d-356">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-356">✔</span></span> | <span data-ttu-id="e6f9d-357">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-357">✔</span></span> | <span data-ttu-id="e6f9d-358">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-358">✔</span></span>
<span data-ttu-id="e6f9d-359">Geometry.SymmetricDifference(Geometry)</span><span class="sxs-lookup"><span data-stu-id="e6f9d-359">Geometry.SymmetricDifference(Geometry)</span></span> | <span data-ttu-id="e6f9d-360">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-360">✔</span></span> | <span data-ttu-id="e6f9d-361">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-361">✔</span></span> | <span data-ttu-id="e6f9d-362">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-362">✔</span></span> | <span data-ttu-id="e6f9d-363">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-363">✔</span></span>
<span data-ttu-id="e6f9d-364">Geometry.ToBinary()</span><span class="sxs-lookup"><span data-stu-id="e6f9d-364">Geometry.ToBinary()</span></span> | <span data-ttu-id="e6f9d-365">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-365">✔</span></span> | <span data-ttu-id="e6f9d-366">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-366">✔</span></span> | <span data-ttu-id="e6f9d-367">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-367">✔</span></span> | <span data-ttu-id="e6f9d-368">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-368">✔</span></span>
<span data-ttu-id="e6f9d-369">Geometry.ToText()</span><span class="sxs-lookup"><span data-stu-id="e6f9d-369">Geometry.ToText()</span></span> | <span data-ttu-id="e6f9d-370">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-370">✔</span></span> | <span data-ttu-id="e6f9d-371">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-371">✔</span></span> | <span data-ttu-id="e6f9d-372">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-372">✔</span></span> | <span data-ttu-id="e6f9d-373">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-373">✔</span></span>
<span data-ttu-id="e6f9d-374">Geometry.Touches(Geometry)</span><span class="sxs-lookup"><span data-stu-id="e6f9d-374">Geometry.Touches(Geometry)</span></span> | <span data-ttu-id="e6f9d-375">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-375">✔</span></span> | | <span data-ttu-id="e6f9d-376">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-376">✔</span></span> | <span data-ttu-id="e6f9d-377">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-377">✔</span></span>
<span data-ttu-id="e6f9d-378">Geometry.Union()</span><span class="sxs-lookup"><span data-stu-id="e6f9d-378">Geometry.Union()</span></span> | | | <span data-ttu-id="e6f9d-379">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-379">✔</span></span>
<span data-ttu-id="e6f9d-380">Geometry.Union(Geometry)</span><span class="sxs-lookup"><span data-stu-id="e6f9d-380">Geometry.Union(Geometry)</span></span> | <span data-ttu-id="e6f9d-381">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-381">✔</span></span> | <span data-ttu-id="e6f9d-382">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-382">✔</span></span> | <span data-ttu-id="e6f9d-383">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-383">✔</span></span> | <span data-ttu-id="e6f9d-384">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-384">✔</span></span>
<span data-ttu-id="e6f9d-385">Geometry.Within(Geometry)</span><span class="sxs-lookup"><span data-stu-id="e6f9d-385">Geometry.Within(Geometry)</span></span> | <span data-ttu-id="e6f9d-386">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-386">✔</span></span> | <span data-ttu-id="e6f9d-387">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-387">✔</span></span> | <span data-ttu-id="e6f9d-388">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-388">✔</span></span> | <span data-ttu-id="e6f9d-389">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-389">✔</span></span>
<span data-ttu-id="e6f9d-390">GeometryCollection.Count</span><span class="sxs-lookup"><span data-stu-id="e6f9d-390">GeometryCollection.Count</span></span> | <span data-ttu-id="e6f9d-391">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-391">✔</span></span> | <span data-ttu-id="e6f9d-392">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-392">✔</span></span> | <span data-ttu-id="e6f9d-393">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-393">✔</span></span> | <span data-ttu-id="e6f9d-394">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-394">✔</span></span>
<span data-ttu-id="e6f9d-395">GeometryCollection [int]</span><span class="sxs-lookup"><span data-stu-id="e6f9d-395">GeometryCollection[int]</span></span> | <span data-ttu-id="e6f9d-396">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-396">✔</span></span> | <span data-ttu-id="e6f9d-397">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-397">✔</span></span> | <span data-ttu-id="e6f9d-398">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-398">✔</span></span> | <span data-ttu-id="e6f9d-399">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-399">✔</span></span>
<span data-ttu-id="e6f9d-400">LineString.Count</span><span class="sxs-lookup"><span data-stu-id="e6f9d-400">LineString.Count</span></span> | <span data-ttu-id="e6f9d-401">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-401">✔</span></span> | <span data-ttu-id="e6f9d-402">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-402">✔</span></span> | <span data-ttu-id="e6f9d-403">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-403">✔</span></span> | <span data-ttu-id="e6f9d-404">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-404">✔</span></span>
<span data-ttu-id="e6f9d-405">LineString.EndPoint</span><span class="sxs-lookup"><span data-stu-id="e6f9d-405">LineString.EndPoint</span></span> | <span data-ttu-id="e6f9d-406">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-406">✔</span></span> | <span data-ttu-id="e6f9d-407">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-407">✔</span></span> | <span data-ttu-id="e6f9d-408">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-408">✔</span></span> | <span data-ttu-id="e6f9d-409">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-409">✔</span></span>
<span data-ttu-id="e6f9d-410">LineString.GetPointN(int)</span><span class="sxs-lookup"><span data-stu-id="e6f9d-410">LineString.GetPointN(int)</span></span> | <span data-ttu-id="e6f9d-411">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-411">✔</span></span> | <span data-ttu-id="e6f9d-412">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-412">✔</span></span> | <span data-ttu-id="e6f9d-413">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-413">✔</span></span> | <span data-ttu-id="e6f9d-414">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-414">✔</span></span>
<span data-ttu-id="e6f9d-415">LineString.IsClosed</span><span class="sxs-lookup"><span data-stu-id="e6f9d-415">LineString.IsClosed</span></span> | <span data-ttu-id="e6f9d-416">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-416">✔</span></span> | <span data-ttu-id="e6f9d-417">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-417">✔</span></span> | <span data-ttu-id="e6f9d-418">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-418">✔</span></span> | <span data-ttu-id="e6f9d-419">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-419">✔</span></span>
<span data-ttu-id="e6f9d-420">LineString.IsRing</span><span class="sxs-lookup"><span data-stu-id="e6f9d-420">LineString.IsRing</span></span> | <span data-ttu-id="e6f9d-421">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-421">✔</span></span> | | <span data-ttu-id="e6f9d-422">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-422">✔</span></span> | <span data-ttu-id="e6f9d-423">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-423">✔</span></span>
<span data-ttu-id="e6f9d-424">LineString.StartPoint</span><span class="sxs-lookup"><span data-stu-id="e6f9d-424">LineString.StartPoint</span></span> | <span data-ttu-id="e6f9d-425">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-425">✔</span></span> | <span data-ttu-id="e6f9d-426">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-426">✔</span></span> | <span data-ttu-id="e6f9d-427">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-427">✔</span></span> | <span data-ttu-id="e6f9d-428">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-428">✔</span></span>
<span data-ttu-id="e6f9d-429">MultiLineString.IsClosed</span><span class="sxs-lookup"><span data-stu-id="e6f9d-429">MultiLineString.IsClosed</span></span> | <span data-ttu-id="e6f9d-430">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-430">✔</span></span> | <span data-ttu-id="e6f9d-431">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-431">✔</span></span> | <span data-ttu-id="e6f9d-432">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-432">✔</span></span> | <span data-ttu-id="e6f9d-433">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-433">✔</span></span>
<span data-ttu-id="e6f9d-434">Point.M</span><span class="sxs-lookup"><span data-stu-id="e6f9d-434">Point.M</span></span> | <span data-ttu-id="e6f9d-435">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-435">✔</span></span> | <span data-ttu-id="e6f9d-436">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-436">✔</span></span> | <span data-ttu-id="e6f9d-437">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-437">✔</span></span> | <span data-ttu-id="e6f9d-438">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-438">✔</span></span>
<span data-ttu-id="e6f9d-439">Point.X</span><span class="sxs-lookup"><span data-stu-id="e6f9d-439">Point.X</span></span> | <span data-ttu-id="e6f9d-440">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-440">✔</span></span> | <span data-ttu-id="e6f9d-441">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-441">✔</span></span> | <span data-ttu-id="e6f9d-442">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-442">✔</span></span> | <span data-ttu-id="e6f9d-443">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-443">✔</span></span>
<span data-ttu-id="e6f9d-444">Point.Y</span><span class="sxs-lookup"><span data-stu-id="e6f9d-444">Point.Y</span></span> | <span data-ttu-id="e6f9d-445">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-445">✔</span></span> | <span data-ttu-id="e6f9d-446">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-446">✔</span></span> | <span data-ttu-id="e6f9d-447">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-447">✔</span></span> | <span data-ttu-id="e6f9d-448">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-448">✔</span></span>
<span data-ttu-id="e6f9d-449">Point.Z</span><span class="sxs-lookup"><span data-stu-id="e6f9d-449">Point.Z</span></span> | <span data-ttu-id="e6f9d-450">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-450">✔</span></span> | <span data-ttu-id="e6f9d-451">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-451">✔</span></span> | <span data-ttu-id="e6f9d-452">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-452">✔</span></span> | <span data-ttu-id="e6f9d-453">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-453">✔</span></span>
<span data-ttu-id="e6f9d-454">Polygon.ExteriorRing</span><span class="sxs-lookup"><span data-stu-id="e6f9d-454">Polygon.ExteriorRing</span></span> | <span data-ttu-id="e6f9d-455">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-455">✔</span></span> | <span data-ttu-id="e6f9d-456">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-456">✔</span></span> | <span data-ttu-id="e6f9d-457">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-457">✔</span></span> | <span data-ttu-id="e6f9d-458">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-458">✔</span></span>
<span data-ttu-id="e6f9d-459">Polygon.GetInteriorRingN(int)</span><span class="sxs-lookup"><span data-stu-id="e6f9d-459">Polygon.GetInteriorRingN(int)</span></span> | <span data-ttu-id="e6f9d-460">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-460">✔</span></span> | <span data-ttu-id="e6f9d-461">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-461">✔</span></span> | <span data-ttu-id="e6f9d-462">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-462">✔</span></span> | <span data-ttu-id="e6f9d-463">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-463">✔</span></span>
<span data-ttu-id="e6f9d-464">Polygon.NumInteriorRings</span><span class="sxs-lookup"><span data-stu-id="e6f9d-464">Polygon.NumInteriorRings</span></span> | <span data-ttu-id="e6f9d-465">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-465">✔</span></span> | <span data-ttu-id="e6f9d-466">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-466">✔</span></span> | <span data-ttu-id="e6f9d-467">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-467">✔</span></span> | <span data-ttu-id="e6f9d-468">✔</span><span class="sxs-lookup"><span data-stu-id="e6f9d-468">✔</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e6f9d-469">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="e6f9d-469">Additional resources</span></span>

* [<span data-ttu-id="e6f9d-470">Datos espaciales de SQL Server</span><span class="sxs-lookup"><span data-stu-id="e6f9d-470">Spatial Data in SQL Server</span></span>](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server)
* [<span data-ttu-id="e6f9d-471">Página principal de SpatiaLite</span><span class="sxs-lookup"><span data-stu-id="e6f9d-471">SpatiaLite Homepage</span></span>](https://www.gaia-gis.it/fossil/libspatialite)
* [<span data-ttu-id="e6f9d-472">Documentación de Npgsql espacial</span><span class="sxs-lookup"><span data-stu-id="e6f9d-472">Npgsql Spatial Documentation</span></span>](http://www.npgsql.org/efcore/mapping/nts.html)
* [<span data-ttu-id="e6f9d-473">Documentación de PostGIS</span><span class="sxs-lookup"><span data-stu-id="e6f9d-473">PostGIS Documentation</span></span>](http://postgis.net/documentation/)