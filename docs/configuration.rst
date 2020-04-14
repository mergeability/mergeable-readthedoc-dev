Configuration
=====================================

**Mergeable** is **highly** configurable.

First, you'll need to start by creating a `.github/mergeable.yml` file in your repository.

.. hint::
  Check out our :ref:Receipe page for examples and most commonly used settings

Next, we'll go into how the configuration is structured.

Basics
=====================================

Mergeable configuration consists of an array of rule sets where each rule set needs to have the following properties:

term (when)
    specify webhook event(s) in which to process the rule set
term (validate)
    specify a series of validator to be checked
term (pass)
    specify a series of action to execute if the validation suite returned a `pass`
term (fail)
    specify a series of action to execute if the validation suite returned a `fail`
term (error)
    specify a series of action to execute if the validation suite returned a `error`

```
    - when: pull_request.*
```

.. note::
    testing note

.. hint::
    testing Hint

.. warning::
    testing warning
