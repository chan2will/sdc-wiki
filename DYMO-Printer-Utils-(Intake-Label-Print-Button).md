## Introduction
Ever noticed this button in the staff portal? It's on the case details page:

![screen shot 2017-03-08 at 10 39 23 am](https://cloud.githubusercontent.com/assets/8734556/23713519/ccabe31c-03eb-11e7-9895-dacea6cd9be8.png)

If your computer is properly equipped with a `DYMO LabelWriter 450 Twin Turbo` label printer, clicking it is supposed to print out intake labels for the case/patient whose detail page you're perusing.

The printer looks like this:

![img_3449](https://cloud.githubusercontent.com/assets/8734556/23713905/10174b04-03ed-11e7-9c68-ba90d81d98c4.JPG)

Great. So, how does the button make the printer do anything?

## Software installation & Setup
When the printer is set up on a given PC, it will need to have a software/driver suite installed. Said suite can be downloaded here:

http://www.dymo.com/en-US/online-support/dymo-user-guides

<img width="407" alt="screen shot 2017-03-08 at 10 56 01 am" src="https://cloud.githubusercontent.com/assets/8734556/23714222/110631b4-03ee-11e7-9173-14bb3dd4b972.png">

Download the appropriate suite and install. Do what it says, which seems to be true in general when installing printers: wait until the software has all been installed prior to connecting the USB cable. Upon completion, the installer will prod you to ensure that the printer shows up in the OS's printer list; on a Mac, successful installation will have you looking at this:

<img width="780" alt="screen shot 2017-03-08 at 11 02 21 am" src="https://cloud.githubusercontent.com/assets/8734556/23714452/c570aa3a-03ee-11e7-89fc-f03654ded5c4.png">

Super. So after the giant installation (200+ megs!), you'll now have a few important things at your disposal.
Of paramount importance to the `Print Intake Labels` button is DYMO's printer daemon, which seems to be a locally-run server that communicates between the printer & browser.

You'll see it in your Mac's menu bar:
<img width="641" alt="screen shot 2017-03-08 at 11 05 17 am" src="https://cloud.githubusercontent.com/assets/8734556/23714614/86575c3a-03ef-11e7-9d38-f2c043226455.png">

or your Windows machine's taskbar:
(screenshot to come)

You can also ensure that the daemon is up & running by paying it a visit at `https://localhost:41951/DYMO/DLS/Printing/Check`.

If these steps are all successful, the computer & printer should be all set up.

### Troubleshooting
1. Is the daemon running?
    * See above for instructions on how to check.
    * Did the printer software suite include it? (if you only install the driver, it won't)
    * Is the computer failing to start it up after rebooting? On Mac it can be restarted with the terminal command `launchctl load com.dymo.dls.webservice`. Perhaps look into having this command run via a `plist` on startup. For Windows, I don't know yet.

## Editing Templates

Upon navigating to `scc-api/smilecheck/sites/staff/labelTemplates`, you'll see a number of xml documents. At present they include `bagLabel.xml`, `checklist.xml`, and `impressionsLabel.xml`.

![screen shot 2017-03-08 at 11 24 58 am](https://cloud.githubusercontent.com/assets/8734556/23715345/e6d585b2-03f1-11e7-9813-5f2c72466d40.png)

Should you open these documents in your code editor, you will likely conclude that their editing & manipulation are burdensome tasks to be avoided for as long as possible. But fear not, intrepid developer! When you installed the giant DYMO software suite earlier, you also installed a shining champion: `DYMO Label`, a GUI-based utility that has been waiting, ever vigilant, to deliver you from this XML madness.

In order to use it, first change the template to edit's file extension from `.xml` to `.label`. This step is needed; `DYMO Label` will refuse to open an xml document. `DYMO Label`'s hook into the OS should kick in, and the file should then be offered up as a `DYMO Label` file.

![screen shot 2017-03-08 at 11 27 26 am](https://cloud.githubusercontent.com/assets/8734556/23715892/e12dec6a-03f3-11e7-91d8-77b2ced3de43.png)

Double-click to open. You'll see something like this:

![screen shot 2017-03-08 at 11 40 08 am](https://cloud.githubusercontent.com/assets/8734556/23715933/06ad6f7e-03f4-11e7-9cf6-21d584d1fdfb.png)

Edit as desired. You probably won't need to do a ton of manipulation; it's more likely that a lab staff member will send `.label` files to you to be added for automation purposes; open it up as described.

You'll notice in the screenshot that I've simply entered in "name", "case", "date" and "mrn". This text is relevant, as it's what the JS code is currently searching for when it conducts its string substitutions. You could use anything, but think of these as variables.

Another important note is that these are four separate text areas. You can create new ones like so:

![screen shot 2017-03-08 at 6 20 11 pm](https://cloud.githubusercontent.com/assets/8734556/23730218/f1f447b4-042b-11e7-8931-26ba39840192.png)

The file you receive, if that's what you're updating, will probably have only one text area that has a bunch of different fields in it. It would be possible to keep things that way and join strings together to inject into the single text area, but I figure it's more straightforward to keep them separate and format each one individually per label type (explanation to follow in the next section). I also figure it's best to keep each one in its own row as pictured, since setting up code to dynamically resize text areas within the xml document in case some string (patient name, for example) winds up being unusually long would probably be an unwise investment of developer time. Each string having its own row will prevent anything spilling out into other text areas.

Save the document and rename the file extension to `.xml`, and you should be set.

## Code

You'll primarily be looking at:
* `smilecheck/sites/staff/static/staff/js/labelUtils.js`
* `smilecheck/sites/staff/views/cases.py`
* `smilecheck/sites/staff/static/staff/js/caseDetail.js`

`smilecheck/sites/staff/static/staff/js/DYMOLabelFramework.js` is the third-party (minified) JS that provides the interface to the printer (server).

Anyway, `cases.py`, starting from around line 152, has code that sets up the labels to be printed. You'll see a `label_data` variable that contains `templatesPrintData` that's an array of print data for each label to be printed when the button is pressed. There's also `templateStringData` that collects info from the case to be injected into each label template. Everything should be straightforward, just note that the sizes are as follows (these are named after the label types):
`s-9629` is a smaller label, while
`s-11286` is larger, rather like a name tag.

That data gets fed to the template & converted to JS via JSON, where a `date` object is appended to the `templateStringData` in order to be formatted & included on the label.

Moving on to `labelUtils.js`: you shouldn't need to mess with this too much, just know that it uses jQuery to traverse the xml document, searching for strings whose text (which you set in `DYMO Label`) matches keys in the `templateStringData` object passed to it.

Additionally, if you need to include more text around one of the templateStringData object's values, you can add a function to `Template.prototype.stringFormatters`; when the class fills in all of the text it has to inject, if it finds one of these functions that matches the template's name (as set up by you in `templatesPrintData`; see `cases.py` for an example), it'll use it to format the strings and/or adorn it with additional text. Should be pretty easy to figure out by looking through the code.

The variables are all standardized to `name`, `case`, `date`, and `mrn`, but you could certainly use anything else you want so long as that's the text present in the xml document.

Last, note that the actual button click handler exists in `caseDetail.js`. It also contains a `sizeMap` that matches each label size to the side of the printer that I'm assuming it's going to be on; feel free to edit if desired. By passing this in to the `labelUtils.printLabels` method, I figure you could have different setups depending on the page, if use of the label printer automation should ever expand beyond that button. You can also pass in an error handler callback in order to provide page-specific error logic in case something goes awry.

The last thing to note is the additional bit of setup in `caseDetail.js`; the JS date object is appended to `labelData.templateStringData` here, and `labelUtils.createLabelGroup` is used to turn the data into a set of label objects that `DYMOLabelFramework.js` can actually print.
