# VCS area of control

The main idea: the more project-related things you keep and version-track inside a project – the better.

[12 Factors Codebase](https://12factor.net/codebase)

[12 Factors Dependencies](https://12factor.net/dependencies)

[Infrastructure as code](https://blog.gruntwork.io/why-we-use-terraform-and-not-chef-puppet-ansible-saltstack-or-cloudformation-7989dad2865c)

GIT makes some basic things unnecessarily hard.
Main design mistake: GIT shares working dir among the branches.
This requires us to use `npm install` after every `git checkout somebranch`.
Which is slow, easy to forget (can be partially solved with automation) and requires an internet connection.
Some aspects of this, like internet connection requirement, can be partially solved by better package managers. 
See [Yarn](https://github.com/yarnpkg/yarn)

It would be much better if GIT fixed this.

See https://twitter.com/adriancolyer/status/790455299450732544
and https://twitter.com/adriancolyer/status/790817674964197376

Until then, including `node_modules` under VCS can be considered a practical
choice for some cases.
