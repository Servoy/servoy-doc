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

The script can be found here https://github.com/Servoy/ti-migration, you can clone the repository or download the code. Then import the **svyTiMigration** project into your workspace and add the solution that contains the forms to be converted as a module, then run the **svyTiMigration** solution.

[TODO: Add screenshot of the Solution Explorer showing the main solution and the module]

[TODO: Add screenshot of the main form of svyTiMigration solution]

[TODO: The following instructions assume that there will be a way to select the form to convert, a button to do the conversion, and way to show it after converted]

Select the form to be converted by typing the name, it will autocomplete, then click the *Convert* button, after it's finished the resulting form will be displayed in the tab panel below.

##### Special Cases and Limitations

- Form elements without name cannot be removed, they will be created as columns but need to be removed by hand [TODO: Implement dummy naming to see if it's possible to remove these elements automatically]
- The column header will be calculated using one the following methods:
  - Label with the `labelFor` property pointing to the field being converted
  - `titleText` property on the field being converted
  - If none of the above can be used then the `dataprovider` will be used as the header
- Labels with the `labelFor` property will also be removed after converting the field they are pointing to even if the label is not in the form body
- Labels with the `imageMedia` property set need to be handled manually, normally via CSS/LESS and using the `styleClass` property in the grid column [TODO Add example below]
- The name of the field being converted will be used as the column id in the grid
  - If no name was set then the `dataprovider` will be used
  - If the field doesn't have neither a name nor a dataprovider then the column won't have an id
- Tooltips as plain text are not supported in the Data Grid component so need to be handled manually, normally by creating a form variable or a calculation and use that as the `tooltipDataprovider` [TODO: Can this be automated by creating a form variable?]
- Element events are handled using Data Grid events checking the column id, if it was not set then it needs to be handled manually. Also, global/scopes methods are called without using the proper scope so they need to be handled manually [TODO: Can this be fixed by checking the event handler definition?]

##### Tweaking Examples

The list below is just a short list of some of the possible tweaks needed in order to have a converted form that is as close as possible to the original one

###### Labels with `imageMedia` property

[TODO Add screenshot of label with imageMedia]

Example of a column showing an icon for editing a record (button name "*btnEdit*")

- Create a style class in CSS/LESS

```css
.grid-edit-icon {
    background: url(pencil.png) no-repeat center center;
    background-size: 20px 20px;
}
```

- After the form is converted find the column that should show the edit icon and set the `styeClass` property to "*grid-edit-icon*"

If this is a recurring change then the script could be modified to check the button name and if it's "*btnEdit*" then add the "*grid-edit-icon*" during the conversion, just add any custom logic to the method `scopes.ti_utils.getCustomImageMediaStyleClass`

```javascript
function getCustomImageMediaStyleClass(componentName, imageMediaName) {
	if (componentName == 'btnEdit') {
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

There will be an if statement for every column, including those without id, so make sure to manually edit the `id` property of every column and replace `'null'` with the values used