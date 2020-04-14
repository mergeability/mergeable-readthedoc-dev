Configuration
=====================================

**Mergeable** is **highly** configurable.

First, you'll need to start by creating a `.github/mergeable.yml` file in your repository.

.. hint::
  Check out our :ref:Receipe page for examples and most commonly used settings

Next, we'll go into how the configuration is structured.

Basics
------------------

Mergeable configuration consists of an array of independent rule sets where each rule set needs to have the following properties:

when:
    specify webhook event(s) in which to process the rule set
validate:
    specify a series of validator to be checked
pass:
    specify a series of action to execute if the validation suite returned a `pass`
fail:
    specify a series of action to execute if the validation suite returned a `fail`
error:
    specify a series of action to execute if the validation suite returned a `error`

Here is a full example of how a rule set looks

::

    version: 2
    mergeable:
        - when: pull_request.*
          validate:
            - do: title
              must_exclude:
                regex: [WIP]
                message: 'PR is still WIP'
          pass:
            - do: checks # default pass case
              status: 'success' # Can be: success, failure, neutral, cancelled, timed_out, or action_required
              payload:
              title: 'Mergeable Run have been Completed!'
              summary: "All the validators have returned 'pass'! \n Here are some stats of the run: \n {{validationCount}} validations were ran"
          fail:
            - do: checks # default fail case
              status: 'failure' # Can be: success, failure, neutral, cancelled, timed_out, or action_required
              payload:
              title: 'Mergeable Run have been Completed!'
              summary: |
                ### Status: {{toUpperCase validationStatus}}
                  Here are some stats of the run:
                  {{validationCount}} validations were ran.
                  {{passCount}} PASSED
                  {{failCount}} FAILED
              text: "{{#each validationSuites}}\n
                #### {{{statusIcon status}}} Validator: {{toUpperCase name}}\n
                {{#each validations }} * {{{statusIcon status}}} ***{{{ description }}}***\n
                     Input : {{{details.input}}}\n
                     Settings : {{{displaySettings details.settings}}}\n
                     {{/each}}\n
                {{/each}}"
          error:
            - do: checks # default error case
              status: 'action_required' # Can be: success, failure, neutral, cancelled, timed_out, or action_required
              payload:
              title: 'Mergeable Run have been Completed!'
              summary: |
                ### Status: {{toUpperCase validationStatus}}
                  Here are some stats of the run:
                  {{validationCount}} validations were ran.
                  {{passCount}} PASSED
                  {{failCount}} FAILED
                  {{errorCount}} ERRORED
              text: "{{#each validationSuites}}\n
                #### {{{statusIcon status}}} Validator: {{toUpperCase name}}\n
                Status {{toUpperCase status}}
                {{#each validations }} * {{{statusIcon status}}} ***{{{ description }}}***\n
                     Input : {{{details.input}}}\n
                     Settings : {{{displaySettings details.settings}}}\n
                      {{#if details.error}}
                      Error : {{{details.error}}}\n
                      {{/if}}
                      {{/each}}\n
                {{/each}}"

.. note::
    There are some default actions that'll be automatically applied based on the events specified

Validators
------------

Validators are checks that mergeable will process in order to determine whether an action should be done.


Approvals
^^^^^^^^^^

::

    - do: approvals
      min:
        count: 2 # Number of minimum reviewers. In this case 2.
        message: 'Custom message...'
      required:
        reviewers: [ user1, user2 ] # list of github usernames required to review
        owners: true # Optional boolean. When true, the file .github/CODEOWNER is read and owners made required reviewers
        assignees: true # Optional boolean. When true, PR assignees are made required reviewers.
        pending_reviewer: true # Optional boolean. When true, all the requested reviewer's approval is required
        message: 'Custom message...'
      block:
        changes_requested: true #If true, block all approvals when one of the reviewers gave 'changes_requested' review
        message: 'Custom message...'

.. note::
    `owners` sub-option only works in public repos right now, we have plans to enable it for private repos in the future.

Supported Events:
::

    'pull_request.*', 'pull_request_review.*'


