# OpenOrderReport

The **OpenOrderReport** Power BI dashboard provides a comprehensive overview of **Open Purchase Orders** from the ERP system. Users can filter and analyze the data by **Vendor**, **Buyer**, and **Part ID**.

Custom buttons powered by time intelligence logic determine the status of each Purchase Order, identifying whether it is **Late** or **Inbound**. The report also flags items currently in **IQC** (Incoming Quality Control), aiding in proactive order and quality tracking.

![OpenOrderReport Screenshot](https://raw.githubusercontent.com/louisehealey/OpenOrderReport/main/OpenOrdeReport.png)

---

## ⏱️ Last Data Refresh

The report uses [worldtimeapi.org](https://worldtimeapi.org/api/timezone/America/New_York) to display the last time the dataset was refreshed when using **Import Mode**.

![Refresh Screenshot](https://raw.githubusercontent.com/louisehealey/OpenOrderReport/main/Refresh%20Pic.png)

---

## 🔘 In-Bound Button
This calculated column flags Purchase Orders with delivery dates within the next **7 days**, helping teams prioritize and manage **upcoming deliveries** effectively.

---

## 🖥️ Power BI Demo

Explore the interactive **Open Purchase Orders** report in action:

![OpenOrderGIF](https://github.com/louisehealey/OpenOrderReport/blob/main/OpenOrderGIF)

---
