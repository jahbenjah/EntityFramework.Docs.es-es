# [Entity Framework](index.md)

## [Comparar EF Core y EF6](efcore-and-ef6/index.md)

### [Qué versión le conviene](efcore-and-ef6/choosing.md)
### [Comparación de características](efcore-and-ef6/features.md)
### [EF6 y EF Core en la misma aplicación](efcore-and-ef6/side-by-side.md)
### [Portabilidad de EF6 a EF Core](efcore-and-ef6/porting/index.md)
#### [Validar los requisitos](efcore-and-ef6/porting/ensure-requirements.md)
#### [Portabilidad de un modelo basado en EDMX](efcore-and-ef6/porting/port-edmx.md)
#### [Portabilidad de un modelo basado en código](efcore-and-ef6/porting/port-code.md)

## [Entity Framework Core](core/index.md)

### [Novedades de EF Core 2.0](core/what-is-new/index.md)
#### [EF Core 1.0 (versión anterior)](core/what-is-new/ef-core-1.0.md)
#### [EF Core 1.1 (versión anterior)](core/what-is-new/ef-core-1.1.md)

### [Introducción](core/get-started/index.md)
#### [Instalación de EF Core](core/get-started/install/index.md)
#### [.NET Framework (consola, WinForms, WPF, etc.)](core/get-started/full-dotnet/index.md)
##### [.NET Framework: nueva base de datos](core/get-started/full-dotnet/new-db.md)
##### [.NET Framework: base de datos existente](core/get-started/full-dotnet/existing-db.md)
#### [.NET Core (Windows, OSX, Linux, etc.)](core/get-started/netcore/index.md)
##### [.NET Core: nueva base de datos](core/get-started/netcore/new-db-sqlite.md)
#### [ASP.NET Core](core/get-started/aspnetcore/index.md)
##### [ASP.NET Core: nueva base de datos](core/get-started/aspnetcore/new-db.md)
##### [ASP.NET Core: base de datos existente](core/get-started/aspnetcore/existing-db.md)
##### [Tutorial de EF Core en el sitio de ASP.NET Core](https://docs.asp.net/en/latest/data/ef-mvc/intro.html)
#### [Plataforma universal de Windows (UWP)](core/get-started/uwp/index.md)
##### [UWP: nueva base de datos](core/get-started/uwp/getting-started.md)

### [Creación de un modelo](core/modeling/index.md)
#### [Inclusión y exclusión de tipos](core/modeling/included-types.md)
#### [Inclusión y exclusión de propiedades](core/modeling/included-properties.md)
#### [Claves (principales)](core/modeling/keys.md)
#### [Valores generados](core/modeling/generated-properties.md)
#### [Propiedades obligatorias y opcionales](core/modeling/required-optional.md)
#### [Longitud máxima](core/modeling/max-length.md)
#### [Tokens de simultaneidad](core/modeling/concurrency.md)
#### [Propiedades paralelas](core/modeling/shadow-properties.md)
#### [Relaciones](core/modeling/relationships.md)
#### [Índices](core/modeling/indexes.md)
#### [Claves alternativas](core/modeling/alternate-keys.md)
#### [Herencia](core/modeling/inheritance.md)
#### [Campos de respaldo](core/modeling/backing-field.md)
#### [Alternancia de modelos con el mismo DbContext](core/modeling/dynamic-model.md)
#### [Modelado de bases de datos relacionales](core/modeling/relational/index.md)
##### [Asignación de tabla](core/modeling/relational/tables.md)
##### [Asignación de columna](core/modeling/relational/columns.md)
##### [Tipos de datos](core/modeling/relational/data-types.md)
##### [Claves principales](core/modeling/relational/primary-keys.md)
##### [Esquema predeterminado](core/modeling/relational/default-schema.md)
##### [Columnas calculadas](core/modeling/relational/computed-columns.md)
##### [Secuencias](core/modeling/relational/sequences.md)
##### [Valores predeterminados](core/modeling/relational/default-values.md)
##### [Índices](core/modeling/relational/indexes.md)
##### [Foreign Key Constraints](core/modeling/relational/fk-constraints.md)
##### [Claves alternativas (restricciones únicas)](core/modeling/relational/unique-constraints.md)
##### [Herencia (base de datos relacional)](core/modeling/relational/inheritance.md)

### [Consulta de datos](core/querying/index.md)
#### [Consulta básica](core/querying/basic.md)
#### [Carga de datos relacionados](core/querying/related-data.md)
#### [Evaluación de cliente frente a servidor](core/querying/client-eval.md)
#### [Seguimiento frente a no seguimiento](core/querying/tracking.md)
#### [Consultas SQL sin formato](core/querying/raw-sql.md)
#### [Consultas asincrónicas](core/querying/async.md)
#### [Funcionamiento de una consulta](core/querying/overview.md)
#### [Filtros de consulta global](core/querying/filters.md)

### [Guardar datos](core/saving/index.md)
#### [Guardado básico](core/saving/basic.md)
#### [Datos relacionados](core/saving/related-data.md)
#### [Eliminación en cascada](core/saving/cascade-delete.md)
#### [Conflictos de simultaneidad](core/saving/concurrency.md)
#### [Transacciones](core/saving/transactions.md)
#### [Guardado asincrónico](core/saving/async.md)
#### [Entidades desconectadas](core/saving/disconnected-entities.md)
#### [Valores explícitos para propiedades generadas](core/saving/explicit-values-generated-properties.md)

### [Implementaciones de .NET compatibles](core/platforms/index.md)

### [Proveedores de bases de datos](core/providers/index.md)
#### [Microsoft SQL Server](core/providers/sql-server/index.md)
##### [Memory-Optimized Tables](core/providers/sql-server/memory-optimized-tables.md)
#### [SQLite](core/providers/sqlite/index.md)
##### [Limitaciones de SQLite](core/providers/sqlite/limitations.md)
#### [PostgreSQL (Npgsql)](core/providers/npgsql/index.md)
#### [IBM Data Server (DB2)](core/providers/ibm/index.md)
#### [MySQL (Official)](core/providers/mysql/index.md)
#### [MySQL (Pomelo)](core/providers/pomelo/index.md)
#### [Microsoft SQL Server Compact Edition](core/providers/sql-compact/index.md)
#### [InMemory (para pruebas)](core/providers/in-memory/index.md)
#### [Devart (MySQL, Oracle, PostgreSQL, SQLite, DB2, etc.)](core/providers/devart/index.md)
#### [Oracle (aún no disponible)](core/providers/oracle/index.md)
#### [MyCat](core/providers/my-cat/index.md)
#### [Firebird-Community](core/providers/firebird-community/index.md)
#### [Escritura de un proveedor de base de datos](core/providers/writing-a-provider.md)

### [Administración de esquemas de base de datos](core/managing-schemas/index.md)
#### [Migraciones](core/managing-schemas/migrations/index.md)
##### [Entornos de equipo](core/managing-schemas/migrations/teams.md)
##### [Operaciones personalizadas](core/managing-schemas/migrations/operations.md)
##### [Uso de un proyecto independiente](core/managing-schemas/migrations/projects.md)
##### [Varios proveedores](core/managing-schemas/migrations/providers.md)
##### [Tabla de historial personalizada](core/managing-schemas/migrations/history-table.md)
#### [🔧 Crear y quitar API](core/managing-schemas/ensure-created.md)
#### [🔧 Ingeniería inversa](core/managing-schemas/scaffolding.md)

### [Referencia de la línea de comandos](core/miscellaneous/cli/index.md)
#### [Consola del administrador de paquetes (Visual Studio)](core/miscellaneous/cli/powershell.md)
#### [CLI de .NET Core](core/miscellaneous/cli/dotnet.md)
#### [Creación de DbContext en tiempo de diseño](core/miscellaneous/cli/dbcontext-creation.md)
#### [Servicios en tiempo de diseño](core/miscellaneous/cli/services.md)

### [Herramientas y extensiones](core/extensions/index.md)
#### [LLBLGen Pro](core/extensions/llbl-gen-pro.md)
#### [Devart Entity Developer](core/extensions/devart-entity-developer.md)
#### [EFSecondLevelCache.Core](core/extensions/efsecondlevelcache-core.md)
#### [Geco (generador de modelos inverso)](core/extensions/geco.md)
#### [EntityFrameworkCore.Detached](core/extensions/entityframeworkcore-detached.md)
#### [EntityFrameworkCore.Triggers](core/extensions/entityframeworkcore-triggers.md)
#### [EntityFrameworkCore.Rx](core/extensions/entityframeworkcore-rx.md)
#### [EntityFrameworkCore.PrimaryKey](core/extensions/entityframeworkcore-primarykey.md)
#### [EntityFrameworkCore.TypedOriginalValues](core/extensions/entityframeworkcore-typedoriginalvalues.md)
#### [EFCore.Practices](core/extensions/efcore-practices.md)
#### [LinqKit.Microsoft.EntityFrameworkCore](core/extensions/linqkit.md)
#### [Microsoft.EntityFrameworkCore.AutoHistory](core/extensions/autohistory.md)
#### [Microsoft.EntityFrameworkCore.DynamicLinq](core/extensions/dynamiclinq.md)
#### [Microsoft.EntityFrameworkCore.UnitOfWork](core/extensions/unitofwork.md)

### Varios
#### [Cadenas de conexión](core/miscellaneous/connection-strings.md)
#### [Registro](core/miscellaneous/logging.md)
#### [Resistencia de conexión](core/miscellaneous/connection-resiliency.md)
#### [Pruebas](core/miscellaneous/testing/index.md)
##### [Pruebas con SQLite](core/miscellaneous/testing/sqlite.md)
##### [Pruebas con InMemory](core/miscellaneous/testing/in-memory.md)
#### [Configuración de un DbContext](core/miscellaneous/configuring-dbcontext.md)
#### [Actualización de 1.0 RC1 a RC2](core/miscellaneous/rc1-rc2-upgrade.md)
#### [Actualización de 1.0 RC2 a RTM](core/miscellaneous/rc2-rtm-upgrade.md)
#### [Actualización a EF Core 2.0](core/miscellaneous/1x-2x-upgrade.md)

### [⤤ Referencia de API](https://docs.microsoft.com/dotnet/api/?view=efcore-2.0)

## [Entity Framework 6](ef6/index.md)
### [⤤ Documentación](http://msdn.com/data/ef)
### [⤤ Referencia de API](https://msdn.microsoft.com/library/dn223258.aspx)