# Open Order Report
This dashboard provides a comprehensive overview of **Open Purchase Orders** from the ERP system. Users can filter and analyze the data by **Vendor**, **Buyer**, and **Part ID**.

Custom buttons powered by time intelligence logic determine the status of each Purchase Order Line, identifying whether it is **Late** or **Inbound**. The report also flags items currently in **IQC** (Incoming Quality Control), aiding in quality tracking.

![OpenOrderReport Screenshot](https://raw.githubusercontent.com/louisehealey/OpenOrderReport/main/OpenOrdeReport.png)

---

## ⏱️ Last Data Refresh

This report uses the [worldtimeapi.org](https://worldtimeapi.org/api/timezone/America/New_York) service to display the most recent time the dataset was refreshed when using **Import Mode** connection type.

![Refresh Screenshot](https://raw.githubusercontent.com/louisehealey/OpenOrderReport/main/Refresh%20Pic.png)

The following Power Query (M Script) retrieves the current local and UTC datetime from the API, then processes and formats it for clear display in the report:

```
let
   Source = Json.Document(Web.Contents("https://worldtimeapi.org/api/timezone/America/New_York")),
   ConvertedToTable = Table.FromRecords({Source}),
   TypedTable = Table.TransformColumnTypes(ConvertedToTable, {
       {"abbreviation", type text}, {"client_ip", type text}, {"datetime", type datetimezone}, 
       {"day_of_week", Int64.Type}, {"day_of_year", Int64.Type}, {"dst", type logical}, 
       {"dst_from", type datetime}, {"dst_offset", Int64.Type}, {"dst_until", type datetime}, 
       {"raw_offset", Int64.Type}, {"timezone", type text}, {"unixtime", Int64.Type}, 
       {"utc_datetime", type datetime}, {"utc_offset", type duration}, {"week_number", Int64.Type}
   }),
   SelectedColumns = Table.SelectColumns(TypedTable, {"datetime", "utc_datetime"}),
   FormattedTable = Table.TransformColumnTypes(SelectedColumns, {
       {"datetime", type time}, {"utc_datetime", type date}
   }),
   FinalTable = Table.RenameColumns(FormattedTable, {
       {"datetime", "Local Time"}, {"utc_datetime", "UTC Date"}
   })
in
   FinalTable
```
---

## 🔘 Late Button
This calculated column identifies Purchase Orders with delivery dates **prior to today**, helping teams track and manage **overdue deliveries** more effectively. It evaluates each PO line individually for precise, line-level tracking.
<br>

This clause `PurchaseOrders[Balance] <> 0` filters out the PO Lines that are late because they are being inspected by quality control. 
```
LATE = 
IF(
    TODAY() > PurchaseOrders[Rev Delivery Date] &&
    PurchaseOrders[Balance] <> 0,
    "Y",
    "N"
)
 ``` 
By creating a Bookmark that filters on the rows where `LATE` equals `Y`, a custom button can be created that displays all late PO Lines. 

Sometimes shipping is delayed or the items may take extra time to be recieved in. Adding a **3 Day Buffer** ensures that only seriously late lines are highlighted—reducing noise from minor delays and allowing teams to focus on exceptions that require follow-up or escalation.

```
LATE = 
IF(
    TODAY() > PurchaseOrders[Rev Delivery Date] + 3 &&
    PurchaseOrders[Balance] <> 0,
    "Y",
    "N"
)

```

## 🔘 In-Bound Button
This calculated column flags Purchase Orders with delivery dates within the next **7 days**, helping teams prioritize and manage **upcoming deliveries** effectively.

This clause `PurchaseOrders[Balance] <> 0` filters out the PO Lines that are late because they are being inspected by quality control. 

 ``` 
IN_BOUND = 
VAR IB = 
    IF(
        PurchaseOrders[Rev Delivery Date] >= TODAY() &&
        PurchaseOrders[Rev Delivery Date] <= TODAY() + 7 &&
        PurchaseOrders[Balance] <> 0,
        "Y",
        "N"
    )
RETURN IB
 ``` 
By creating a Bookmark that filters on the rows where `IN_BOUND` equals `Y`, a custom button can be created that displays all In Bound PO Lines.  

---
## 🔘 IQC Button
This calculated column flags Purchase Order Lines that have been **received but are currently undergoing incoming quality control (IQC)**. These lines are identifiable by the following characteristics:

- The **Purchase Order remains open**
- The **Balance Due is 0**, indicating that all items have been received

These orders remain in an open state until inspection is complete and the items are transferred to their designated stock location, at which point the PO is officially closed.

```
IN_QC = 
IF(
    PurchaseOrders[Balance Due] = 0,
    "Y", "N"
)
```
Because the report is only displaying Open Purchase Orders, we can exclude this from the fiter. 


## 🖥️ Power BI Demo

Explore the interactive **Open Purchase Orders** report in action:

![OpenOrderGIF](https://github.com/louisehealey/OpenOrderReport/blob/main/OpenOrderGIF)

---
