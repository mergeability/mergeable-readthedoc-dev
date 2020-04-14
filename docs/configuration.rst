Configuration
=====================================

**Mergeable** is **highly** configurable.

First, you'll need to start by creating a `.github/mergeable.yml` file in your repository.

.. hint::
  Check out our :ref:Receipe page for examples and most commonly used settings

Next, we'll go into how the configuration is structured.

Basics
=====================================

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
