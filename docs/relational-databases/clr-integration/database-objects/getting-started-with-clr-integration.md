---
title: "Getting Started with CLR Integration"
description: This article describes the namespaces and libraries required to compile database objects using the Microsoft SQL Server integration with the .NET Framework CLR.
author: rwestMSFT
ms.author: randolphwest
ms.date: "08/02/2016"
ms.prod: sql
ms.technology: clr
ms.topic: quickstart
ms.custom: intro-quickstart
helpviewer_keywords:
  - "database objects [CLR integration]"
  - "namespaces [CLR integration]"
  - "database objects [CLR integration], about CLR integration"
  - "stored procedures [CLR integration]"
  - "common language runtime [SQL Server], about CLR integration"
  - "building database objects [CLR integration], about CLR integration"
  - "common language runtime [SQL Server], stored procedures"
  - "common language runtime [SQL Server], namespaces"
  - "Hello World example [CLR integration]"
  - "library [CLR integration]"
dev_langs:
  - "TSQL"
  - "VB"
  - "CSharp"
ms.assetid: c73e628a-f54a-411a-bfe3-6dae519316cc
---
# Getting Started with CLR Integration

[!INCLUDE [SQL Server](../../../includes/applies-to-version/sqlserver.md)]

This topic provides an overview of the namespaces and libraries required to compile database objects using the [!INCLUDE[msCoName](../../../includes/msconame-md.md)] [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] integration with the .NET Framework common language runtime (CLR). The topic also shows you how to write, compile, and run a simple CLR stored procedure written in [!INCLUDE[msCoName](../../../includes/msconame-md.md)] Visual C#.  
  
## Required Namespaces  

The components required to develop basic CLR database objects are installed with [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)]. CLR integration functionality is exposed in an assembly called system.data.dll, which is part of the .NET Framework. This assembly can be found in the Global Assembly Cache (GAC) as well as in the .NET Framework directory. A reference to this assembly is typically added automatically by both command line tools and [!INCLUDE[msCoName](../../../includes/msconame-md.md)] Visual Studio, so there is no need to add it manually.  
  
The system.data.dll assembly contains the following namespaces, which are required for compiling CLR database objects:  
  
- `System.Data`  
- `System.Data.Sql`  
- `Microsoft.SqlServer.Server`  
- `System.Data.SqlTypes`  

> [!TIP]
> Loading CLR database objects on Linux is supported, but they must be built with the .NET Framework (SQL Server CLR integration does not support .NET Core). Also, CLR assemblies with the EXTERNAL_ACCESS or UNSAFE permission set are not supported on Linux.

## Writing A Simple "Hello World" Stored Procedure  

Copy and paste the following Visual C# or [!INCLUDE[msCoName](../../../includes/msconame-md.md)] Visual Basic code into a text editor, and save it in a file named "helloworld.cs" or "helloworld.vb".  
  
```csharp  
using System;  
using System.Data;  
using Microsoft.SqlServer.Server;  
using System.Data.SqlTypes;  
  
public class HelloWorldProc  
{  
    [Microsoft.SqlServer.Server.SqlProcedure]  
    public static void HelloWorld(out string text)  
    {  
        SqlContext.Pipe.Send("Hello world!" + Environment.NewLine);  
        text = "Hello world!";  
    }  
}  
```  
  
```vb  
Imports System  
Imports System.Data  
Imports Microsoft.SqlServer.Server  
Imports System.Data.SqlTypes  
Imports System.Runtime.InteropServices  
  
Public Class HelloWorldProc  
    \<Microsoft.SqlServer.Server.SqlProcedure> _   
    Public Shared  Sub HelloWorld(\<Out()> ByRef text as String)  
        SqlContext.Pipe.Send("Hello world!" & Environment.NewLine)  
        text = "Hello world!"  
    End Sub  
End Class  
  
```  
  
This simple program contains a single static method on a public class. This method uses two new classes, **[SqlContext](/dotnet/api/microsoft.sqlserver.server.sqlcontext)** and **[SqlPipe](/dotnet/api/microsoft.sqlserver.server.sqlpipe)**, for creating managed database objects to output a simple text message. The method also assigns the string "Hello world!" as the value of an out parameter. This method can be declared as a stored procedure in [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)], and then run in the same manner as a [!INCLUDE[tsql](../../../includes/tsql-md.md)] stored procedure.  
  
Compile this program as a library, load it into [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)], and run it as a stored procedure.  
  
## Compile the "Hello World" stored procedure  

[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] installs the [!INCLUDE[msCoName](../../../includes/msconame-md.md)] .NET Framework redistribution files by default. These files include csc.exe and vbc.exe, the command-line compilers for Visual C# and Visual Basic programs. In order to compile our sample, you must modify your path variable to point to the directory containing csc.exe or vbc.exe. The following is the default installation path of the .NET Framework.  
  
`C:\Windows\Microsoft.NET\Framework\(version)`  
  
Version contains the version number of the installed .NET Framework redistributable. For example:  
  
`C:\Windows\Microsoft.NET\Framework\v4.6.1`

Once you have added the .NET Framework directory to your path, you can compile the sample stored procedure into an assembly with the following command. The **/target** option allows you to compile it into an assembly.  
  
For Visual C# source files:  
  
`csc /target:library helloworld.cs`  
  
 For Visual Basic source files:  
  
`vbc /target:library helloworld.vb`  
  
These commands launch the Visual C# or Visual Basic compiler using the /target option to specify building a library DLL.  
  
## Loading and Running the "Hello World" Stored Procedure in SQL Server  

Once the sample procedure has successfully compiled, you can test it in [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)]. To do this, open [!INCLUDE[ssManStudioFull](../../../includes/ssmanstudiofull-md.md)] and create a new query, connecting to a suitable test database (for example, the AdventureWorks sample database).  
  
The ability to execute common language runtime (CLR) code is set to OFF by default in [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)]. The CLR code can be enabled by using the **sp_configure** system stored procedure. For more information, see [Enabling CLR Integration](../../../relational-databases/clr-integration/clr-integration-enabling.md).  
  
We will need to create the assembly so we can access the stored procedure. For this example, we will assume that you have created the helloworld.dll assembly in the C:\ directory. Add the following [!INCLUDE[tsql](../../../includes/tsql-md.md)] statement to your query.  
  
`CREATE ASSEMBLY helloworld from 'c:\helloworld.dll' WITH PERMISSION_SET = SAFE`  
  
Once the assembly has been created, we can now access our HelloWorld method by using the create procedure statement. We will call our stored procedure "hello":  
  
```sql
CREATE PROCEDURE hello  
@i nchar(25) OUTPUT  
AS  
EXTERNAL NAME helloworld.HelloWorldProc.HelloWorld  
-- if the HelloWorldProc class is inside a namespace (called MyNS),  
-- the last line in the create procedure statement would be  
-- EXTERNAL NAME helloworld.[MyNS.HelloWorldProc].HelloWorld  
```  
  
Once the procedure has been created, it can be run just like a normal stored procedure written in [!INCLUDE[tsql](../../../includes/tsql-md.md)]. Execute the following command:  
  
```sql
DECLARE @J nchar(25)  
EXEC hello @J out  
PRINT @J  
```  
  
This should result in the following output in the [!INCLUDE[ssManStudioFull](../../../includes/ssmanstudiofull-md.md)] messages window.  
  
```
Hello world!  
Hello world!  
```  
  
## Removing the "Hello World" Stored Procedure Sample  

When you are finished running the sample stored procedure, you can remove the procedure and the assembly from your test database.  
  
First, remove the procedure using the drop procedure command.  
  
```sql
IF EXISTS (SELECT name FROM sysobjects WHERE name = 'hello')  
   drop procedure hello  
```  
  
Once the procedure has been dropped, you can remove the assembly containing your sample code.  
  
```sql
IF EXISTS (SELECT name FROM sys.assemblies WHERE name = 'helloworld')  
   drop assembly helloworld  
```  
  
## Next steps

For more information about CLR integration in SQL Server, see the following articles:

- [CLR Stored Procedures](/dotnet/framework/data/adonet/sql/clr-stored-procedures)
- [SQL Server In-Process Specific Extensions to ADO.NET](../../../relational-databases/clr-integration-data-access-in-process-ado-net/sql-server-in-process-specific-extensions-to-ado-net.md)
- [Debugging CLR Database Objects](../../../relational-databases/clr-integration/debugging-clr-database-objects.md)
- [CLR Integration Security](../../../relational-databases/clr-integration/security/clr-integration-security.md)
