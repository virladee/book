---
title: Monitoring
layout: default
nav_order: 2
parent: Virtual Labs
---

{: .no_toc }
<details open markdown="block">
  <summary>
    Table of contents
  </summary>
  {: .text-delta }
- TOC
{:toc}
</details>

# Monitoring of the physical labs

Monitoring of physical labs is included among the key feature of the
more general Digital Twin architecture, as discussed in the article by Modoni et al. \[1\].

An effective monitoring asks for 1) predefined schema to formalize data
that are exchanged and 2) a communication channel. The following
sections address these two requirements and present the solution
developed for Virtual Labs.

## Formalization of monitoring data

During monitoring, the states and properties of assets evolve along
time. Unfortunately, the data formalization adopted for defining assets
of a physical lab is not appropriate, since the properties
are static. Therefore, herein another data schema is used to capture the
evolution of monitoring data while keeping consistency with the schema
defining the assets.

The proposed schema can be used to serialize data in JSON files that are
produced/consumed by software applications related to the virtual lab.
The same schema can be used to formalize the evolution of assets related
to historical data or simulated data. In same cases, the raw message can
be serialized according to a different schema, thus needing a
preliminary elaboration and conversion (e.g. example in Sect.4).

The JSON schema is composed of three root properties:

-   “**context**”: set of properties of initial setup

-   "**nodes**": an array defining what happens during the asset
    evolution

-   "**sequences**": an array defining sequences of asset animation
    (mainly space&time)

The “**context**” is characterized by the following property:

-   "**UnitOfMeasureScale**": (optional) unit of measure scale (e.g.
    0.01 stands for centimeter, whereas 1 stands for meter). If not
    defined, then the unit of measure of the associated scene is
    adopted.

Each item in the “**nodes**” array contains the following properties
(all required):

-   "**id**": unique identifier of the asset that is characterized in
    terms of evolution \[required\]

-   "**actions**": array of actions that are relevant for the asset
    evolution

Each item in the “**actions**” array contains the following properties (all required):

-   "**trigger**": specifying when the action takes place with
    properties “type” and “data”

-   "**event**": specifying what action takes place with properties
    “type” and “data”

Possible values for “**type**” of “trigger”:

-   "**timestamp**": the value in “data” is interpreted as time in
    milliseconds after the start of asset evolution

Possible values for “**type**” of “event”:

-   "**animation**": the asset starts an animation. The value of “data”
    property defines the animation sequence to be employed, specified
    either as the ID of an item in the array of "sequences" or as the
    path of the file (.bin or .txt) containing the animation sequence as
    text. The positions and rotations are defined in absolute terms, but
    if the additional property “placementRelTo” is defined then it must
    be intended in relative terms.

-   "**animationAdditive**": the asset starts an animation. The value of
    “data” property defines the animation sequence to be employed,
    specified either as the ID of an item in the array of "sequences" or
    as the path of the file (.bin or .txt) containing the animation
    sequence as text. The animation is intended as additive
    (incremental) with respect to the current placement (position,
    rotation) of the asset.

-   “**show**”: the asset is shown in the scene. The place where the
    asset appears is defined with additional properties (as for the
    scene definition): "position" (default value \[0.0,0.0,0.0\]),
    “scale" (default value \[1.0,1.0,1.0\]), "rotation" (default value
    \[0.0,0.0,0.0\] Euler angles YXZ in radians), "placementRelTo". For
    instance, if only "placementRelTo" is defined, then position, scale
    and rotation have default values.

-   “**attach**”: the asset keeps its absolute position, rotation and
    scale, but its location in the scene graph is updated according to
    the value of property "placementRelTo". If "placementRelTo" is empty
    or not defined, then the asset is attached to the root node in the
    scene graph.

-   “**hide**”: the asset is hidden from the scene

-   “**state**”: the asset changes its state in terms of 3D
    representation and description. The state is specified by the
    following properties, all optional:

    -   “**data**” property is a string with the file path of the 3D
        representation file (e.g. .obj). The new 3D representation
        completely replace the (possibly) existing mesh while inheriting
        its attributes (e.g. position, rotation, scaling).

    -   "**descr**" is a string with the description of the current
        state. This description is incremental and does not replace the
        static description of the asset.

-   “**link**”: link to a (text) file that is specified by the following
    properties:

    -   “**data**” is a string with the local file path

    -   "**descr**" is a string with the description of the content that
        is found at the link

    -   “**URL**” is the address where the file can be downloaded from

Each item in the “**sequences**” array contains the following
properties:

-   "**id**": unique identifier of the animation sequence

-   "**FPS**": integer defining the FPS (frame per second).

-   "**pos**": \[optional\] array of positions in the 3D space for each
    frame.

-   "**rot**": \[optional\] array of rotations (as Euler angles YXZ in
    radians or alternatively as quaternion for "animation" and
    axis-angle for "animationAdditive") in the 3D space for each frame.

## MQTT communication

An efficient and effective communication channel is fundamental for
monitoring and synchronizing the physical lab and its virtual
counterpart.

[MQTT (Message Queuing Telemetry Transport)](https://mqtt.org/) is a lightweight,
publish-subscribe messaging protocol that is commonly used in the
Internet of Things (IoT) and other applications. MQTT can be exploited
for monitoring assets, but also for bi-directional communications with
assets that can:

-   send feedback about current state and value of dynamic properties

-   receive commands for the execution of actions, change of setup, etc.

<img src="images/VLmonitoring/image2.jpg"
style="width:6.179in;height:3.38749in" />

*MQTT architecture*

Different programming languages can be used to develop MQTT clients that
participate in a MQTT communication architecture, e.g. JavaScript and
Pyhton libraries. In addition to publishers and subscribers, the MQTT
architecture needs a broker to manage messages.

Several resources are freely available online to develop MQTT
architectures:

-   tools to develop and test MQTT architecture/components (e.g.
    MQTTLens)

-   free public MQTT broker, e.g. [broker.emqx.io](https://www.emqx.com/en/mqtt/public-mqtt5-broker)

-   desktop MQTT client, e.g. [MQTTX](https://mqttx.app/)

-   tutorials on MQTT and related tools, e.g. [this tutorial](http://www.steves-internet-guide.com/using-mqtt-lens/)

-   libraries to develop MQTT components, e.g. [MQTT.js library](https://www.npmjs.com/package/mqtt)

## OntoGuiWeb

OntoGuiWeb is a web application providing a graphical user interface for
the instantiation and exploration of digital models, in particular
defined as OWL ontologies. OntoGuiWeb was first developed during
previous projects and works on any browser without specific
configuration of the hardware is needed. OntoGuiWeb is developed in
JavaScript making use of libraries rdflib, opn, path, server,
xmlhttprequest. Source code of OntoGuiWeb is available upon request for
non-commercial applications.

A demo of OntoGui Web is [available online](https://difactory.github.io/DF/tools/OntoGuiWeb.html) and includes the
following basic modules: Control Panel, Utilities, Asset Design, System
Design, System Control, Performance Evaluation, Virtual Environment,
StateChart, SPARQL.

During the VirLaDEE project the novel OntoGuiWeb module name MQTT Sync
was designed and implemented, taking advantage of the JavaScript library
MQTT.js.

The MQTT Sync module provides an MQTT client that can:

-   publish a message on a topic

-   subscribe to topics

In addition, it is possible to load a sequence of messages ("Message
Sequence") as a JSON list and publish them automatically and iteratively
based on a predefined time interval ("Message interval").

<img src="images/VLmonitoring/image3.png"
style="width:5.94462in;height:4.98041in"
alt="https://files.gitbook.com/v0/b/gitbook-x-prod.appspot.com/o/spaces%2F-Lh1dC9-F-cM6usakky7%2Fuploads%2FcYJwgvS2Aj8QPFj7Rmmr%2Fimmagine.png?alt=media&amp;token=7d74b69e-9b54-4b22-b4f6-daeabfb1bcd2" />

*OntoGuiWeb – MQTT Synchronization module interface*

## PERFORM lab example

The PERFORM lab contains two COMAU NS16 robots that can be monitored in
terms of joint states while a robot is executing a trajectory. During
the execution of the trajectory, the robot controller can communicate
the joint state (i.e. position, velocity, force/torque, etc.) that in
turn can be stored (e.g. in a text-based file) or published (e.g. via
MQTT). Herein, we take in consideration only the joint position, i.e.
the rotation angle of the joint.

The joint angles are likely to be measured by sensors or encoders that
are attached to the robot joints. These sensors can generate data at a
high rate, potentially several times per second or even faster,
depending on the specific application and the performance requirements.

The joint positions of the robot in the PERFORM Lab are saved at 10 Hz
(i.e. one position is stored every 0.1 \[s\]) in a JSON file that
contains a list of position items, where each item defines the angles of
the joints in radians \[rad\]. Here below an example of item is shown:


```
{
"J1": 0.0,
"J2": 0.0,
"J3": 1.57,
"J4": 0.0,
"J5": -1.57,
"J6": 0.0,
"J7": 0.0,
"J8": 0.0,
"J9": 0.0,
"J10": 0.0
}
```


Each item of the list reports by default the value of 10 joint angles.
Robot\_1 of PERFORM Lab consists of 6 joints, therefore only joints from
"J1" to "J6" contain relevant values while the others (from "J7" to
"J10") will be always set to zero.

An example of robot trajectory can be found in the file
PERFORM\_trajectory\_example.json that defines a trajectory moving the
tool along the vertical axis. Using the command “**Load and Start
Publish**” of OntoGuiWeb – MQTT Synchronization module, the trajectory
can be published on the selected topic (e.g. “/VL/PERFORM”) via MQTT
communication, thus emulating what the real robot controller would do.
Thanks to the “SUBSCRIBE” functionalities of MQTT Synchronization
module, it is possible to subscribe to the same topic to validate the
behaviour of the web application. The following figure shows a
screenshot of OntoGuiWeb while the messages of the trajectory are
published and received.

<img src="images/VLmonitoring/image4.png"
style="width:5.07666in;height:7.22322in" />

*OntoGuiWeb – MQTT Synchronization module interface*


## References

1.  Modoni GE, Caldarola EG, Sacco M, Terkaj W (2019) Synchronizing
    physical and digital factory: benefits and technical challenges.
    Procedia CIRP, 79:472-477


