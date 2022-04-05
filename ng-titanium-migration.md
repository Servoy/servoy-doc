---
layout: default
title: Migrating to NG Titanium
nav_order: 1
toc: true
---

This guide will illustrate the steps and best practices for migrating from NG Client to NG Titanium.
* TOC
{:toc}
------



### Overview

The NG Titanium client is the latest evolution in Servoy's app deployment technology. With this offering, the NG Client gets a brand new engine, allowing Servoy applications to stay up-to-date without having to rewrite or undergo a major refactor. This guide is intended for developers that have NG Client applications that they'd like to migrate to NG Titanium.

### Getting Started

**Update Servoy Developer**

It is recommended to [download](https://servoy.com/download) and install the latest stable version of Servoy Developer. Use version `2022.3.x` at a minimum.

Be sure to commit any outstanding changes or back up your workspace. If you are using source control, then it is a good idea to move to a new branch in your repository as you work through the changes.

**Update Component Packages**

Your solution must use Titanium-compatible component packages in order to run the NG Titanium client. You will need to update each package to ensure compatibility.

In Servoy Developer, open the Servoy Package Manager (SPM) and review the Components and Services that are in use. 

**How do I know if a component package is compatible ?**

You will likely be updating from a semantic version to a year-based version. Be sure your package is minimum `2022.3.x`
For example, updating grid components could look like this:  `NG-Grids 2.9.1`  -->  `NG-Grids 2022.3.1`

**What to do if a package is not compatible ?**

Component packages which are not Ti-compatible may prevent your solution from running in NG Titanium. By now, most supported packages shipped by Servoy have been updated to include Titanium sources and the remaining are planned.  However, it's possible that your application references **custom/3rd-party packages** which are not Ti-compatible. In this case, you will need to migrate these packages, a task which may not be trivial. 

If this sounds like your situation, then please read the [Titanium Component Development Guide](https://servoy.github.io/servoy-doc/ng-titanium-component-development-guide.html). 



#### Running your solution on Titanium

Foobar

#### Key Differences between NG and Ti

Foobar

#### Table View / List View forms

Foobar

### Solution Styling

Foobar

### Components



### Deploying Titanium