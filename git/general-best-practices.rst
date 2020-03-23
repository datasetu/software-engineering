Best practices for large projects
---------------------------------

**NOTE**: This is still WIP

The following are some best practices for git projects curated from various sources.

* **Limit the number of maintainers**: A small to medium sized project should ideally have just one maintainer. Only they should have write access to the repository. All other collaborators should open pull requests to add features, fix bugs etc. Any PR that has to be merged has to be approved by the maintainer(s)

* **Use branch protection**: If the project is too large, then there might be a need to have several maintainers (aka CODEOWNERS). In that case, to avoid accidents, it is recommended to have branch protection to prevent deletion of branches, force pushes etc. (See `this <https://help.github.com/en/enterprise/2.16/admin/developer-workflow/configuring-protected-branches-and-required-status-checks>`_)

* **Tests**: Always have tests no matter how small the project is. This will be dealt in depth at a later stage.

* **Minimum acceptable coverage**: Merely writing tests for a project is pointless if the percentage of code covered by the tests is not known. Minimum acceptable coverage should be ``80 %``

* **Set up a CI pipeline**: Having a tool that automaticaly runs tests against pull requests can be very useful. If the coverage is high (higher 80s and above) and the tests pass, then there is a strong chance that the incoming code is bug-free (note that it is very hard to guarantee it)

* **Don't merge if build doesn't pass**: Maintainers should only accept pull requests that have passed all quality checks and code reviews before merging. As a rule, no PR should be merged if any of the checks fail.

* **Integrate with external tools**: Integrating the project with external tools for ensuring code quality, coverage etc is essential. These tools will help in doing a first-pass check before it goes to a human reviewer.

* **Release cycles**: Having periodic release cycles that follow semver is paramount.

* **Addressing issues and PRs**: Tagging, assigning and resolving issues and PRs as soon as possible is very important. Several high severity CVEs have first come up as issues.

* **Backing up the repository**: Scheduling daily backups of the repository can come in quite handy when someone accidentally or intentionally deletes the repository (happens more often than people care to admit)
