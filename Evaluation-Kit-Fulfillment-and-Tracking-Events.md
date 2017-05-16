# KIT FULFILLMENT EVENTS
All times are in are in CDT.

## Shipment Task

Monday - Friday

Timing - 24 hours a day - This tasks runs at 10, 30, and 50 minutes past the hour (i.e. 9:10, 9:30, 9:50, 10:10, 10:30,10:50).

The task processes impression kits that have a fulfillment status of WAITMAT (Waiting on Materials). The task 
changes the fulfillment status to READY.  This only processes orders with products impr, imprrtk (impression kits 
and kit retakes).

## Fulfillment Task

Monday - Friday

Timing - 5:30, 6:30, 8:30, 10:30, 12:30, 2:30, 3:30, 4:30, 5:40

Saturday

Timing - 6:00, 8:00, 10:00

The task processes the READY kits and changes the status to PROCESSING.  The batch will then get transmitted (FTP) to Phoenix upon completion of the processing.

## FedEx Pickup at Phoenix

Monday - Friday

Timing - 6:30 - 7:00

## Nightly Shipment Tracking Number Assignments Task

Daily (Sunday - Saturday)

Timing - 9:00 PM

This task processes the uploaded shipping file from Phoenix on our FTP server.  The task updates tracking numbers and marks shipment batches as complete unless there are items without tracking numbers.

## KIT TRACKING EVENTS

All times are in are in CDT.

## Outbound Impression Kit Tracking File Phoenix Transmit to SDC

Daily (Sunday - Saturday)

Timing - 8:15 PM

Outbound IK Tracking event file transmitted to SDC

## Inbound Impression Kit Tracking File Phoenix Transmit to SDC

Daily (Sunday - Saturday)

Timing - 10:15 PM

Inbound IK Tracking event file transmitted to SDC

## Outbound Impression Kit Tracking File SDC Processing

Daily (Sunday - Saturday)

Timing - 4:00 AM

SDC Processes Outbound IK Tracking file received from Phoenix
(Silverpop is sent tracking events as they are processed)

## Inbound Impression Kit Tracking File SDC Processing

Daily (Sunday - Saturday)

Timing - 4:00 AM

SDC Processes Inbound IK Tracking file received from Phoenix
(Silverpop is sent tracking events as they are processed) 

## RedShift Data Synchronization

Daily (Sunday - Saturday)

Timing - 6:00 AM

Sync RedShift `shipment_tracking` table