BrowserExt - php extension for web scraping and browser emulation
=================================================================

LAST UPDATE: Upgrading to Qt5.


Features
--------

BrowserExt PHP extension is a programmatic browser, based on QtWebKit
and intended for web scraping.

+ Supports javascript and AJAX.
+ Uses xpath for selecting elements
+ Allows you to fill forms, click on the elements of the document
+ Allows to retrieve attributes, properties and other parameters
  of the elements of the document, iterate through the elements in the tree
+ Allows to download files by links
+ Allows to scroll the page vertically
+ Supports a list of proxy servers, checking proxies in few threads

A short example:

```php
$br = new PhpBrowser();
$br->load('http://localhost/index.html');

//retrieves all links to files
$links = $br->elements('//div[@id="files"]/a');
foreach ($links as $l)
{
    //extracts href property
    $href = $l->prop('href');
    //downloads a file by href link and saves it in С:\test
    $br->download($href, 'C:\\test\\'.basename($href));
}
```


Installation
------------

### Linux (Ubuntu)

It requires X11 Server. On the server without desktop you must use Xvfb.
For loading Xvfb at system startup add this line to /etc/rc.local:

`Xvfb :0 -screen 0 1024x768x16 > /dev/null 2>&1 &`

For user, from which web server is running, you must set 
the environment variable DISPLAY with server number with which
the extension will work. For apache2 this can be done by adding
this line to envvars file:

`export DISPLAY=:0.0`

To EC2 ubuntu machine is neccesary adding this line in /etc/apache2/envvars:

`export DISPLAY=localhost:0.0`


The extension requires compilation in Linux. This requires:

+ gcc (g++)
+ make
+ php (php5, php5-dev)
+ Qt5 (qtbase5-dev, qt5-qmake, libqt5webkit5, libqt5webkit5-dev, qt5-image-formats-plugins, qt5-default)

Follow these steps:

1.  For compilation you must run the build script

    `$ ./build.sh`

2.  For installation of the compiled extension

    `$ sudo ./install.sh`

3.  Next add this line to php.ini

    `extension=browserext.so`

4.  And restart the web server, for example:

    `$ sudo service apache2 restart`


### Windows

For Windows the extension comes with binaries for php 5.6
and is located in the directory `binaries\win32`.

For extension work required:

+ php 5.6
+ Qt5 with QtWebKit - Qt 5.4 or 5.3 for example (install Qt5 and set QTDIR environment variable)
+ Microsoft Visual C++ 2012 Redistributable Package (x86)

To install, do the following:

1.  Copy php_browserext.dll, the appropriate version of php 
    in the extensions directory. For example, it may be C:\php\ext.

2.  Download and install Microsoft Visual C++ 2012 Redistributable
    Package (x86).

3.  In the php.ini file, enable the extension by adding the line
    
    `extension=php_browserext.dll`


If you want to compile BrowserExt in Windows, see
[BUILDWIN.md](docs/BUILDWIN.md)



Usage
-----

First you need to create a browser class:

```php
$br = new PhpBrowser();
```

Next lets loading the page:

```php
$br->load('http://localhost');
```

Each page is loaded in a new tab. To load in the same tab,
you must pass a second parameter to true.
To go to the previous page you call the `back()`.

You can click on a link or button, passing its xpath.

```php
$br->click('//input[@type="submit"]');  
```

The page will be loaded in a new tab, to load in same tab,
you must pass a second parameter to true.

You can select elements by xpath:

```php
$els = $br->elements('//a');
```

This method returns an array of objects of class PhpWebElement.
For each element you can retrieve attributes, properties, tag name,
element value and others:

```php
$id = $els[0]->attr('id');
$prop = $els[0]->prop('href');
$tag = $els[0]->tagName();
$text = $els[0]->text();
```

You can go to the parent or to the child elements, they will
also be an objects of PhpWebElement class:

```php
$parent = $els[0]->parent();
$arr = array();
while (!$parent->isNull())
{
    $arr[] = $parent->tagName();
    $parent = $parent->parent();
}
```

In the above we iterate through all parents and stores its tags in an array.

For element can be performed a relative xpath:

```php
$items = $br->elements('//*[@class="item"]');
foreach ($items as $item)
{
    $a1 = $item->elements('./a[1]');
    $a2 = $item->elements('./a[2]');
    echo $item->tagName().' '.$a1[0]->text().' '.$a2[0]->text();
}
```

This code loops through all the elements with a class item and
displays the text of the first and second links.

You can retrieve the xpath of the element or click on it:

```php
$xp = $items[0]->getXPath();
$items[0]->click();
```

The browser can use a list of proxy servers for loading pages.
Each new page is loaded with new proxy:

```php
$proxy = array('192.168.0.2:3128', 'user:psw@example.com:8888');
$br->setProxyList($proxy, true);
var_dump($br->proxyList());
```

In the above given an array of two proxies and pass to the browser.
The second parameter specifies the need to check the proxy. Next command
returns a list of remaining proxies after checking.



API
---

Detail class description see in [API.md](docs/API.md)



XPath Inspector - a Browser utility
-----------------------------------

Also supplied with the extension program browser, which can be
used to retrieve or test xpath of the page elements.
Browser is a very simple web browser. The top line is introduced url page.
Any page opens in a new tab, right click on the page
displays a context menu with Close tab and Get XPath menu 
(Inspector window shows xpath). Several elements can be
highlight by pressing Ctrl. In this case, will be extracted common xpath
expression. For example, one can obtain xpath all items of the list,
highlighting two elements and clicking Get XPath. In Inspector selecting
multiple items also working.


License
-------

BrowserExt is licensed under the MIT license.

