## Development guideline
```standard-version``` package is used for generating *Change Logs* and *Version number* tag. In order use this features, we need to use the below ```git``` commit message types.

### Commit message uses **"\<type>: \<description>"**
```git commit -a -m"<type>[optional scope]: <description>"```

* **feat**: introduces a new feature to the codebase (this correlates with a MINORin SemVer es: 2.0.0 -> 2.1.0).
* **fix**: a bugfix in your codebase (this correlates with a PATCH in semVer es: 2.0.0 -> 2.0.1).
* **BREAKING CHANGE**: is a total change of your code, this is also can be used with a previous tag like BREAKING CHANGE: feat: <description> (this correlates with a MAJOR in SemVer es: 2.0.0 -> 3.0.0).
* **docs**: a change in the README or documentation
* **refactor**: a change in production code focused on upgrade code readability and style

Refereces:

* ["How to generate Changelog using Conventional Commits"](https://medium.com/jobtome-engineering/how-to-generate-changelog-using-conventional-commits-10be40f5826c)
* 
