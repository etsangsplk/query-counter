# TS Multiple Query Counter

This is a simple app that lets you set up one or more query with some explanatory
text so that it can display a simple count of the items that match the
query(s).

![example](images/query-counter.png "Example Display")

To display the count for one or more queries, use the App Settings...

For each query that you would like to display a count for, define the following:
1.  Count Variable Name (this must be unique)
2.  Artifact Type (the type of artifact to be counted)
3.  Query

In the rich text field, enter the text that you would like to display in the app.
To show the counts for the different queries, use the notation "{myVar}" where
"myVar" represents the "Count Variable Name" from above.
The app will replace any defined variables with the count.

If there is an error with the query, then the app will display the error in red in
place of the variable.

![example](images/query-counter-settings.png "Example Configuration")

To not use queries, remove all queries using the - symbol.  To add a query back,
use the + symbol.

## Timebox scoping

If this app is on a Timebox Scoped page, then the appropriate timebox filter
will be applied if the Timebox is valid for the queried model.  If the Timebox
is not valid for the selected model, then the Timebox scope will be ignored.

If the page is scoped to a Milestone Timebox, there is an additional setting
to allow the user to respect or ignore the global project setting. This is
useful when Portfolio Items associated with a specific Milestone are not
constrained to a specific subset of the Project Hierarchy tree.

## Export

The current variables and values can be exported in CSV format

## Portfolio Item Ancestor

A setting allows a portfolio item ancestor filter to be added to the page. If a portfolio item
of that type is selected, all query values are filtered down to those that have the selected item
as an ancestor. Types that don't have that portfolio item type as an ancestor will return 0.

If placed on a page that also has a [pi-ancestor-filter-broadcaster](https://github.com/RallyTechServices/pi-ancestor-filter-broadcaster),
and filter by ancestor portfolio item is enabled, it will listen to the broadcaster for the selected ancestor.

## Development Notes

* Using the Rally rich text editor to get easy formatting, but it doesn't give
us a lot of options for size of the font.
* TODO: If a user does some formatting that interferes with {#}, it may not
populate properly because we're just doing a simple replace.

### First Load

If you've just downloaded this from github and you want to do development,
you're going to need to have these installed:

 * node.js
 * grunt-cli
 * grunt-init

Since you're getting this from github, we assume you have the command line
version of git also installed.  If not, go get git.

If you have those three installed, just type this in the root directory here
to get set up to develop:

  npm install

#### Deployment & Tests

If you want to use the automatic deployment mechanism, be sure to use the
**makeauth** task with grunt to create a local file that is used to connect
to Rally.  This resulting auth.json file should NOT be checked in.

### Structure

  * src/javascript:  All the JS files saved here will be compiled into the
  target html file
  * src/style: All of the stylesheets saved here will be compiled into the
  target html file
  * test/fast: Fast jasmine tests go here.  There should also be a helper
  file that is loaded first for creating mocks and doing other shortcuts
  (fastHelper.js) **Tests should be in a file named <something>-spec.js**
  * test/slow: Slow jasmine tests go here.  There should also be a helper
  file that is loaded first for creating mocks and doing other shortcuts
  (slowHelper.js) **Tests should be in a file named <something>-spec.js**
  * templates: This is where templates that are used to create the production
  and debug html files live.  The advantage of using these templates is that
  you can configure the behavior of the html around the JS.
  * config.json: This file contains the configuration settings necessary to
  create the debug and production html files.
  * package.json: This file lists the dependencies for grunt
  * auth.json: This file should NOT be checked in.  This file is needed for deploying
  and testing.  You can use the makeauth task to create this or build it by hand in this'
  format:
    {
        "username":"you@company.com",
        "password":"secret",
        "server": "https://rally1.rallydev.com"
    }

### Usage of the grunt file
#### Tasks

##### grunt debug

Use grunt debug to create the debug html file.  You only need to run this when you have added new files to
the src directories.

##### grunt build

Use grunt build to create the production html file.  We still have to copy the html file to a panel to test.

##### grunt test-fast

Use grunt test-fast to run the Jasmine tests in the fast directory.  Typically, the tests in the fast
directory are more pure unit tests and do not need to connect to Rally.

##### grunt test-slow

Use grunt test-slow to run the Jasmine tests in the slow directory.  Typically, the tests in the slow
directory are more like integration tests in that they require connecting to Rally and interacting with
data.

##### grunt deploy

Use grunt deploy to build the deploy file and then install it into a new page/app in Rally.  It will create the page on the Home tab and then add a custom html app to the page.  The page will be named using the "name" key in the config.json file (with an asterisk prepended).

You can use the makeauth task to create this file OR construct it by hand.  Caution: the
makeauth task will delete this file.

The auth.json file must contain the following keys:
{
    "username": "fred@fred.com",
    "password": "fredfredfred",
    "server": "https://us1.rallydev.com"
}

(Use your username and password, of course.)  NOTE: not sure why yet, but this task does not work against the demo environments.  Also, .gitignore is configured so that this file does not get committed.  Do not commit this file with a password in it!

When the first install is complete, the script will add the ObjectIDs of the page and panel to the auth.json file, so that it looks like this:

{
    "username": "fred@fred.com",
    "password": "fredfredfred",
    "server": "https://us1.rallydev.com",
    "pageOid": "52339218186",
    "panelOid": 52339218188
}

On subsequent installs, the script will write to this same page/app. Remove the
pageOid and panelOid lines to install in a new place.  CAUTION:  Currently, error checking is not enabled, so it will fail silently.

##### grunt watch

Run this to watch files (js and css).  When a file is saved, the task will automatically build, run fast tests, and deploy as shown in the deploy section above.

##### grunt makeauth

This task will create an auth.json file in the proper format for you.  **Be careful** this will delete any existing auth.json file.  See **grunt deploy** to see the contents and use of this file.

##### grunt --help

Get a full listing of available targets.
