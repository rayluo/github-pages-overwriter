Github Page Overwriter
======================

A Github Action that overwrites your Github Pages branch with the content of current workdir, thus deploy/publish without polluting your repo history.


What is it?
-----------

Github Page Overwriter is:

* the **simplest** possible Github Page publisher, because it requires no configuration
* the **most lightweight** Github Page publisher (therefore the **fastest** possible),
  because its implementation requires no runtime dependency other than git itself.
* the **cleanest** possible Github Page publisher,
  because it is designed to leave no extra commits in your repo history.


How to use it?
--------------

Since Github Page Overwriter requires no configuration (more on this later),
you simply add it as one extra step into your own github action.

```yaml
name: Your github action

on:
  push:
    branches:
      - master

jobs:
  publish:
    runs-on: ubuntu-latest
    steps:
      - name: Check out
        uses: actions/checkout@v2

      - name: Generate your content
        run: ......  # Your project's generate step here

      - name: Publish current workdir (which contains generated content) to GitHub Pages
        uses: rayluo/github-pages-overwriter@v1
```


How does it work?
-----------------

Behind its simplest usage pattern,
Github Page Ovewriter is based on a sophisticated Version Control System philosophy:

1. Your repo stores the history of your project's source code and assets.
2. Any artifact that can be built from a snapshot of those source code and assets,
   should **not** be stored inside the repo history.

Yet, in order to take advantage of the free hosting service of Github Pages,
those artifacts would need to be stored inside a repo, thus violating tenet 2 above.
Some publisher actions work around this by pushing the artifacts into a dummy repo
which is only used for hosting the static website.
That's fine, but you'd need to provision a personal access token (PAT) for it,
and then configure your Github Action to use the PAT.

Github Page Overwriter takes a simpler approach.
The artifact will be forcefully pushed into - thus overwrite - the `gh-pages` branch
inside your same main repo.
This way, that publish commit would be overwritten each time,
thus won't become part of your repo's long term history.

1. For example, your repo starts like this when you are ready for the first publish:

   ```
    D---E---F  main
   ```

2. Trigger this action once, your repo would look like:

   ```
              A  gh-pages
             /
    D---E---F  main
   ```

3. Later you added more work into your repo, and now ready for a new publish.

   ```
              A  gh-pages
             /
    D---E---F---G---H  main
   ```

4. Trigger this action one more time, your repo would look like:

   ```
                      A'  gh-pages
                     /
    D---E---F---G---H  main
   ```


Configuration
-------------

Well, you can still configure the target branch from the default `gh-pages`
to something else you want.

```yaml
jobs:
  publish:
    runs-on: ubuntu-latest
    steps:
      - ...
      - name: Publish generated content to GitHub Pages
        uses: rayluo/github-pages-overwriter@v1
        with:
          target-branch: my_website_branch
```

But why bother? You may rather configure your Github repo to publish the conventional `gh-pages` branch instead.

