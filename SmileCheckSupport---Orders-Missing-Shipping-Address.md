# Orders Missing Shipping Address

**1. Check the Periscope Report:Orders Missing Shipment Address**
https://app.periscopedata.com/app/smiledirectclub/156694/Orders-Missing-Shipment-Address

**2. Enter a Smilecheck Support ticket for the order.**
  * SmileCheck Support Category = Fulfillment
  * Label = MissingShippingAddress

**3. If the customer has a shipping address:**
  * In Django Admin, search for the order.
  * Update the Order Items > Shipping Address with the ID from the Shipping Address ID in the Periscope report.

**4. If the customer does not have a shipping address:**
  * In Django Admin, create a shipping address for the customer using the billing address values.
  * Follow the steps for Step 3 above to update the Order Items > Shipping Address.

**5. Verify the shipping address displays for the order.**

**6. Resolve the SmileCheck Support ticket.**