# Repo Housekeeper

This repository contains some simple tools for helping to keep the `puppetlabs/*` repository
namespace cleaned up and welcoming. For example, the first task is a weekly email reminder to
our community-experience@puppet.com mailing list with a sampling of some of our oldest open
pull requests and some instructions on cleaning them up.

## Using the tools

Note: Right now, there's only a few tasks; `stale_pulls` that calls out old stagnant pull requests,
and `codeowner_coverage` that just looks for public repositories without `CODEOWNERS` files.

The tasks are self-configured in the repository as `config.yaml`. You'll also need the Community
Team sendgrid token for sending mail. You can get that from the Community Team for testing, but
when running as a GitHub action, it's accessible as the `SENDGRID_PASSWORD` secret.

### Running during development

Before running any task, you'll need to export the SendGrid token as an environment variable.
Note that many tasks will expect other environment variables set as well. For example, this
is how you would run the `stale_pulls` task:

```
$ bundle install --path vendor/bundle
...
$ export SENDGRID_PASSWORD=<sendgrid token -- get this from the Community Team>
$ export EMAIL_ADDRESS='my_email_address@puppet.com'
$ bundle exec rake stale_pulls
```

## Tasks

###  `stale_pulls`

This task is very simple. It gets a list of the 100 oldest open pull requests in the entire
`@puppetlabs` namespace. Then it takes a random sampling of 15 of those and emails a report.
The sampling ensures that it's not just reporting about the same PRs every week.

It's configured via environment variables in the [workflow](https://github.com/puppetlabs/repo_housekeeper/blob/master/.github/workflows/stale_pulls-workflow.yml).

* `GITHUB_TOKEN`: comes automatically with the workflow and allows higher API rate limits
* `SENDGRID_PASSWORD`: configured as a GitHub secret
* `EMAIL_ADDRESS`: the email address to send the report to.

The email content is configured by [html](https://github.com/puppetlabs/repo_housekeeper/blob/master/templates/stale_prs.html.erb) and [text](https://github.com/puppetlabs/repo_housekeeper/blob/master/templates/stale_prs.txt.erb)
format templates.

###  `codeowner_coverage`

This task just iterates all public repositories and builds a list of those without `CODEOWNERS`. Right
after we did the push to get all the repos covered, that list should be empty. But over time as people
create new repos, this task will help keep that coverage. This task requires the escalated privileges
of the `GITHUB_TOKEN_EXTENDED` token.

It's currently configured via environment variables in the poorly named [workflow](https://github.com/puppetlabs/repo_housekeeper/blob/master/.github/workflows/stale_pulls-workflow.yml)
file.

* `GITHUB_TOKEN_EXTENDED`: a personal access token created on @binford2k's account that allows org-team access.
* `SENDGRID_PASSWORD`: configured as a GitHub secret
* `EMAIL_ADDRESS`: the email address to send the report to.

The email content is configured by [html](https://github.com/puppetlabs/repo_housekeeper/blob/master/templates/stale_prs.html.erb) and [text](https://github.com/puppetlabs/repo_housekeeper/blob/master/templates/stale_prs.txt.erb)
format templates.

