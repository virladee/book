---
title: Development
layout: default
nav_order: 1
parent: Virtual Labs
---

{:.no_toc}
# Design and Development of Virtual Labs

{: .no_toc }
<details open markdown="block">
  <summary>
    Table of contents
  </summary>
  {: .text-delta }
- TOC
{:toc}
</details>

## Data Collection and Formalization

The detailed information of the selected physical labs must be collected, elaborated, and
formalized in terms of assets, i.e. basic elements composing the lab,
e.g. physical objects like machine tools, parts, conveyors, buffers, but
also processes and plans.

Specifically, the VirLaDEE project adopted a schematic template to formalize the assets
and data were serialized as [spreadsheet data](https://virtualfactory.gitbook.io/vlft/kb/instantiation/assets/spreadsheet). The template consists of
two sheets:
-   “**Context**”: definition of context setup.
-   “**Assets**”: detailed definition of assets that are included (or
    not) in the scene. Assets not included in the scene are
    models/templates that are referenced or could be later instantiated
    in the scene.

The **Context** sheet contains the following fields:
-   “**UnitOfMeasureScale**”: The default unit of measurement scale for
    distances (e.g. 0.01 stands for centimeter, whereas 1 stands for
    meter)
-   “**Zup**”: is a boolean parameter specifying the convention for the
    3D Cartesian coordinate system. The parameter is set to true if
    Z-axis is the vertical axis pointing up from the ground (i.e. Zup
    convention), or set to false if the Y-axis is the vertical axis
    pointing up from the ground (i.e. Yup convention). In both cases the
    right-handed system convention is assumed.

The Assets sheet contains items characterized by the following fields in
columns:
-   “**id**”: unique identifier of the asset \[required\]
-   “**inScene**”: equal to 1 if the asset is included in the scene, 0
    otherwise \[required\]
-   “**descr**”: textual static description of the asset \[optional\]
-   “**type**”: the type of the asset, i.e. OWL class of the Factory
    Data Model it belongs to \[required\]
-   “**model**”: ID of the model of the asset (if existing), e.g. the
    model of a machine tool that is described in a catalog. In turn, the
    model can have a model. Please refer to the Object Typing pattern.
-   “**file**”: file path of the 2D/3D model representation, as relative
    to the RepoPath \[optional\]. The file can be available on a local
    or remote file system (see example), as any online repository
    accessible via HTTPS (see example). The “file” property can be used
    also as a reference to a specific component inside the hierarchy of
    a 3D model by adding a hashtag and the ID of the component to the
    file path (e.g. \#componentID'). For instance, the “file” property
    will have the value “FileName.glb#nodeId” if it refers to a node
    with unique ID “nodeId” inside a GLTF file named “FileName.glb”.
-   “**unit**”: Unit of measure to interpret a 3D representation (e.g.
    0.01 stands for centimeter, whereas 1 stands for meter) \[required
    if “file” is defined\]
-   “**position**”: The Position of the asset in terms of x, y, z
    values. If missing, the default value is \[0.0,0.0,0.0\] or the
    value of the corresponding node in a GLTF hierarchy, if available.
-   “**rotation**”: The rotation of the asset as Euler angles YXZ
    defined in radians. If missing, the default value is the rotation of
    the corresponding node in a GLTF hierarchy (if available), otherwise
    \[0.0,0.0,0.0\].
-   “**placementRelTo**”: ID of the asset with respect to which the
    placement (position and rotation) is defined in relative terms. This
    means that a rototranslation must be applied with respect to the
    placement of the asset identified by the value of placementRelTo.
    This relation happens between nodes directly connected in a scene
    graph. For instance, the placement of a pallet can be defined as
    relative to the placement of a conveyor.
-   “**parentObject**”: ID of the asset that is decomposed (it may be
    empty or missing) when an aggregated asset is represented. If a
    parentObject is defined, then placementRelTo is defined as equal to
    parentObject. However, if a placementRelTo value is defined, then it
    is not necessarily also the parentObject. For instance, machine
    components decompose a workstation, whereas a pallet doesn't
    decompose a conveyor.
-   “**connectedTo**”: list of assets ID that are connected downstream
    to the asset. The list may be empty or missing \[optional\]
-   “**assignmentTo**”: list of assignments to other assets. The list
    may be empty or missing \[optional\]
-   “**successors**”: successor of a task \[optional\]
-   “**taskTime**”: execution time of a task \[optional\]
-   “**bufferCap**”: buffer capacity \[optional\]
-   “**TTF**”: time to failure (for devices like machines) in terms of
    probability distribution and value \[optional\]
-   “**TTR**”: time to repair (for devices like machines) in terms of
    probability distribution and value \[optional\]
-   “**duration**”: duration of a production plan \[optional\]
-   “**quantity**”: quantity to be produced in a production plan
    \[optional\]


    ## Ontology for Virtual Labs

The digital representation of the use case labs has been built grounding
on a standard, extensible, and common data model for the representation
of factory objects related to production systems, resources, processes
and products, defined as an [OWL ontology](https://www.w3.org/TR/owl2-overview/) to enhance interoperability. 
The OWL ontology is named [Factory Data Model](https://virtualfactory.gitbook.io/vlft/kb/fdm).

The following table reports the list of **prefixes that have been
defined for the ontology modules composing the Factory Data Model.** All
modules are available online at the same address, except *fsm* that
can be found at [http://people.cs.aau.dk/
dolog/fsm/fsm.owl](http://people.cs.aau.dk/%7Edolog/fsm/fsm.owl) and
*ifc* that can be found at <http://www.ontoeng.com/IFC4_ADD1>.

<table>
<colgroup>
<col style="width: 50%" />
<col style="width: 50%" />
</colgroup>
<thead>
<tr class="header">
<th>
<p>Prefix</p>
</th>
<th>Value</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td>
<p>expr</p>
</td>
<td><a
href="https://w3id.org/express">https://w3id.org/express#</a></td>
</tr>
<tr class="even">
<td>
<p>list</p>
</td>
<td><a href="https://w3id.org/list">https://w3id.org/list#</a></td>
</tr>
<tr class="odd">
<td>
<p>fsm</p>
</td>
<td><a
href="http://www.learninglab.de/%7Edolog/fsm/fsm.owl">http://www.learninglab.de/~dolog/fsm/fsm.owl#</a></td>
</tr>
<tr class="even">
<td>
<p>stat</p>
</td>
<td><a
href="https://w3id.org/ontoeng/statistics">https://w3id.org/ontoeng/statistics#</a></td>
</tr>
<tr class="odd">
<td>
<p>time</p>
</td>
<td><a
href="http://www.w3.org/2006/time">http://www.w3.org/2006/time#</a></td>
</tr>
<tr class="even">
<td>
<p>ex</p>
</td>
<td><a
href="https://w3id.org/ontoeng/expression">https://w3id.org/ontoeng/expression#</a></td>
</tr>
<tr class="odd">
<td>
<p>sosa</p>
</td>
<td><a
href="http://www.w3.org/ns/sosa/">http://www.w3.org/ns/sosa/</a></td>
</tr>
<tr class="even">
<td>
<p>ssn</p>
</td>
<td><a
href="http://www.w3.org/ns/ssn/">http://www.w3.org/ns/ssn/</a></td>
</tr>
<tr class="odd">
<td>
<p>osph</p>
</td>
<td><a
href="https://w3id.org/ontoeng/osph">https://w3id.org/ontoeng/osph#</a></td>
</tr>
<tr class="even">
<td>
<p>ifc</p>
</td>
<td><a
href="https://standards.buildingsmart.org/IFC/DEV/IFC4/ADD1/OWL">https://standards.buildingsmart.org/IFC/DEV/IF
ADD1/OWL#</a></td>
</tr>
<tr class="odd">
<td>
<p>ifcext</p>
</td>
<td><a
href="https://w3id.org/ontoeng/IFC4_ADD1_extension">https://w3id.org/ontoeng/IFC4_ADD1_extensio</a></td>
</tr>
<tr class="even">
<td>
<p>cs</p>
</td>
<td><a
href="https://w3id.org/ontoeng/controlSystem">https://w3id.org/ontoeng/controlSystem#</a></td>
</tr>
<tr class="odd">
<td>
<p>fa</p>
</td>
<td><a
href="https://w3id.org/ontoeng/factory">http://www.ontoeng.com/factory#</a></td>
</tr>
</tbody>
</table>


The most used OWL classes of the Factory Data Model to model the assets
in the use case labs are reported in the next table. **fa:** prefix stands for [https://w3id.org/ontoeng/factory#](https://w3id.org/ontoeng/factory)

<table>
<colgroup>
<col style="width: 35%" />
<col style="width: 35%" />
<col style="width: 29%" />
</colgroup>
<thead>
<tr class="header">
<th>Subclass of ifc:IfcProduct</th>
<th>Description</th>
<th>
<p>Subclass of ifc:IfctTypeProduct</p>
</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td>fa:Artifact</td>
<td>Part that is the result of a production activity</td>
<td>
<p>fa:ArtifactType</p>
</td>
</tr>
<tr class="even">
<td>fa:BufferElement</td>
<td>Object or space dedicated to hosting objects (e.g. artifacts,
pallets)</td>
<td>
<p>fa:BufferElementType</p>
</td>
</tr>
<tr class="odd">
<td>fa:MachineTool</td>
<td>Generic machine tool</td>
<td>
<p>fa:MachineToolType</p>
</td>
</tr>
<tr class="even">
<td>fa:Robot</td>
<td>Robot</td>
<td>
<p>fa:RobotType</p>
</td>
</tr>
<tr class="odd">
<td>fa:Pallet</td>
<td>Object dedicated to hosting artifacts that are transported in a
system (e.g. production system).</td>
<td>
<p>fa:PalletType</p>
</td>
</tr>
<tr class="even">
<td>fa:Tool</td>
<td>Tool</td>
<td>
<p>fa:ToolType</p>
</td>
</tr>

<tr class="odd">
<td>fa:AssemblyTask</td>
<td>Task executing an assemby operation.</td>
<td>
<p>fa:AssemblyTaskType</p>
</td>
</tr>
<tr class="even">
<td>fa:DisassemblyTask</td>
<td>Task executing an disassemby operation.</td>
<td>
<p>fa:DisassemblyTaskType</p>
</td>
</tr>
<tr class="odd">
<td>fa:MaintenanceTask</td>
<td>Task executing a maintenance operation.</td>
<td>
<p>fa:MaintenanceTaskType</p>
</td>
</tr>
<tr class="even">
<td>fa:ManufacturingTask</td>
<td>Task executing a manufacturing operation.</td>
<td>
<p>fa:ManufacturingTaskType</p>
</td>
</tr>
<tr class="odd">
<td>fa:MachiningTask</td>
<td>Task executing a manufacturing operation.</td>
<td>
<p>fa:MachiningTaskType</p>
</td>
</tr>
<tr class="even">
<td>fa:QualityControlTask</td>
<td>Task executing a quality control.</td>
<td>
<p>fa:QualityControlTaskType</p>
</td>
</tr>
<tr class="odd">
<td>fa:TransportTask</td>
<td>Task executing a transportation.</td>
<td>
<p>fa:TransportTaskType</p>
</td>
</tr>
</tbody>
</table>


## JSON Serialization of Virtual Labs

The model of the virtual lab can be serialized as a JSON file defined according to
a [specific schema](https://virtualfactory.gitbook.io/vlft/kb/instantiation/assets/json).

The schema of the JSON serialization consists of three root properties:
-   "**context**": definition of context setup.
-   "**scene**": definition of the 3D scene in terms of visible assets,
    i.e. an array consisting of asset IDs that are included in the 3D
    scene.
-   "**assets**": array of detailed definition of assets that are
    included or not in the scene.

The **context** contains the following properties (all required):
-   "**UnitOfMeasureScale**": The unit of measure scale (e.g. 0.01
    stands for centimeter, whereas 1 stands for meter)
-   "**Zup**": is a boolean parameter specifying the convention for the
    3-D Cartesian coordinate system. The parameter is set to true if
    Z-axis is the vertical axis pointing up from the ground (i.e. Zup
    convention), or set to false if the Y-axis is the vertical axis
    pointing up from the ground (i.e. Yup convention). In both cases the
    right-handed system convention is assumed.

The **assets** array contains items characterized by the following
properties:
-   "**id**": unique identifier of the asset \[required\];
-   "**type**": The type of the asset, i.e. OWL class of the Factory
    Data Model it belongs to \[required\]
-   "**model**": ID of the model of the asset (if existing), e.g. the
    model of a machine tool that is described in a catalog. In turn, the
    model can have a model. Please refer to the Object Typing pattern.
-   "**representations**": Array of 2D/3D representations of the asset.
    Each item of the array may have the following properties:
-   "**file**": file path of the 2D/3D model representation
    \[optional\]. The file can be available on a local or remote file
    system (see example), as any online repository accessible via HTTPS
    (see example). The “file” property can be used also as a reference
    to a specific component inside the hierarchy of a 3D model by adding
    a hashtag and the ID of the component to the file path (e.g.
    \#componentID'). For instance, the “file” property will have the
    value "FileName.glb#nodeId" if it refers to a node with unique id
    "nodeId" inside a GLTF file named "FileName.glb".
-   "**unit**": Unit of measure to interpret a 3D representation (e.g.
    0.01 stands for centimeter, whereas 1 stands for meter) \[required
    if “file” is defined\]
-   "**position**": The Position of the asset. If missing, the default
    value is \[0.0,0.0,0.0\];
-   "**scale**": The scaling of the asset. If missing, the default value
    is \[1.0,1.0,1.0\]. Scaling is defined independently of the unit of
    measurement of the 3D representation (cf. “unit” in
    “representations”);
-   "**rotation**": The rotation of the asset defined as Euler angles
    YXZ in radians, or quaternion or rotation matrix. If missing, the
    default value is the rotation of the corresponding node in the GLTF
    hierarchy (if available), otherwise \[0.0,0.0,0.0\] as Euler angles,
    \[1.0, 0.0, 0.0, 0.0\] as quaternion, or \[\[1 0 0\], \[0 1 0\], \[0
    0 1\]\] as rotation matrix.
-   "**placementRelTo**": ID of the asset with respect to which the
    placement (position and rotation) is defined in relative terms. This
    means that a roto-translation must be applied with respect to the
    placement of the asset identified by the value of placementRelTo.
    This relation happens between nodes directly connected in a scene
    graph. For instance, the placement of a pallet can be defined as
    relative to the placement of a conveyor.
-   "**parentObject**": ID of the asset that is decomposed (it may be
    empty or missing) when an aggregated asset is represented. If a
    parentObject is defined, then placementRelTo is defined as equal to
    parentObject. However, if a placementRelTo value is defined, then it
    is not necessarily also the parentObject. For instance, machine
    components decompose a workstation, whereas a pallet doesn't
    decompose a conveyor.


## Visualization of Virtual Labs

Once the assets of the lab have been formalized and modelled (including
3D models), the virtual lab can be visualized using Virtual Reality
tools. Herein, it is shown how VEB.js (Virtual Environment based on
Babylon.js) can be employed, but several other tools can be used as
well, e.g. Unity3D, UnrealEngine, Three.js.

[VEB.js](https://virtualfactory.gitbook.io/vlft/tools/vebjs) is a reconfigurable model-driven virtual environment
application based on Babylon.js, a complete JavaScript framework and
graphics engine for building 3D applications with HTML5 and WebGL (Web
Graphics Library). Babylon.js enables to load and draw 3D objects,
manage these 3D objects, create and manage special effects, play and
manage spatialized sounds, create gameplays and more. Babylon.js library
is free to use, thus enabling also didactic use. VEB.js works on any
Operating System (OS) and browser that supports WebGL.

VEB.js supports the selection and loading of a 3D scene (e.g. a virtual
lab) by either setting URL parameters or by manually importing JSON file
or an ontology. 
The users can visualize, navigate and interact with a **scene** in VEB.js. A
scene contains the assets of the lab, which are placed mimicking
the physical counterpart. Light sources and cameras are also included.
The application enables the user to use different functionalities, including:
1.  Visualize the virtual representation of the lab space and relevant assets
2.  Navigate the scene using the keyboard or mouse. 
3.  Change point of view by switching the camera: this can be done by
    accessing the *Inspector* and activating/deactivating the camera of
    interest.
4. Interact with the light of the environment: choosing the preferred
    light source.
5.  Extract screen captures from the Data generation panel.
6.  Moving assets: the assets included in the scene can be selected with
    the cursor, and moved with the buttons *Move* and *Rotate*.