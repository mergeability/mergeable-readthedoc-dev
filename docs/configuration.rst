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

Validator List

.. toctree::
    validators/approval.rst
    validators/assignee.rst
    validators/changeset.rst
    validators/commit.rst
    validators/dependent.rst
    validators/description.rst
    validators/label.rst
    validators/milestone.rst
    validators/project.rst
    validators/size.rst
    validators/stale.rst
    validators/title.rst


Actions
------------


