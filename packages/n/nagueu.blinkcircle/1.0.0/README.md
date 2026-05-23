# Nagueu.blinkcircle

**A Power BI visual library for creating intelligent visual alerts using colored circles and animations, designed to simplify KPI monitoring, real-time supervision, and rapid identification of critical situations in Power BI reports.**

---

## Why Smart.blinkcircle?

Have you ever missed a critical alert in a crowded dashboard? Or spent too much time analyzing multiple KPIs before identifying an urgent issue?

**Smart.blinkcircle** solves this problem by transforming your Power BI indicators into dynamic and intuitive visual alerts.

Using intelligent colored circles and automatic blinking for critical alerts, this library allows users to instantly identify important issues in their dashboards.

Whether you are monitoring industrial machines, financial indicators, IoT sensors, or network performance, Smart.blinkcircle immediately draws attention to critical elements.

---

# Features

Smart.blinkcircle displays intelligent visual indicators inside Microsoft Power BI reports.

The component automatically detects the alert color and applies the appropriate visual behavior.

---

## Key Features

- 🔴 Automatic blinking for critical alerts
- 🟡 Static display for warning alerts
- 🟢 Visual indicators for normal status
- 🔵 Support for informational alerts
- ⚪ Custom color support
- 📊 Compatible with real-time dashboards
- ⚡ Smooth and lightweight animations
- 🎨 Fully customizable styles
- 🔄 Support for DirectQuery and Streaming Datasets

---

# Use Cases

## 🔍 Industrial Monitoring

Monitor industrial equipment and instantly detect critical anomalies.

dax
AlertColor = 
IF([Temperature] > 90, "red", "green")
## Model Independence

All functions are model-independent—they work with any Power BI semantic model without requiring specific table or column names. Just import the library and start querying.

## Getting Started

1. Import the library into your Power BI semantic model using DAX View
2. Start exploring with `nagueu.blinkcircle.Explain()` to see all available functions
3. Use the examples above to get started

## Author

**Lionel Nagueu**

- LinkedIn: [lionel-perin-nagueu-djambong-7a4a1715a](https://www.linkedin.com/in/lionel-perin-nagueu-djambong-7a4a1715a/)
- GitHub: [nagueuleo/Nagueu.BlinkCircle](https://https://github.com/nagueuleo/Nagueu.BlinkCircle)

---

**Version:** 1.0.0