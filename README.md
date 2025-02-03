# vega-bullet-chart-type1
https://stargate.swissre.com/workspace/module/edit/ri.workshop.main.module.2d0ecc3f-5299-46d8-a695-f32722a02c77
![image](https://github.com/user-attachments/assets/9cfe8de4-7bb2-4afd-bbef-4af2210ccf30)

## Features
- Responsive
- Tooltips show claims and revenue (formatting could be improved)
- Pulls data from Foundry (must be added via Input data section) and updates in real time
- Dynamic loss ratio calculation based on values shown in chart, meaning loss ratio can never diverge from constituent parts
- Revenue hardcoded to highlight "danger zone" with red/green colouring. Can be removed:
  - Green is 80% of revenue
  - Red is 20% of revenue
- Claims data
  - Shown as grey overlay bar, clearly indicating if claims are in "danger zone" or indeed exceed revenue
  - Loss ratio shown in text at end of claims bar

## How to implement
- Within a Foundry Module, add a Vega Widget (not Vega lite)
- With the widget selected, scroll to the bottom of the right hand panel, to Editor. Paste the JSON code there.
- This json schema expects two data sources configured via INPUT DATA (see screenshots)
  - *claims_data* grouped by *gender* --> aggregated with sum to *sum_claims_payment*
  - *revenue_data* grouped by *gender* --> aggregated with sum to *sum_revenue*
- You need to select and configure your own claims and revenue data. If you rename these fields (or change the case) you must edit the JSON
![image](https://github.com/user-attachments/assets/657a7e3b-19b6-4b6d-a744-039746e5cf59)
![image](https://github.com/user-attachments/assets/b78fcd66-7006-4a62-91c4-bed7e7ba6fef)

## How to understand the code
This makes the chart size responsive. It will never be less than 200 px high and 300 px wide. It will scale to 0.8 x container height and 0.9 x container width.
```
    "autosize": {
        "type": "fit",
        "contains": "padding"
      },
      "signals": [
        {
            "name": "chartWidth",
            "update": "max(300, containerSize()[0] * 0.9)",
            "on": [{"events": "window:resize", "update": "max(300, containerSize()[0] * 0.9)"}]
          },          
        {
          "name": "chartHeight",
          "update": "max(200, containerSize()[1] * 0.8)",
          "on": [{"events": "window:resize", "update": "max(200, containerSize()[1]) * 0.8"}]
        }
      ],
      "width": {"signal": "chartWidth"},
      "height": {"signal": "chartHeight"},
```
