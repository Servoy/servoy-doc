---
layout: default
title: Smart-Doc Editor Guide
nav_order: 3
toc: true
---

This guide shows how to include document management in your Servoy applications

* TOC
{:toc}


------


### Getting Started

Servoy ships a **[Smart Doc Editor](https://github.com/Servoy/smartDocumentEditor/wiki)** component for rich in-app document editing. To compliment this component, we introduced this `svyUtils$documentEditor` module as part of the `SvyUtils` offering. This module provides additional utilities to manage custom tag libraries, document merge and PDF export.

Install the [Smart Doc Editor](https://github.com/Servoy/smartDocumentEditor/wiki)  component via the Servoy Package Manager (SPM), if you have not already.
`Help > Download Install with SPM`  This is a required dependency.

Then install the `svyUtils$documentEditor` module, also via the SPM. (Choose the *"Modules"* tab.) The module should be added as a dependency to your project, and the API will be available to you. You're ready to begin coding.



------


### Custom Tag Libraries

This module provides methods to embed custom data-bound tag libraries in the component with a simple API. The tags allow end users do design custom documents which can easily be [merged with data](#document-merge). This gives the best experience for end-users, who won't need to understand anything about your database or your code. 

*For more info on tag libs and how to set tags at design-time, please see the [Smart Doc Editor](https://github.com/Servoy/smartDocumentEditor/wiki) wiki topic on **mention feeds***.

#### Basic Example

```javascript
function createTags() { 
	
	// create editor object (stored as form variable)
	editor = scopes.svyDocEditor.getInstance(elements.editor);
	
	// setup tag library
	editor.tagBuilder(datasources.db.example_data.orders)
		.addField('orderid')
		.addField('shipcity')
		.addField('orders_to_order_details.quantity')
		.build()
}
```

As you can see, a [DocumentEditor](#documenteditor) object was created and bound to the runtime component. Then a [TagBuilder](#tagbuilder) object was then requested and bound to a specific data source `datasources.db.example_data.orders`. The tag builder can then generate field tags, using the [addField](#addfield) method. Each field can be a primary or related `DataProvider` in the context of the tag builder's data source.  

The list of available field tags is shown in a type-ahead selector whenever the `#` key is pressed. The result could look something like this:

<img src="images/TagLibs.JPG" alt="Basic Example" width="50%" height="50%" />



#### Customize Tag Display Value

Each tag has a `displayValue`, which is what the user will see. 

By default, the [TagBuilder](#tagbuilder) will generate a default value based on the data provider. If it's a *real* column in the tag builder's data source, the [JSColumn.title](https://wiki.servoy.com/display/DOCS/JSColumn#JSColumn-getTitle()) property is used. If the title property is empty, or the data provider is derived (i.e. calculated or aggregated), then the builder will *prettify* the name, with capital casing and spaces. For example, `product_description` would become `Product Description`.

Custom display values can be specified with the [addField](#addfield) method as an optional parameter `displayValue`.  For example:

```javascript
editor.tagBuilder(datasources.db.example_data.orders)
		.addField('shipcity', 'Destination City')
		.build()
```

The result could look something like this:

<img src="images/svyDocEditorTagLibsDisplayValue.JPG" alt="Tag Display Value" width="33%" height="33%" />



#### Showing Related Data

The [TagBuilder](#tagbuilder) can generate data-bound tags for any path in your data model, as long as it is traversable from the data source. Simply add the full path of the related data provider as a `String`. For example:

```javascript
editor.tagBuilder(datasources.db.example_data.orders)
		.addField('orders_to_customers.companyname', 'Customer.Name')
```

The result could look something like this:

<img src="images/svyDocEditorTagLibsRelated.JPG" alt="Related Data" width="33%" height="33%" />



#### Repeater Tags

Because tag libraries are data-bound, they can be used to generate repeating content. This happens automatically when [related data](#showing-related-data) is specified in the [addField](#addfield) method. The TagBuilder assumed a related [FoundSet](https://wiki.servoy.com/display/DOCS/JSFoundSet) could have many records. Therefore any related data provider will also generate a repeat tag, unless the boolean `repeats` argument is set to `false`.

The list of available repeater tags is shown in a type-ahead selector whenever the `$` key is pressed. For example:

```javascript
tagBuilder
    .addField('orders_to_order_details.quantity','Order Details: Quantity')
    .addField('orders_to_order_details.order_details_to_products.productname',
              'Order Details - Product',false)
```

The result could look something like this:

<img src="images/TagLibs_Repeater.JPG" alt="Repeat Tags" width="33%" height="33%" />

You can see that a `$startRepeater` tag was automatically generated for the `quantity` field. This is ideal, because we know that each order can have **many** order detail lines. But also notice that no repeater tags were created for the `productname`  field. This is because the (3rd) `repeats` parameter was explicitly set to `false`, as we know that `order_details_to_products` is a **1:1** relation.

When the document is [merged](#documentmerge), all the content between the `$startRepeater` tag and the `$endRepeater` will be repeated for each record in the repeater tag's related [FoundSet](https://wiki.servoy.com/display/DOCS/JSFoundSet).



------

### Document Merge

A document editor component can be initialized to have [custom tag libraries](#custom-tag-libraries) embedded in it. Each tag contains a **display value** (what the user will see) and a **real value** (what the application will see). There are also [repeater tags](repeater-tags), that specify where content will be repeated. 

When the document is merged, the tags will be replaced with record data. For example:

```javascript
function mergeTags(){

	// get merged content for the selected record in the form
	content = editor.mergeTags(foundset.getSelectedRecord());
}
```

Here, the instance method [DocumentEditor](documenteditor).[mergeTags(record)](merge-tags) was called and the selected record a form's foundset was used for the data source. The merged document is returned as a string, and in this case, it is set back into the `content` data provider to which the editor component is bound. The editor will render the merged document (like a print preview!).

The result could look something like this:

| Document with tags                                         | Merged content                                             |
| ---------------------------------------------------------- | ---------------------------------------------------------- |
| <img src="images/doc_merge_before.JPG" alt="Repeat Tags"/> | <img src="images/doc_merge_after.JPG" alt="Repeat Tags" /> |

As you can see the `#` field tags were replaced with the actual record data from the `orders` table. And the [related data](#related-data) from the `order_details` and `products` tables were also merged. The `$` repeater tags were applied to the <u>3</u> records in the related `orders_to_order_details` foundset. (You may also notice that the table header was intuitively skipped.) 

Not bad for 1 line of code!

### PDF Export

If you have made it this far, then you probably want to print, email or archive a merged document. Fortunately this module provides support for PDF export. Content from the Smart Docs Editor maybe converted to PDF format using a simple [Export API](#exporter).

Here's a quick example:

```javascript
function print(){

    // get exported PDF file as bytes
	var bytes = scopes.svyDocEditor.getExporter()
		.setContent(content)
		.exportToPDF();
	
	// Save file locally and open
	var pdf = plugins.file.createFile('export.pdf')
	plugins.file.writeFile(pdf,bytes);
	plugins.file.openFile(pdf);
}
```

Here the [getExporter](#getexporter) method was used to return an [Exporter](#exporter) object, which has methods to set content, among other options.

#### Export Options

The [Exporter](#exporter) supports various options, such as page size, orientation and margins to name a few. Here is an example of exporting with additional options:

```javascript
var bytes = scopes.svyDocEditor.getExporter()
		.setMargin(.25, .25, .25, .25)
		.setOrientation(scopes.svyDocEditor.ORIENTATION.LANDSCAPE)
		.setContent(content)
		.exportToPDF();
```



#### API Key

Converting rich HTML documents to PDF is no small task and Servoy delivers this functionality as a commercial-strength **cloud service**, greatly reducing the distribution footprint and developer requirements needed. All you need is an API key and a few lines of code to turn documents into PDFs.

##### Obtain an API Key

You can obtain a FREE key from the [Servoy Cloud Control Center](https://admin.servoy-cloud.eu/). From the navigation choose "Add-Ons", and under "Document Printing", click "Generate Key". Your secret key will be displayed. Copy it to your clipboard.

<img src="images/API_Key_gen.JPG" alt="Repeat Tags" width="50%" height="50%"/>

##### Register your key

There are two ways to register your key:

1. <u>By Configuration</u>: Open your properties configuration file `<SERVOY_HOME>/application_server/servoy.properties` and set the following property: `svyDocumentEditorAPIKey=<your-secret-key>`. This is the preferred approach for deployments.
   Note: that should only edit the properties file when the Servoy Developer or App Server instance is stopped.
2. <u>At Runtime</u>: Use the [registerAPIKey](registerapikey) method to set the key dynamically. This is ideal for testing in development. It will override any configuration.

##### Unlimited Printing

The PDF export service is ***FREE*** and subject to daily quotas and limitations. It is ideal for testing purposes and low-volume production scenarios. [Upgrade](mailto:sales@servoy.com) to unlimited printing for high-volume production scenarios and a dedicated document server.
