## Development guideline
```commit-and-tag-version``` package is used for generating *Change Logs* and *Version number* tag. In order use this features, we need to use the below ```git``` commit message types.

### Commit message uses **"\<type>: \<description>"**
```git commit -a -m"<type>[optional scope]: <description>"```

* **feat**: introduces a new feature to the codebase (this correlates with a MINORin SemVer es: 2.0.0 -> 2.1.0).
* **fix**: a bugfix in your codebase (this correlates with a PATCH in semVer es: 2.0.0 -> 2.0.1).
* **chore**: a change is irrelevant to functions
* **refactor**: a change in production code focused on upgrade code readability and style
* **docs**: a change in the README or documentation
* **style**: a change in the user interface
* **perf**: a change for performance upgrade
* **test**, **build**, **ci**: a change for test, build and ci respectively 
* `feat!:`, or `fix!:`, `refactor!:`, etc., which represent a breaking change (indicated by the !) and will result in a SemVer major. (this correlates with a MAJOR in SemVer es: 2.0.0 -> 3.0.0).

Refereces:

* ["How to generate Changelog using Conventional Commits"](https://medium.com/jobtome-engineering/how-to-generate-changelog-using-conventional-commits-10be40f5826c)
