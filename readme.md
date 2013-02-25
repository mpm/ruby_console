# Ruby Console for Appium

Install the following gems.

`gem install bundler pry selenium-webdriver ap crack`

Ruby 1.9.3 and Appium from GitHub are required. Run Appium from source.

`node server.js -V 1`

Export the path to your .app bundle `MyApp.app` or zipped app bundle `MyApp.app.zip`

`export APP_PATH="../MyApp.app"`

Run with

`pry -r ./console.rb`

# Reset Appium

Reset Appium after pulling the latest changes.

```
rm -rf node_modules/
npm install .
```

Rebuilt test apps if you're testing against them

`grunt buildApp:TestApp`

Run downloadApp if UICatalog fails to build.

```
grunt downloadApp
grunt buildApp:UICatalog
```

#### Documentation

##### [ruby_console on rubydoc.info](http://rubydoc.info/github/appium/ruby_console/master/toplevel)

- [iOS UI Automation](http://developer.apple.com/library/ios/#documentation/DeveloperTools/Reference/UIAutomationRef/_index.html) Example use `@driver.execute_script "UIATarget.localTarget().frontMostApp().mainWindow().rect()"
`
- [Ruby selenium-webdriver](http://selenium.googlecode.com/svn/trunk/docs/api/rb/index.html)
- [Appium](https://github.com/appium/appium/blob/master/README.md)
- [Appium extension](https://github.com/appium/appium/wiki/Automating-mobile-gestures)
- [mechanic names of elements](https://github.com/jaykz52/mechanic/blob/8c490e1d225f384847e47ffdafb47cc2248bb96c/src/mechanic-core.js#L28)

Example use of Appium's mobile gesture.

> @driver.execute_script 'mobile: tap', :x => 0, :y => 500

`console.rb` uses some code from [simple_test.rb](
https://github.com/appium/appium/blob/82995f47408530c80c3376f4e07a1f649d96ba22/sample-code/examples/ruby/simple_test.rb) and is released under the [same license](https://github.com/appium/appium/blob/c58eeb66f2d6fa3b9a89d188a2e657cca7cb300f/LICENSE) as Appium. The [Accessibility Inspector](https://developer.apple.com/library/ios/#documentation/UserExperience/Conceptual/iPhoneAccessibility/Testing_Accessibility/Testing_Accessibility.html) is helpful for discovering button names and textfield values.

--

Tag Name  | UIA
       --:|:--
button    | UIAButton
textfield | UIATextField
secure    | UIASecureTextField
text      | UIAStaticText

--

#### alert
0. `(void) alert_accept` Accept the alert.
0. `(String) alert_accept_text` Get the text of the alert's accept button.
0. `(void) alert_click(value)` Tap the alert button identified by value.
0. `(void) alert_dismiss` Dismiss the alert.
0. `(String) alert_dismiss_text` Get the text of the alert's dismiss button.
0. `(String) alert_text` Get the alert message text.

#### button
0. `(Button) button(name, number = 0)` Find a button by name and optionally number.
0. `(Button) button_include(name)` Get the first button that includes name.
0. `(Button) button_name(name)` Get the first button element that exactly matches name.
0. `(Button) button_name_num(name, number = 1)` Get the button element exactly matching name and occurrence.
0. `(Array<String>, Array<Buttons>) buttons(name = nil)` Get an array of button names or button elements if name is provided.
0. `(Array<Button>) buttons_include(name)` Get all buttons that include name.
0. `(Array<Button>) e_buttons` Get an array of button elements.
0. `(Button) first_button` Get the first button element.
0. `(Button) last_button` Get the last button element.

#### secure
0. `(Array<Secure>) e_secure` Get an array of secure textfield elements.
0. `(Secure) first_secure` Get the first secure textfield element.
0. `(Secure) last_secure` Get the last secure textfield element.
0. `(Secure) secure(value)` Get the first secure textfield that matches value.
0. `(Secure) secure_include(value)` Get the first secure textfield that includes value.
0. `(Array<String>) secures` Get an array of secure textfield values.

#### textfield
0. `(Array<Textfield>) e_textfields` Get an array of textfield elements.
0. `(Textfield) first_textfield` Get the first textfield element.
0. `(Textfield) last_textfield` Get the last textfield element.
0. `(Textfield) textfield(value)` Get the first textfield that matches value.
0. `(Textfield) textfield_include(value)` Get the first textfield that includes value.
0. `(Array<String>) textfields` Get an array of textfield values.

#### text
0. `(Array<Text>) e_texts` Get an array of text elements.
0. `(Text) first_text` Get the first text element.
0. `(Text) last_text` Get the last text element.
0. `(Text) text(name)` Get the first element that matches name.
0. `(Text) text_include(name)` Get the first textfield that includes name.
0. `(Array<String>) texts` Get an array of text names.

#### window
0. `(Object) window_size` Get the window's size.

--

```ruby
e.name # button, text
e.value # secure, textfield
e.type
e.tag_name # calls .type (patch.rb)
e.text
e.size
e.location
e.rel_location

# alert example without helper methods
alert = $driver.switch_to.alert
alert.text
alert.accept
alert.dismiss

# Secure textfield example.
#
# Find using default value
s = secure 'Password'
# Enter password
s.send_keys 'hello'
# Check value
s.value == password('hello'.length)
```

[routing.js](https://github.com/appium/appium/blob/master/app/routing.js#L69) lists not yet implemented end points.

--

#### Driver

`driver` will restart the driver.

`x` will quit the driver and exit Pry.

`execute_script` calls `@driver.execute_script`

.click to tap an element.
.send_keys to type on an element.

#### Raw UIAutomation

[mechanic](https://github.com/jaykz52/mechanic/) can be used directly.
`$driver.manage.timeouts.implicit_wait = 0` must be set or it will hang forever.

`$driver.execute_script "$('button')"` is the same as
`$driver.execute_script "mechanic('button')"`

`execute_script "au.lookup('button')[0].tap()"` is the same as
`execute_script 'UIATarget.localTarget().frontMostApp().buttons()[0].tap()'`

See [app.js](https://github.com/appium/appium/blob/master/app/uiauto/appium/app.js#L3) for more au methods.
Note that raw UIAutomation commands are not offically supported.

#### XPath

See [#194](https://github.com/appium/appium/pull/194/files) for details.

```ruby
$driver.find_element  :xpath, 'button'
$driver.find_elements :xpath, 'button'

$driver.find_element  :xpath, 'button[@name="Sign In"]'
$driver.find_elements :xpath, 'button[@name="Sign In"]'

$driver.find_element  :xpath, 'button[contains(@name, "Sign In")]'
$driver.find_elements :xpath, 'button[contains(@name, "Sign")]'

$driver.find_element :xpath, 'textfield[@value="Email"]'
$driver.find_element :xpath, 'textfield[contains(@value, "Email")]'

$driver.find_element  :xpath, 'text[contains(@name, "Reset")]'
$driver.find_elements :xpath, 'text[contains(@name, "agree")]'
```


