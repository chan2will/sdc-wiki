# Selenium IDE (Firefox) automation

Tired of typing in the same thing over and over when testing (taking an EK purchase all the way to aligner purchase, etc.)? Do these things:

* __Download Firefox__:

   https://www.mozilla.org/en-US/firefox/new/

* __Download Selenium IDE__:

   https://addons.mozilla.org/en-US/firefox/addon/selenium-ide/

   Install and restart Firefox.

* __Open Firefox__

* __Menu Bar: click Tools -> Selenium IDE__

* __Menu Bar (on whatever screen the IDE opened up on): click File -> Open Test Suite...__

   - If you don't see these options, click on the Selenium IDE console (window) and try the menu bar again.
   - Make sure to use the "open test suite" option as this sets the file path for the photo assessment uploads.
   - Navigate to */Users/**{yourusername}**/dev/scc-api/smilecheck/sites/website/tests/SeleniumIDEBrowserPluginTests*
   - Open **v2_tests**


* __Run Tests__:

  **01_IMPR_purchase**
  - Open a new private window (command + shift + P; manual browser window opening/closing is on account of CSRF issues. Hope to automate in the future.)
  - In the Selenium IDE console, double click "01_IMPR_purchase"
  - Menu Bar: **Actions**, **Play Current Test Case**
  - Browser should erupt in a frenzy of eCommerce

  - You can see how far it has gotten by watching the Selenium console table rows turn green
  - If things hang for whatever reason and you can't restart the test via the same menus, need to stop the current test by clicking the yellow pause (symbol) button in the Selenium console GUI (to the right of the speed slider and play buttons)
  - Might need to close the system dialogue boxes that the OS opens for the photo assessment file uploads when finished (just click cancel in them)
  - Leave this how it finished-- logged in & on the thank-you page.

  **02_IMPR_purchase_to_CC_ready_for_aligner_order**
  - ***Leave the last window alone, and open another non-private new window (command + n)***
  - Go to the staff portal (localhost:8000/staff or wherever ((not prod)) ), and log on if necessary.
  - ** Make sure that the "Photo Assessments" tab is collapsed (not showing the workqueues, "Started", "Ready for Review", etc.)**
  - In the Selenium IDE console, double click "02_IMPR_purchase_to_CC_ready_for_aligner_order"
  - Menu Bar: **Actions**, **Play Current Test Case**
  - Browser should erupt in a frenzy of staff activity
  - The case from 01_IMPR_purchase should now be ready to buy aligners, so:

  **03_CC_aligner_purchase**
  - Close the Staff Portal window
  - Click the window you used in **01_IMPR_purchase**
  - In the Selenium IDE console, double click "03_CC_aligner_purchase"
  - Menu Bar: **Actions**, **Play Current Test Case**

  **04_SCEKSS_purchase**
  - Repeat steps from 01_IMPR_purchase, making the obvious changes (use a different private window).

05 (get SCEKSS purchase ready for aligner purchase) & 06 (the aligner purchase) are to come.

Note that these tests are probably quite brittle relative to text changes to the pages, so don't be surprised if I/we/someone has to do tweaking as those changes happen.