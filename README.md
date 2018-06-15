# Tribe Extensions

Tribe Extensions are small plugins that add features to official Tribe plugins. They're designed to be installed and activated alongside the Tribe plugin that they extend. 

# Framework Features

The extension framework has an ever-growing list of benefits for the code that runs atop of it. 

Instead of writing a simple code snippet and pasting it in your theme's `functions.php` file, it will often make sense to write a simple extension instead. **The benefits of writing an extension:**

* Specify which Tribe plugins need to be present for your code to work.
  * Most snippets require one or more plugins to be active, or else errors will be generated. These can be easily specified within the framework. If they are not present the admin may automatically receive a notice walking them through which plugins they need to download and install.
* Communicates with Tribe plugins letting them know you're running.
 * Most modifications _don't_ do this, so the Tribe plugin does not know when it is being extended. 
* All future features—this framework is still young.
  * As time goes on more features will be added. Current plans include adding an update mechanism to the framework, for example. Extensions will automatically receive these new features when Tribe Common is updated—which users will get via updates to The Events Calendar or Event Tickets—so no changes to the extension itself will be necessary.
* All the benefits of turning a snippet into a plugin.
  * For instance: Easy installation and deactivation, each extension identifies itself in the system info, etc. 

At times it won't  make sense to convert a snippet to an extension. Extensions are harder for users to modify than `functions.php` snippets, so **for snippets that are intended to be modified by the user, generally do not make them into extensions**—keep themas snippets.

# How to Create an Extension

**Start by making a local copy of the [https://github.com/mt-support/extension-template](https://github.com/mt-support/extension-template) repository.**

Then modify as needed and push to its own new repository.

### Specify Tribe Plugins Required by the Extension (Optional)

**Any Tribe plugins that this Extensions needs to be present should be specified within the `construct()` function.** 

Inserting this line indicates that it requires The Events Calendar version 4.3 or greater to be active for our example extension to run:

```
$this->add_required_plugin( 'Tribe__Events__Main', '4.3' );
```

The Main class for The Events Calendar is `Tribe__Events__Main`. Any call to `$this->add_required_plugin()` should specify the Tribe plugin by its Main class name.

The second argument `, '4.3'` is optional. This specifies a minimum version number required by the Extension in order for it to run. 

Typically, when the required plugin is not active on the calendar an admin notice will be shown to the user. This notice will inform them of the missing plugins, and provide links to download and install them.

Here is an up to date list of the current Tribe Plugins that can be specified:

```
$this->add_required_plugin( 'Tribe__Tickets__Main', '4.3' );
$this->add_required_plugin( 'Tribe__Tickets_Plus__Main', '4.3' );
$this->add_required_plugin( 'Tribe__Events__Main', '4.3' );
$this->add_required_plugin( 'Tribe__Events__Pro__Main', '4.3' );
$this->add_required_plugin( 'Tribe__Events__Community__Main', '4.3' );
$this->add_required_plugin( 'Tribe__Events__Community__Tickets__Main', '4.3' );
$this->add_required_plugin( 'Tribe__Events__Filterbar__View', '4.3' );
$this->add_required_plugin( 'Tribe__Events__Facebook__Importer' );
$this->add_required_plugin( 'Tribe__Events__Ical_Importer__Main' );
$this->add_required_plugin( 'Tribe__Events__Tickets__Eventbrite__Main', '4.3' );
$this->add_required_plugin( 'Tribe_APM', '4.3 );
```

If your extension requires one or more of those plugins to be present, make sure to copy the relevant line(s) inside your `construct()` function. If the extension further requires a specific minimum version, ensure that argument is specified.

#### Example

The Hide Tribe Bar extension requires that The Events Calendar 3.0 or greater is present. This line is the only one needed to specify that:

```
$this->add_required_plugin( 'Tribe__Events__Main', '3.0' );
```

Note: All extensions require Tribe Common 4.3.3 or greater to be active. The Events Calendar 3.0 did not include that library. It would take exotic circumstance for an extension to run on an older version like 3.0. For example, if Event Tickets 4.3.3 was active and working properly, then this version comparison would run. If Event Tickets was not active, nor any other plugin that includes Tribe Common, the Extension would cease running even before a version comparison was made.

## Step 6: Insert Your Custom Code

Now the fun begins. Insert or begin writing your custom code inside of the init() function:

```
/**
 * Extension initialization and hooks.
 */
public function init() {
    [Insert custom code here.]
}
```

Replace the `[Insert custom code here.]` bit with your code. Many modifications will declare multiple functions. In most cases it makes sense to add those functions as public functions inside of this class. 

#### Example

Like many customizations, our Tribe Bar hider begins by hooking into a filter and specifying a callback function. Since that is where it begins, that code goes inside of `init()`.

```
/**
 * Extension initialization and hooks.
 */
public function init() {
    add_filter( 'tribe_get_template_part_content', array( $this, 'filter_template' ), 10, 5 );
}
 
/**
 * Filters Tribe Bar, but outputs all other template parts.
 *
 * @see (WP filter) tribe_get_template_part_content
 */
public function filter_template( $html, $template, $file, $slug, $name ) {
    if ( 'modules/bar' === $slug ) {
        $html = '';
    }
    return $html;
}
```

Note how the callback was added add as a second public function called `filter_template`. 

# Notes

## Requirements

The Extension framework requires Tribe Common 4.3.3 or greater to be active on the website. Currently two plugins contain Tribe Common. If neither of these are active on the WordPress install, the extension will cease running without showing any errors or messages:
* [The Events Calendar 4.3.3+](https://wordpress.org/plugins/the-events-calendar/)
* [Event Tickets 4.3.3+](https://wordpress.org/plugins/event-tickets/)

## Standards

Official extensions adhere to the following code standards where applicable:

* [Tribe Products Coding Standards](http://moderntribe.github.io/products-engineering/guidelines/)
* [WordPress version of PHPDoc](https://make.wordpress.org/core/handbook/best-practices/inline-documentation-standards/php/)

## Updating an Extension

If you are updating an extension that is already in use by the general public, you will need to update the version number. The Version should follow the [Semantic Versioning](http://semver.org/) standard. The initial public release will always be `1.0.0`. From there the major, minor, and patch versions will be incremented with each release, depending on what type of change was made in the update.

You should alter the version as it appears in the plugin header:
 ```
  * Version: 1.0.1
 ```

## TheEventsCalendar.com Extension Library

Every Extension in this repository has an official download page on [TheEventsCalendar.com: Extension Library](theeventscalendar.com/extensions/). There you will find packaged versions of each extension along with a guide for using it.