# grunt-cep
> A Grunt plugin that helps debugging and packaging HTML5-based extensions for Adobe Creative Cloud applications.

By using this task you can debug and package HTML5-based extensions for Adobe&reg; Creative Cloud&reg; products directly from Grunt, in a totally automated manner and without the Adobe Extension Builder plugin.

The tool can automatically generate self-signed certificates for extension packaging and automates some error-prone tasks such as extension manifest files creation. It supports hybrid extensions and lets you create different builds of the extension based on targeted Adobe products.

It is based on the `csxs` command line tool by [Creative Market](https://github.com/creativemarket/csxs).

**Please note:** the plugin is currently in alpha stage and may contain bugs. Options are not freezed and may change in future versions.

## Getting Started
This plugin requires Grunt `~0.4.4`. Please refer to the [official documentation](http://gruntjs.com/getting-started) to get started with Grunt. You may install this plugin with this command:

```shell
npm install grunt-cep --save-dev
```

Once the plugin has been installed, it may be enabled inside your Gruntfile as usual:

```js
grunt.loadNpmTasks('grunt-cep');
```

### Project Boilerplate
You can use the [_grunt-init-cep_](https://github.com/fcamarlinghi/grunt-init-cep) template to easily get started. The template tool will install all the required files automatically and provide you with a basic extension boilerplate, already configured to run inside the chosen target applications.

Please refer to the [_grunt-init-cep_](https://github.com/fcamarlinghi/grunt-init-cep) documentation for installation and usage instruction.

### Advanced Setup
All the icons, manifest, MXI and `update.xml` files provided by the template can also be found in the `res/extension` folder located inside the plugin installation folder. If you prefer, you can manually copy these files instead of using the _grunt-init-cep_ template.

_grunt-cep_ is setup to use these files by default, so just copy the `res/expresso` folder to the root of your project (that is, into the same folder of your `Gruntfile.js`) and you should be good to go.

#### Custom Manifest, XML and MXI Files
XML and MXI template files are populated at build time using configuration properties. For most extensions the provided templates should work just fine, but for complex extensions it might be necessary to make changes to these files or even provide custom ones.

Note that _grunt-cep_ makes no assumptions about and does no validation on extension files content, so be sure to provide valid files and to modify extension configuration accordingly.

## The _cep_ Task
You may run the task with this command:

```shell
grunt cep
```

### Options
As the task configuration object has several root properties and child objects that you can customize to fit your needs, _grunt-cep_ options are better kept in an external Javascript or JSON file for easier editing and imported in your `Gruntfile.js` configuration at run-time using either:

```js
require('cep-config.js');
```

or:

```js
grunt.file.readJSON('cep-config.json');
```

Base options defined in the external file can be overwritten in child tasks as needed (see the example below). Default values and a more in-depth description for all the properties below can be found in the <code>/options/defaults.js</code> file in the plugin folder.

#### cep
These properties define base task options.

<table width="100%">
	<tr>
		<td valign="top" width="140px"><strong>profile</strong></td>
		<td valign="top" width="50px">String</td>
		<td valign="top"><p>The active build profile. Valid properties include:<p>
		    <ul>
		        <li><p><code>debug</code>: builds the extension in debug mode, that includes files such as <code>.debug</code> for remote extension debugging.</p><p><b>Please note:</b> when building using the <code>debug</code> profile a <code>.debug</code> suffix is automatically added to the name and id of the extension.</p></li>
		        <li><code>launch</code>: builds the extension in debug mode as above, but also installs it into the host application <code>extensions</code> folder and starts the host application.</li>
		        <li><code>package</code>: compiles all the extension builds (see below) in release mode and creates the final <i>.ZXP</i> package ready for distribution.</li>
		    </ul>
		</td>
	</tr>
	<tr>
		<td valign="top"><strong>staging</strong></td>
		<td valign="top">String</td>
		<td valign="top">Path of the temporary folder that the plugin should use when building and packaging the extension. The results of the build can be found in this folder. It should be excluded from source control.</td>
	</tr>
	<tr>
		<td valign="top"><strong>source</strong></td>
		<td valign="top">String</td>
		<td valign="top"><p>Path to the input folder containing extension files such as HTML and ExtendScript files that the plugin should compile for debugging and packaging.</p><p>This can either be a folder containing raw source files or the result of the automated build process when using other Grunt plugins such as <i>grunt-contrib-requirejs</i></p></td>
	</tr>
</table>

#### cep.extension
Object that holds extension related information such as extension name, author, version, etc. The properties defined here are used to fill in manifest and other extension related file templates.

<table width="100%">
	<tr>
		<td valign="top" width="140px"><strong>version</strong></td>
		<td valign="top" width="50px">String</td>
		<td valign="top">Extension version number (format: <code>X.X.X</code>).</td>
	</tr>
	<tr>
		<td valign="top"><strong>id</strong></td>
		<td valign="top">String</td>
		<td valign="top">Unique identifier for the extension (used by Creative Cloud and Extension Manager).</td>
	</tr>
	<tr>
		<td valign="top"><strong>name</strong></td>
		<td valign="top">String</td>
		<td valign="top">Extension name displayed in the panel's header and when installing the extension.</td>
	</tr>
	<tr>
		<td valign="top"><strong>author_name</strong></td>
		<td valign="top">String</td>
		<td valign="top">Author or company name.</td>
	</tr>
	<tr>
		<td valign="top"><strong>mainPath</strong></td>
		<td valign="top">String</td>
		<td valign="top">The extension entry point, usually <code>index.html</code> or similar.</td>
	</tr>
	<tr>
		<td valign="top"><strong>scriptPath</strong></td>
		<td valign="top">String</td>
		<td valign="top">Main ExtendScript file for the extension.</td>
	</tr>
	<tr>
		<td valign="top"><strong>description</strong></td>
		<td valign="top">String</td>
		<td valign="top">Description of the extension to display in the Extension Manager (supports HTML markup). This is only displayed if an URL is not entered in the <code>description_href</code> property.</td>
	</tr>
	<tr>
		<td valign="top"><strong>description_href</strong></td>
		<td valign="top">String</td>
		<td valign="top">A URL that points to a HTML file containing the description displayed in the Extension Manager when the extension is selected. If provided <code>description</code> is not used.</td>
	</tr>
	<tr>
		<td valign="top"><strong>license_agreement</strong></td>
		<td valign="top">String</td>
		<td valign="top">License agreement shown when installing the extension (supports HTML).</td>
	</tr>
	<tr>
		<td valign="top"><strong>update_url</strong></td>
		<td valign="top">String</td>
		<td valign="top">URL that contains extension XML update file and packages. Extension Manager will check for extension updates at <code>{update_url}/update.xml</code> and automatically download the updated package from <code>{update_url}/{name}_{version}.zxp</code>.</td>
	</tr>
	<tr>
		<td valign="top"><strong>ui_access</strong></td>
		<td valign="top">String</td>
		<td valign="top">Description of how to access the extension, displayed in Extension Manager (supports HTML markup).</td>
	</tr>
	<tr>
		<td valign="top"><strong>icons</strong></td>
		<td valign="top">Object</td>
		<td valign="top">Extension icons, each icon should be a 23x23px PNG. Check the default values in the <code>/options/defaults.js</code> source file for a full description of the object.</td>
	</tr>
	<tr>
		<td valign="top"><strong>size</strong></td>
		<td valign="top">Object</td>
		<td valign="top">Panel dimensions (in pixels). Check the default values in the <code>/options/defaults.js</code> source file for a full description of the object.</td>
	</tr>
</table>

#### cep.builds
The ability to specify single builds is one of the most powerful feature of the _grunt-cep_ when dealing with complex extensions.

The `cep.builds` property is an array of objects describing the various builds that should be executed, each one resulting in a seperate _ZXP_ file that will be bundled with the final _ZXP_ installer.

Each build object contains the following properties and extends the main task configuration, giving you the ability to override base configuration values on a per-build basis.

<table width="100%">
	<tr>
		<td valign="top" width="140px"><strong>manifest</strong></td>
		<td valign="top" width="50px">String</td>
		<td valign="top">Extension manifest file template, filled in at run-time with extension information. You can use the provided manifest (<code>manifest.cc.xml</code>) or provide your own.</td>
	</tr>
	<tr>
		<td valign="top"><strong>products</strong></td>
		<td valign="top">Array</td>
		<td valign="top">An array of strings containing the name of the products targeted by this build. Valid product names include: <code>photoshop</code>, <code>illustrator</code>, <code>indesign</code>, <code>flash</code>, <code>dreamweaver</code>, <code>premiere</code>, <code>prelude</code>.</td>
	</tr>
	<tr>
		<td valign="top"><strong>families</strong></td>
		<td valign="top">Array</td>
		<td valign="top">Currently unused. An array of strings containing the name of the product families targeted by this build. This can be useful in the future if Adobe releases a new family of products with a name different from Creative Cloud or with a different product set. Valid family names include: <code>CC</code>.</td>
	</tr>
</table>

#### cep.launch
Only used when the <code>launch</code> profile is active, holds information needed to launch a target host application.

<table width="100%">
	<tr>
		<td valign="top" width="140px"><strong>product</strong></td>
		<td valign="top" width="50px">String</td>
		<td valign="top">Host application to launch for debugging, defaults to the first one specified in the first build of the 'builds' array. Valid values are the same of <code>builds.products</code>.</td>
	</tr>
	<tr>
		<td valign="top"><strong>family</strong></td>
		<td valign="top">String</td>
		<td valign="top">Currently unused (see <code>build.families</code>).</td>
	</tr>
</table>

#### cep.package
Only used with the <code>package</code> profile, holds information related to extension packaging and distribution.

<table width="100%">
	<tr>
		<td valign="top" width="140px"><strong>mxi</strong></td>
		<td valign="top" width="50px">String</td>
		<td valign="top">Path to the MXI file template for this extension. You can use the provided manifest template or provide your own.</td>
	</tr>
	<tr>
		<td valign="top"><strong>certificate</strong></td>
		<td valign="top">Object</td>
		<td valign="top"><p>Holds information about the certificate used to sign the extension It has two sub-properties:</p>
		<ul>
		    <li><strong>file</strong> (String): path to the certificate file. If a valid certificate is not found at the specified location, a self-signed one is automatically generated using the password below.</li>
		    <li><strong>password</strong> (String): certificate password.</li>
		</ul>
		</td>
	</tr>
	<tr>
		<td valign="top"><strong>update</strong></td>
		<td valign="top">Object</td>
		<td valign="top"><p>When packaging an extension, the task tries to find a changelog file named as the current extension version (<code>x.x.x.txt</code>) in the <code>changelogs</code> folder. This file is used to fill in the <code>update.xml</code> template file which can be used to support automatic updates for your extension through the Adobe Extension Manager CC application.</p>
		<ul>
		    <li><strong>file</strong> (String): path to the <code>update.xml</code> file template (see below).</li>
		    <li><strong>changelogs</strong> (String): path to the folder containing changelog files.</li>
		</ul>
		</td>
	</tr>
</table>

## Usage Examples
The example configuration below is based on the [_grunt-init-cep_](https://github.com/fcamarlinghi/grunt-init-cep) project template and defines an extension for Adobe Photoshop CC. It registers two tasks (`debug` and `release`) which respectively launch debug inside Adobe Photoshop CC and package the full extension.

All the icons and file templates referenced in the configuration are available in the project template and inside the plugin `res/extension` folder (see the Getting Started section).

```javascript
// cep-config.js
module.exports =
{
    extension: {
        version: '0.1.0',
        id: 'com.example',
        name: 'Example',
        author_name: 'Foo',
        icons: {
            mxi: 'extension/icon-mxi.png',
            panel: {
                light: {
                    normal: 'extension/panel-icons/icon-light.png',
                    hover: 'extension/panel-icons/icon-light-hover.png',
                    disabled: 'extension/panel-icons/icon-light-disabled.png'
                },
                dark: {
                    normal: 'extension/panel-icons/icon-dark.png',
                    hover: 'extension/panel-icons/icon-dark-hover.png',
                    disabled: 'extension/panel-icons/icon-dark-disabled.png'
                },
            }
        },
        size: {
            normal: { width: 320, height: 440 },
            min: { width: 320, height: 440 },
            max: { width: 600, height: 600 },
        },
        mainPath: 'example.html',
        scriptPath: 'extendscript/example.jsx',
    },

    builds: [
        // Adobe Photoshop CC
        {
            manifest: 'extension/manifest.cc.xml',
            products: ['photoshop'],
            source: 'src',
        },
    ],

    'package': {
        mxi: 'extension/example.mxi',
        certificate: {
            file: 'extension/certificate.p12',
            password: 'example_password',
        }
    },
};
```

```javascript
// Grunfile.js
grunt.initConfig({

    // ...

    cep: {
        options: require('./cep-config.js'),

        debug: {
            options: { 
                profile: 'launch',
                launch: {
                    product: 'photoshop',
                },
            }
        },

        release: {
            options: { profile: 'package' }
        },
    },

    // ...
    
});
```
By running: 
```shell
grunt cep:debug
```
_grunt-cep_ will compile all the files in the `src` folders along with other extension files, install the resulting extension and launch Photoshop for testing.

If you want to package the Example extension just run:
```shell
grunt cep:release
```
and a compiled _.ZXP_ file will be created in the `staging` folder.

### Automatic Deploying
Something that I find particularly useful is the ability to automatically deploy the compiled extension to a website. This can be easily achieved using the `grunt-ftp-deploy` plugin:

```javascript
grunt.initConfig({
	// grunt-cep
	cep: {
		// Your config here...
	},

	// FTP deploy
	"ftp-deploy": {
		release: {
			auth: {
				host: 'foo.it',
				port: 21,
				authKey: 'foo'
			},
			src: 'staging',
			dest: '/release',
			exclusions: ['debug', 'package', 'release'] // Exclude work directories
		},
	},
});

// Load plugins
grunt.loadNpmTasks('grunt-cep');
grunt.loadNpmTasks('grunt-ftp-deploy');

// Integrated packaging and deploying
grunt.registerTask('deploy', ['cep:release', 'ftp-deploy:release']);
```

When the `deploy` task is run, the `cep:release` task will package your extension and `ftp-deploy` will upload the relevant files (ZXP installer and "update.xml") to the `foo.it` website.

## Contributing
Contributions are extremely welcome! Extension development for Adobe applications is a complex subject and has a lot of scarcely documented features and issues. Feel free to file issues or contribute fixes to code or documentation.

## License
Copyright &copy; 2014 Francesco Camarlinghi

Licensed under the Apache License, Version 2.0 (the "License"); you may not use this file except in compliance with the License. You may obtain a copy of the License at: http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software distributed under the License is distributed on an "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied. See the License for the specific language governing permissions and limitations under the License.

### Copyrights & Trademarks
Based on code created by [Creative Market](https://creativemarket.com).
The included binaries are [copyright](http://labs.adobe.com/downloads/extensionbuilder3.html) Adobe Systems Incorporated. "Creative Suite" and "Creative Cloud" are registered trademarks of Adobe Systems Incorporated.