React vs Vue
=================

Quickstart
----------

.. code-block:: bash

   $ yarn

   $ ./scripts/github.sh

   # for Vue.js
   $ cd vue/
   $ yarn
   $ npm run build

   # for Vue.js
   $ cd vue/
   $ yarn
   $ npm run build

   # for React
   $ cd react/
   $ yarn
   $ npm run build

Structure
--------

- `vue/ <vue/>`_: `Vue.js`_ version
- `react/ <react/>`_: `React`_ version
- `lib/ <lib/>`_: Common code (reducers/filters code, initial data collections)
- `scripts/ <scripts/>`_: GitHub Scraper
- `static/ <static/>`_: Static assets (images) shared across versions
- yarn.lock / package.json: Packages for *scripts/* files
- `data/ <data/>`_: initial data

  - *my_actors.json*: `Actors`_ noun, person, place, thing, etc.
  - *my_activities.json*: `Activities`_ verb, action, event, happening
    in relation to an *actor*

  - *scraped/*: data pulled from remote API's (such as
    *scripts/github.js*)

    - *gh_activities.json*: Pull requests for users *except* yourself / your
      own repos. Same format as *my_activities.json*, combined.
    - *gh_actors.json*: GitHub Repos. Same schema as
      *my_actors.json*), combined.

Concepts
--------

Common code reused between Vue/React versions
"""""""""""""""""""""""""""""""""""""""""""""

To make an accurate, holistic comparsion between React/Redux and Vue/Vuex.

Careful attention is made strike a balance between avoiding extra processing
steps and being greatest common chunks of code between the two.

`Yarn Workspaces`_ ensures both the Vue and React.js detect file updates and
always use the freshest "live" common code.

This is done via symlinks. Yarn Workspaces orchestrates what ``npm link`` /
``yarn link`` would have each user to manually, every time they cloned
the repo.

*lib/* is mapped to the ``cv-lib`` package, *data/* to ``cv-data``

Rationale:

- Using relative file links to ``../data`` and ``../lib`` from the
  *vue/package.json* and *react/package.json* would **copy** the package,
  which means the react/vue's file watching wouldn't detect changes made
  to *lib/* and *data/*, but rather *react/node_modules/cv-lib* and
  *react/node_modules/cv-data*, and so on. Hot updates wouldn't work
  if you modifed the real data, only the files that were copied to
  *node_modules/* (which you probably wouldn't edit directly).

  The data would be needlessly duplicated, and would grow stale if changes were
  made in development.
- ``npm link`` and ``yarn link`` don't stick between systems and can't be
  saved to ``package.json``/``yarn.lock``. This is the problem Yarn
  Workspaces / `lerna`_ solves.
- Personal experience with Yarn Workspaces on two projects that share common
  SCSS/JS code and packages. Workspaces have proven seemless and productive.


Actor / Activity are just nouns / verbs
"""""""""""""""""""""""""""""""""""""""

The first project would use find all repositories a user had, and all
contributors the repositories had. It would use asynchronous requests
to GitHub to pull the information and render the information.

At the time, due to limitations with GitHub's API, there was no way to
pull the information without crafting individual child requests for each
repository. This would incur rate limits for very active GH users.

This new project finds all pull requests the user has made. It downloads the
information before hand, instead of doing it life via the browser. A
utility script queries from GitHub's very solid `GraphQL`_ API. See
*scripts/github.js*.

GraphQL makes it easier to articulate efficient queries to access the
information in one request.
