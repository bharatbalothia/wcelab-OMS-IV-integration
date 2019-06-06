# Reservation Options

Steve and I had some quick conversation on making and consume reservation through OMS and IV. We believe there are three options:

1.	Reservation in IV, remove after OMS scheduling
2.	Rough source, IV Reservation, remove after OMS order creation and sourcing
3.	Reservation in OMS, IV reservation, remove IV Reservation after OMS order creation

Option Breakdown


1.	Reservation Creation in IV, remove after OMS scheduling

In this approach, applications make reservation directly with IV. Probably at DG level. It avoids a sourcing call to OMS to figure out the exact shipnode. We will need to extend the OMS scheduling event to remove the DG reservation in IV once it is sourcing and scheduled to a shipnode. 

2.	Rough source, IV Reservation in IV, remove after OMS order creation and sourcing

In this approach, applications make an initial sourcing estimate call to OMS or WOO or use some static rule engine + IV availability to determine a likely shipnode and make reservation against that shipnode in IV. We will extend the OMS order creation or scheduling logic to do an actual sourcing in OMS and cancel the IV reservation. 

3.	OMS reservation, IV reservation, remove IV Reservation after OMS order creation

Applications make reservation to OMS, which does the actual sourcing and make reservation in IV for the sourced shipnode. When the order gets into OMS, the OOB logic will change the reserved order lines to RSRV demand type. We will extend the creation process to remove the IV reservation. 

Below is a table with the approaches. I believe option 3 will be the most straight forward to implement and it is our product direction. If we are looking for the ability to make reservation without OMS, we will need to venture into option 1 and 2. 


|Approach |Reserve Against | Initial Sourcing | Reservation Level| OMS needs to source after order creation|When to Remove| Pro |Con|
|-| -- | --- | --- | --- | -- | -------------------- | -------------------- |
|1|IV|Static DG|DG|Yes|OMS Scheduling|<ul><li>Quick Reservation Creation in IV without access OMS</li></ul> | <ul><li>DGs with shared shipnodes could overpromise</li></ul>|
|2|IV |OMS or WOO or Static Rules|Ship Node|Yes|OMS Order Creation or Scheduling|<ul><li>Flexibility in initial sourcing logic</li><li>Potentially avoid OMS traffic (if not using OMS to initial sourcing)</li><li>Can be combined with other shipnode level reservation approach</li></ul>|<ul><li>OMS might need to source twice</li><li>Could be difficult to maintain rough sourcing logic externally</li></ul>|
|3|OMS|OMS actual sourcing|Ship Node|No|OMS Order Creation|<ul><li>Sterling will support this flow OOB in the future</li><li>OMS source once</li><li>Can be combined with other shipnode level reservation approach</li></ul>|<ul><li>Reservation needs to go through OMS and OMS sourcing</li></ul>|

## Option 1 Process and Concerns.

Option 1: Reservation Creation in IV, remove after OMS scheduling

In this approach, applications make reservation directly with IV. Probably at DG level. It avoids a sourcing call to OMS to figure out the exact shipnode. We will need to extend the OMS scheduling event to remove the DG reservation in IV once it is sourcing and scheduled to a shipnode. 
  
Process steps:

1.	Order Capture makes reservation at DG level. (OOB)
    1.	This reduces the availability from the DG.
    1.	This also indirectly reduces how many item can be reserved at the shipnodes of this DG. IV does not allow a shipnode level reservation if ANY of the shipnode’s DGs is out of availability.
2.	Order Capture passes the order & order lines to OMS with IV reservation number as an extended reference on order line.
3.	OMS creates the order with an open order demand (OOB)
4.	OMS sends the Open Order demand in IV without Shipnode or DG. This demand does NOT impact any availability because it has not no shipnode or DG. (OOB)
5.	During sourcing and scheduling, OMS sources the order line to a shipnode based on IV availability (OMS and IV integration. It is part of PoC)
6.	OMS updates IV with the Shipnode availability change (OOB) after removing the DG level reservation demand (Extension)
    1.	IV API to update the demand also takes reservation number. IV can remove the DG level reservation while creating shipnode level Allocated demand. If the reservation is for a DG that shipnode does not belong to, IV will create the allocated demand but not remove the reservation at DG. 


### Use Case

- Use IV to reserve ship-to-home at DG level
- Use IV to reserve click-n-collect at shipnode level

#### Example: DG1 has DC1, STR1, STR2. All of 1 Qty of ITEM1.

|Reservation | Result | DC1 Ava| STR1 Ava | STR2 Ava | DG1 Ava|
| --------- | ----- | -- | -- | -- | -- |
|Initial State|N/A|1|1|1|3|
|Reserve Qty 1 @ DG1|Success|1|1|1|2|
|Reserve Qty 1 @ DC1|Success|0|1|1|1|
|Reserve Qty 1 @ DG1|Success|0|1|1|0|
|Reserve Qty 1 @ STR1|Fail|0|1|1|0|

Once DG1’s availability for ITEM1 is 0, no reservation can be made at its shipnodes (for ITEM1).

### Solution Concern:

1.	Reservation could fail for shipnodes that appear to have availability (because its DG is out)
2.	Sharing shipnodes among DGs could result in overpromising for DG level reservation
3.	IV doesn’t consider capacity in reservation


### Option 1 for a warehouse in multiple DGs

*Below is example for a single line order*

1. Configure the shared warehouse in its own DG and remove it from all other reservation DGs.
2. Capture application reserves against the warehouse DG first. If reserved qty == 0, capture application reserves against the store network DG. The operation ordering doesn't really matter. Implementation can be coded to use the more likely to success DG first. 
3. If either reservation succeed (reserved qty == ordered qty), take the order. Which DG that the capture system took the reservation is not important. The only important part is that capture system has a reservation for the order line.
4. OMS sources and schedules the order to best available shipnode without the consideration where the order line was reserved. 
5. OMS updates IV with actual allocation at shipnode (the warehouse or a store in DGs) and removes the DG level reservation demand in IV.

*For multiline order or multiqty lines, need to add the logic to take partial reservation then reserve the rest.*
