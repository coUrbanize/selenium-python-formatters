Selenium Python Formatters
==========================

Improved formatters for the Selenium IDE to output more useful python.

## Additions ##

 * **getEval**: exported tests now implement the code to test `eval` statements.
 * **runScript**: exported tests now execute arbitrary JavaScript. The return value of the JavaScript is not stored or verified. Use `getEval` to store, assert, or verify the result of a JavaScript command.
 * **rollup**: exported tests now expand any user-defined `rollup` commands that exist in their user extensions.

#### Author ####

[David Zwarg](dzwarg@courbanize.com) is the Lead Front End Developer at [coUrbanize](http://courbanize.com).