---
layout: default
title: Ti Migration Utilities
has_children: false
nav_order: 1
parent: Migrating to NG Titanium
---



### Ti Migration Utilities

#### Table View to Data Grid Conversion Script

##### Overview

The purpose of this script is to help in the conversion of legacy Table View forms, not supported by the new NG Titanium client, to Record View forms with a Data Grid component that will have the fields and labels of the form body as columns, the elements in other form parts will be left untouched.

The conversion is not an automatic process and the script might not recreate the original design exactly as it was before, there could be a lot of tweaking of the script itself, the CSS/LESS, or the resulting form to make sure it looks and works as it was before. Please test with a simple form first and start tweaking as needed.

The script will modify the form, so any existing reference will stay the same i.e. `containsForm` property in tab panels, and it will create a copy of the original form with the "_OLD" suffix as a reference, once the converted form looks and works as expected the copy can be deleted.

##### Getting Started

The script can be found here https://github.com/Servoy/ti-migration, you can clone the repository or download the code. Then import the **svyTiMigration** project into your workspace and add it as a module to the solution that contains the forms to be converted, then run the main solution.

![](/images/ti-migration-utils-solution-explorer.png)

The **svyTiMigration** has one form that can be used as a helper, it needs to be launched from the *Command Console* (menu *Window > Show View*) by typing:

```javascript
forms.svyTiMigration$Main.controller.show()
```

![](/images/ti-migration-utils-helper-form.png)

Select the form to be converted by typing the name, it will autocomplete, then click the *Convert Form* button, after it's finished the resulting form will be displayed in two tab panels below, the converted form and the OLD copy.

The same conversion can be done using the *Command Console* alone, without showing the `svyTiMigration$Main` form, by typing:

```javascript
scopes.svyTiMigration.convertTableFormToGrid(formName)
```

It will open the converted form in the Developer after finished, there is an optional parameter that allows converting List View forms as well and treat them as a Table View form.

All the result for the conversion is printed in the *Console*, make sure to check for messages related to unsupported features or things that need to be handled manually.

##### Special Cases and Limitations

- As form elements without name cannot be removed the script will give set a dummy name just to be able to remove them, if there is any issue the console will output the element properties to help you identify it in the OLD copy.
- The column header will be calculated using one the following methods:
  - Label with the `labelFor` property pointing to the field being converted
  - `titleText` property on the field being converted
  - If none of the above can be used then the `dataprovider` will be used as the header
- Labels with the `labelFor` property will also be removed after converting the field they are pointing to even if the label is not in the form body
- Labels with the `imageMedia` property set need to be handled manually, normally via CSS/LESS and using the `styleClass` property in the grid column ( See example below)
- The name of the field being converted will be used as the column id in the grid
  - If no name was set then the `dataprovider` will be used
  - If the field doesn't have neither a name nor a dataprovider then the column will have an auto-generated id (the id is important for the event handlers, see below)
- Element events are handled using Data Grid events checking the column id, if it was auto-generated then it needs to be handled manually. Also, if the event handler is an Entity method it needs to be handled manually because it will be used as if it's a Form method
- Tooltips as plain text are not supported in the Data Grid component, the script will create a form variable using the column id and use the tooltip text as the variable value, the new variable will be used as the tooltip dataprovider for the grid column

##### Tweaking Examples

The list below is just a short list of some of the possible tweaks needed in order to have a converted form that is as close as possible to the original one

###### Labels with `imageMedia` property

Example of a column showing an icon for editing a record (button name "*btnEdit*")

- Create a style class in CSS/LESS

```css
.grid-edit-icon {
    background: url(pencil.png) no-repeat center center;
    background-size: 20px 20px;
}
```

- After the form is converted find the column that should show the edit icon and set the `styeClass` property to "*grid-edit-icon*"

If this is a recurring change then the script could be modified to check the button name and if it's "*btnEdit*" then add the "*grid-edit-icon*" during the conversion, just add any custom logic to the method `scopes.ti_utils.getCustomColumnStyleClass`

```javascript
function getCustomColumnStyleClass(component) {
	if (component.name == 'btnEdit') {
		return 'grid-edit-icon';
	}
}
```

###### Event handler for column without `id`

If it was not possible to set the the id of the column then any event handler will look like this:

```javascript
function onCellClick(foundsetindex, columnindex, record, event) {
	/** @type {CustomType<aggrid-groupingtable.column>} */
	var col = elements[event.getElementName()].getColumn(columnindex);

	if (col == 'null') {
        // Call to some method
    }
	if (col == 'null') {
        // Call to some other method
    }
}
```

There will be an if statement for every column, including those without id, so make sure to manually edit the `id` property of every column and replace all the `'null'` strings with the ids used

