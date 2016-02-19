Install CasperJS 1.1.0-beta3 and PhantomJS 2.0 on Mac OSX El Capitan
====================================================================

Installing CasperJS 1.1.0-beta3 with PhantomJS 2.0 can be a pain in the backside to get working. The solution is simple, but often difficult to find online. I've written this guide for myself, and thought it might be helpful to others.

Installing PhantomJS 2.0
------------------------

    npm install -g phantomjs2
    
Installing CasperJS 1.1.0-beta3
-------------------------------

    npm install -g casperjs@1.1.0-beta3
    
Modify Boostrap.js
------------------

If you try to run `casperjs --version` you'll be greeted with an error:

    CasperJS needs PhantomJS v1.x
    
    /usr/local/lib/node_modules/casperjs/bin/bootstrap.js:91 in __die
    
To fix, you need to modify the `bootstrap.js` file. Open the boostrap file in your favorite text editor. The location of the bootstrap file is:

    /usr/local/lib/node_modules/casperjs/bin/bootstrap.js

Find the following line:

    /*jshint maxstatements:34, maxcomplexity:10*/

Underneath it paste the following code:

    var system = require('system');
    var argsdeprecated = system.args;
    argsdeprecated.shift();
    phantom.args = argsdeprecated;
    
Next, find the following code in the same file:

    (function(version) {
        // required version check
        if (version.major !== 1) {
            return __die('CasperJS needs PhantomJS v1.x');
        } if (version.minor < 8) {
            return __die('CasperJS needs at least PhantomJS v1.8 or later.');
        }
        if (version.minor === 8 && version.patch < 1) {
            return __die('CasperJS needs at least PhantomJS v1.8.1 or later.');
        }
    })(phantom.version);
    
Delete it, and replace it with the following:

    (function (version) {
        // required version check
        if (phantom.casperEngine === 'phantomjs') {
            if (version.major === 1) {
                if (version.minor < 9) {
                    return __die('CasperJS needs at least PhantomJS v1.9 or later.');
                }
                if (version.minor === 9 && version.patch < 1) {
                    return __die('CasperJS needs at least PhantomJS v1.9.1 or later.');
                }
            } else if (version.major === 2) {
                // No requirements yet known
            } else {
                return __die('CasperJS needs PhantomJS v1.9.x or v2.x');
            }
        }
    })(phantom.version);
    
Save, and close the file. Now run: `casperjs --version` and your output should look similar to mine:

    Jamess-iMac:Projects jamesjeffery$ casperjs --version
    1.1.0-beta3
