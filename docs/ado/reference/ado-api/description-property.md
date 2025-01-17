---
title: "Description Property"
description: Learn about the description property of the Error object in ADO that returns a string value containing a description of the error.
author: rothja
ms.author: jroth
ms.date: "01/19/2017"
ms.prod: sql
ms.technology: ado
ms.topic: reference
f1_keywords:
  - "Error::Description"
  - "Error::GetDescription"
  - "Error::get_Description"
helpviewer_keywords:
  - "Description property"
apitype: "COM"
---
# Description Property
Describes an [Error](../../../ado/reference/ado-api/error-object.md) object.  
  
## Return Value  
 Returns a **String** value that contains a description of the error.  
  
## Remarks  
 Use the **Description** property to obtain a short description of the error. Display this property to alert the user to an error that you cannot or do not want to handle. The string will come from either ADO or a provider.  
  
 Providers are responsible for passing specific error text to ADO. ADO adds an [Error](../../../ado/reference/ado-api/error-object.md) object to the **Errors** collection for each provider error or warning it receives. Enumerate the **Errors** collection to trace the errors that the provider passes.  
  
## Applies To  
 [Error Object](../../../ado/reference/ado-api/error-object.md)  
  
## See Also  
 [Description, HelpContext, HelpFile, NativeError, Number, Source, and SQLState Properties Example (VB)](../../../ado/reference/ado-api/description-helpcontext-helpfile-nativeerror-number-source-example-vb.md)   
 [Description, HelpContext, HelpFile, NativeError, Number, Source, and SQLState Properties Example (VC++)](../../../ado/reference/ado-api/description-helpcontext-helpfile-nativeerror-number-source-example-vc.md)   
 [HelpContext, HelpFile Properties](../../../ado/reference/ado-api/helpcontext-helpfile-properties.md)   
 [Number Property (ADO)](../../../ado/reference/ado-api/number-property-ado.md)   
 [Source Property (ADO Error)](../../../ado/reference/ado-api/source-property-ado-error.md)
