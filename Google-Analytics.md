# Welcome to class!

This wiki post is going to teach you how to kick butt at Google Analytics!

# Accounts, Properties, and Views

We have one GA account (the "new" one). It has multiple properties - we've got prod, staging, and dev environments, plus probably other marketing-related things such as the YouTube channel, the Tumblr blog, etc. Each property has its own GA tracking code.

Each property has multiple views.  These views come in various flavors - one in EVERY property is labeled "RAW". This view is the raw, pure incoming data from Google Analytics, totally unfiltered. You definitely want to always have that view for every property, in case you make a mistake with a filter, you'll always be able to recover. The next view you'll always want is the "BASIC FILTERS" view.  This involves a series of filters described below:

### Lowercase Hostname

This filter lowercases the hostname, so that `zachmccormick.me` and `ZachMcCormick.ME` get counted as the same thing!  It is a "custom, lowercase" filter that selects `hostname` as the filter field.

### Hostname Filter

This is a "custom, inclusion" filter that goes next in the list.  You'll select `include`, then for `filter field`, you'll select `Hostname`.  For the filter pattern, you'll want a regular expression something like `^(www\.)?zachmccormick\.me$`.  That expression matches both `zachmccormick.me` and `www.zachmccormick.me`, but it won't match `www.get.more.search.results.at` or other spammy sites that try reporting to GA accounts as a way to advertise to marketers!  Basically, this filter kicks them out of our data.

### Remove WWW

This is a "custom, search-and-replace" filter that goes after the hostname filter.  You'll select `Search and Replace`, then type in a regular expression like `^www.zachmccormick\.me$` in the `Search String` field, and text to replace it with, such as `zachmccormick.me` in the `Replace String` field.  This filter, as the name describes, will strip `www.` from the hostname.

### Lowercase Request URIs

This is a "custom, lowercase" filter that goes next.  You'll select `Lowercase`, then in the `Filter Field`, choose `Request URI`.  This will make consistent things like `/Smile_Assessment` vs. `/smile_assessment` in the URLs.

### Lowercase UTM Filters

This series of filters are all named something like `Lowercase Campaign Content`.  They are all "custom, lowercase" filters that choose a filter field (like campaign content) and lowercase them.  That way `www.zachmccormick.me/?utm_campaign=facebook` and `www.zachmccormick.me/?utm_campaign=Facebook` will both count as the same campaign.  Easy, right?

### Append Slash

This is a "custom, advanced" filter that goes last in the list.  It appends a slash to all URLs without a slash.  That way you have consistency for urls like `/smile_assessment` vs. `/smile_assessment/`.  This caused a lot of problems in the past.  It's done by selecting "advanced" for filter type, using the regular expression `^(/[a-zA-Z0-9/_\-]*[^/])$` for Extract A, and the expression `$A1/` under Output To. `Field A Required` and `Overwrite Output Field` should both be checked.

The order of all of these filters is important!  They should be in that order!

# Session Unification and User ID feature

Read this article: https://support.google.com/analytics/answer/3123662?hl=en

We can send the `user.id` value from Django back to GA for it to more accurately track our users.  Moreover, we can turn on Session Unification to allow hits collected before the User ID is assigned to be associated with the ID.  This gives us more accurate analytics when looking at different funnel analyses.

# Goals

More to be said later on goals...