---
layout: default
title: Migrating to NG Titanium
nav_order: 1
toc: true
has_children: true
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

Once you have updated Servoy Developer and your component packages, you should be able to test out your solution on NG Titanium straight away.

**Enable NG Titanium in Servoy Developer**

Configure Servoy Developer to launch Titanium when starting the NG Client. From the menu bar, choose `Window > Preferences > Servoy` and enable the check box property `Start NG Client should launch Titanium`. After this, whenever you launch the NG Client, you will be running Titanium.

#### Key Differences between NG and Titanium

Foobar

#### Table View / List View forms

Foobar

### Solution Styling

Styling and theming can be a bit different in Titanium compared to classic NG. In some cases, the underlying markup has changed quite a bit, therefore some CSS selectors will not match.


With 2021.03 release we added a special Servoy theme for NGClient2, the best way to start using this for testing NGClient2 but not disrupting also NGClient1 is to make a copy of your solution stylesheet, if you stylesheet is named "mysolution.less", make a copy and name that copy "mysolution_ng2.less". If you startup a NGClient2 then when serving out the stylesheet, Servoy will check if there is a "ng2" variant of the given solution stylesheets name. In that "mysolution_ng2.less" stylesheet you can then point to another copy: @import 'custom_servoy_theme_properties_ng2.less'; you can make, and that properties less file then points to the new "2021.3.0_ng2" Servoy theme file. This way if you start the same solution in NG1 you will just be served out the normal less/css file including our ng1 servoy stylesheet through 'custom_servoy_theme_properties.less' and starting in in NG2 we will serve out another set of stylesheets and a different Servoy theme file. After this you can then start tweaking the NG2 look in the "ng2" solution stylesheet.

### Components



### Deploying Titanium