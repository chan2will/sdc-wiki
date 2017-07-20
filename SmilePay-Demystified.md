# SmilePay Demystified

SmilePay&trade; has become a rather loaded term as of late. Let's try to break everything down.

## What _is_ SmilePay?

SmilePay, across the commerce site, is referring to a Klarna-based payment plan option that we offer qualified customers. We don't know what the exact pricing will be until the user completes a soft credit check during aligner checkout, at which point they're presented with Klarna's terms and pricing.

If the user _passes_ the soft check, naturally, SmilePay continues to refer to Klarna. However, should they fail, the checkout front-end instead offers (during the last step of checkout) our internal SmilePay, which will be referred to as "SmilePay Classic".

Remember that this is the _only_ customer-facing part of the commerce site that will refer to a SmilePay that is _not_ Klarna: aligner checkout, step 3, _if_ the user fails Klarna's soft credit check.

![screen shot 2017-07-20 at 5 46 30 pm](https://user-images.githubusercontent.com/8734556/28442000-71a6ba40-6d73-11e7-9206-766a73392954.png)

## Klarna SmilePay:

Astute readers may ask: if SmilePay refers to Klarna until a hypothetical Klarna soft check failure, but we don't know what the Klarna pricing will _be_, exactly how are we able to present users with SmilePay pricing info across the commerce site?

The answer can be found in the class `KlarnaCheckoutSession`, which has a class property called `placeholder_pricing` that pulls data from other hardcoded properties on the class. This data is formatted the same way as a SmilePay Classic pricing plan and is just a hardcoded estimate of what the Klarna pricing will be.

🚨 👉 __So if you ever need to update SmilePay pricing on the commerce site, remember that SmilePay refers to Klarna, we're displaying hardcoded estimates that are _only_ relevant at the presentation layer level, and that changes can be made to those estimates in `KlarnaCheckoutSession`.__ 👈 🚨 (I recently went back in and used those values in place of hardcoded pricing data for SmilePay; most if not all should be pulling the values from `KlarnaCheckoutSession`).

That's probably about all there is to Klarna SmilePay, inasmuch as we're applying the label of "SmilePay" to it. Naturally Klarna _itself_ has plenty going on, but we won't be addressing it here.

## SmilePay Classic

For users who fail Klarna's soft credit check (unfortunate), we are presenting the option of paying for their aligners with our own, internally-developed SmilePay Classic payment plan (fortunate, as it actually winds up costing them less 🤐).

SmilePay Classic exists as the `OrderPaymentSubscription` model, which is currently undergoing some changes. Let's clarify & define the nomenclature for both v1 and v2 SmilePay Classic models, as there is some (ample?) room for confusion.

Note that "v1" and "v2" SmilePay Classic do not refer to different models, instead referring to the same `OrderPaymentSubscription` model at different times. You're probably dealing with v2.

### v1 SmilePay Classic Model

__tldr__: __`recurring_amt`__ is a hardcoded constant. __`installments`__ is variable.

`recurring_amt` is the amount that each installment payment will cost (except for the last one, which may be less depending on how much remains to be paid).

`initial_amt`, aka "down payment", is set separately from `recurring_amt` and is the price of the first payment that the user makes at the time of purchase. If I recall correctly, It used to be $250, but then went on to be set to the same price as the installments themselves, $95.

But they (`initial_amt` and subsequent installment-based payments) are distinct, and are treated as distinct entities; `initial_amt` is _not_ counted as an installment in the `installments` count.

To illustrate, here's how `installments` is calculated (not exactly the same as the code, but this is what's happening):

```
installments = math.ceil( ( total cost - initial_amt ) / recurring_amt )
```

This means that in order to get the _total number of payments that the user will make in completing a SmilePay Classic (v1) Order Payment Subscription_, you'll need to use `installments + 1`, which will include the initial, non-installment-based payment as well as each installment-based payment.

### v2 SmilePay Classic Model

__tldr__: __`recurring_amt`__ is variable. __`installments`__ is a hardcoded constant.

`installments` is no longer calculated; each payment subscription has the same number of installment-based payments to make.

`initial_amt` is no longer considered a distinct entity from the rest of the installments. So if the total number of installments is `24`, the `initial_amt` is counted as one of those installments. The total number of payments that the user will make over the course of the order payment subscription is the same as `installments`.

As you would probably guess,
```
recurring_amt = math.ceil( total cost / installments )
```

## Down Payments Waffle Switch
There is a Waffle switch called `'down_payments'`.

If inactive, the `initial_amt` will simply be the same as the calculated `recurring_amt` and _each_ payment (even the first one) will be subject to the `SMILEPAY_INSTALLMENT_FEE` (which, last I checked, was 0).

If active, the `initial_amt` will be whatever it has been set to in the `ProductPrice` model/table, and `SMILEPAY_INSTALLMENT_FEE` will not be applied to that initial payment.

## To Be Determined
How the SmilePay Classic (new) model interacts with discounts and refunds. 