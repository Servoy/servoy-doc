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

![Solution Explorer](https://servoy.github.io/servoy-doc/images/ti-migration-utils-solution-explorer.png)

The **svyTiMigration** module has one form that can be used as a helper, it can be added to a temporary menu option or a button, or it can be launched from the *Command Console* (menu *Window > Show View*) by typing:

```javascript
forms.svyTiMigration$Main.controller.show()
```

![Command Console](https://servoy.github.io/servoy-doc/images/ti-migration-command-console.png)

Select the form to be converted by typing the name, it will autocomplete, then click the *Convert Form* button, after it's finished the resulting form will be displayed in two tab panels below, the converted form and the OLD copy.

![Helper Form	](https://servoy.github.io/servoy-doc/images/ti-migration-utils-helper-form.png)

The same conversion can be done using the *Command Console* alone, without showing the `svyTiMigration$Main` form, by typing:

```javascript
scopes.svyTiMigration.convertTableFormToGrid(formName)
```

If the conversion is done directly from the Command Console the converted form will be opened in the Developer after finished, there is an optional parameter that allows converting List View forms as well and treat them as a Table View form.

All the result for the conversion is printed in the *Console*, make sure to check for messages related to unsupported features or things that need to be handled manually.

##### Special Cases and Limitations

- As form elements without name cannot be removed the script will set a dummy name just to be able to remove them, if there is any issue the console will output the element properties to help you identify it in the OLD copy.
- The column header will be calculated using one the following methods:
  - Label with the `labelFor` property pointing to the field being converted
  - `titleText` property on the field being converted
  - If none of the above can be used then the `dataprovider` will be used as the header
- Labels with the `labelFor` property will also be removed after converting the field they are pointing to even if the label is not in the form body
- Labels with the `imageMedia` property set need to be handled manually, normally via CSS/LESS and using the `styleClass` property in the grid column (see the [Tweaking Examples](https://servoy.github.io/servoy-doc/ng-titanium-migration-utilities.html#tweaking-examples) section below)
- The name of the field being converted will be used as the column id in the grid
  - If no name was set then the `dataprovider` will be used
  - If the field doesn't have neither a name nor a dataprovider then the column will have an auto-generated id (the id is important for the event handlers, see below)
- Element events are handled using Data Grid events checking the column id, if it was auto-generated then it needs to be handled manually. Also, if the event handler is an Entity method it needs to be handled manually because it will be used as if it's a Form method
- Tooltips as plain text are not supported in the Data Grid component, the script will create a form variable using the column id and use the tooltip text as the variable value, the new variable will be used as the tooltip dataprovider for the grid column
  - Tooltips with dataprovider tags are not supported and will be rendered as plain text, you need to handle them manually (see the [Tweaking Examples](https://servoy.github.io/servoy-doc/ng-titanium-migration-utilities.html#tweaking-examples) section below)


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

/* Handle legacy rolloverCursor:Hand and rolloverImageMedia properties */
.grid-edit-icon:hover {
    background: url(pencil_hover.png) no-repeat center center;
    cursor: pointer;
}
```

- After the form is converted find the column that should show the edit icon and set the `styeClass` property to "*grid-edit-icon*"

###### Customize resulting grid and columns

If setting a particular class or property after a form is converted is a recurring change this can be done by passing a callback function to the conversion script to apply the change automatically.

The above example of the edit button could look like this:

```javascript
// This function will be called for each component being converted to a column
function columnCallback(formName, originalComponent, newGridColumn) {
	if (originalComponent.name == 'btnEdit' ||
       		originalComponent.imageMedia && originalComponent.imageMedia.getName() == 'pencil.png')) {
		newGridColumn.styleClass += ' grid-edit-icon';
	}
}

// Call to the migration script passing the function above
scopes.svyTiMigration.convertTableFormToGrid(formName, false, false, null, columnCallback);
```

Something similar can be done if the grid needs to be customized after created.

```javascript
function gridCallback(formName, newGrid) {
	if (formName.includes('report_')) {
		// The grid header in all forms used as a report don't have a border between columns
		newGrid.setJSONProperty('styleClass', newGrid.getJSONProperty('styleClass') + ' header-no-column-border');
	}
}

// Call to the migration script passing the function above
scopes.svyTiMigration.convertTableFormToGrid(formName, false, false, gridCallback);
```

The CSS/LESS could be something like this:

```css
.ag-theme-bootstrap.header-no-column-border .ag-header-cell {
	border: none;
}
```

###### Event handler for column with auto-generated id

If the id of the column results in an auto-generated value then any event handler will look like this:

```javascript
function onCellClick(foundsetindex, columnindex, record, event) {
	/** @type {CustomType<aggrid-groupingtable.column>} */
	var col = elements[event.getElementName()].getColumn(columnindex);

	if (col == 'c1649707517388') {
        // Call to some method
    }
}
```

If you would like to use a more descriptive id then make sure to edit both the column `id` property and the event handler code.

###### Tooltips with tags

If you have dataproviders tags in the `tooltipText` property of a component being converted the script will consider it as plain text so an extra step needs to be done manually depending on what's being shown in the tooltip.

For instance if the tooltip of a "Status" component shows extra information about the status like `%%status_description%% (%%status_code%%)` the column will show that text explicitly after converted using a form variable

```javascript
// This variable is created automatically by the script
var status_tooltip = "%%status_description%% (%%status_code%%)";
```

One way to fix it is by creating a calculation in the foundset and removing the form variable

```javascript
function status_tooltip() {
	return status_description + ' (' + status_code + ')';
}
```

In that way the tooltip property of the column doesn't change (if the calculation has the same name as the variable) and it will show the same text as the legacy component.
