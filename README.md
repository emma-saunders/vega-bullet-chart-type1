# vega-bullet-chart-type1
![image](https://github.com/user-attachments/assets/9cfe8de4-7bb2-4afd-bbef-4af2210ccf30)

## Features
- Responsive
- Tooltips show claims and revenue (formatting could be improved)
- Pulls data from Foundry (must be added via Input data section) and updates in real time
- Dynamic loss ratio calculation based on values shown in chart, meaning loss ratio can never diverge from constituent parts
- Revenue hardcoded to highlight "danger zone" with red/green colouring. Can be removed:
  - Green is 80% of revenue
  - Red is 20% of revenue
- Claims data (CURRENTLY USING WRONG DATA and hardcoded divided by 4 to look reasonable: "expr": "datum.sum_claims_payment / 4")
  - Shown as grey overlay bar, clearly indicating if claims are in "danger zone" or indeed exceed revenue
  - Loss ratio shown in text at end of claims bar

## How to implement
- Within a Foundry Module, add a Vega Widget (not Vega lite)
- With the widget selected, scroll to the bottom of the right hand panel, to Editor. Copy - paste the JSON code there.
- This json schema expects two data sources configured via INPUT DATA (see screenshots)
  - claims_data grouped by gender
  - revenue_data grouped by gender
- You need to select and configure your own claims and revenue data. If you rename these fields (or change the case) you must edit the JSON
![image](https://github.com/user-attachments/assets/657a7e3b-19b6-4b6d-a744-039746e5cf59)
![image](https://github.com/user-attachments/assets/b78fcd66-7006-4a62-91c4-bed7e7ba6fef)


