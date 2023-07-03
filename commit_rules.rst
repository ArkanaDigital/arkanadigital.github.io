##################################
|TITLE| (|DATE|)
##################################

.. |TITLE| replace:: How to manage your git repository
.. |DATE| replace:: 2023/03/07

.. contents:: Table of contents
    :depth: 4

.. sectnum::



************
Introduction
************

Please use the following rules to manage your repository on arkana

Use a consistent naming convention for branches
===============================================

A good naming convention for your branches can make it easier to know who is working on what and what
the branch's purpose is. The format could include the creator's name, the type
of change (feature, bugfix, etc.), and a brief description.

Example:
feature/sale_order_tax_adjustment

Protect the main branches
=========================

Main branches (like master or main) should be protected. That means no direct pushes - all changes should go through code review.

Example:

.. code-block:: vim

    # Avoid:
    git checkout main
    git commit -m "A quick fix"
    git push origin main
    
    # Instead, use:
    git checkout -b fix/sale_order_quick_fix
    # make changes
    git commit -m "Fix for sale order"
    git push origin fix/sale_order_quick_fix
    Then open a pull request for the changes to be reviewed.

Always use pull requests for changes
====================================

Pull requests give your team an opportunity to review the code before it's merged into the main branch.
It's a best practice to never directly push to the main branch. Pull requests can help ensure that
at least one other person has looked over the code and agreed that it's ready to go.

Example:

.. code-block:: bash

    # First, you create your feature branch
    git checkout -b john/feature/add-login-button
    # Then, make changes and commit them
    git commit -m "Added login button"
    # Then, push to your branch
    git push origin john/feature/add-login-button
    # Finally, go to your Git hosting service (like GitHub) and create a new pull request

Use clear and concise commit messages
=====================================

Commit messages should clearly and concisely describe the changes. This helps others understand the purpose
of the change and makes it easier to find specific changes later.

Example:

.. code-block:: bash

    git commit -m "Add logout functionality"

Rebase frequently to avoid merge conflicts
==========================================

Rebasing often will ensure that your feature branch is up-to-date with the main branch.
This can help prevent large, complex merge conflicts.

Example:

.. code-block:: bash

    # While in your feature branch
    git fetch origin
    git rebase origin/main

Use .gitignore
==============

Use a .gitignore file to prevent certain files from being committed to your repository.
This could include user-specific settings, build artifacts, or secret keys.

Example:
Create a .gitignore file in your project root with the following content:

.. code-block:: bash

    # .gitignore
    *.pyc
    *.pyo
    *.csv
    env/
    __pycache__/
    *.pot

Atomic commits
==============

Try to make each commit a self-contained change to the code. This makes it easier to understand the change
and to roll back if necessary. It's better to have more smaller commits than fewer larger ones.

Example:
Rather than making one large commit that adds a feature, consider committing the tests, implementation,
and documentation separately.

Don't commit directly to the main branch
========================================

This is to avoid introducing errors into the main branch and to ensure that all changes are reviewed and
tested before being deployed.

Example:
Instead of committing to the main branch, you create a new branch, make your changes there, 
and then open a pull request.

Use git hooks
=============

Git hooks are scripts that run automatically when a specific event occurs in a Git repository. 
They let you automate all sorts of workflows.

Example:
You might set up a pre-commit hook to run your test suite, preventing a commit from being made if the tests fail.

Resolve conflicts in favor of the main branch
=============================================

If you have a merge conflict between your branch and the main branch, 
the default behavior should be to resolve the conflict in favor of the main branch. This ensures that your branch
does not inadvertently overwrite someone else's work.

Example:
While rebasing, if a conflict arises, always consider the changes in the main branch before overwriting them with your branch changes.

*****
Rules
*****

Create a new repository
=======================

To initialize the branches on the Github repository, locally run the following commands:

.. code-block:: bash

    export REPO=name_of_the_repo
    mkdir $REPO
    cd $REPO
    git init

Then, for each branch run:

.. code-block:: bash

    export BRANCH=16.0
    git checkout -b $BRANCH

then after checkout new breanch you can commit your code after adding some files/code.
You have to put your requirements.txt if you have external dependencies, create requirements.txt
by executing following command:

.. code-block:: bash

    touch requirements.txt

then you can edit with your favorite editors like vim, pico or nano, after editing your file
you can add and commit it

.. code-block:: bash

    git add . 
    git commit
    git remote add origin git@github.com:ArkanaDigital/$REPO
    git push origin $BRANCH:$BRANCH

Merge/squash commits in pull requests
=====================================

It is good practice that every time that you do a pull request, the commit history contains the minimum necessary commit messages.

Merge/squash your own commits
If you have worked locally on some feature, and produced a lot of commits in the process
to reach to the final state, the rest of the world does not necessarily need to know your
intermediate back and forth activity. You may just want to merge all these commits into
a single one, and provide a nice summary of the changes that the commit introduces.

In order to do squash your own commits you can use ``git rebase -i <first commit SHA>``, 
where ``<first commit SHA>`` is the commit SHA from which you want to start the review,
or git rebase -i HEAD~N, where N is the number of commits that you have introduced.

Example:

.. code-block:: bash

    pick 1949129 [IMP] module: Introduce feature A
    pick d2cf643 Fix bug 1 of feature A
    pick 42bd9e8 Fix bug 2 of feature A
    pick 7f767d5 Fix bug 3 of feature A

You want a single commit with one description only, do git rebase HEAD~4. Then change the lines to:

.. code-block:: bash

    pick 1949129 [IMP] module: Introduce feature A
    fixup d2cf643 Fix bug 1 of feature A
    fixup 42bd9e8 Fix bug 2 of feature A
    fixup 7f767d5 Fix bug 3 of feature A

This produces the following result:

.. code-block:: bash

    1949129 [IMP] module: Introduce feature A
