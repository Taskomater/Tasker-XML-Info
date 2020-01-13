# Tasker XML Info

This is a project that provides info on how Android [Tasker App] XML files work. Check [tasker_config_utils] for bash util script(s) to extract information and modify Tasker App XML files.
##


### Contents
- [Tasker XML Export Types](#Tasker-XML-Export-Types)
- [Tasker XML File Structure](#Tasker-XML-File-Structure)
- [Tasker XML File Nodes And Tags](#Tasker-XML-File-Nodes-And-Tags)
- [Tasker XML Codes](#Tasker-XML-Codes)
- [Tasker XML Examples](#Tasker-XML-Examples)
- [Downloads](#Downloads)
- [Current Features](#Current-Features)
- [Planned Features](#Planned-Features)
- [Issues](#Issues)
- [Worthy Of Note](#Worthy-Of-Note)
- [FAQs And FUQs](#FAQs-And-FUQs)
- [Changelog](#Changelog)
- [Contributions](#Contributions)
##


### Tasker XML Export Types

Tasker supports 4 types of XML exports.

- **`Data Backup`**
	It contains the entire tasker config with all profiles, scenes and tasks.  
	*XML file suffix:* `.xml`  
	*Import Instructions:* `Tasker Options In Tasker Home Screen` -> `Data` -> `Restore` -> `User Local Backup`  
	*Export Instructions 1:* `Tasker Options In Tasker Home Screen` -> `Data` -> `Backup`  
	*Export Instructions 2:* Tasker Task Action  `Tasker` -> `Data Backup`  

- **`Project`**
	It contains all profiles, scenes and tasks that are referenced in the project that is exported from the tasker config. Basically if you are exporting project 1, then the exported XML file will contain all profiles, scenes and tasks that are inside project 1 in the tasker config. Additionally, it will also contains profiles, scenes and tasks from projects other than project 1 that are referred or called by project 1 in some way. This automatically creates a standalone project file and does not require the users to export/import multiple projects. Tasker exports everything that is required for a user to run the project.  
	*XML file suffix:* `.prj.xml`  
	*Import Instructions:* `Long Press Anywhere On Bottom Nav Bar In Tasker Home Screen` -> `Import Project`  
	*Export Instructions:* `Long Press On The <Project Name> Tab In Bottom Nav Bar In Tasker Home Screen` -> `Export` -> `XML to Storage`  

- **`Profile`**
	It contains the profile and all tasks that are referenced in the profile that is exported from the tasker config. This will also export anonymous tasks of the profile that are not named and contain only an id in the tasker config, like for example the entry and exit tasks which were created without naming them. Note the `Optional` when you select `New Task` will creating a profile.  
	*XML file suffix:* `.prf.xml`  
	*Import Instructions:* `Long Press PROFILES Tab In Top Nav Bar In Tasker Home Screen` -> `Import Profile`  
	*Export Instructions:* `Long Press On The <Profile Name> In the PROFILES Tab In Tasker Home Screen` -> `Tasker Options` -> `Export` -> `XML to Storage`  

- **`Scene`**
	It contains the scene and all tasks that are referenced in the scene that is exported from the tasker config. This will also export anonymous tasks of the scene and sub scenes that are not named and contain only an id in the tasker config. The key and element tasks of the scenes are anonymous tasks.  
	*XML file suffix:* `.scn.xml`  
	*Import Instructions:* `Long Press SCENES Tab In Top Nav Bar In Tasker Home Screen` -> `Import One Scene`  
	*Export Instructions:* `Long Press On The <Scene Name> In the SCENES Tab In Tasker Home Screen` -> `Tasker Options` -> `Export` -> `XML to Storage`  

- **`Task`**
	It contains only the task that is exported from the tasker config.  
	*XML file suffix:* `.tsk.xml`  
	*Import Instructions:* `Long Press TASKS Tab In Top Nav Bar In Tasker Home Screen` -> `Import Task`  
	*Export Instructions:* `Long Press On The <Task Name> In the TASKS Tab In Tasker Home Screen` -> `Tasker Options` -> `Export` -> `XML to Storage`  

If the profile, scene or task XML file does not have the correct suffix, it will not be displayed in the import menu by Tasker.

The initial project's name is `Base` and it cannot be exported. Create a new project and move required profiles, scenes and tasks to it and then export that. 

If your tasker config contains a named profile, scene or task and you are trying to import a XML file that contains a profile, scene or task with the same name, then the import will fail. 
##


### Tasker XML File Structure

The exported files depending on the export type will have the following XML node structure:
```
<TaskerData sr="" dvi="1" tv="5.9.rc">
	<dmetric>1440.0,2392.0</dmetric>
	<Profile sr="prof1" ve="2">
		...
		<id>1</id>
		...
	</Profile>
	<Profile sr="prof2" ve="2">
		...
		<id>2</id>
		...
	</Profile>
	....
	<Project sr="proj0" ve="2">
		...
		<name>Base</name>
		<pids>1,2</pids>
		<scenes>Scene 1,Scene 2</scenes>
		<tids>1,2</tids>
	</Project>
	...
	<Scene sr="sceneScene 1">
		...
		<nme>Scene 1</nme>
		...
	</Scene>
	<Scene sr="sceneScene 2">
		...
		<nme>Scene 2</nme>
		...
	</Scene>
	...
	</Task>
		<Task sr="task1">
		...
		<id>1</id>
		...
	</Task>
	</Task>
		<Task sr="task2">
		...
		<id>2</id>
		...
	</Task>
	...
</TaskerData>
```

To find what type of exported file a given XML file is, even if it is not correctly named or described, examine the nodes in the XML file.
```
- If there is only one `Task` node; then
	It's a Task file.
- If there is only one `Scene` node and some optional `Task` nodes without `<nme>` tags; then
	It's a Scene file.
- If there is only one `Profile` node and some `Task` nodes; then
	It's a Project file.
- If the there is only one `Project` node and its `<name>` tag does not contain `Base`; then
	It's a Project file.
- Else
	It's a Data Backup file.
```
##


### Tasker XML File Nodes And Tags

The `Profile`, `Project`, `Scene` and `Task` nodes are optional depending on the export type. At least one needs to exist.

- **`dmetric Tag:`**

	The `<dmetric>` tag is an optional tag depending on if the XML contains a `Scene` node. It stands for `display metrics`. The tag contains the width and height of the display that is usable by an App. The height value will be the real height of the display minus the height of the navigation bar. The values will depend on which device the XML file was exported from so that the required scaling of scenes can be done when the XML file is imported into a Tasker on a device with different display metrics.
##


- **`Profile Node:`**

	*Required Root Tags:*
	
	- `<id>` defines the unique id of the profile.

	*Optional Root Tags:*
	- `<mid0>` defines the entry task id of the profile.
	- `<mid1>` defines the exit task id of the profile.
	- `<nme>` defines the unique name of the profile.

	The `<mid0>` and `<mid1>` tags will only exist if a task is actually set as the profile entry or exit task. At least one needs to be defined. If either tags exists and the task id in the tag is not referencing a named task, then it must be referencing an anonymous task.

	*Optional Sub Nodes:*
	- `App` defines the info for an Application Context.
	- `Day` defines the info for a Day Context.
	- `Event` defines the info for an Event Context.
	- `Loc` defines the info for a Location Context.
	- `State` defines the info for a State Context.
	- `Time` defines the info for a Time Context.

	At least one `Context` sub node needs to be defined.
##


- **`Project Node:`**

	*Required Root Tags:*
	
	- `<name>` defines the unique name of the project.	

	*Optional Root Tags:*
	- `<pids>` defines the profile ids of project.
	- `<scenes>` defines the scene names of project.
	- `<tids>` defines the task ids of project.

	The `<pids>`, `<scenes>` and `<tids>` tags will only exist if at least one profile, scene or task exists in the project respectively. The ids of anonymous profile or scene tasks are not listed in the `<tids>` tag of exported `Data Backup` XMl files. However, they are listed in exported `Project` XML files.
##


- **`Scene Node:`**

	*Required Root Tags:*
	
	- `<nme>` defines the unique name of the scene.

	*Optional Root Tags:*

	*Required Sub Nodes:*
	- `PropertiesElement` defines the info for the `Scene Properties`.

	*Optional Sub Nodes:*
	- `ButtonElement` defines the info for a `Button` Element.
	- `CheckBoxElement` defines the info for a `Checkbox` Element.
	- `DoodleElement` defines the info for a `Doodle` Element.
	- `EditTextElement` defines the info for a `TextEdit` Element.
	- `ImageElement` defines the info for an `Image` Element.
	- `ListElement` defines the info for a `Menu` Element.
	- `OvalElement` defines the info for an `Oval` Element.
	- `PickerElement` defines the info for an `Oval` Element.
	- `RectElement` defines the info for a `Rect` Element.
	- `SceneElement` defines the info for a `Map` Element.
	- `SliderElement` defines the info for a `Slider` Element.
	- `SpinnerElement` defines the info for a `Spinner` Element.
	- `SwitchElement` defines the info for a `Switch` Element.
	- `TextElement` defines the info for a `Text` Element.
	- `ToggleElement` defines the info for a `Toggle` Element.
	- `VideoElement` defines the info for a `Video` Element.
	- `WebElement` defines the info for a `WebView` Element.

	Depending on the elements in the scene or the key event of the scene, the nodes may contain one or more tags called  `checkchangeTask`, `clickTask`, `focuschangeTask`, `itemselectedTask`, `keyTask`, `linkclickTask`, `longclickTask`, `mapclickTask`, `maplongclickTask`, `pageloadedTask`, `strokeTask`, `valueselectedTask`, `videoTask`, etc. The tags will contain the task id of the anonymous task that will be run when the respective event is triggered. The tags and anonymous task nodes will only exist if at least one action is defined in the respective event tabs like `TAP`, `LONG TAP`, `STROKE`, etc. An anonymous task will be created regardless of if the only action is a `Perform Task` action.
##


- **`Task Node:`**

	*Required Root Tags:*
	
	- `<id>` defines the unique id of the task.

	*Optional Root Tags:*
	- `<nme>` defines the unique name of the task.
	- `<rty>` defines the `Collision Handling` option of the task. `0` or `no tags` == `Abort New Task`, `1` == `Abort Existing Task` and `2` == `Run Both Together`.
	- `<pri>` defines the priority of the task used in some cases.
	- `<stayawake>` defines the `Keep Device Awake` option of the task. `false` or `no tags` == `Do Not Keep Awake` and `true` == `Keep Awake`.

	*Required Sub Nodes:*
	- `Action` defines the info for an action.

	At least one `Action` sub node needs to be defined.
##


## Tasker XML Codes

Check [Tasker_XML_Codes.md](Tasker_XML_Codes.md) file for list of codes of that are used to identify different **`Task Actions`**, **`Profile Events`** and **`Profile States`**.
##


## Tasker XML Examples

```
<TaskerData sr="" dvi="1" tv="5.9.rc">
	<dmetric>1440.0,2392.0</dmetric>
	<Profile sr="prof10" ve="2">
		...
		<id>10</id>
		<mid0>112</mid0>
		<nme>Profile 1</nme>
		<Event sr="con0" ve="2">
			...
		</Event>
	</Profile>
	<Profile sr="prof15" ve="2">
		...
		<id>15</id>
		<mid0>150</mid0>
		<mid1>151</mid1>
		<nme>Profile 2</nme>
		<Event sr="con0" ve="2">
			...
		</Event>
		<Event sr="con1" ve="2">
			...
		</Event>
		...
	</Profile>
	<Project sr="proj0" ve="2">
		...
		<name>Some Project</name>
		<pids>10,15</pids>
		<scenes>Scene 1,Scene 2</scenes>
		<tids>112,150</tids>
	</Project>
	<Scene sr="sceneScene 1">
		...
		<nme>Scene 1</nme>
		...
		<TextElement sr="elements0" ve="3">
			...
			<itemclickTask>101</itemclickTask>
			...
		</TextElement>
		<TextElement sr="elements1" ve="3">
			...
		</TextElement>
		...
		<PropertiesElement sr="props">
			...
		</PropertiesElement>
	</Scene>
	<Scene sr="sceneScene 2">
		...
		<nme>Scene 2</nme>
		...
		<PropertiesElement sr="props">
			...
			<keyTask>103</keyTask>
			...
		</PropertiesElement>
	</Scene>
	<Task sr="task112">
		...
		<id>112</id>
		<nme>Task Called By Profile 1 As Entry Task</nme>
		...
		<Action sr="act0" ve="7">
			...
		</Action>
		<Action sr="act1" ve="7">
			...
		</Action>
		...
	</Task>
		<Task sr="task150">
		...
		<id>150</id>
		<nme>Task Called By Profile 2 As Entry Task</nme>
		...
		<Action sr="act0" ve="7">
			...
		</Action>
		<Action sr="act1" ve="7">
			...
		</Action>
		...
	</Task>
	</Task>
		<Task sr="task151">
		...
		<id>151</id>
		...
		<Action sr="act0" ve="7">
			...
		</Action>
		...
	</Task>
	</Task>
		<Task sr="task101">
		...
		<id>101</id>
		...
		<Action sr="act0" ve="7">
			...
		</Action>
		...
	</Task>
	</Task>
		<Task sr="task103">
		...
		<id>103</id>
		...
		<Action sr="act0" ve="7">
			...
		</Action>
		...
	</Task>
</TaskerData>
```

The `Profile 1` will run the named task with id `112` as entry task set in the `<mid0>` tag.
The `Profile 1` will not run any exit task as no `<mid1>` tag exists.

The `Profile 2` will run the named task with id `150` as entry task set in the `<mid0>` tag.
The `Profile 2` will run the anonymous task with id `151` as exit task set in the `<mid1>` tag.

The `TAP` event of the Text Element with `sr="elements0"` of `Scene 1` will run the anonymous task with id `101` set in the `<itemclickTask>` tag.
The `Key` event of the `Scene 2` will run the anonymous task with id `103` set in the `<keyTask>` tag.
##


### Downloads

- [GitHub releases](https://github.com/Taskomater/Tasker-XML-Info/releases).
##


### Current Features-
- Tasker XML export types info.
- Tasker XML file structure info.
- Tasker XML file nodes and tags info.
- Tasker XML codes info.
- Tasker XML examples.
##


### Planned Features

- Additional info for other sub nodes and tags.
##


### Issues

`-`
##


### Worthy Of Note


- This is an unofficial repository and its info has not been *blessed* by Tasker dev(s), atleast currently.
##


### FAQs And FUQs

Check [FAQs_And_FUQs.md](FAQs_And_FUQs.md) file for the **Frequently Asked Questions(FAQs)** and **Frequently Unasked Questions(FUQs)**.
##

### Changelog

Check [CHANGELOG.md](CHANGELOG.md) file for the **Changelog**.

##


### Contributions

`-`
##

[Tasker App]: https://play.google.com/store/apps/details?id=net.dinglisch.android.taskerm
[tasker_config_utils]: https://github.com/Taskomater/tasker_config_utils
