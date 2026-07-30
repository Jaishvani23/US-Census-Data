DAX Document

Total US Population = 
CALCULATE(
    SUM('Data'[Total_Estimate]),
    'Data'[Label] = "Civilian noninstitutionalized population"
)

National Insured Rate = 
CALCULATE(
    SUM('Data'[Insured_Percent]),
    'Data'[Label] = "Civilian noninstitutionalized population"
)

National Uninsured Rate = 
CALCULATE(
    SUM('Data'[Uninsured_Percent]),
    'Data'[Label] = "Civilian noninstitutionalized population"
)

Selected Segment Insured % = SELECTEDVALUE('Data'[Insured_Percent])
Selected Segment Uninsured % = SELECTEDVALUE('Data'[Uninsured_Percent])
Gap = [Selected Segment insured %] - [Selected Segment Uninsured %]

NO Risk Segment Name = 
CALCULATE(
    SELECTEDVALUE('Data'[Label]),
    FILTER(
        ALLSELECTED('Data'),
        'Data'[Uninsured_Percent] = [Low Risk Segment Rate]
    )
)

Highest Risk Segment Name = 
CALCULATE(
    SELECTEDVALUE('Data'[Label]),
    FILTER(
        ALLSELECTED('Data'),
        'Data'[Uninsured_Percent] = [Highest Risk Segment Rate]
    )
)
