---
title: Escribir a un proveedor de base de datos - Core EF
author: anmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 1165e2ec-e421-43fc-92ab-d92f9ab3c494
ms.technology: entity-framework-core
uid: core/providers/writing-a-provider
ms.openlocfilehash: 9d6d3748a9097b3b8eeee2a8a516c53f3b2afa58
ms.sourcegitcommit: 01a75cd483c1943ddd6f82af971f07abde20912e
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/27/2017
---
# <a name="writing-a-database-provider"></a>Escribir un proveedor de base de datos

Para obtener información sobre cómo escribir un proveedor de base de datos de Entity Framework Core, vea [por lo que desea escribir un proveedor de EF Core](https://blog.oneunicorn.com/2016/11/11/so-you-want-to-write-an-ef-core-provider/) por [Arthur Vickers](https://github.com/ajcvickers).

La base de código de núcleo de EF es código abierto y contiene varios proveedores de base de datos que pueden usarse como referencia. Puede encontrar el código fuente en https://github.com/aspnet/EntityFramework.

## <a name="the-providers-beware-label"></a>La etiqueta de proveedores beware

Una vez que comenzar a trabajar en un proveedor, espere a que el [ `providers-beware` ](https://github.com/aspnet/EntityFramework/labels/providers-beware) etiqueta en nuestros GitHub problemas y solicitudes de extracción. Esta etiqueta se usa para identificar los cambios que puedan afectar a los escritores de proveedores.

## <a name="suggested-naming-of-third-party-providers"></a>Sugiere la nomenclatura de los proveedores de terceros

Se recomienda utilizar la siguiente nomenclatura para los paquetes de NuGet. Esto es coherente con los nombres de los paquetes entregados por el equipo de EF Core.

`<Optional project/company name>.EntityFrameworkCore.<Database engine name>`

Por ejemplo:
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Npgsql.EntityFrameworkCore.PostgreSQL`
* `EntityFrameworkCore.SqlServerCompact40`