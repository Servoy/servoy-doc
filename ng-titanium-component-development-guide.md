---
layout: default
title: Ti Component Development Guide
has_children: false
nav_order: 3
parent: Migrating to NG Titanium
toc: true
---
### Titanium Component Development Guide

```
This Guide is under construction. Please come back in a few days
```



------

SummaryIntroductionBuild & DebugMigrating Component from NG1How to use libraries from npmDeveloping the componentHow to build the releasePlay with WPM NG2 components

AngularX (New) ConceptsAngular Tutorial is useful to get familiar with the Angular concepts.Compared to Angular1 note the difference in explicitly import modules for common used functionalities such as NgModel, NgIf, NgFor
Tutorial: Tour of Heroes https://angular.io/tutorialTutorial: Template driven Form: https://angular.io/guide/formsTypeScript ConceptsBuild & DebugThe NG2 solution is available at the URL http://localhost:8080/solution/{solutionName}/index.html.To run the NG2 solution you have to make sure Servoy has fully build & compiled the NG2 source.You can verify the status by looking into the NG2Console
![img](https://lh4.googleusercontent.com/l_KC2n-PkYhiHfFGe7Um6aot9-OGD805xke-9rcoy3LQYe2HTc8LTgYFPkh2ZbnhaF9CW8t2Rw1WGnw3GSQcTJd5-GMonr0LTjoJiAx89zHDmn8HSjXX_SKqkphYnHGRBEvsPyWl)
Upon build the Servoy Developer copies all the Component source code within the .metadata folder of the WORKSPACE. In the Developer IDE you can verify if Servoy has copied and compiled all NG2 sources looking in the NG2 Console. If there are no errors and all is done, you will see a line like the following:
Finished running 'run build_debug_nowatch ' time: 136s
Total time to check/install NG2 target folder: C:\Servoy\DistriData\DistriData\.metadata\.plugins\com.servoy.eclipse.ngclient.ui\target is 186s
At that point you can run the build_debug to watch for changes. Changes in NG2 components will be watched and compiled so you can test your changes real time in the NG2 Client.
If it doesn't work you can try to force manually the “Copy & Build” by right clicking the solution and select the option “Copy the NGClient2 Source”.
To watch for changes and build upon any edit go to the following folder
WORKSPACE\.metadata\.plugins\com.servoy.eclipse.ngclient.ui\target
run the command line:
\> npm run build_debug
Debug in the Browser.The ts files can be debugged in the Browser.In Chrome use the browser inspector, open the source tab and search for the folder:
webpack:///./packages/@servoy/svywebcam/src/webcam/webcam.ts

Migrating Component from NG1
When creating a new Web Component package the Servoy IDE ( since 2021.12 ) automatically generates the NG2 project folder.
As well when creating new component for NG2, the Servoy IDE setup the NG2 source files.
When creating the NG2 version for an existing component; easiest way is to create a new brand package with a new brand component in it and copy the files for ng2.**1) Copy Files**Copy the following files in the Web Component package folder
.sourcepathangular.jsonpackage.jsontsconfig.jsonprojects\*scripts\build.js
The file below should be updated with the name of the package module {module} 
MANIFEST.MF\projects\ng2package\karma.conf\projects\ng2package\ng-package.json\projects\ng2package\package.json\projects\ng2package\ng2package.module
**2) Rename ng2package.module.ts**Rename the package module ng2package.module using an unique name for the package ( e.g. for the svyWebcam component use svywebcam.module.ts ).
Make sure to update all references
 dir:export * from './{package}.module'; \projects\ng2package\src\public-api.ts

**3) Update the MANIFEST.MF**In the top section of the MANIFEST.MF file, add the reference to the NPM Package name and NG2 Module
…NPM-PackageName: @servoy/{module}NG2-Module: {module}ModuleEntry-Point: dist/servoy/{package} MANIFEST.MF
**4) Replace module name**In all files below, replace the {module} name with the actual package name.

 dir: require('path').join(__dirname, '../../../coverage/servoy/{module}'), \projects\ng2package\karma.conf
 "umdId": "servoy.{module}" \projects\ng2package\ng-package.json
 "name": "@servoy/{module}", \projects\ng2package\package.json
 export class {module}Module {} \projects\ng2package\{package}.module.ts
**5) Import the Component** 
Creating a new component for the ng2 packageCreating a new component these are the component related files.
\projects\ng2package\src\{package}.module.ts\projects\ng2package\src\public-api
\projects\ng2package\src\{name}\{name}.html\projects\ng2package\src\{name}\{name}.ts\projects\ng2package\src\{name}\{name}.spec.ts
Make sure the component is imported properly by the package module by importing the component in the files below
import { {Name}} from './{name}/{name}; import { NgModule } from '@angular/core'; @NgModule({  declarations: [  {Name},  ],  providers: [],  imports: [  ],  exports: [  {Name},   ]})export class {module}Module {} \projects\ng2package\src\{package}.module.ts

 export * from './{name}/{name}; \projects\ng2package\src\public-api
**6) Copy the Component files**
Make sure the correct {module} name and component {name} is used in the files.

import { Component, Input, SimpleChanges, Renderer2, ChangeDetectorRef } from '@angular/core';import { ServoyBaseComponent } from '@servoy/public'; @Component({  selector: '{module}-{name}',  templateUrl: './{name}.html'})export class {Name} extends ServoyBaseComponent<HTMLDivElement>{   @Input() yourName: string;   constructor(protected readonly renderer: Renderer2, protected cdRef: ChangeDetectorRef) {     super(renderer, cdRef);  }    svyOnInit() {    super.svyOnInit();  }    svyOnChanges( changes: SimpleChanges ) {    super.svyOnChanges(changes);  }  }\projects\ng2package\src\{name}\{name}.ts

import { ComponentFixture, TestBed, waitForAsync } from '@angular/core/testing'; import { {Name} } from './{Name}; //{name} ?? describe({Name}, () => { let component: {Name}; let fixture: ComponentFixture<{Name}>;  beforeEach(waitForAsync(() => {  TestBed.configureTestingModule({   declarations: [ {Name} ]  })  .compileComponents(); }));  beforeEach(() => {  fixture = TestBed.createComponent({Name});  component = fixture.componentInstance;  component.servoyApi = jasmine.createSpyObj('ServoyApi', ['getMarkupId','trustAsHtml','registerComponent','unRegisterComponent']);  fixture.detectChanges(); });  it('should create', () => {  expect(component).toBeTruthy(); });}); \projects\ng2package\src\{name}\{name}.spec.ts
For the template, modify the .html file.
**7) Rename the ng2package folder**
For convenience rename the ng2package folder with the actual {package} name (e.g. svywebcam)Renaming the package, make sure to update all pathsEasiest way is to search (CTRL + h) for ng2package in the Servoy IDE.
.sourcepathangular.jsonMANIFEST.MFng-package.json**8) Adjust the build.js**
Make sure the correct {module} name and component {name} are referenced by the build.js file.The build.js script will be later used to prepare the build ( see later section “How to build the release” )

var AdmZip = require('adm-zip'); // creating archivesvar zip = new AdmZip(); zip.addLocalFolder("./META-INF/", "/META-INF/");zip.addLocalFolder("./dist/servoy/{module}/", "/dist/servoy/{module}/");zip.addLocalFolder("./{name}/", "/{name}/"); zip.writeZip("{package}.zip"); projects\scripts\build.js
How to use libraries from npmIn the webcomponent project folder run the npm command to install the library
npm install --save {library}The npm install will add the dependencies in the root package.json of the project.Make sure to add the dependencies also in the projects\ng2package\src\package.jsonThe example below is based on the ngx-webcam library https://www.npmjs.com/package/ngx-webcam

… "dependencies": {  "ngx-webcam": "^0.4.1",  "tslib": "*" },… projects\{package}\src\package. json

Make sure to include in the .gitignore the generated node_modules and dist files.
/dist//node_modules//svywebcam.zip .gitignore
The library should be then imported in the package module
import { Webcam } from './webcam/webcam';import { WebcamModule } from 'ngx-webcam';import { NgModule } from '@angular/core'; @NgModule({  declarations: [  Webcam,  ],  providers: [],  imports: [   WebcamModule  ],  exports: [  Webcam,   ]})export class svywebcamModule {}\projects\{package}\src\{package}.module

Can finally be used in the component .ts module
import { Component, Input, SimpleChanges, Renderer2, ChangeDetectorRef } from '@angular/core';import { ServoyBaseComponent } from '@servoy/public';import {WebcamImage, WebcamInitError, WebcamUtil} from 'ngx-webcam'; 


\projects\{package}\src\{name}\{name}.ts

Creating Component from scratchCreating components from scratch is actually easier since Servoy does most of the job generating the package and component template for you.Use the Developer wizard to create a new package and a new component.
Note however that Servoy yet uses a generic name “ng2package” for the module name.Which means, still steps 2) and 7) of the section ( Migrate Components from NG1 ) still applies, as you still need to:**2) Rename ng2package.module.ts**Rename the package module ng2package.module using an unique name for the package ( e.g. for the svyWebcam component use svywebcam.module.ts ).
Make sure to update all references
 dir:export * from './{package}.module'; \projects\ng2package\src\public-api.ts
**7) Rename the ng2package folder**
For convenience rename the ng2package folder with the actual {package} name (e.g. svywebcam)Renaming the package, make sure to update all pathsEasiest way is to search (CTRL + h) for ng2package in the Servoy IDE.
.sourcepathangular.jsonMANIFEST.MFng-package.json
Developing the componentThe NG2 Component will share the .spec & server.js ( component_server.js ) files with the NG1 version.Only the template and the typescript ( instead of the NG1 javascript ) will be specific to NG2.
The template file will look similar to the NG1 version; can be copied from the NG1 version, all bindings will then be adjusted.HTML TemplateExample of NG1 Button vs NG2 Button
<button type="button" class='bts-button' ng-class="model.styleClass"  ng-if='model.showAs !== "html" && model.showAs !== "trusted_html" && model.visible'  svy-click='handlers.onActionMethodID($event)'  svy-dblclick='handlers.onDoubleClickMethodID($event)'  svy-rightclick='handlers.onRightClickMethodID($event)'  sablo-tabseq='model.tabSeq'  ng-disabled="!model.enabled"  svy-attributes='model.attributes'  id="{{::model.svyMarkupId}}">     <span ng-class="model.imageStyleClass" aria-hidden="true"></span> {{model.text}}</button>button.html ( NG1 )
<button  #element  class="bts-button"  *ngIf="showAs !== 'html' && showAs !== 'trusted_html'"  [sabloTabseq]="tabSeq"  [id]="servoyApi.getMarkupId()"  [svyTooltip]="toolTipText">  <span [ngClass]="imageStyleClass" aria-hidden="true"></span>  {{text}}</button>button.html (NG2)

Note the syntax between NG1 and NG2 is slightly different.There is not the model object of the scope anymore.Use square brackets [ for properties and parenthesis ( to bind methods *ngIf and *ngFor are the equivalent of ng-if and ng-repeat
NgModel NgIf & NgRepeatUsing the *ngIf and *ngFor. You can get an error of such:
Can't bind to 'ngIf' since it isn't a known property of 'div'
In order to use the ngIf & ng repeat the module should be imported in the package module ( {package}.module.ts )
Same for the NgModel used by input fields with 2 way binding; NgModel lives in the FormsModule which has to be imported by the package module ( From Angular Tutorial https://angular.io/tutorial/toh-pt1#two-way-binding )
…import { CommonModule } from '@angular/common';import { FormsModule } from '@angular/forms'; @NgModule({  …  imports: [   CommonModule,   FormsModule  ],  …});{module}.module.tsTypescript FileThe typescript is the equivalent of the component javascript file in NG1.All Components in NG2 will extend the base class ServoyBaseComponent.
Example of WebCam component. The WebCam component is implemented on top of the third party library ngx-webcam.
import { Component, Input, SimpleChanges, Renderer2, ChangeDetectorRef, ElementRef, ViewChild } from '@angular/core'; // (required) import from Angular Core, list can be expanded ( e.g. with ElementRef )import { ServoyBaseComponent } from '@servoy/public'; // (required) import servoy base component import {Subject, Observable} from 'rxjs'; // (example) import from rxjsimport {WebcamImage, WebcamInitError, WebcamUtil} from 'ngx-webcam'; // (example) import from third party module @Component({  selector: 'svywebcam-webcam',  // means can drop this template in a parent template using the <svywebcam-webcam> tag  templateUrl: './webcam.html'})export class Webcam extends ServoyBaseComponent<HTMLDivElement>{   // Model Input: define as input all model properties which can be used for binding in template  @Input() styleClass: string;  @Input() options: object;   // Handler Input: define as input all handler functions  @Input() getBase64Data: (data: string) => void;   // equivalent of component scope property which can be used for bindings in template  public optMirror = "never";    constructor(protected readonly renderer: Renderer2, protected cdRef: ChangeDetectorRef) {     super(renderer, cdRef);  }   /* These are the Web Component API */  capture() {    // implement client side API of the component   }  }
Element ReferenceInstead of the $element used in NG1, the element can be referenced using the this.elementRef.Note it requires to import the ElementRef from @angular/core.Also to let Servoy know which is the target element, the element tag in the template has to be identified by adding the #element attribute:
<div [id]="servoyApi.getMarkupId()" #element  …</div>

  // Note: to hook in the svyXxx methods the Component should know how to resolve the element. Add the #element in the target element tag of your template.  // Example: <div [id]="servoyApi.getMarkupId()" #element >  svyOnInit() {    super.svyOnInit();     // listen for resize to adjust height & width    this.resizeObserver = new ResizeObserver(() => {      this.height = this.elementRef.nativeElement.clientHeight;      this.width = this.elementRef.nativeElement.clientWidth;     });    this.resizeObserver.observe(this.elementRef.nativeElement);     this.width = this.elementRef.nativeElement.clientWidth;    this.height = this.elementRef.nativeElement.clientHeight;  }    svyOnChanges( changes: SimpleChanges ) {    if (changes) {      for (const property of Object.keys(changes)) {        switch (property) {          case 'options':       // refresh options when option object is changed            this.refreshOptions();            break;        }      }    }     super.svyOnChanges(changes);  } {name}.ts

How to build the releaseTo prepare the .zip release of the package.Go into the component folder, where angular.json is located and run
Make sure the version to be exported is correct in the Manifest file and all other files where version is specified ( package.json files )
\> npm install> npm run build> npm run make_release
If build is successful, upgrade version number in Manifest & package.json files to be ready for next release.
From https://wiki.servoy.com/pages/viewpage.action?pageId=57180181
Troubleshooting
**build error**Building Angular PackageConfiguration doesn't match the required schema.Data path "/lib" must NOT have additional properties (umdId).
Solution: remove the udmId from the ng-package.json. Is not needed since Angular 13


**make_release error**Error: Cannot find module 'C:..\{package}\scripts\build.js'  at Function.Module._resolveFilename (internal/modules/cjs/loader.js:880:15)  at Function.Module._load (internal/modules/cjs/loader.js:725:27)  at Function.executeUserEntryPoint [as runMain] (internal/modules/run_main.js:72:12)  at internal/main/run_main_module.js:17:47 { code: 'MODULE_NOT_FOUND', requireStack: []
Solution: the script build.js is missing. Make sure to include the build.js script as documented above.
**make_release error**C:\...\node_modules\adm-zip\adm-zip.js:310        throw new Error(Utils.Errors.FILE_NOT_FOUND.replace("%s", localPath));        ^Error: File not found: lib\  at Object.addLocalFolder (C:\...\node_modules\adm-zip\adm-zip.js:310:23)  at Object.<anonymous> (C:\...\scripts\build.js:9:5)  at Module._compile (internal/modules/cjs/loader.js:1063:30)  at Object.Module._extensions..js (internal/modules/cjs/loader.js:1092:10)  at Module.load (internal/modules/cjs/loader.js:928:32)  at Function.Module._load (internal/modules/cjs/loader.js:769:14)  at Function.executeUserEntryPoint [as runMain] (internal/modules/run_main.js:72:12)  at internal/main/run_main_module.js:17:47npm ERR! code ELIFECYCLEnpm ERR! errno 1npm ERR! svywebcam@1.0.0 make_release: `node scripts/build.js`npm ERR! Exit status 1
Solution: make sure all folders referenced in the script/build.js exists and are properly referenced. In the error above the folder lib was not found since indeed didn’t exist at the specified path.

Play with WPM NG2 components ( No Source )
You can play with NG2 components in your local developer without running components from source; you don't need to run components from source ( just using the components from the .zip package).Note of course any change won’t be persisted; this is just a playground to get familiar with existing components installed via WPM.NG2 Source can be copied from the developer into the local workspace by right click the active solution > Copy the NGClient2 sources NG2 source code is all available in the .metadata folder of the workspace.Specifically in the folder:
WORKSPACE\.metadata\.plugins\com.servoy.eclipse.ngclient.ui\target
To watch for changes and build upon any edit run the command line:
\> npm run build_debug