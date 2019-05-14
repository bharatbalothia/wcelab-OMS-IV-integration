# RTAM process

## Publish from OMS

Currently (May 2019), IV doesn't have capacity consideration. OMS needs to be leveraged for RTAM if capacity is considerable. 

Here is the option to publish RTAM based on activity including capacity consideration when IV is the sole system with avaibility. Step 2 is the only extension that we will need to implement.

Step 4 might appear to be a redundant call to IV since IV just sent the availability for the node. However, the call is just for one shipnode (or small number of the shipnodes in the DG that just had activity) so implementing a local OMS caching might be more complicated than its worth.

1.	Very soon (maybe by the time you read this email), IV will publish events based on availability change at ShipNode (OOB)
2.	Sterling OMS subscribes to the event and creates inventory activity for the availability change at the shipnode (extension)
3.	Sterling OMS’ OOB logic triggers RTAM for impacted DG/Network RTAM based on the inventory change from last step (OOB)
4.	Sterling OMS recalculates the availability for the DG/Network based on previous RTAM information, shipnode capacity, and latest shipnode availability from IV. From 9.5, OMS has the ability to inquire availability of only changed shipnode. Prior to 9.5, Sterling would have checked availabilities for all shipnodes in all DGs. (OOB)
5.	Sterling OMS publishes the RTAM (OOB)
