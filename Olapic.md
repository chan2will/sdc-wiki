##  Welcome to the Wonderful World of Olapic

###  What is Olapic? 
Olapic self defines itself as a "Earned Content Platform [that] amplifies engagement and performance for brands like West Elm, American Eagle Outfitters, & JetBlue using real customer photos." Olapic also seems to be a tool used for marketing teams to aggregate images from social media platforms, and place said images on to an application through the use of an iframe based widget.

###  Before Developing 
Olapic does not have a formal QA environment or source-control. This means that if you make a change to a "widget" that is currently deployed to our site, that the change is irreversible and that change will be pushed to our "live" site. Because of this design feature, I have decided that it is best to "version" each widget, to insure that state of the widget that is being used. 

This means that you should create a new widget and then apply "Gallery B" styling prior to development.

###  How to Develop on the Widget? 
1. Contact a representative at Olapic and demand a login. A few members of the Olapic team that you may consider contacting are listed below.
    1. David Lange email: david.lange@olapic.com
    1. Tomi Alveroni email: tomi@olapic.com
    1. Erin Shields email: erin.shields@olapic.com
1. You may view the Olapic Dashboard [here](https://www.photorank.me/admin/widgets/) and make changes as you see fit. 
1. You can apply a "source" to the widget. A source is the pool of images that will be applied to the gallery. Each one of these sources are curated by our team at SDC. Currently Kaitlin Andrews is in charge of making decisions involved with the streams/sources. 
1. While developing the widget you will want to place the widget into development mode. You can do this through the Olapic dashboard. You should avoid placing any widget in production in development mode, as this will disable the widget for our users. You can view the widget through the use of the `?olapicForceRender` or `olapicDevMode` route params. This will allow you to view changes immediately. 
1. Once you have completed development of the widget you should find the hash associated with the widget, and include it in `smilecheck/settings/service_access.yaml`. You can find the hash in the "Grab the Code" portion of the Olapic Dashboard. 
