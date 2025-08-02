# Real-Time Trip Event Analysis – Azure Event-Driven Architecture

This project demonstrates a real-time taxi trip analysis system using Azure Event Hub, Azure Functions, and Logic Apps to classify trips and notify teams with insights.

---

## Architecture Overview

![Architecture Diagram](https://github.com/alkh0115/cst8917-lab4/blob/main/logicapp-workflow-screenshot.JPG?raw=true)


**Components:**

- **Azure Event Hub:** Ingests trip events in real-time.
- **Azure Function (`func-trip-analyzer-analyze-tripp`)**: Processes each trip, detects patterns like cash payments or suspicious vendor activity.
- **Azure Logic App (`trip-logic-app`)**: 
  - Triggers when a new event is available in Event Hub.
  - Iterates over each trip item.
  - Applies conditions to classify trips as:
    - 🔴 **Suspicious Trip**
    - 🚨 **Interesting Trip**
    - ✅ **Normal Trip**
  - Sends Adaptive Cards to Microsoft Teams channel accordingly.

---

## Azure Function Logic

The `func-trip-analyzer-analyze-tripp` function:
- Accepts incoming trip data in JSON format.
- Analyzes each trip for:
  - Vendor ID anomalies (`V999`)
  - Payment type (`CashPayment`)
  - Distance or passenger irregularities
- Returns enriched trip records with computed `insights` (e.g., `SuspiciousVendorActivity`, `CashPayment`, etc.).

Sample logic:
```python
if vendor_id == 'V999':
    insights.append('SuspiciousVendorActivity')
if payment_type == 2:
    insights.append('CashPayment')
```

---

## Example Input & Output

### Input Sample
```json
{
  "vendor": "V100",
  "distance": 7.55,
  "passengers": 3,
  "payment": 1
}
```

### Output (Normal)
```json
{
  "vendor": "V100",
  "distance": 7.55,
  "passengers": 3,
  "payment": 1,
  "summary": "Trip normal",
  "insights": []
}
```

### Output (Suspicious)
```json
{
  "vendor": "V999",
  "distance": 5.5,
  "passengers": 1,
  "payment": 2,
  "summary": "Interesting trip with suspicious vendor",
  "insights": ["CashPayment", "SuspiciousVendorActivity"]
}
```

---

## Adaptive Card Example (Posted to Teams)

- **Suspicious Trip Detected**
  - 🔴 Header: "Suspicious Trip Detected"
  - Details: Vendor, Distance, Passengers, Payment, Insights

- **Interesting Trip**
  - 🚨 Header: "Interesting Trip Detected"
  - Triggered by Cash Payment alone.

- **Normal Trip**
  - ✅ Header: "Trip Analyzed - No Issues"

---

## Suggested Improvements

- Visualize suspicious activity in Power BI dashboards.
- Add alerts for specific patterns using Azure Monitor.

---

## Demo Video



