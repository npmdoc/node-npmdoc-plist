# api documentation for  [plist (v2.0.1)](https://github.com/TooTallNate/node-plist#readme)  [![npm package](https://img.shields.io/npm/v/npmdoc-plist.svg?style=flat-square)](https://www.npmjs.org/package/npmdoc-plist) [![travis-ci.org build-status](https://api.travis-ci.org/npmdoc/node-npmdoc-plist.svg)](https://travis-ci.org/npmdoc/node-npmdoc-plist)
#### Mac OS X Plist parser/builder for Node.js and browsers

[![NPM](https://nodei.co/npm/plist.png?downloads=true)](https://www.npmjs.com/package/plist)

[![apidoc](https://npmdoc.github.io/node-npmdoc-plist/build/screenCapture.buildNpmdoc.browser._2Fhome_2Ftravis_2Fbuild_2Fnpmdoc_2Fnode-npmdoc-plist_2Ftmp_2Fbuild_2Fapidoc.html.png)](https://npmdoc.github.io/node-npmdoc-plist/build/apidoc.html)

![npmPackageListing](https://npmdoc.github.io/node-npmdoc-plist/build/screenCapture.npmPackageListing.svg)

![npmPackageDependencyTree](https://npmdoc.github.io/node-npmdoc-plist/build/screenCapture.npmPackageDependencyTree.svg)



# package.json

```json

{
    "author": {
        "name": "Nathan Rajlich",
        "email": "nathan@tootallnate.net"
    },
    "bugs": {
        "url": "https://github.com/TooTallNate/node-plist/issues"
    },
    "contributors": [
        {
            "name": "Hans Huebner",
            "email": "hans.huebner@gmail.com"
        },
        {
            "name": "Pierre Metrailler"
        },
        {
            "name": "Mike Reinstein",
            "email": "reinstein.mike@gmail.com"
        },
        {
            "name": "Vladimir Tsvang"
        },
        {
            "name": "Mathieu D'Amours"
        }
    ],
    "dependencies": {
        "base64-js": "1.1.2",
        "xmlbuilder": "8.2.2",
        "xmldom": "0.1.x"
    },
    "description": "Mac OS X Plist parser/builder for Node.js and browsers",
    "devDependencies": {
        "browserify": "13.0.1",
        "mocha": "2.4.5",
        "multiline": "1.0.2",
        "zuul": "3.10.1"
    },
    "directories": {},
    "dist": {
        "shasum": "0a32ca9481b1c364e92e18dc55c876de9d01da8b",
        "tarball": "https://registry.npmjs.org/plist/-/plist-2.0.1.tgz"
    },
    "gitHead": "c71d9f7d6cc8460abd666112bc27c016c850e5d8",
    "homepage": "https://github.com/TooTallNate/node-plist#readme",
    "keywords": [
        "apple",
        "browser",
        "mac",
        "plist",
        "parser",
        "xml"
    ],
    "license": "MIT",
    "main": "index.js",
    "maintainers": [
        {
            "name": "TooTallNate",
            "email": "nathan@tootallnate.net"
        },
        {
            "name": "tootallnate",
            "email": "nathan@tootallnate.net"
        },
        {
            "name": "mreinstein",
            "email": "reinstein.mike@gmail.com"
        }
    ],
    "name": "plist",
    "optionalDependencies": {},
    "readme": "ERROR: No README data found!",
    "repository": {
        "type": "git",
        "url": "git://github.com/TooTallNate/node-plist.git"
    },
    "scripts": {
        "test": "make test"
    },
    "version": "2.0.1"
}
```



# <a name="apidoc.tableOfContents"></a>[table of contents](#apidoc.tableOfContents)

#### [module plist](#apidoc.module.plist)
1.  [function <span class="apidocSignatureSpan">plist.</span>build (obj, opts)](#apidoc.element.plist.build)
1.  [function <span class="apidocSignatureSpan">plist.</span>parse (xml)](#apidoc.element.plist.parse)

#### [module plist.parse](#apidoc.module.plist.parse)
1.  [function <span class="apidocSignatureSpan">plist.</span>parse (xml)](#apidoc.element.plist.parse.parse)



# <a name="apidoc.module.plist"></a>[module plist](#apidoc.module.plist)

#### <a name="apidoc.element.plist.build"></a>[function <span class="apidocSignatureSpan">plist.</span>build (obj, opts)](#apidoc.element.plist.build)
- description and source-code
```javascript
function build(obj, opts) {
  var XMLHDR = {
    version: '1.0',
    encoding: 'UTF-8'
  };

  var XMLDTD = {
    pubid: '-//Apple//DTD PLIST 1.0//EN',
    sysid: 'http://www.apple.com/DTDs/PropertyList-1.0.dtd'
  };

  var doc = xmlbuilder.create('plist');

  doc.dec(XMLHDR.version, XMLHDR.encoding, XMLHDR.standalone);
  doc.dtd(XMLDTD.pubid, XMLDTD.sysid);
  doc.att('version', '1.0');

  walk_obj(obj, doc);

  if (!opts) opts = {};
  // default 'pretty' to 'true'
  opts.pretty = opts.pretty !== false;
  return doc.end(opts);
}
```
- example usage
```shell
...
    "bundle-identifier": "com.company.app",
    "bundle-version": "0.1.1",
    "kind": "software",
    "title": "AppName"
  }
];

console.log(plist.build(json));

// <?xml version="1.0" encoding="UTF-8"?>
// <!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
// <plist version="1.0">
//   <key>metadata</key>
//   <dict>
//     <key>bundle-identifier</key>
...
```

#### <a name="apidoc.element.plist.parse"></a>[function <span class="apidocSignatureSpan">plist.</span>parse (xml)](#apidoc.element.plist.parse)
- description and source-code
```javascript
function parse(xml) {
  var doc = new DOMParser().parseFromString(xml);
  if (doc.documentElement.nodeName !== 'plist') {
    throw new Error('malformed document. First element should be <plist>');
  }
  var plist = parsePlistXML(doc.documentElement);

  // the root <plist> node gets interpreted as an Array,
  // so pull out the inner data first
  if (plist.length == 1) plist = plist[0];

  return plist;
}
```
- example usage
```shell
...

Then 'require()' the _plist_ module in your file:

''' js
var plist = require('plist');

// now use the 'parse()' and 'build()' functions
var val = plist.parse('<plist><string>Hello World!</string></plist>');
console.log(val);  // "Hello World!"
'''


### Browser

Include the 'dist/plist.js' in a '<script>' tag in your HTML file:
...
```



# <a name="apidoc.module.plist.parse"></a>[module plist.parse](#apidoc.module.plist.parse)

#### <a name="apidoc.element.plist.parse.parse"></a>[function <span class="apidocSignatureSpan">plist.</span>parse (xml)](#apidoc.element.plist.parse.parse)
- description and source-code
```javascript
function parse(xml) {
  var doc = new DOMParser().parseFromString(xml);
  if (doc.documentElement.nodeName !== 'plist') {
    throw new Error('malformed document. First element should be <plist>');
  }
  var plist = parsePlistXML(doc.documentElement);

  // the root <plist> node gets interpreted as an Array,
  // so pull out the inner data first
  if (plist.length == 1) plist = plist[0];

  return plist;
}
```
- example usage
```shell
...

Then 'require()' the _plist_ module in your file:

''' js
var plist = require('plist');

// now use the 'parse()' and 'build()' functions
var val = plist.parse('<plist><string>Hello World!</string></plist>');
console.log(val);  // "Hello World!"
'''


### Browser

Include the 'dist/plist.js' in a '<script>' tag in your HTML file:
...
```



# misc
- this document was created with [utility2](https://github.com/kaizhu256/node-utility2)
