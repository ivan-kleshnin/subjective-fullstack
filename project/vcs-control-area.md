# VCS area of control

The main idea: the more project-related things you keep and track inside a project – the better.

[12 Factors Codebase](https://12factor.net/codebase)

[12 Factors Dependencies](https://12factor.net/dependencies)

[Infrastructure as code](https://blog.gruntwork.io/why-we-use-terraform-and-not-chef-puppet-ansible-saltstack-or-cloudformation-7989dad2865c)

GIT makes some basic things unnecessarily hard.
Main design mistake: GIT shares working dir among the branches.
This requires us to use `npm install` after `git checkout somebranch`.
Which is easy to forget and even with automation it requires time and internet connection.

It would be much better if GIT fixed this.

See https://twitter.com/adriancolyer/status/790455299450732544
and https://twitter.com/adriancolyer/status/790817674964197376

Until then, including `node_modules` under VCS can be considered a practical
choice for some cases.
