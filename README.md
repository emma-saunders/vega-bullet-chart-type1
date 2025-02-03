# vega-bullet-chart-type1
https://stargate.swissre.com/workspace/module/edit/ri.workshop.main.module.c8b5c209-aca4-45cf-afb9-0037bbb4caed
![image](https://github.com/user-attachments/assets/c84c2484-314e-48f6-86c9-3d227fe90195)


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
The following code specifies the data inputs. The first two, claims_data and revenue_data, must match the INPUT DATA specified in the Vega widget right hand side panel. The third one, loss-ratios, divides claims by revenue and is hardcoded so does not draw upon a separate data source:
```
    "data": [
      {
        "name": "claims",
        "source": "claims_data"
      },
      {
        "name": "revenue",
        "source": "revenue_data"
      },
      {
        "name": "loss_ratios",
        "source": "claims",
        "transform": [
            {
                "type": "lookup",
                "from": "revenue",
                "key": "gender",
                "fields": ["gender"],
                "values": ["sum_revenue"]
            },
            {
                "type": "formula",
                "as": "loss_ratio",
                "expr": "(datum.sum_claims_payment / datum.sum_revenue) * 100"
            }
        ]
    }
    
    ],
```
The `marks` section tells the browser where to place the shapes, and how often to repeat them. There are four objects within the marks array:
- The first object says to add a rect element based on revenue: it should run from an xScaled value of 0 to 0.8 x total revenue and be green
- The second object says to add a rect element based on revenue: this one should be red and run from 0.8 x revenue to 1 x revenue
- The third object adds a rect element for claims on top. The order of these objects in the array denotes which is added first, and therefore which can be seen.
- The final object, a text element, adds the loss ratio at the end of whichever is wider (claims or revenue)

```
"marks": [
    {
        "type": "rect",
        "from": {"data": "revenue"},
        "encode": {
            "enter": { "y": {"scale": "yscale", "field": "gender"}, "height": {"scale": "yscale", "band": 1}, "x": {"scale": "xscale", "value": 0}, "x2": {"scale": "xscale", "signal": "datum.sum_revenue * 0.8"}, "fill": {"value": "#63B590"}, "opacity": {"value": 1} },
            "update": { "tooltip": {"signal": "{'Gender': datum.gender, 'Total Revenue': datum.sum_revenue}"} }
        }
    },
"marks": [
    {
        "type": "rect",
        "from": {"data": "revenue"},
        "encode": {
            "enter": ...,
            "update": ...
        }
    },
    {
        "type": "rect",
        "from": {"data": "revenue"},
        "encode": {
            "enter": ...,
            "update": ...
        }
    },
    {
        "type": "rect",
        "from": {"data": "claims"},
        "encode": {
            "enter": ...,
            "update": ...
        }
    },
    {
        "type": "text",
        "from": {"data": "loss_ratio"},
        "encode": {
            "enter": ...,
            "update": ...
        }
    },
```
