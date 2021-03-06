
# airpr-honeybadger

> An ember-cli-deploy plugin that first __integrates honeybadger to your application__ and second __uploads your source maps to honeybadger__.

This plugin will integrate honeybadger into your `index.html` file and uploads generated source maps via honeybadger API. During upload of the source maps to honeybadger you must provide `source_code` string that will match the error raised in honeybadger with the same value. To do this, honeybadger snippet is injected into `index.html` after the revision data is generated with revision key as `source_code`. After all, the plugin uploads the source maps with the same revision key as injected into `index.html`.

[You can take a look here how honeybadger defines its flow with source maps][5]

## What is an ember-cli-deploy plugin?

A plugin is an addon that can be executed as a part of the ember-cli-deploy pipeline. A plugin will implement one or more of the ember-cli-deploy's pipeline hooks.

For more information on what plugins are and how they work, please refer to the [Plugin Documentation][1].

## Quick Start
To get up and running quickly, do the following:

- Ensure [ember-cli-deploy-build][2] is installed and configured
- Ensure [ember-cli-deploy-revision-data][4] is installed and configured

- Install this plugin

```bash
$ ember install airpr-honeybadger
```

- Run the pipeline

```bash
$ ember deploy
```

## Installation
Run the following command in your terminal:

```bash
ember install airpr-honeybadger
```

## ember-cli-deploy Hooks Implemented

For detailed information on what plugin hooks are and how they work, please refer to the [Plugin Documentation][6].

- `willUpload` (inject honeybadger snippet)
- `upload` (upload source maps)
- `didDeploy` (send information about deploy to honeybadger)

## Configuration Options

For detailed information on how configuration of plugins works, please refer to the [Plugin Documentation][7].

### accessToken (required)

honeybadger client access token to trigger errors.

### accessServerToken (required)

honeybadger server access token to allow uploading source maps to your account.

### minifiedPrependUrl (required)

honeybadger demands to upload both source map and URL to minified file. This config let's you define the prepend to URL your assets will be available after upload. E.g. if you are using `ember-cli-deploy-s3`, add the same string as in `fingerprint/prepend` option in your `ember-cli-deploy` file.

This property can be a string or a function returning a string or an array of strings, which is passed the deploy `context`. Use a function here if you want to add multiple sources for multiple deploy locations. A possible scenario would be that you deploy both by git hash and a production deploy:

```javascript
minifiedPrependUrl: function(context) {
  return [
    'https://dev.example.com/' + context.revisionData.revisionKey + '/',
    'https://app.example.com/'
  ];
}
```

### enabled

Defines internal `enabled` honeybadger config.

*Default:* `true`
*Alternatives:* `false`

### environment

Defines internal `environment` honeybadger config.

*Default:* environment setting from ember-cli-deploy-build || `production`
*Alternatives:* any other env

### captureUncaught

Defines internal `captureUncaught` honeybadger config.

*Default:* `true`
*Alternatives:* `false`

### username

honeybadger `local_username` config that is displayed in Deploys section.

*Default:* `unknown user`
*Alternatives:* any string or function returning string

### honeybadgerFileURI

Defines the URI to download the honeybadger JS file.

*Default:* `//js.honeybadger.io/v0.5/honeybadger.min.js`
*Alternatives:* any string that points to the file (e.g. https://mycdn.com/js/honeybadger.min.js)

### additionalFiles

Defines additional sourcemap files to be uploaded to honeybadger. Use this if you build .js files other than `projectName.js` and `vendor.js`.

Set to an array of filenames excluding their extentions. For example in an app that builds `exta-functionality.js` and `additional-library.js` set to `['exta-functionality', 'additional-library']`.

*Default:* `[]`
*Alternatives:* an array of filenames without extensions

## Prerequisites

The following properties are expected to be present on the deployment `context` object:

- `distDir`      (provided by [ember-cli-deploy-build][2])
- `distFiles`    (provided by [ember-cli-deploy-build][2])
- `revisionData` (provided by [ember-cli-deploy-revision-data][4])

## Plugins known to work well with this one

* [ember-cli-deploy-redis](https://github.com/ember-cli-deploy/ember-cli-deploy-redis)
* [ember-cli-deploy-s3](https://github.com/ember-cli-deploy/ember-cli-deploy-s3)

## Known issues
* You must enable source maps in your `ember-cli-build.js` file, even in `production` env. However, you don't need to upload them anywhere (they won't be available online) - they are only needed during `upload` phase in deploy pipeline.
* If you are using gzipping, make sure that you are not gzipping source maps - honeybadger will not accept gzipped files.
* If you bump in any other issue in your deployment flow, give me a sign and I'll try to make this addon more flexible for you.

[1]: http://ember-cli-deploy.com/docs/v0.6.x/ "Plugin Documentation"
[2]: https://github.com/ember-cli-deploy/ember-cli-deploy-build "ember-cli-deploy-build"
[3]: https://github.com/ember-cli/ember-cli-deploy "ember-cli-deploy"
[4]: https://github.com/ember-cli-deploy/ember-cli-deploy-revision-data "ember-cli-deploy-revision-data"
[5]: https://docs.honeybadger.io/guides/source-maps.html "honeybadger Documentation"
[6]: http://ember-cli-deploy.com/docs/v0.6.x/pipeline-hooks/ "Plugin Documentation"
[7]: http://ember-cli-deploy.com/docs/v0.6.x/configuration-overview/ "Plugin Documentation"
