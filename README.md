# Scm Base
[![Version][npm-image]][npm-url] ![Downloads][downloads-image] [![Build Status][wercker-image]][wercker-url] [![Open Issues][issues-image]][issues-url] [![Dependency Status][daviddm-image]][daviddm-url] ![License][license-image]

> Base class for defining the behavior between screwdriver and source control management systems

## Usage

```bash
npm install screwdriver-scm-base
```

## Interface
This is a promise based interface for interacting with a source control management system

### configure
The `configure` function takes in an object and resets the configuration values

### formatScmUrl
Required parameters:

| Parameter        | Type  |  Description |
| :-------------   | :---- | :-------------|
| scmUrl        | String | Scm Url to format |

#### Expected Outcome
A formatted scm url to be used as a unique key. This function is synchronous.

### getPermissions
Required parameters:

| Parameter        | Type  |  Description |
| :-------------   | :---- | :-------------|
| config        | Object | Configuration Object |
| config.scmUrl | String | The scmUrl to get permissions on |
| config.token | String | The github token to check permissions on |

#### Expected Outcome
Permissions for a given token on a repository in the form of:
```
{
    admin: true,
    push: true,
    pull: true
}
```

#### Expected Promise response
1. Resolve with a permissions object for the repository
2. Reject if not able to get permissions

### getCommitSha
Required parameters:

| Parameter        | Type  |  Description |
| :-------------   | :---- | :-------------|
| config        | Object | Configuration Object |
| config.scmUrl | String | The scmUrl to get permissions on |
| config.token | String | The github token to check permissions on |

#### Expected Outcome
The commit sha for a given branch on a repository.

#### Expected Promise response
1. Resolve with a commit sha string for the given `scmUrl`
2. Reject if not able to get a sha

### updateCommitStatus
The parameters required are:

| Parameter        | Type  | Required | Description |
| :-------------   | :---- | :------- | :-------------|
| config        | Object | true | Configuration Object |
| config.scmUrl | String | true | The scmUrl to get permissions on |
| config.token | String | true | The github token to check permissions on |
| config.sha | String | true | The github sha to update a status for |
| config.buildStatus | String | true | The screwdriver build status to translate into github commit status |
| config.url | String | false | The target url for setting up details |

#### Expected Outcome
Update the commit status for a given repository and sha.

#### Expected Promise Response
1. Resolve when the commit status was updated
2. Reject if the commit status fails to update

### getFile
The parameters required are:

| Parameter        | Type  | Required | Description |
| :-------------   | :---- | :------- | :-------------|
| config        | Object | true | Configuration Object |
| config.scmUrl | String | true | The scmUrl to get permissions on |
| config.token | String | true |The github token to check permissions on |
| config.path | String | true | The path to the file on github to read |
| config.ref | String | false | The reference to the github repo, could be a branch or sha |

#### Expected Outcome
The contents of the file at `path` in the repository

#### Expected Promise Response
1. Resolve with the contents of `path`
2. Reject if the `path` cannot be downloaded, decoded, or is not a file

## Extending
To make use of the validation functions, the functions to override are:
1. `formatScmUrl` 
2. `_getPermissions`
3. `_getCommitSha`
4. `_updateCommitStatus`
5. `_getFile`
6. `stats` 

```js
class MyScm extends ScmBase {
    // Implement the interface
    _getFile(config) {
        // do stuff here to lookup scmUrl
        return Promise.resolve('these are contents that are gotten')
    }
}

const scm = new MyScm({});
scm.getFile({
    scmUrl: 'git@github.com:repo/foo.git#master',
    path: 'screwdriver.yaml',
    token: 'abcdefg'
}).then(data => {
    // do something...
});
```

## Testing

```bash
npm test
```

## License

Code licensed under the BSD 3-Clause license. See LICENSE file for terms.

[npm-image]: https://img.shields.io/npm/v/screwdriver-scm-base.svg
[npm-url]: https://npmjs.org/package/screwdriver-scm-base
[downloads-image]: https://img.shields.io/npm/dt/screwdriver-scm-base.svg
[license-image]: https://img.shields.io/npm/l/screwdriver-scm-base.svg
[issues-image]: https://img.shields.io/github/issues/screwdriver-cd/scm-base.svg
[issues-url]: https://github.com/screwdriver-cd/scm-base/issues
[wercker-image]: https://app.wercker.com/status/047a025b5041a63e8fa01aa29b9df0c0
[wercker-url]: https://app.wercker.com/project/bykey/047a025b5041a63e8fa01aa29b9df0c0
[daviddm-image]: https://david-dm.org/screwdriver-cd/scm-base.svg?theme=shields.io
[daviddm-url]: https://david-dm.org/screwdriver-cd/scm-base
