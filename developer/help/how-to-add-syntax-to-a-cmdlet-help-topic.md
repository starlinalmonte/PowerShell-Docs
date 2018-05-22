---
title: "How to Add Syntax to a Cmdlet Help Topic | Microsoft Docs"
ms.custom: ""
ms.date: "09/12/2016"
ms.reviewer: ""
ms.suite: ""
ms.tgt_pltfrm: ""
ms.topic: "article"
ms.assetid: d0c6d03f-1c1a-43d8-928e-e3290e90e0bc
caps.latest.revision: 5
---
# How to Add Syntax to a Cmdlet Help Topic
This section describes how to add syntax information to a Windows PowerShell® cmdlet Help topic.

 Topics in this section include the following: The syntax diagram is a structured depiction that shows the user how to write a valid command. The diagram contains the required and optional components of the command, including the parameters. In the cmdlet Help topic, there is a syntax diagram for each parameter set used by the cmdlet.

-   [Parameter Attributes](#ParameterAttributes)

-   [Parameter Value Attributes](#ParameterValueAttributes)

-   [Gathering Syntax Information](#GatheringSyntaxInformation)

-   [Coding the Syntax Diagram XML](#codesyntax)

## Things to Know About the Syntax Diagram in Cmdlet Help
 Before you start to code the XML for the syntax diagram in the cmdlet Help file, read this section to get a clear picture of the kind of data you need to provide, such as the parameter attributes, and how that data is displayed in the syntax diagram..

###  <a name="ParameterAttributes"></a> Parameter Attributes
 Windows PowerShell® needs to know the attributes of each parameter of the cmdlet to generate the syntax diagram and parameter list. The following table shows the attributes for a set of parameters (Name, ID, List, and Type). As an aid in coding the syntax diagram, create a similar table for all the parameters of your cmdlet. You may need a separate table for each parameter set that your cmdlet uses.

|Parameter Name|Required|Position|Pipeline Input|Globbing|Parameter Type|
|--------------------|--------------|--------------|--------------------|--------------|--------------------|
|Name|true|1|True (by value,property name)|true|System.String|
|ID|true|named|True (by property name)|false|System.Int32|
|List|false|named|false|false|SwitchParameter|
|Type|false|named|false|false|Enumeration Type|

 Here is a brief description of the different parameter attributes.

-   Required

    -   If true, the parameter must appear in all commands that use the parameter set.

    -   If false, the parameter is optional in all commands that use the parameter set.

-   Position

    -   If named, the parameter name is required.

    -   If positional, the parameter name is optional. When it is omitted, the parameter value must be in the specified position in the command. For example, if the value is position="1", the parameter value must be the first or only unnamed parameter value in the command.

-   Pipeline Input

    -   If true (ByValue), you can pipe input to the parameter. The input is associated with ("bound to") the parameter even if the property name and the object type do not match the expected type. The Windows PowerShell® parameter binding components try to convert the input to the correct type and fail the command only when the type cannot be converted. Only one parameter in a parameter set can be associated by value.

    -   If true (ByPropertyName), you can pipe input to the parameter. However, the input is associated with the parameter only when the parameter name matches the name of a property of the input object. For example, if the parameter name is `Path`, objects piped to the cmdlet are associated with that parameter only when the object has a property named path.

    -   If true (ByValue, ByPropertyName), you can pipe input to the parameter either by property name or by value. Only one parameter in a parameter set can be associated by value.

    -   If false, you cannot pipe input to this parameter.

-   Globbing

    -   If true, the text that the user types for the parameter value can include wildcard characters.

    -   If false, the text that the user types for the parameter value cannot include wildcard characters.

###  <a name="ParameterValueAttributes"></a> Parameter Value Attributes
 The attributes listed here are associated with the value of a parameter, not the actual parameter.

-   Required

    -   If true, the specified value must be used whenever using the parameter in a command.

    -   If false, the parameter value is optional. Typically, a value is optional only when it is one of several valid values for a parameter, such as in an enumerated type.

 The Required attribute of a parameter value is different from the Required attribute of a parameter.

 The required attribute of a parameter indicates whether the parameter (and its value) must be included when invoking the cmdlet. In contrast, the required attribute of a parameter value is used only when the parameter is included in the command. It indicates whether that particular value must be used with the parameter.

 Typically, parameter values that are placeholders are required and parameter values that are literal are not required, because they are one of several values that might be used with the parameter.

###  <a name="GatheringSyntaxInformation"></a> Gathering Syntax Information
 Here is a procedure for writing a syntax diagram. Writing a syntax diagram for your cmdlet may help you to identify what information you need to code the XML. The cmdlet Get-Tech is used as an example.

1.  Start with the cmdlet name.

    ```
    SYNTAX
        Get-Tech
    ```

2.  List all the parameters of the cmdlet. Type a dash (also known as a "dash" or "minus sign" (ASCII 45) before each parameter name. Separate the parameters into parameter sets (some cmdlets may have only one parameter set). In this example the Get-Tech cmdlet has two parameter sets.

    ```
    SYNTAX
        Get-Tech -name -type
        Get-Tech -ID -list -type
    ```

     Start each parameter set with the cmdlet name.

     List the default parameter set first. The default parameter is specified by the cmdlet class.

     For each parameter set, list its unique parameter first, unless there are positional parameters that must appear first. In the previous example, the Name and ID parameters are unique parameters for the two parameter sets (each parameter set must have one parameter that is unique to that parameter set). This makes it easier for users to identify what parameter they need to supply for the parameter set.

     List the parameters in the order that they should appear in the command. If the order does not matter, list related parameters together, or list the most frequently used parameters first.

     Be sure to list the WhatIf and Confirm parameters if the cmdlet supports ShouldProcess.

     Do not list the common parameters (such as Verbose, Debug, and ErrorAction) in your syntax diagram. The Get-Help cmdlet adds that information for you when it displays the Help topic.

3.  Add the parameter values. In Windows PowerShell®, parameter values are represented by their .NET type. However, the type name can be abbreviated, such as "string" for System.String.

    ```
    SYNTAX
        Get-Tech -name string -type basic advanced
        Get-Tech -ID int -list -type basic advanced
    ```

     Abbreviate types as long as their meaning is clear, such as "string" for System.String and "int" for System.Int32.

     List all values of enumerations, such as the -type parameter in the previous example, wich can be set to "basic" or "advanced".

     Switch parameters, such as -list in the previous example, do not have values.

4.  Add angle brackets to parameters values that are placeholder, as compared to parameter values that are literals.

    ```
    SYNTAX
        Get-Tech -name <string> -type basic advanced
        Get-Tech -ID <int> -list -type basic advanced
    ```

5.  Enclose optional parameters and their vales in square brackets.

    ```
    SYNTAX
        Get-Tech -name <string> [-type basic advanced]
        Get-Tech -ID <int> [-list] [-type basic advanced]
    ```

6.  Enclose optional parameters names (for positional parameters) in square brackets. The name for parameters that are positional, such as the Name parameter in the following example, do not have to be included in the command.

    ```
    SYNTAX
        Get-Tech [-name] <string> [-type basic advanced]
        Get-Tech -ID <int> [-list] [-type basic advanced]
    ```

7.  If a parameter value can contain multiple values, such as a list of names in the Name parameter, add a pair of square brackets directly following the parameter value.

    ```
    SYNTAX
        Get-Tech [-name] <string[]> [-type basic advanced]
        Get-Tech -ID <int[]> [-list] [-type basic advanced]
    ```

8.  If the user can choose from from parameters or parameter values, such as the Type parameter, enclose the choices in curly brackets and separate them with the exclusive OR symbol(&#124;).

    ```
    SYNTAX
        Get-Tech [-name] <string[]> [-type {basic | advanced}]
        Get-Tech -ID <int[]> [-list] [-type {basic | advanced}]
    ```

9. If the parameter value must use specific formatting, such as quotation marks or parentheses, show the format in the syntax.

    ```
    SYNTAX
        Get-Tech [-name] <"string[]"> [-type {basic | advanced}]
        Get-Tech -ID <int[]> [-list] [-type {basic | advanced}]
    ```

##  <a name="codesyntax"></a> Coding the Syntax Diagram XML
 The syntax node of the XML begins immediately after the description node, which ends with the \</maml:description> tag. For information about gathering the data used in the syntax diagram, see [Gathering Syntax Information](#GatheringSyntaxInformation).

### Adding a Syntax Node
 The syntax diagram displayed in the cmdlet Help topic is generated from the data in the syntax node of the XML. The syntax node is enclosed in a pair if \<command:syntax> tags. With each parameter set of the cmdlet enclosed in a pair of \<command:syntaxitem> tags. There is no limit to the number of \<command:syntaxitem> tags that you can add.

 The following example shows a syntax node that has syntax item nodes for two parameter sets.

```
<command:syntax>
  <command:syntaxItem>
    ...
    <!--Parameter Set 1 (default parameter set) parameters go here-->
    ...
  </command:syntaxItem>
  <command:syntaxItem>
    ...
    <!--Parameter Set 2 parameters go here-->
    ...
  </command:syntaxItem>
</command:syntax>
```

### Adding the Cmdlet Name to the Parameter Set Data
 Each parameter set of the cmdlet is specified in a syntax item node. Each syntax item node begins with a pair of \<maml:name> tags that include the name of the cmdlet.

 The following example includes a syntax node that has syntax item nodes for two parameter sets.

```
<command:syntax>
  <command:syntaxItem>
    <maml:name>Cmdlet-Name</maml:name>
  </command:syntaxItem>
  <command:syntaxItem>
    <maml:name>Cmdlet-Name</maml:name>
  </command:syntaxItem>
</command:syntax>
```

### Adding Parameters
 Each parameter added to the syntax item node is specified within a pair of \<command:parameter> tags. You need a pair of \<command:parameter> tags for each parameter included in the parameter set, with the exception of the common parameters that are provided by Windows PowerShell®.

 The attributes of the opening \<command:parameter> tag determine how the parameter appears in the syntax diagram. For information on parameter attributes, see [Parameter Attributes](#ParameterAttributes).

> [!NOTE]
>  The \<command:parameter> tag supports a child element \<maml:description> whose content is never displayed. The parameter descriptions are specified in the parameter node of the XML. To avoid inconsistencies between the information in the syntax item bodes and the parameter node, omit the (\<maml:description> or leave it empty.

 The following example includes a syntax item node for a parameter set with two parameters.

```
<command:syntaxItem>
  <maml:name>Cmdlet-Name</maml:name>
  <command:parameter required="true" globbing="true"
    pipelineInput="true (ByValue)" position="1">
    <maml:name>ParameterName1</maml:name>
    <command:parameterValue required="true">
      string[]
    </command:parameterValue>
  </command:parameter>
  <command:parameter required="true" globbing="true"
    pipelineInput="true (ByPropertyName)">
    <maml:name>ParameterName2</maml:name>
    <command:parameterValue required="true">
      int32[]
    </command:parameterValue>
  </command:parameter>
</command:syntaxItem>
```