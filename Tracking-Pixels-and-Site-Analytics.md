

### Implemented 

**Universal Pixels**:  
Google Analytics (A)  
Facebook/Instagram (A)  
Rakuten (A)  

**Event Based Pixels**:  

- Rakuten (I)
	- Order complete: All orders (contains: order uuid and order total)

- Twitter (I)
	- Order complete: EK orders
	- Order complete: Scan orders
	- Post smile assessment
	- Get started
 
**Affiliates/Referrals**:
*None*
 
*Key*  
**A**: Active  
**I**: Inactive  

----

### Not implemented

Google AdWords
Bing Ads (1)
Facebook Universal Pixel (FB and Instagram)
Pinterest
Taboolah
Outbrain (2)
Sniply
Groupon (3)
Yahoo (Gemini)

**Affiliates/Referrals**:
ShareASale (2)

*Key*
**1**: Represents exclusionary list based on those who went to `/thank-you`
**2**: May/may not be implemented on old commerce website
**3**: No html/js code, implemented through other means


----

### Questions to ask:

* What pages does each piece go on/how does it work?
* 99% of the time simply include script on the page (sometimes conditionally)

### EXAMPLE CODE FOR TRACKING PIXEL FRAMEWORK
- All tracking pixel code is located inside of `smilecheck/sites/website/tracking.py`. Manipulation of URLS for tracking pixels should handled inside of the base class if possible. See example for how this might be done.

```
from django.core.urlresolvers import resolve


class TrackingPixel(object):

    def __init__(self, request):
        route = resolve(request.path)
        url_name = route.url_name
        self.request = request
        self.is_smile_assessment_page = url_name == 'website-smile-assessment'
        self.is_post_smile_assessment_page = url_name == 'website-order-kit'
        self.is_order_complete_page = url_name in ['website-thank-you-kit', 'website-thank-you-aligner']

    def get_script(self):
        return ''


class GoogleAnalytics(TrackingPixel):

    def get_script(self):
        return """
        <script src="google_analytics_example_src.js">
            // Multiline string for large scripts with additional functionality
        </script>
        """


class TwitterPixel(TrackingPixel):

    def get_script(self):
        if self.is_smile_assessment_page:
            return '<script src="twttr.js?lead={pre_smile_lead}"></script>'.format(pre_smile_lead='sa_lead')
        elif self.is_post_smile_assessment_page:
            return '<script src="twttr.js?lead={post_smile_lead}"></script>'.format(post_smile_lead='post_lead')
        else:
            return ''


class RakutenPixel(TrackingPixel):

    def get_script(self):
        if self.is_order_complete_page:
            return '<script src="rakuten_script.js?order_total={order_total}"></script>'.format(order_total='9900')
        else:
            return ''


class BingTrackingPixel(TrackingPixel):

    def get_script(self):
        if self.is_order_complete_page:
            return '<script src="bing_script.js?order_total={order_total}"></script>'.format(order_total='9900')
        else:
            return '<script src="bing_script.js"></script>'

```