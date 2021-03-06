[![PyPI version](https://badge.fury.io/py/jupyter_dashboards.svg)](https://badge.fury.io/py/jupyter_dashboards) [![Build Status](https://travis-ci.org/jupyter-incubator/dashboards.svg?branch=master)](https://travis-ci.org/jupyter-incubator/dashboards) [![Google Group](https://img.shields.io/badge/-Google%20Group-lightgrey.svg)](https://groups.google.com/forum/#!forum/jupyter)

# Jupyter Dynamic Dashboards from Notebooks

Extension for Jupyter Notebook that enables the layout and presentation of grid-based dashboards from notebooks.

![Dashboard layout screenshot](etc/dashboards_intro.png)

This repository is a portion of the `jupyter-incubator/dashboards` effort which covers:

* [Arranging](https://github.com/jupyter-incubator/dashboards) notebook outputs in a grid-layout (**this repo**)
* [Bundling](https://github.com/jupyter-incubator/dashboards_bundlers) notebooks and associated assets for deployment as dashboards
* [Serving](https://github.com/jupyter-incubator/dashboards_server) notebook-defined dashboards as standalone web apps

It is also has close ties to [jupyter-incubator/declarativewidgets](https://github.com/jupyter-incubator/declarativewidgets) which provides one way (but not the only way) of enabling rich interactivity in notebook-defined dashboards.

## What It Gives You

* Dashboard layout mode for arranging notebook cell outputs in a grid-like fashion
* Dashboard view mode for interacting with an assembled dashboard within the Jupyter Notebook
* Ability to share notebooks that have dashboard layout metadata in them with other Jupyter Notebook users for layout and viewing
* Ability to deploy dashboards as standalone web applications when used in conjunction with the [dashboards_bundlers](https://github.com/jupyter-incubator/dashboards_bundlers) and [dashboards_server](https://github.com/jupyter-incubator/dashboards_server) projects

## Try It

If you want to try the dashboard extension and demos without installing it yourself, visit the [jupyter-incubator/showcase binder](http://mybinder.org/repo/jupyter-incubator/showcase). If the binder site is full, try the tmpnb instance at [http://jupyter.cloudet.xyz](http://jupyter.cloudet.xyz).

Note that both of these deployments tend to lag the latest stable release.

## Install It

### Prerequisites

* Jupyter Notebook 4.2.x, 4.1.x, or 4.0.x running on Python 3.x or Python 2.7.x
* Edge, Chrome, Firefox, or Safari

Note: If you're running IPython Notebook 3.2.x, you can install the older 0.1.x version of the extension.

### Dashboard Layout and Preview

In Jupyter Notebook 4.2, you install and activate the dashboard layout and preview features in two commands like so:

```bash
# install the python package
pip install jupyter_dashboards

# Install all parts of the extension to the active conda / venv / python env
# and enable all parts of it in the jupyter profile in that environment
# See jupyter dashboards quick-setup --help for other options (e.g., --user)
jupyter dashboards quick-setup --sys-prefix
# The above command is equivalent to this sequence of commands:
# jupyter nbextension install --py jupyter_dashboards --sys-prefix
# jupyter nbextension enable --py jupyter_dashboards --sys-prefix
```

In Jupyter Notebook 4.1 and 4.0, you install and activate the extension like so:

```bash
# Install the python package
pip install jupyter_dashboards
# Register the notebook frontend extensions into ~/.local/jupyter
# See jupyter dashboards install --help for other options (e.g., --sys-prefix)
jupyter dashboards install --user --symlink --overwrite
# Enable the JS and server extensions in your ~/.jupyter
jupyter dashboards activate
```

If you also want to download or deploy your dashboards as web applications, read the next section about *Deploying Dashboards*.

### Dashboard Deployment

It's within the scope of the dashboard incubator projects to allow users to both:

1. Create dashboard layouts within notebooks, persist the layout metadata within the notebook JSON, and share those dashboard-notebooks with other Jupyter users (this project).
2. Convert and deploy dashboard-notebooks as standalone web applications (the [jupyter-incubator/dashboard_bundlers](https://github.com/jupyter-incubator/dashboards_bundlers) and [jupyter-incubator/dashboards_server](https://github.com/jupyter-incubator/dashboards_server) projects).

We consider the code which addresses the first use case stable for the time being. On the other hand, we are actively maturing support for the latter use case by following our [dashboard deployment roadmap](https://github.com/jupyter-incubator/dashboards/wiki/Deployment-Roadmap) which largely seeks to address the [threats identified](https://github.com/jupyter-incubator/dashboards/wiki/Deployed-Dashboard-Threat-Analysis) in our initial proof-of-concept deployment mechanisms.

If you'd like to try the **experimental** support for deploying dashboards as standalone web apps today, see the [jupyter-incubator/dashboards_bundlers](https://github.com/jupyter-incubator/dashboards_bundlers) README for details.

## Uninstall It

In Jupyter Notebook 4.2:

```bash
# Remove all parts of the extension from the active conda / venv / python env
# See jupyter dashboards quick-remove --help for other options (e.g., --user)
jupyter dashboards quick-remove --sys-prefix
# The above command is equivalent to this sequence of commands:
# jupyter nbextension disable --py jupyter_dashboards --sys-prefix
# jupyter nbextension uninstall --py jupyter_dashboards --sys-prefix

# Remove the python package
pip uninstall jupyter_dashboards
```

In Jupyter Notebook 4.0 and 4.1:

```bash
# Disable extensions, but no way to remove frontend assets in this version
jupyter dashboards deactivate

# Remove the python package
pip uninstall jupyter_dashboards
```

## Develop It

This repository is setup for a Dockerized development environment. On a Mac, do this one-time setup if you don't have a local Docker environment yet.

```bash
brew update

# make sure you're on Docker >= 1.7
brew install docker-machine docker
docker-machine create -d virtualbox dev
eval "$(docker-machine env dev)"
```

Clone this repository in a local directory that docker can volume mount:

```bash
# make a directory under ~ to put source
mkdir -p ~/projects
cd !$

# clone this repo
git clone https://github.com/jupyter-incubator/dashboards.git
```

Pull a base Docker image and build a subimage from it that includes `bower`, `nodejs`, and `npm` both as a dashboard dev dependency and as a prereq for example notebooks that use declarative widgets.

```bash
cd dashboards
make build
```

Install the necessary JS dependencies. Re-run this command any time your `bower.json` or `package.json` changes.

```bash
make js
```

Run the notebook server in a docker container.

```bash
# run notebook server in container
make dev
```

The final `make` command starts a local Docker container with the critical pieces of the source tree mounted where they need to be to get picked up by the notebook server in the container. Most code changes on your Mac will have immediate effect within the container.

To see the Jupyter instance with extensions working:

1. Run `docker-machine ls` and note the IP of the dev machine.
2. Visit http://THAT_IP:9500 in your browser

See the Makefile for other dev, test, build commands as well as options for each command.

### Develop with Declarative Widgets

If you want [declarative widgets](https://github.com/jupyter-incubator/declarativewidgets) available in you development environment, do the following:

```bash
# On your host, clone the widgets project as a peer of the dashboards folder
git clone https://github.com/jupyter-incubator/declarativewidgets.git

# Build the widgets into a source tarballs
cd declarativewidgets
make sdist

# Run a container that has both
cd ../dashboards
make dev-with-widgets
```

To see the Jupyter instance with both extensions working:

1. Run `docker-machine ls` and note the IP of the dev machine.
2. Visit http://THAT_IP:9500 in your browser

### Develop Against Python 2.7

You can run a development environment against python 2.7 by adding an environment variable to your make calls.

```bash
# Run a development environment against 2.7
make dev-python2
# Run a development environment, with declarative widgets, against 2.7
make dev-with-widgets-python2
# Run unit tests against 2.7
make test-python2
```

## Package It

The dashboard features are implemented as a Jupyter Notebook extension against the stock 4.x version of the notebook project, not a fork. With the dev setup above, if you run `make sdist` you should get a source tarball in the `dist/` directory of your clone. You should be able to install that tarball using `pip` anywhere you please.
