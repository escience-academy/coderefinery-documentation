---
layout: episode
title: "Deploying Sphinx documentation to Read the Docs"
teaching: 0
exercises: 15
questions:
  - How do Python projects deploy their documentation?
  - Can we use their solutions for projects which do not use Python?
objectives:
  - Create a basic workflow which you can take home and adapt for your project.
---

## [Read the Docs](https://readthedocs.org)

- Free Sphinx hosting
- RST or Markdown
- PDF can be generated on the fly
- Equations and images no problem
- Layout can be styled
- Many projects use [Read the Docs](https://readthedocs.org) as their main site
- It is no problem to serve using your own URL `http://myproject.org` instead of `http://myproject.readthedocs.io`

---

## Typical Read the Docs workflow

- Host source code with documentation sources on GitHub.
- Each time you `git push` to the repository, a `post-receive` hook sends POST
  request to Read the Docs to rebuild the documentation.

```shell
$ curl -X POST http://readthedocs.org/build/myproject
```

- Read the Docs then fetches changes (typically from GitHub but can be
  somewhere else provided the repo is public) and rebuilds HTML and PDF.
- No problem to build several branches (versions) of your documentation.

---

## Exercise: Deploy Sphinx documentation to Read the Docs

In this exercise we will host our previously 
created Sphinx documentation on GitHub and deploy it to Read the Docs.
If you haven't created any Sphinx documentation yet, 
an alternative approach is provided below based on forking an example repository 

We will use GitHub for this exercise but it will also work with any Git
repository with public read access.

### Alternative approach: Make a copy (fork) of the [example repostitory](https://github.com/coderefinery/doc-example)

Fork [this repository](https://github.com/coderefinery/doc-example) and
then clone the fork to your laptop:

```shell
$ git clone git@github.com:user/doc-example.git  # adapt user, also ok to clone via https if you prefer
$ cd doc-example
```

### Build HTML pages locally

Inside the cloned repository, run `sphinx-build` and you should see this:

```shell
$ sphinx-build doc _build

Running Sphinx v1.5
making output directory...
loading pickled environment... not yet created
building [mo]: targets for 0 po files that are out of date
building [html]: targets for 3 source files that are out of date
updating environment: 3 added, 0 changed, 0 removed
reading sources... [100%] index
looking for now-outdated files... none found
pickling environment... done
checking consistency... done
preparing documents... done
writing output... [100%] index
generating indices... genindex
writing additional pages... search
copying static files... done
copying extra files... done
dumping search index in English (code: en) ... done
dumping object inventory... done
build succeeded.
```

Then point your browser to e.g.
`file:///home/user/doc-example/_build/index.html`. Adapt the path to the actual
path where you have cloned to (the `/home/user` part is almost certainly wrong in your case).

Hopefully you can now see a website. If so, then you are able to build Sphinx pages locally.
This is useful to check how things look before pushing changes to GitHub.


### Enable the project on [Read the Docs](https://readthedocs.org)

- Log into [Read the Docs](https://readthedocs.org) and visit your [dashboard](https://readthedocs.org/dashboard/)
- Click "Import a Project"
- We could now "Connect to GitHub" and that would be easier but we will follow "Import Manually" which is more general
- Specify "Name:", e.g.: "user-doc-example" (replace "user")
- Set "Repository URL:", e.g.: https://github.com/user/doc-example.git (replace "user")
- Click "Next"

Now we should see the warning: "This repository doesn't have a valid webhook
set up. That means it won't be rebuilt on commits to the repository."

Right - we need to set that up. A webhook is a script that is executed every
time we push to the repository and sends a POST request to a web service.

Let us set up the webhook:


### Create a webhook and verify that changes to the repository automatically refresh the website

You may first need to set up an incoming webhook integration on Read the Docs: 

- Click on your project's "Integrations" admin dashboard page and select "Add integration"
- As integration type, choose "GitHub incoming webhook"
- After clicking "Add integration", copy to your clipboard the URL for the integration on the integration detail page
 
Next, we set up the webhook on GitHub:

- In a new browser tab visit your doc-example repository, e.g.: https://github.com/user/doc-example
- Click "Settings"
- Click "Webhooks"
- Click "Add webhook"
- Under "Payload URL" use the URL of the integration on Read the Docs (adding "https://" in front)
- Click the green "Add webhook"
- Then go back to the Read the Docs browser tab and reload the dashboard, warning should now be gone

Now we are ready to go, visit
[http://user-doc-example.readthedocs.io](http://user-doc-example.readthedocs.io)
(replace project name).

Finally, make some changes to the documentation under `doc/`, commit and push
them, and verify that the documentation website refreshes after your changes
(can take few seconds or a minute).