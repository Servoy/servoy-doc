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

The NG Titanium client is the latest evolution in Servoy's app deployment technology. With this offering, the NG Client gets a brand new engine, allowing Servoy applications to stay up-to-date without having to rewrite or undergo a major refactor. This guide is intended for developers that have **NG Classic** applications that they'd like to migrate to **NG Titanium**.

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

##### Running your solution on Titanium

Once you have updated Servoy Developer and your component packages, you should be able to test out your solution on NG Titanium straight away.

**Enable NG Titanium in Servoy Developer**

Configure Servoy Developer to launch Titanium when starting the NG Client. From the menu bar, choose `Window > Preferences > Servoy` and enable the check box property `Start NG Client should launch Titanium`. After this, whenever you launch the NG Client, you will be running Titanium.

**Comparing NG Classic with Titanium**

It is possible to compare how your solution appears in Titanium vs NG Classic. To compare the results,

1. Copy the URL from your Titanium instance. 
2. Open a new browser tab or window, then paste the URL in the address bar.
3. Adjust the URL to match the NG Classic syntax, i.e. [http://localhost:8183/solution**s**/my-solution-name](http://localhost:8183/solutions/my-solution-name) 
   (Note: the "s" in "solutions" with ensure that NG Classic is launched)
4. Append the `nodebug` option to the url query string. This will ensure that both clients can run simultaneously.
   Note: while the no-debug option allows multiple clients, the client is not updated when the solution is changed.

### Solution Styling

Styling and theming can be a bit different in Titanium compared to NG Classic. In some cases, the underlying markup of components has changed quite a bit. Therefore, some CSS selectors will not match. As a result, you will have to revisit your CSS/LESS files to ensure that your solution looks the same from Classic to Titanium.

##### Creating a Titanium Style

It's important to be able to modify CSS/LESS code to adapt to Titanium components without disturbing your NG Classic solution. To facilitate this, Servoy can dynamically switch between your NG Classic and a new, separate Titanium-specific stylesheet. We recommend this approach:

1. Create a copy of your solution's main LESS file in the solution's media folder.
2. The new file should be named the same, plus a `"_ng2.less"` suffix.
   For example: `my_solution.less` would be named `my_solution_ng2.less`
3. When you launch the Titanium client, the solution stylesheet will be substituted with the "_ng2" variant .
4. Continue to modify your "_ng2" stylesheet as needed to address style differences between components.
5. If you are using a theme, then you will need to change the import statement to include a Titanium-compatible theme.
   `@import 'custom_servoy_theme_properties_ng2.less';` More on this in the [next section](#using-a-titanium-theme).

```
NOTE: If you are creating a solution from scratch, then the "_ng2" stylesheet will be automatically generated and it will contain the correct import statement for the Titanium theme.
```



##### Using a Titanium Theme

Another way to simplify the process of styling for Titanium is by using themes.

If your solution is already using the [SvyThemeRoller](https://github.com/Servoy/svyThemeRoller/wiki) project, then you will be able to rely on a Titanium-compatible version of the theme to manage the changes to components. This can be quite handy, as you don't need to handle everything in the LESS file. Follow these steps to start using a Titanium theme in your solution.

1. If you are using a theme, you will have a file in your media folder called `custom_servoy_theme_properties.less`. Create a copy of this file.
2. The new file should be named the same, plus a `"_ng2.less"` suffix: `custom_servoy_theme_properties_ng2.less`
3. Be sure to change the `Servoy Theme Version` to a `2022.3.x_ng2`.
4. Be sure to add the `@import` statement for this theme to your solution's Ti-compatible stylsheet as described in the [previous section](#creating-a-titanium-style)

```
NOTE: If you are creating a solution from scratch, then the Titanium theme will be automatically generated. The theme version will be correct and the reference will be imported into the solution's stylesheet.
```



### Table View / List View forms

NG Classic solutions which have been grandfathered in from older versions of Servoy may have legacy **List View** and **Table View** forms. While these forms were deprecated in NG Classic, they are now removed in NG Titanium.

##### Table View Forms

Classic Table-View forms will not render correctly in the Titanium client. They should be converted to forms containing a NG Grid component. This can be done by renaming the classic form and manually creating the new form with the same name.

In some cases this can be partially automated. To facilitate this, Servoy has developed a utility project. More info can be found in the [Ti Migration Utilities](https://servoy.github.io/servoy-doc/ng-titanium-migration-utilities.html) page.

##### List View Forms

Classic List-View forms will not render correctly in the Titanium client. It is best to use a **FormComponent** inside a List Container.

1. Create a new [FormComponent](https://wiki.servoy.com/display/DOCS/Form+Component), adding the same elements as in the classic form. 
2. Create a new form (which will be your container form) and add your FormComponent via a **ListFormComponentContainer**.
3. Set the list container's `pageLayout` property to `listview`.
4. You can optionally set infinite scrolling by adding the styleClass `svy-listformcomponent-scroll`.
5. To preserve business logic, copy content from the classic form's .js to the new container form.
6. Refactor element references to go through the form component. For example, `elements.my_text_box` becomes `elements.my_form_component.my_text_box`
7. Reconnect FormComponent element event bindings to container form logic.

```
NOTE: This process is entirely manual. Servoy is evaluating the possibility of a conversion utility for Classic ListViewForms. This page will be updated accordingly.
```



### Components

Foobar

### Deploying Titanium

Foobar

TODO: runtime checking isTitanium