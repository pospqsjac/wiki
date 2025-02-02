# Web UI

<div class="alert alert-danger" role="alert">

  <span class="glyphicon glyphicon glyphicon-exclamation-sign"></span>
  &nbsp;
  Web UI is currently experimental and _is not_ recommended for new users!
</div>

<div class="alert alert-info" role="alert">

  <span class="glyphicon glyphicon glyphicon-comment"></span>
  &nbsp;
  We're re-writing the Web UI in React! Check out the new page [here](/Web-UI/v2)!
</div>

**Related pages**

* [UI mockups](https://flexget.mybalsamiq.com/projects) - accounts can be requested
* [Concepts](/Web-UI/Concepts)
* [Roadmap (more ideas)](/Roadmap)
* [Git Project](https://github.com/Flexget/Flexget/projects/1)
* [API](/API)

## Enabling Web UI

The web UI is enabled by placing the `web_server` key in your configuration file. SSL is optional, but it is highly recommended if the UI is exposed to the internet.

Note that `web_server` is one of a handful of [top-level keys](/Configuration#top-level-keys) in the configuration file. Do not indent `web_server`.

### `web_server` Usage 
This is the simple configuration that uses default values. It will enable the API and web UI at `http://0.0.0.0:5050` (IP `0.0.0.0`, port `5050`) by default.
```yaml
web_server: yes
```

You can also set a different port using an alternate simple configuration mode.
```yaml
web_server: 8080
```
<br>

#### All configuration options
- `bind`: IP address to bind to
- `port`: Port at which the web UI and API will be accessed
- `ssl_certificate`: Path to certificate file
- `ssl_private_key`: Path to private key file
- `web_ui`: `yes|no` Set to no to disable the web UI; only the API will run
- `base_url`: Set a different base_url; default is `/`

Fully configured example:
```YAML
web_server:
  bind: 0.0.0.0
  port: 3539
  ssl_certificate: '/etc/ssl/private/myCert.pem'
  ssl_private_key: '/etc/ssl/private/myKey.key'
  web_ui: yes
  base_url: /foo
```

## Starting Web UI
Set a password and start FlexGet in daemon mode to start the web server.

```bash
$ flexget web passwd <some_password>
$ flexget daemon start --daemonize
```

Using the full configuration example above, the Flexget Web UI would now be available at `http://flexget_ip:3539/`. Full API documentation would be available at `http://flexget_ip:3539/api/`.

Visit the [API page](/wiki/API) for more information about it.


## Authentication
The login username is `flexget` and the password is what you set using the `web passwd` command above.

You can also use an authorization header to access the API with the following format: `Authorization: Token <TOKEN>`

You can view or reset the API token using the following [CLI](/CLI/web) commands:

```bash
# View token
flexget web showtoken

# Generate new token
flexget web gentoken
```

## Sections currently working
| **Section** | **Functionality** |
| --- | --- |
| Log | <ul><li>View real-time logs</li><li>Search in the logs (by task, item, keyword, ...)</li></ul> |
| Execute | <ul><li>Start execution of tasks</li><li>View results (accepted and undecided)</li></ul> |
| Config | <ul><li>Edit your config from an in-browser editor</li><li>Auto config reload when saved</li><li>Edit your variables</li></ul> |
| History | <ul><li>View latest accepted history</li><li>Search by task</li></ul> |
| Series | <ul><li>View all series in your database</li><li>Set beginning of the series</li><li>Manage series<ul><li>Delete show itself</li><li>Delete episode</li><li>Delete releases</li><li>Forget downloaded releases (to redownload)</li></ul></li></ul> |
| Movies | <ul><li>Manage movie lists<ul><li>Delete lists</li><li>Add lists</li></ul></li><li>Manage movies per list<ul><li>Delete Movie</li><li>Add movie to list</li></ul></li></ul> |
| Seen | <ul><li>View latest seen entries</li><li>Delete seen entries</li></ul> |
| Schedule | <ul><li>View configured schedule</li></ul> |
| Miscellaneous | <ul><li>Shutdown Flexget</li><li>Reload config</li><li>Database management<ul><li>Trigger cleanup</li><li>Trigger vacuum</li><li>Reset database for plugin</li></ul></li></ul> |

## Development
The UI has a solid base but we need help building the plugins. If you would like to get your hands dirty in AngularJS, CSS or UX Design then please read below and join our chat on [Gitter](https://gitter.im/Flexget/Flexget).

To get started you will first need to setup your environment from Git.

### Setup from Git

1. You will need to install NPM (see [https://nodejs.org/en/](https://nodejs.org/en/)).<br />
The following commands will do that as user. This requires g++ to be installed.
```bash
$ echo 'export PATH=$HOME/local/bin:$PATH' >> ~/.bashrc
$ . ~/.bashrc
$ mkdir ~/local
$ mkdir ~/node-latest-install
$ cd ~/node-latest-install
$ curl http://nodejs.org/dist/node-latest.tar.gz | tar xz --strip-components=1
$ ./configure --prefix=~/local
$ make install # ok, fine, this step probably takes more than 30 seconds...
$ curl -L https://www.npmjs.org/install.sh | sh


# Alternative NPM installation to virtualenv (untested)
$ curl http://nodejs.org/dist/node-latest.tar.gz | tar xvz
$ cd node-v*
$ ./configure --prefix=$VIRTUAL_ENV
$ make install


# Alternative NPM installation (untested)
# install node wherever.
# use sudo even, it doesn't matter
# we're telling npm to install in a different place.
$ echo prefix = ~/local >> ~/.npmrc
$ curl https://www.npmjs.org/install.sh | sh
```

2. Install bower and gulp. This needs to be done as `root` unless you installed NPM as user with the previous commands.

```bash
$ npm install -g bower
$ npm install -g gulp
```

3. Next, install the Web UI dependencies by running the following commands under the `<flexget github folder>/flexget/ui/v1` folder. Running `gulp build` will compile all the ui files.

```bash
$ npm install
$ bower update
$ gulp build
```

### Running from Git
The UI communicates to the FlexGet daemon using the API. When starting the daemon it will make the UI available at `http://flexget_ip:port/ui/`.

The daemon will load the compiled UI files if you are _not_ in debug mode. To enable debug mode run:

```bash
$ flexget -L debug daemon start
```

### Plugin development from Git

For plugin development, see examples on  [Github](/https://github.com/Flexget/Flexget/tree/develop/flexget/ui/plugins), and also feel free to chat with us on [Gitter](/https://gitter.im/Flexget/Flexget).

To make development easy we are using browserify, so when you change a file it will be automatically refreshed on your browser. To get this working you will need to start the flexget daemon (as above). Then you can run

```bash
#option 1: run ui using local flexget daemon
$ gulp serve

#option 2: run UI against a different flexget server
$ gulp serve --server <flexget_api:port>
```

## Testing
We have recently started adding tests to existing parts of the UI. There are about 250 tests currently present. If you modify/add parts of the UI, ensure you also modify/add tests for the relevent changes (at least most of them).

The tests can be run be using a number of commands: 
```bash
#Option 1: Standard command line
$ karma start

#Option 2: Starting a web browser to run the tests in*
$ gulp serve-specs

#Option 3: Shorthand for the browser command
$ npm test
```

This gulp task watches for file changes, not file additions. After adding a new test file, this task needs to be restarted.

All of these commands need to be run inside the `/flexget/ui` folder.

When using the `gulp` task, a browser should open by itself. If it does not, you can navigate to `http://localhost:5050` where the tests will be available.

Results of the tests (that are run on the develop branch) can be viewed here: http://ci.flexget.com/job/Test-UI/.

The frameworks currently used for the tests are:  

- [Karma](/http://karma-runner.github.io/0.13/index.html)
- [Mocha](/https://mochajs.org)
- [Sinon](/http://sinonjs.org)
- [Chai](/http://chaijs.com)
- [Bardjs](/https://github.com/wardbell/bardjs)

If you have any more questions regarding testing of the different parts and modules of the UI, you can ask them in our [Gitter chat](/https://gitter.im/Flexget/Flexget).

### Attachments
* [Web UI Flow](/attachments/Web-UI/Flow.png)
* [UI](/attachments/Web-UI/UI.png)