# Roomedit3dv3

[![Node.js](https://img.shields.io/badge/Node.js-4.4.3-blue.svg)](https://nodejs.org/)
[![npm](https://img.shields.io/badge/npm-2.15.1-blue.svg)](https://www.npmjs.com/)
[![License](http://img.shields.io/:license-mit-blue.svg)](http://opensource.org/licenses/MIT)

[![oAuth2](https://img.shields.io/badge/oAuth2-v1-green.svg)](http://developer.autodesk.com/)
[![Data-Management](https://img.shields.io/badge/Data%20Management-v1-green.svg)](http://developer.autodesk.com/)
[![OSS](https://img.shields.io/badge/OSS-v2-green.svg)](http://developer.autodesk.com/)
[![Model-Derivative](https://img.shields.io/badge/Model%20Derivative-v2-green.svg)](http://developer.autodesk.com/)

## Description

Forge Viewer extension to move building elements and update the Revit BIM in real-time using [socket.io](http://socket.io).

This is a [node.js](https://nodejs.org) web server implementing a Forge Viewer extension.

- [Forge Components, Prerequisites and Sample Setup](#1)
- [Round-Trip BIM Manipulaton via Forge and Roomedit3dv3](#2)
- [Connecting desktop and cloud](#3)
- [Interactive model modification in the Forge Viewer](#4)
- [Communication Back from Viewer Client to Node.js Web Server to Desktop BIM](#5)


## <a name="1"></a>Forge Components, Prerequisites and Sample Setup

`Roomedit3dv3` is based
on [Philippe Leefsma](http://twitter.com/F3lipek)'s
node.js-based boilerplate projects for the [Autodesk Forge Web Services APIs](http://forge.autodesk.com).

The following Forge APIs and components are used to manipuate a Revit BIM model:

- Authenticate and authorise the user &ndash; [Authentication (OAuth)](https://developer.autodesk.com/en/docs/oauth/v2)
- Access and download a RVT project file from A360 &ndash; [Data Management API](https://developer.autodesk.com/en/docs/data/v2)
- Translate and access its geometry and metadata &ndash; [Model Derivative API](https://developer.autodesk.com/en/docs/model-derivative/v2)
- Display to the user &ndash; [Viewer](https://developer.autodesk.com/en/docs/viewer/v2)

Just as Philippe original boilerplate code, this sample illustrates use of the following Forge npm packages:

- [forge.oauth2-js](https://github.com/Autodesk-Forge/forge.oauth2-js)
- [forge.oss-js](https://github.com/Autodesk-Forge/forge.oss-js)
- [forge.model.derivative-js](https://github.com/Autodesk-Forge/forge.model.derivative-js)
- [forge.data.management-js](https://github.com/Autodesk-Forge/forge.data.management-js)

Please refer to Philippe's original documentation for
the [prerequisites](https://github.com/Autodesk-Forge/forge-boilers.nodejs#prerequisites)
and [sample setup](https://github.com/Autodesk-Forge/forge-boilers.nodejs#boilers-setup).

Its describes the detailed steps required to set up your own Forge account,
install and modify the sample to use your credentials and deploy as a local server or on a platform such
as [Heroku](https://heroku.com).


## <a name="2"></a>Round-Trip BIM Manipulaton via Forge and Roomedit3dv3

The `roomedit3dv3` viewer extension enables interactive selection and movement of selected BIM elements in the model on screen.

The updated elements and their new locations are transferred back from the viewer client to the web server via a REST API call.

The server in turn uses [socket.io](http://socket.io) to broadcast the updates to the rest of the universe.

This broadcast is picked up by the [Roomedit3dApp](https://github.com/jeremytammik/Roomedit3dApp) C# .NET Revit add-in client.

This version supersedes its precursor [roomedit3d](https://github.com/jeremytammik/roomedit3d), which was hardwired for a specific model.

In `roomedit3dv3`, any model can be selected.

Todo: add a project identifier to the broadcasts to enable the C# add-in broadcoast receivers to igonre all messages not pertaining to the current Revit BIM.

The selected element is identified via its Revit UniqueId.


## <a name="3"></a>Connecting Desktop and Cloud

`Roomedit3dv3` is a member of the suite of samples connecting the desktop and the cloud.

Each of the samples consists of a C# .NET Revit API desktop add-in and a web server:

- [RoomEditorApp](https://github.com/jeremytammik/RoomEditorApp) and  the [roomeditdb](https://github.com/jeremytammik/roomedit) CouchDB
	database and web server demonstrating real-time round-trip graphical editing of furniture family instance location and rotation plus textual editing of element properties in a simplified 2D representation of the 3D BIM.
- [FireRatingCloud](https://github.com/jeremytammik/FireRatingCloud) and
	the [fireratingdb](https://github.com/jeremytammik/firerating) node.js
	MongoDB web server demonstrating real-time round-trip editing of Revit element shared parameter values.
- [Roomedit3dApp](https://github.com/jeremytammik/Roomedit3dApp) and
  the first [roomedit3d](https://github.com/jeremytammik/roomedit3d) Forge Viewer extension demonstrating translation of furniture family instances in the viewer and updating the Revit BIM in real time via a socket.io broadcast with a hard-coded sample model.
- [Roomedit3dApp](https://github.com/jeremytammik/Roomedit3dApp) and
  the [roomedit3dv3](https://github.com/jeremytammik/roomedit3d) Forge Viewer extension demonstrating the same functionality with a user selected model stored in A360.


## <a name="4"></a>Interactive Model Modification in the Forge Viewer

The `Roomedit3dTranslationTool` implements a viewer extension that enables the user to select a component and interactively move it around on the screen, defining a translation to be applied to it and communicated back to the source CAD model.


## <a name="5"></a>Communication Back from Viewer Client to Node.js Web Server to Desktop BIM

![Roomedit3dv3 architecture](img/roomedit3dv3_architecture.png "Roomedit3dv3 architecture and communication path")

The Forge Viewer itself provides viewing functionality only, no editing.

The pre-defined Forge communication path is one-way only, from the desktop to the cloud, from the source 'seed' CAD model to the translated Forge API bucket and JSON data bubble stream.

This sample demonstrates an interactive modification of the [three.js](http://threejs.org) graphics presented by the viewer and a communication path to send updated element location information back to the desktop product in real time.

In this case, the source desktop CAD model is a Revit BIM, and the modifications applied are building element translations.

The viewer client in the browser uses [fetch](https://github.com/github/fetch) to implement a REST API POST call to communicate the modified element external id and translation back to the node.js server.

The node.js server uses a [socket.io](http://socket.io) broadcast to notify the desktop of the changes.

The dedicated C# .NET Revit add-in [Roomedit3dApp](https://github.com/jeremytammik/Roomedit3dApp) subscribes to the socket.io channel, retrieves the updating data and raises an external event to obtain a valid Revit API context and apply it to the BIM.


## <a name="98"></a>Authors

- [Philippe Leefsma](http://adndevblog.typepad.com/cloud_and_mobile/philippe-leefsma.html),
[Autodesk](http://www.autodesk.com) [Forge Partner Development](http://forge.autodesk.com)
- Jeremy Tammik,
[The Building Coder](http://thebuildingcoder.typepad.com) and
[The 3D Web Coder](http://the3dwebcoder.typepad.com),
[ADN](http://www.autodesk.com/adn)
[Open](http://www.autodesk.com/adnopen),
[Forge Partner Development](http://forge.autodesk.com),
[Autodesk Inc.](http://www.autodesk.com)


## <a name="99"></a>License

This sample is licensed under the terms of the [MIT License](http://opensource.org/licenses/MIT).
Please see the [LICENSE](LICENSE) file for full details.
