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
      - when: {{event}}, {{event}} # can be one or more
        validate:
          # list of validators. Specify one or more.
          - do: {{validator}}
            {{option}}: # name of an option supported by the validator.
              {{sub-option}}: {{value}} # an option will have one or more sub-options.
        pass: # list of actions to be executed if all validation passes. Specify one or more. Omit this tag if no actions are needed.
          - do: {{action}}
        fail: # list of actions to be executed when at least one validation fails. Specify one or more. Omit this tag if no actions are needed.
          - do: {{action}}
        error: # list of actions to be executed when at least one validator throws an error. Specify one or more. Omit this tag if no actions are needed.
          - do: {{action}}

.. note::
    There are some default actions that'll be automatically applied based on the events specified

Validators
------------

Validators are checks that mergeable will process in order to determine whether an action should be executed.

.. note::
    Each validator have certain events that it can support, so keep an eye out for them.


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

.. warning::
    ``owners`` sub-option only works in public repos right now, we have plans to enable it for private repos in the future.

Supported Events:
::

    'pull_request.*', 'pull_request_review.*'


Assignee
^^^^^^^^^^

::

    - do: assignee
      max:
        count: 2 # There should not be more than 2 assignees
        message: 'test string' # this is optional
      min:
        count: 2 # min number of assignees
        message: 'test string' # this is optional

Supported Events:
::

    'pull_request.*', 'pull_request_review.*', 'issues.*'


Dependent
^^^^^^^^^^
Validates that the files specified are all part of a pull request (added or modified).
::

  - do: dependent
    files: ['package.json', 'yarn.lock'] # list of files that are dependent on one another and must all be part of the changes in a PR.
    message: 'Custom message...' # this is optional, a default message is used when not specified.

Alternatively, to validate dependent files only when a specific file is part of the pull request, use the changed option:

::

    - do: dependent
        changed:
          file: package.json
          files: ['package-lock.json', 'yarn.lock']
        message: 'Custom message...' # this is optional, a default message is used when not specified.

The above will validate that both the files package-lock.json and yarn.lock is part of the modified or added files if and only if package.json is part of the PR.

Supported Events:
::

    'pull_request.*', 'pull_request_review.*'

Size
^^^^^^^^^^
``size`` validates that the size of changes in the pull request conform to a specified limit. We can pass in three options: ``total``, ``additions`` or ``deletions``. Each of this take in a count and message.
Validates that the files specified are all part of a pull request (added or modified).
::

  - do: size
    lines:
      total:
        count: 500
        message: Change is very large. Should be under 500 lines of additions and deletions.
      additions:
        count: 250
        message: Change is very large. Should be under 250 lines of additions
      deletions:
        count: 500
        message: Change is very large. Should be under 250 lines of deletions.

``max`` is an alias for total, so the below configuration is still valid.

::

     - do: size
       lines:
         max:
           count: 500
           message: Change is very large. Should be under 500 lines of additions and deletions.

It also supports an ``ignore`` setting to allow excluding certain files from the total size (e.g. for ignoring automatically generated files that increase the size a lot).

This option supports glob patterns, so you can provide either the path to a specific file or ignore whole patterns:

::

     - do: size
       ignore: ['package-lock.json', 'src/tests/__snapshots__/**', 'docs/*.md']
       lines:
         total:
           count: 500
           message: Change is very large. Should be under 500 lines of additions and deletions

Note that the glob functionality is powered by the minimatch library. Please see their documentation for details on how glob patterns are handled and possible discrepancies with glob handling in other tools.

The size validator currently excludes from the size count any files that were completely deleted in the PR.

Supported Events:
::

    'pull_request.*', 'pull_request_review.*'

Description
^^^^^^^^^^^^^^

::

    - do: description
      no_empty:
         enabled: false # Cannot be empty when true.
         message: 'Custom message...' # this is optional, a default message is used when not specified.
      must_include:
         regex: '### Goals|### Changes'
         regex_flag: 'none' # Optional. Specify the flag for Regex. default is 'i', to disable default use 'none'
         message: >
          Please describe the goals (why) and changes (what) of the PR.
        # message is is optional, a default message is used when not specified.
      must_exclude:
         regex: 'DO NOT MERGE'
         regex_flag: 'none' # Optional. Specify the flag for Regex. default is 'i', to disable default use 'none'
         message: 'Custom message...' # optional
      begins_with:
         match: '### Goals' # or array of strings
         message: 'Some message...' #optional
      ends_with:
         match: 'Any last sentence' # array of strings
         message: 'Come message...' # optional

Supported Events:
::

    'pull_request.*', 'pull_request_review.*', 'issues.*'

Actions
------------


