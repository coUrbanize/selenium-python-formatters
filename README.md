Selenium IDE: Improved Python Formatters
========================================

Improved formatters for the Selenium IDE to output more Selenese commands to Python. This plugin is derived from the excellent plugin "Selenium IDE: Python Formatters 2.4.0", by Adam Goucher.


## Supported IDE commands ##

 * **getEval**: exported tests now implement the code to test `eval` statements.
 * **runScript**: exported tests now execute arbitrary JavaScript. The return value of the JavaScript is not stored or verified. Use `getEval` to store, assert, or verify the result of a JavaScript command.
 * **dragAndDrop**: exported tests now perform simple `dragAndDrop` commands, using ActionChains.
 * **rollup**: exported tests now expand any user-defined `rollup` commands that exist in their user extensions.


## Installation ##

Grab the newest version of the IDE from [here](http://testing.courbanize.com.s3.amazonaws.com/selenium-python-formatters/se-py-fmtr-1.1.xpi).

Download link: http://testing.courbanize.com.s3.amazonaws.com/selenium-python-formatters/se-py-fmtr-1.1.xpi

You can also review the changes from the last release in the [Release Notes](http://testing.courbanize.com.s3.amazonaws.com/selenium-python-formatters/se-py-fmtr-1.1.xpi.xhtml)


Usage
=====


### Options ###

Once the formatter plugin is installed, you will see a new format under Options / Formats, named "Improved Python 2 / unittest / WebDriver".  You can set the options here for each exported test. These options are mostly the same for the regular "Python 2 / unittest / WebDriver" formatter, with the exception of the ActionChains import statement.  You can modify these options at your leisure, with the exception of the "Header" section -- it's possible to remove something important here, so only change if you understand the ramifications!


### Selenium IDE Commands ###

These commands are already supported by the IDE, but not by the normal Selenium formatters:


#### getEval ####

  * Command Name: [storeEval](http://release.seleniumhq.org/selenium-core/1.0.1/reference.html#storeEval)
  * Also used by:

      * assertEval
      * assertNotEval
      * verifyEval
      * verifyNotEval
      * waitForEval
      * waitForNotEval

  * Arguments:

      * *script*: The JavaScript to evaluate, and retrieve the value of.
      * *variableName*: The name of the Selenese variable used to store the value in.

  * Implementation:

      * The improved python formatter leverages pythons triple-double-quote syntax to wrap the JavaScript string, so there is no need to worry about single or double quotes interfering in the output of this command in python.
      * The return value from this command is always a string.

  * `assertEval` Example:

      **Selenese**
      ```html
<tr>
    <td>assertEval</td>
    <td>window.location.href == 'http://localhost/'</td>
    <td>true</td>
</tr>
      ```

    **Python Formatter**
    ```python
# ERROR: Caught exception [ERROR: Unsupported command [getEval | window.location.href == 'http://localhost/' | ]]
    ```

      **Improved Python Formatter**
      ```python
self.assertEqual("true", driver.execute_script("""return (window.location.href == 'http://localhost/').toString();"""))
      ```

  * `waitForEval` Example:

      **Selenese**
      ```html
<tr>
    <td>waitForEval</td>
    <td>window.location.href == 'http://localhost/'</td>
    <td>true</td>
</tr>
      ```

    **Python Formatter**
    ```python
# ERROR: Caught exception [ERROR: Unsupported command [getEval | window.location.href == 'http://localhost/' | ]]
    ```

      **Improved Python Formatter**
      ```python
for i in range(60):
    try:
        if "true" == driver.execute_script("""return (window.location.href == 'http://localhost/').toString();"""): break
    except: pass
    time.sleep(1)
else: self.fail("time out")
      ```


#### runScript ####

  * Command Name: [runScript](http://release.seleniumhq.org/selenium-core/1.0.1/reference.html#runScript)
  * Arguments:

      * *script* The JavaScript to evaluate; any return value is discarded.

  * Implementation:

      * The improved python formatter leverages pythons triple-double-quote syntax to wrap the JavaScript string, so there is no need to worry about single or double quotes interfering in the output of this command in python.
      * The return value from this command is never stored.

  * `runScript` Example:

      **Selenese**
      ```html
<tr>
    <td>runScript</td>
    <td>window.alert('hello world');</td>
    <td></td>
</tr>
      ```

    **Python Formatter**
    ```python
# ERROR: Caught exception [ERROR: Unsupported command [runScript | window.alert('hello world'); | ]]
    ```

      **Improved Python Formatter**
      ```python
driver.execute_script("""window.alert('hello world');""")
      ```


#### dragAndDrop ####

  * Command Name: [dragAndDrop](http://release.seleniumhq.org/selenium-core/1.0.1/reference.html#dragAndDrop)
  * Arguments:

      * *locator*: The locator of the element to initiate the drag and drop action upon. The coordinates for the beginning of the drag are the middle of the element.
      * *movementsString*: A pair of X,Y coordinates that describe the offset in pixels before dragging completes, and the mouse actions complete. Examples: `10,100`, `-10,100`, `+10,-100`

  * Implementation:

      * Behind the scenes, this uses the ActionChain pattern, documented officially in [googlecode](http://selenium.googlecode.com/svn/trunk/docs/api/py/webdriver/selenium.webdriver.common.action_chains.html), and more detailed examples in the [project wiki](https://code.google.com/p/selenium/wiki/AdvancedUserInteractions). This command builds a set of operations that are performed sequentially, starts them, then waits for 1s for them to finish. (**Note**: the timeout of 1s may not be suitable for your application)

  * `dragAndDrop` example:

      **Selenese**
      ```html
<tr>
    <td>dragAndDrop</td>
    <td>css=body</td>
    <td>0, 100</td>
</tr>
      ```

      **Python Formatter**
      ```python
# ERROR: Caught exception [ERROR: Unsupported command [dragAndDrop | css=body | 0, 100]]
      ```

      **Improved Python Formatter**
      ```python
action_chain = ActionChains(driver)
action_chain.drag_and_drop_by_offset(driver.find_element_by_css_selector("body"), 0, 100)
action_chain.perform()
time.sleep(1)
      ```


#### rollup ####

  * Command Name: [rollup](http://release.seleniumhq.org/selenium-core/1.0.1/reference.html#rollup)
  * Arguments:

      * *rollupName*: The name of the rollup to recall. The rollup rule must be defined in your `user-extensions.js` file.
      * *args*: The key/value pairs of args to be sent to your rollup. Example: `name=David, company=coUrbanize`

  * Implementation:

      * The instructions for creating a rollup are documented in the UI-Element reference, from the Help menu of the Selenium IDE.
      * When this instruction is expanded, the individual commands from the rollup are output into a sequence of python commands.
      * A rollup command may contain another rollup command.

  * `rollup` example:

      **Selenese**
      ```html
<tr>
    <td>rollup</td>
    <td>clickAthenB</td>
    <td>locatorA=css=button.first, locatorB=css=button.second</td>
</tr>
      ```

      **Python Formatter**
      ```python
# ERROR: Caught exception [ERROR: Unsupported command [rollup | clickAthenB | locatorA=css=button.first, locatorB=css=button.second]]
      ```

      **Improved Python Formatter**
      ```python
driver.find_element_by_css_selector("button.first").click()
driver.find_element_by_css_selector("button.second").click()
      ```


## Support ##

For issues with the Improved Selenium Formatters, please open an issue on the github project:

  * [Project Page](https://github.com/coUrbanize/selenium-python-formatters)
  * [Issues (Bugs & Feature Requests)](https://github.com/coUrbanize/selenium-python-formatters/issues)


## Author ##

[David Zwarg](dzwarg@courbanize.com) is the Lead Front End Developer at [coUrbanize](http://courbanize.com)

[coUrbanize](http://courbanize.com) uses [Selenium](http://docs.seleniumhq.org/) and [Python](https://www.python.org/) extensively, and utilize the features in the improved Python formatters extensively. This plugin is a critical component in translating [Selenium IDE](http://docs.seleniumhq.org/projects/ide/) scripts into unit tests integrated into a [Continuous Integration](https://codeship.io/) and [Continuous Delivery](http://en.wikipedia.org/wiki/Continuous_delivery) framework.