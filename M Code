let
    Source = Excel.Workbook(File.Contents("C:\Users\HLBKFZR\OneDrive - Solventum\Documents\My Git Projects\US census Data\Selected Characteristics of Health Insurance Coverage in the United States.xlsx"), null, true),
    Data_Sheet = Source{[Item="Data",Kind="Sheet"]}[Data],

    #"Changed Type" = Table.TransformColumnTypes(Data_Sheet,{{"Column1", type text}, {"Column2", type text}, {"Column3", type text}, {"Column4", type text}, {"Column5", type text}, {"Column6", type text}, {"Column7", type text}, {"Column8", type text}, {"Column9", type text}, {"Column10", type text}, {"Column11", type text}}),
    #"Removed Top Rows" = Table.Skip(#"Changed Type",2),
    #"Promoted Headers" = Table.PromoteHeaders(#"Removed Top Rows", [PromoteAllScalars=true]),

    #"Renamed Columns" = Table.RenameColumns(#"Promoted Headers",{
        {"Label", "Label"},
        {"Estimate", "Total_Estimate"},
        {"Margin of Error", "Total_MOE"},
        {"Estimate_1", "Insured_Estimate"},
        {"Margin of Error_2", "Insured_MOE"},
        {"Estimate_3", "Insured_Percent"},
        {"Margin of Error_4", "Insured_Percent_MOE"},
        {"Estimate_5", "Uninsured_Estimate"},
        {"Margin of Error_6", "Uninsured_MOE"},
        {"Estimate_7", "Uninsured_Percent"},
        {"Margin of Error_8", "Uninsured_Percent_MOE"}
    }),

    #"Trimmed Label" = Table.TransformColumns(#"Renamed Columns", {{"Label", Text.Trim}}),

    // Flag section header rows (e.g. "AGE", "SEX") before any numeric conversion
    #"Added IsHeaderRow" = Table.AddColumn(#"Trimmed Label", "IsHeaderRow", each 
        [Total_Estimate] = null or [Total_Estimate] = ""),
    #"Added Category" = Table.AddColumn(#"Added IsHeaderRow", "Category", each 
        if [IsHeaderRow] then [Label] else null),
    #"Filled Down Category" = Table.FillDown(#"Added Category", {"Category"}),
    #"Removed Header Rows" = Table.SelectRows(#"Filled Down Category", each [IsHeaderRow] = false),
    #"Removed IsHeaderRow Column" = Table.RemoveColumns(#"Removed Header Rows", {"IsHeaderRow"}),

    // Clean Margin of Error columns: strip "±" symbol
    #"Cleaned MOE Symbols" = Table.TransformColumns(#"Removed IsHeaderRow Column", {
        {"Total_MOE", each Text.Replace(_, "±", ""), type text},
        {"Insured_MOE", each Text.Replace(_, "±", ""), type text},
        {"Insured_Percent_MOE", each Text.Replace(_, "±", ""), type text},
        {"Uninsured_MOE", each Text.Replace(_, "±", ""), type text},
        {"Uninsured_Percent_MOE", each Text.Replace(_, "±", ""), type text}
    }),

    // Clean Percent columns: strip "%" symbol so they convert cleanly
    #"Cleaned Percent Symbols" = Table.TransformColumns(#"Cleaned MOE Symbols", {
        {"Insured_Percent", each Text.Replace(_, "%", ""), type text},
        {"Uninsured_Percent", each Text.Replace(_, "%", ""), type text}
    }),

    // Locale-aware numeric conversion (handles comma thousand separators)
    #"Changed Type Final" = Table.TransformColumnTypes(#"Cleaned Percent Symbols",{
        {"Total_Estimate", Int64.Type},
        {"Total_MOE", Int64.Type},
        {"Insured_Estimate", Int64.Type},
        {"Insured_MOE", Int64.Type},
        {"Insured_Percent", type number},
        {"Insured_Percent_MOE", type number},
        {"Uninsured_Estimate", Int64.Type},
        {"Uninsured_MOE", Int64.Type},
        {"Uninsured_Percent", type number},
        {"Uninsured_Percent_MOE", type number}
    }, "en-US"),

    // Reorder so Category sits next to Label
    #"Reordered Columns" = Table.ReorderColumns(#"Changed Type Final",
        {"Category", "Label", "Total_Estimate", "Total_MOE", "Insured_Estimate", "Insured_MOE",
         "Insured_Percent", "Insured_Percent_MOE", "Uninsured_Estimate", "Uninsured_MOE",
         "Uninsured_Percent", "Uninsured_Percent_MOE"})
in
    #"Reordered Columns"
