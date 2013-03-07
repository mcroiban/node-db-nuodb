# node-db-nuodb: NuoDB database bindings for Node.js #

[![Build Status](https://api.travis-ci.org/nuodb/node-db-nuodb.png?branch=master)](http://travis-ci.org/nuodb/node-db-nuodb)

For detailed information about this and other Node.js
database bindings visit the [Node.js db-nuodb homepage] [nodedbhomepage].

## REQUIREMENTS

Make sure to install nodeunit globally:

```bash
npm install nodeunit -g
```

## BUILDING ##

```bash
git submodule update --init

export PATH=/usr/local/bin:$PATH
export NODE_PATH=/usr/local/bin/node
export NUODB_INCLUDE_DIR=${NUODB_ROOT}/include
export NUODB_LIB_DIR=${NUODB_ROOT}/lib64

node-waf configure && node-waf clean build test
```

## TESTING ##

Start up a minimal chorus in order to run the tests:

```bash
java -jar ${NUODB_ROOT}/jar/nuoagent.jar --broker &
${NUODB_ROOT}/bin/nuodb --chorus test --password bar --dba-user dba --dba-password user --verbose debug --archive /var/tmp/nuodb --initialize --force &
${NUODB_ROOT}/bin/nuodb --chorus test --password bar --dba-user dba --dba-password user &
```

In order to run tests you should start NuoDB using the settings specified in
the test-settings.json file. This can be done simply via the quickstart script
in rhe NuoDB distribution.

If you opt to install Node.js on Mac using a .pkg file, you will need to build thusly:

```bash
sudo NUODB_LIB_DIR=/Users/rbuck/tmp/nuodb/lib64 NUODB_INCLUDE_DIR=/Users/rbuck/tmp/nuodb/include PATH=$PATH:/usr/local/bin npm install . -g
```

## RELEASE PROCEDURE ##

### TAGGING ###

Tag the product using tags per the SemVer specification; our tags have a v-prefix:

```bash
git tag -a v1.0.3 -m "SemVer Version: v1.0.3"
```

If you make a mistake, take it back quickly:

```bash
git tag -d v1.0.3
git push origin :refs/tags/v1.0.3
```

### PUBLISHING ###

```bash
npm publish
```

If you bugger it up, pull it back quickly, or cause grief for users:

```bash
npm unpublish  db-nuodb@1.0.3
```

To view published versions:

```bash
npm view  db-nuodb
```


## INSTALL ##

Before proceeding with installation, you need to have NuoDB installed;
the examples below assume an installation location of /opt/nuodb, but
the location may vary.

In order for the installation script to locate dependencies properly, you'll 
need to set the NUODB_INCLUDE_DIR and NUODB_LIB_DIR environment variables. 
For example:

```bash
$ export NUODB_INCLUDE_DIR=/opt/nuodb/include
$ export NUODB_LIB_DIR=/opt/nuodb/lib64
```

Once the environment variables are set, install with npm:

```bash
$ npm install db-nuodb
```

Also, verify you have both these files installed to the same directory as the
nuo_bindings.node file:

  libNuoRemote.so

## QUICK CHECK ##

Run the following command to verify the shared library can be loaded by node:

```bash
$ node
````

In node run the following Javascript commands, then CTRL-C twice to exit:

```javascript
> var mod = require('./db-nuodb');
undefined
> mod;
{}
> 
(^C again to quit)
````

## QUICK START ##

```javascript
var nuodb = require('db-nuodb');
new nuodb.Database({
    hostname: 'localhost',
    user: 'root',
    password: 'password',
    database: 'node'
}).connect(function(error) {
    if (error) {
        return console.log("CONNECTION ERROR: " + error);
    }

    this.query().select('*').from('users').execute(function(error, rows) {
        if (error) {
            return console.log('ERROR: ' + error);
        }
        console.log(rows.length + ' ROWS');
    });
});
```

## HOW TO CHANGE SUBMODULE URL ##

In the following procedure, remove the existing entries found in the first two
files mentioned below. Save and exit from VI in both circumstances. Doctor the
following instructions to suit.

```bash
vi .gitmodules 
vi .git/config 
git rm --cached lib/node-db
rm -rf lib/node-db/
rm -fr .git/modules/lib/node-db/
git status
git add -u
git status
git commit -m "Remove prior submodule."
rm -fr *
git reset --hard
git submodule add git://github.com/lbayas/node-db.git lib/node-db
git add -u
git status
git commit -m "Repointed successfully to lbayas fork. Bumped version number."
git push
```

## LICENSE ##

This module is released under the [NUODB License] [license].

[homepage]: https://github.com/nuodb/nuodb-drivers/tree/master/nodejs
[license]: https://github.com/nuodb/nuodb-drivers/blob/master/LICENSE
[node.js v0.6.9]: http://nodejs.org/dist/v0.6.9/
[nodedbhomepage]: http://nodejsdb.org/
