# Questions to ask:

* What pages does each piece go on/how does it work?
* Let's 99% of the time simply include script on the page (sometimes conditionally)
* 

# What do we use right now?

* Google Analytics
 * Spartax code
 * No Vulcan code
* Google AdWords
 * Spartax code
 * No Vulcan code
* Bing Ads
 * Spartax code
 * No Vulcan code
 * Exclusionary list based on those who went to `/thank-you`
* Facebook Universal Pixel (FB and Instagram)
 * Spartax code
 * No Vulcan code
 * Exclusionary list based on those who went to `/thank-you`
* Pinterest
 * Spartax code
 * No Vulcan code
* Twitter
 * Spartax code
 * No Vulcan code
* MediaForge (Rakuten)
 * Spartax code
 * No Vulcan code
* Taboolah
 * Spartax code
 * No Vulcan code
* Outbrain
 * Spartax code (maybe works)
 * No Vulcan code
* Sniply
 * Spartax code
 * No Vulcan code
* Groupon
 * No code (we have coupon code infra for that)
* Yahoo (Gemini)
 * Spartax code
 * No Vulcan code


# Affiliates/Referrals

* ShareASale
 * Spartax code (maybe works)
 * No Vulcan code

# EXAMPLE CODE FOR TRACKING PIXEL FRAMEWORK

```
CURRENTLY_ACTIVE_TRACKING_PIXELS = [
    GoogleAnalytics,
    BingUniversalPixel
]

class TrackingPixel(object):
    render_script_on_smile_assessment = True
    render_script_on_order_complete = True
    def get_script(self, request):
        return ''

    def on_smile_assessment_complete(self, request):
        return ''

    def on_order_complete(self, request):
        return ''

class GoogleAnalytics(TrackingPixel):
    def get_script(self, request):
        return '<script src="foo">'

class RakutenPixel(TrackingPixel):
    render_script_on_smile_assessment = False
    def get_script(self, request):
        user = request.user
        order = user.get_active_order()

        return '<script src="foo&{}">'.format(order.amount)

    def on_smile_assessment_complete(self):
        return '<script src="bar">'


class FooPixel(TrackingPixel):
    def on_smile_assessment_complete(self):
        return '<script src="bar">'
```