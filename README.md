# Business Connectors

The most elegant solution is usually the simplest. This repository only contains a Microsoft Excel workbook which uses Power Query to get the lists of standard and premium connectors available in Power Automate, Power Apps, and Azure Logic Apps from the Microsoft Docs website.

This offers an easy way of looking at the connectors available, and sharing with teams instead of having to read through Microsoft Docs.

Using this file is at your own risk, and it is always recommended to scan any file downloaded.

If you are hesitent to download, I have provided the Power Query code below to recreate in either Microsoft Excel, or even Power BI.


### Standard Connectors


```

let
    Source = Web.Page(Web.Contents("https://docs.microsoft.com/en-us/connectors/connector-reference/connector-reference-standard-connectors")),
    Data1 = Source{1}[Data],

    #"Changed Type" = Table.TransformColumnTypes(Data1,{{"Column1", type text}, {"Column2", type text}, {"Column3", type text}, {"Column4", type text}}),

    #"Remove provider" = Table.TransformColumns(#"Changed Type", {{"Column1", each Text.BeforeDelimiter(_, "By:"), type text}, {"Column2", each Text.BeforeDelimiter(_, "By:"), type text}, {"Column3", each Text.BeforeDelimiter(_, "By:"), type text}, {"Column4", each Text.BeforeDelimiter(_, "By:"), type text}}),

    #"Clean up whitespace" = Table.TransformColumns(#"Remove provider",{{"Column1", Text.Clean, type text}, {"Column2", Text.Clean, type text}, {"Column3", Text.Clean, type text}, {"Column4", Text.Clean, type text}}),
    #"Order connectors" = Table.UnpivotOtherColumns(#"Clean up whitespace", {}, "Attribute", "Value"),

    #"Removed Columns" = Table.RemoveColumns(#"Order connectors",{"Attribute"}),
    #"Add conenctor type" = Table.AddColumn(#"Removed Columns", "Type", each "Standard"),

    #"Renamed Columns" = Table.RenameColumns(#"Add conenctor type",{{"Value", "Connector Name"}}),

    #"Reordered Columns" = Table.ReorderColumns(#"Renamed Columns",{"Type", "Connector Name"})
in
    #"Reordered Columns"

```

### Premium Connectors


```

let
    Source = Web.Page(Web.Contents("https://docs.microsoft.com/en-us/connectors/connector-reference/connector-reference-premium-connectors")),

    Data1 = Source{1}[Data],

    #"Changed Type" = Table.TransformColumnTypes(Data1,{{"Column1", type text}, {"Column2", type text}, {"Column3", type text}, {"Column4", type text}}),

    #"Remove provider" = Table.TransformColumns(#"Changed Type", {{"Column1", each Text.BeforeDelimiter(_, "By:"), type text}, {"Column2", each Text.BeforeDelimiter(_, "By:"), type text}, {"Column3", each Text.BeforeDelimiter(_, "By:"), type text}, {"Column4", each Text.BeforeDelimiter(_, "By:"), type text}}),

    #"Clean up whitespace" = Table.TransformColumns(#"Remove provider",{{"Column1", Text.Clean, type text}, {"Column2", Text.Clean, type text}, {"Column3", Text.Clean, type text}, {"Column4", Text.Clean, type text}}),
    #"Order connectors" = Table.UnpivotOtherColumns(#"Clean up whitespace", {}, "Attribute", "Value"),

    #"Removed Columns" = Table.RemoveColumns(#"Order connectors",{"Attribute"}),
    #"Add conenctor type" = Table.AddColumn(#"Removed Columns", "Type", each "Premium"),

    #"Renamed Columns" = Table.RenameColumns(#"Add conenctor type",{{"Value", "Connector Name"}}),

    #"Reordered Columns" = Table.ReorderColumns(#"Renamed Columns",{"Type", "Connector Name"})
in
    #"Reordered Columns"

```

### Combined List

```

let
    Source = Table.Combine({#"List of Standard Connectors", #"List of Premium Connectors"})
in
    Source

```
