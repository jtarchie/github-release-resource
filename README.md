# GitHub Releases Resource

Fetches and creates versioned GitHub resources.

> If you're seeing rate limits affecting you then please add a token to the source
> configuration. This will increase your number of allowed requests.

## Source Configuration

* `user`: *Required.* The GitHub username or organization name for the
  repository that the releases are in.

* `repository`: *Required.* The repository name that contains the releases.

* `access_token`: *Optional.* Used for accessing a release in a private-repo
   during an `in` and pushing a release to a repo during an `out`. The access
   token you create is only required to have the `repo` or `public_repo` scope.

* `github_api_url`: *Optional.* If you use a non-public GitHub deployment then
  you can set your API URL here.

* `drafts`: *Optional.* When set to `true` only returns draft releases with
  semver compliant tags. Defaults to `false` returning only final releases
  with semver compliant tags.

### Example

``` yaml
- name: gh-release
  type: github-release
  source:
    user: concourse
    repository: concourse
    access_token: abcdef1234567890
```

``` yaml
- get: gh-release
```

``` yaml
- put: gh-release
  params:
    name: path/to/name/file
    tag: path/to/tag/file
    body: path/to/body/file
    globs:
    - paths/to/files/to/upload-*.tgz
```

## Behavior

### `check`: Check for released versions.

Releases are listed and sorted by their tag, using
[semver](http://semver.org) semantics if possible.

### `in`: Fetch assets from a release.

Fetches artifacts from the given release version. If the version is not
specified, the latest version is chosen using [semver](http://semver.org)
semantics.

Also creates the following files:

* `tag` containing the git tag name of the release being fetched.
* `version` containing the version determined by the git tag of the release being fetched.

#### Parameters

* `globs`: *Optional.* A list of globs for files that will be downloaded from
  the release. If not specified, all assets will be fetched.

* `include_source_tarball`: *Optional.* Enables downloading of the source
  artifact tarball for the release as `source.tar.gz`. Defaults to `false`.

* `include_source_zip`: *Optional.* Enables downloading of the source
  artifact zip for the release as `source.zip`. Defaults to `false`.

### `out`: Publish a release.

Given a name specified in `name`, a body specified in `body`, and the tag to use
specified in `tag`, this creates a release on GitHub then uploads the files
matching the patterns in `globs` to the release.

#### Parameters

* `name`: *Required.* A path to a file containing the name of the release.

* `tag`: *Required.* A path to a file containing the name of the Git tag to use
  for the release.

* `commitish`: *Optional.* A path to a file containing the commitish (SHA, tag,
  branch name) that the release should be associated with.

* `body`: *Optional.* A path to a file containing the body text of the release.

* `globs`: *Optional.* A list of globs for files that will be uploaded alongside
  the created release.

* `draft`: *Optional.* A boolean to set the release in draft mode or not.
