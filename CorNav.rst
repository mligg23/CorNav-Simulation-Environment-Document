Simulation Environment Document
===============================

Download Simulator
------------------

First download the
`simulator <https://drive.google.com/drive/folders/1zbywYhxFCbSnSy4vDGaEieX_nlVhbhyl>`__
and unzip it.

Launch Simulator
----------------

Devices with graphical interfaces
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

You just need to run the appropriate executable file in windows or
Linux.

In windows: Run the GrabSimClient.exe executable.

In Linux:

.. code:: shell

   bash /path/to/LinuxClient/GrabSimClient.sh

.. _devices-without-graphical-interfaces1:

Devices without graphical interfaces [1]_
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Verifying GitHub Access
^^^^^^^^^^^^^^^^^^^^^^^

Verify that you can access the Unreal Engine source code repository on
GitHub: https://github.com/EpicGames/UnrealEngine. If you cannot access
the repository then you will need to `link your GitHub account with your
Epic Games Account <https://www.unrealengine.com/en-US/ue-on-github>`__.

Authenticating with GitHub Container Registry
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

To download container images from GitHub Container Registry using Docker
you will need to authenticate using a personal access token. If you do
not already have a personal access token with the ``read:packages``
scope then you will need to `follow the steps to create
one <https://docs.github.com/en/github/authenticating-to-github/keeping-your-account-and-data-secure/creating-a-personal-access-token>`__.

Once you have created a personal access token with the required scope,
use the ``docker login`` command to authenticate with GitHub Container
Registry as described in the `instructions from
GitHub <https://docs.github.com/en/packages/working-with-a-github-packages-registry/working-with-the-container-registry#authenticating-to-the-container-registry>`__.
This command will need to be run from the command-line interface。

Once you have opened the command-line prompt then run the command shown
below, replacing ``ACCESS_TOKEN`` with your personal access token and
``USERNAME`` with your GitHub username:

::

   echo ACCESS_TOKEN | docker login ghcr.io -u USERNAME --password-stdin

If the authentication process was successful then you should see the
message *“Login Succeeded”* displayed.

Pulling Prebuilt Container Images
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The official prebuilt container images for Unreal Engine are stored as
image tags in the
`ghcr.io/epicgames/unreal-engine <https://ghcr.io/epicgames/unreal-engine>`__
repository. To download the Linux development image for Unreal Engine
4.27, use the ``docker pull`` command shown below:

::

   docker pull ghcr.io/epicgames/unreal-engine:dev-4.27

This will download a container image that encapsulates the files for
Unreal Editor and build tools, which are quite large. Depending on the
speed of your internet connection, the download process may take some
time. When the download is complete, you should see the message
*“Status: Downloaded newer image for
ghcr.io/epicgames/unreal-engine:dev-4.27”* displayed.

Building the Container
^^^^^^^^^^^^^^^^^^^^^^

You need to make sure you have sufficient permissions on the
LinuxClient:

::

   cd LinuxClient
   chmod -R 775 Engine
   chmod -R 775 GrabSim
   chmod 777 GrabSimClient.sh

Use the following docker file code to create a new one to run.

::

   FROM ghcr.io/epicgames/unreal-engine:runtime-pixel-streaming

   COPY --chown=ue5:ue5 LinuxClient path/to/LinuxClient

   ENV NVIDIA_DRIVER_CAPABILITIES all

   ENTRYPOINT ["bin/bash", "/path/to/LinuxClient/GrabSimClient.sh", "-RenderOffScreen"]
   CMD []

After creating the container you can enter as root if needed:

::

   docker exec -it -u root YOUR_CONTAINER /bin/bash

To exit the container, run the command shown below:

::

   exit

Note that our simulator runs in a docker container and occupies port
30001. If you want to map to other ports(eg. port 30020) on the host and
control the simulator on the server, you need to build the container
with the following command:

::

   nvidia-docker run --privileged -it --gpus='"device=1"' -p 30020:30001 --name sim30020 -v /YOUR/Mount/FOLDER:/home YOUR_BUILDED_IMAGE /bin/bash

   cd /PATH/TO/LinuxClient
   bash GrabSim.sh -RenderOffScreen

Communication Protocol
----------------------

-  **Protocol**: gRPC
-  **Port**: 30001

Data Types
----------

Only the data type is introduced, please refer to the interface method
section for interface details.

Count
~~~~~

Used for initword( In version 1)

+---+---+---+--------------------------------------------------------+
| F | T | V | Description                                            |
| i | y | a |                                                        |
| e | p | l |                                                        |
| l | e | u |                                                        |
| d |   | e |                                                        |
+===+===+===+========================================================+
| v | i | ( | Number of scenes in world. Means num_processes.        |
| a | n | 0 | Usually set to 1                                       |
| l | t | ) |                                                        |
| u | 3 |   |                                                        |
| e | 2 |   |                                                        |
+---+---+---+--------------------------------------------------------+

**Usage:**

::

   GrabSim_pb2.Count(value=1)

BatchMap
~~~~~~~~

Used for initword( In version 2)

+---+---+-----+-------------------------------------------------------+
| F | T | Va  | Description                                           |
| i | y | lue |                                                       |
| e | p |     |                                                       |
| l | e |     |                                                       |
| d |   |     |                                                       |
+===+===+=====+=======================================================+
| c | i | (0) | Number of scenes in world. Means num_processes.       |
| o | n |     | Usually set to 1                                      |
| u | t |     |                                                       |
| n | 3 |     |                                                       |
| t | 2 |     |                                                       |
+---+---+-----+-------------------------------------------------------+
| m | i | 3   | Meaning of mapID: 3 : Coffee 4 : Restaurant 5 : Nurse |
| a | n | ,4, | home                                                  |
| p | t | 5   |                                                       |
| I | 3 |     |                                                       |
| D | 2 |     |                                                       |
+---+---+-----+-------------------------------------------------------+

**Usage:**

::

   GrabSim_pb2.BatchMap(count=1, mapID=3)

Nothing
~~~~~~~

No content, used when interface does not need input or output values.(
in version 1)

**Usage:**

::

   GrabSim_pb2.Nothing()

NUL
~~~

No content, used when interface does not need input or output values.
Equal to the Data Type: Nothing. (in version 2)

**Usage:**

::

   GrabSim_pb2.NUL()

SceneID
~~~~~~~

+---+---+---+--------------------------------------------------------+
| F | T | V | Description                                            |
| i | y | a |                                                        |
| e | p | l |                                                        |
| l | e | u |                                                        |
| d |   | e |                                                        |
+===+===+===+========================================================+
| v | i | ( | Scene ID. The desirable range is [0, Count-1]. Default |
| a | n | 0 | is 0                                                   |
| l | t | ) |                                                        |
| u | 3 |   |                                                        |
| e | 2 |   |                                                        |
+---+---+---+--------------------------------------------------------+

**Usage:**

::

   GrabSim_pb2.SceneID(value=0)

World
~~~~~

====== ========== ========================================
Field  Type       Description
====== ========== ========================================
scenes list/Scene All scenes in world
error  string     Partial error information from execution
====== ========== ========================================

ResetParams
~~~~~~~~~~~

====== ===== ========== ==========================================
Field  Type  Value      Description
====== ===== ========== ==========================================
scene  int32 (0)        Target scene ID
adjust bool  (False)    Set to True for init params to take effect
height float 78.5~111.5 (90.4) Table height
width  float 50~150     (107.4) Table width
====== ===== ========== ==========================================

**Usage:**

::

   GrabSim_pb2.ResetParams()

Object
~~~~~~

======== ======== ===============
Field    Type     Description
======== ======== ===============
name     string   Object name
location Location Object position
rotation Rotation Object rotation
======== ======== ===============

**Usage:**

::

   GrabSim_pb2.Object(name = "AA",type =
   "ADMilk",location = GrabSim_pb2.Location(X=30,Y=-260,Z=84))

Location
~~~~~~~~

===== ===== ============
Field Type  Description
===== ===== ============
X     float X coordinate
Y     float Y coordinate
Z     float Z coordinate
===== ===== ============

**Usage:**

::

   GrabSim_pb2.Location(X=30,Y=-260,Z=84)

Rotation
~~~~~~~~

===== ===== =========================
Field Type  Description
===== ===== =========================
angle float Rotation angle in degrees
===== ===== =========================

MakeObjects
~~~~~~~~~~~

+------+----------+------+--------------------------------------------+
| F    | Type     | V    | Description                                |
| ield |          | alue |                                            |
+======+==========+======+============================================+
| s    | int32    | (0)  | Target scene ID                            |
| cene |          |      |                                            |
+------+----------+------+--------------------------------------------+
| ap   | bool     | (Fa  | Set to append objects or clear existing    |
| pend |          | lse) | ones                                       |
+------+----------+------+--------------------------------------------+
| obj  | lis      |      | List of objects                            |
| ects | t/Object |      |                                            |
+------+----------+------+--------------------------------------------+

ObjectList.Object
~~~~~~~~~~~~~~~~~

===== ===== ===== ====================================
Field Type  Value Description
===== ===== ===== ====================================
x, y  float (0)   Object position, height at table top
type  int         Object ID
===== ===== ===== ====================================

RemoveObjects
~~~~~~~~~~~~~

========= ========== ===== =================================
Field     Type       Value Description
========= ========== ===== =================================
scene     int32      (0)   Target scene ID
objectIDs list/int32       Index of objects in Scene.Objects
========= ========== ===== =================================

Action
~~~~~~

+---+----+------------------------------+------------------------------+
| F | Ty | Value                        | Description                  |
| i | pe |                              |                              |
| e |    |                              |                              |
| l |    |                              |                              |
| d |    |                              |                              |
+===+====+==============================+==============================+
| s | i  | (0)                          | Target scene ID              |
| c | nt |                              |                              |
| e | 32 |                              |                              |
| n |    |                              |                              |
| e |    |                              |                              |
+---+----+------------------------------+------------------------------+
| a | en | Gr                           | WalkTo: Adjust robot         |
| c | um | abSim_pb2.Action.ActionType. | position, 5 params Grasp:    |
| t |    | WalkToGrabSim_pb2.Action.Act | Control grasping, left/right |
| i |    | ionType.GraspGrabSim_pb2.Act | hand in valuesRelease:       |
| o |    | ion.ActionType.ReleaseGrabSi | Control releasing,           |
| n |    | m_pb2.Action.ActionType.Move | left/right hand in values    |
|   |    |                              | Move: Control joint angles,  |
|   |    |                              | 21 params in values          |
+---+----+------------------------------+------------------------------+
| v | li | ([0, …])                     | WalkTo: Adjust robot         |
| a | st |                              | position, 5 paramsX, Y, Yaw, |
| l | /f |                              | 0/-1/1, distance: If it      |
| u | lo |                              | cannot be reached, it will   |
| e | at |                              | find the target within 10    |
| s |    |                              | cm# 0: Query only, not move  |
|   |    |                              | # -1: Teleport to target     |
|   |    |                              | position # 1: Navigation to  |
|   |    |                              | target position              |
+---+----+------------------------------+------------------------------+

**Usage:**

::

   GrabSim_pb2.Action(
               scene=0,
               action=GrabSim_pb2.Action.ActionType.WalkTo,
               values=[location[0], location[1], location[2], -1, 10]
           )

Joint
~~~~~

======== ======== ==============
Field    Type     Description
======== ======== ==============
name     string   Joint name
location Location Joint position
rotation Rotation Joint rotation
======== ======== ==============

**Usage:**

.. code:: python

   GrabSim_pb2.Joint(name="joint1", location=Location(1.0, 2.0, 3.0), rotation=Rotation(45))

Scene
~~~~~

+------+-------------+-------------------------------------------------+
| F    | Type        | Description                                     |
| ield |             |                                                 |
+======+=============+=================================================+
| sce  | int32       | Scene ID                                        |
| neID |             |                                                 |
+------+-------------+-------------------------------------------------+
| loca | Location    | Robot coordinates (center of workspace, Scene   |
| tion |             | coordinate system)                              |
+------+-------------+-------------------------------------------------+
| rota | Rotation    | Robot rotation angles                           |
| tion |             |                                                 |
+------+-------------+-------------------------------------------------+
| jo   | list/       | Pose information for robot joints               |
| ints | Scene.Joint |                                                 |
+------+-------------+-------------------------------------------------+
| fin  | list/S      | Pose information for robot finger joints        |
| gers | cene.Finger |                                                 |
+------+-------------+-------------------------------------------------+
| obj  | list/S      | Position and info of all objects in scene.      |
| ects | cene.Object | First object is table, last few are hands with  |
|      |             | no position info                                |
+------+-------------+-------------------------------------------------+
| t    | int64       | Nanoseconds since 1970/1/1                      |
| imes |             |                                                 |
| tamp |             |                                                 |
+------+-------------+-------------------------------------------------+
| e    | string      | Partial error information from execution        |
| rror |             |                                                 |
+------+-------------+-------------------------------------------------+

**Usage:**

::

   import numpy as np
   p_x, p_y = scene.location.X, scene.location.Y
   yaw = scene.rotation.Yaw * np.pi / 180

::

   scene = stub.Observe(GrabSim_pb2.SceneID(value=0))
   print('------------------show_env_info----------------------')
   print(
       f"location:{[scene.location.X, scene.location.Y]}, rotation:{scene.rotation.Yaw}\n",
       f"joints number:{len(scene.joints)}, fingers number:{len(scene.fingers)}\n", f"objects number: {len(scene.objects)}\n"
       f"rotation:{scene.rotation}, timestep:{scene.timestep}\n"
       f"timestamp:{scene.timestamp}, collision:{scene.collision}, info:{scene.info}")

Scene.Joint
~~~~~~~~~~~

======== ======== ==============
Field    Type     Description
======== ======== ==============
name     string   Joint name
location Location Joint position
angle    float    Joint angle
======== ======== ==============

Scene.Finger
~~~~~~~~~~~~

======== ============= ====================================
Field    Type          Description
======== ============= ====================================
name     string        Finger name
location list/Location Position of each joint of the finger
angle    float         Joint angle
======== ============= ====================================

Scene.Object
~~~~~~~~~~~~

======== ======================== =================================
Field    Type                     Description
======== ======================== =================================
name     string                   Object name
location Location                 Object position
rotation Rotation                 Object rotation angle (-180, 180)
boxes    list/Object.Box.Diagonal Bounding boxes of object
======== ======================== =================================

**Usage:**

::

   GrabSim_pb2.Scene.Object([{'name': 'NFCJuice', 'location': [2525, 2510], 'angle': [0, 0, 0]}])

Pose
~~~~

========= ======================== ========================
Field     Type                     Description
========= ======================== ========================
timestamp int64                    Timestamp in nanoseconds
joints    list\ `Joint <#joint>`__ Robot joint poses
========= ======================== ========================

**Usage:**

::

   GrabSim_pb2.Pose(X=loc[0], Y=loc[1], Yaw=180)

.. _makeobjects-1:

MakeObjects
~~~~~~~~~~~

+------+----------+------+--------------------------------------------+
| F    | Type     | V    | Description                                |
| ield |          | alue |                                            |
+======+==========+======+============================================+
| s    | int32    | (0)  | Target scene ID                            |
| cene |          |      |                                            |
+------+----------+------+--------------------------------------------+
| ap   | bool     | (Fa  | Set to append objects or clear existing    |
| pend |          | lse) | ones                                       |
+------+----------+------+--------------------------------------------+
| obj  | lis      |      | List of objects                            |
| ects | t/Object |      |                                            |
+------+----------+------+--------------------------------------------+

.. _objectlist.object-1:

ObjectList.Object
~~~~~~~~~~~~~~~~~

===== ===== ===== ====================================
Field Type  Value Description
===== ===== ===== ====================================
x, y  float (0)   Object position, height at table top
type  int         Object ID
===== ===== ===== ====================================

.. _removeobjects-1:

RemoveObjects
~~~~~~~~~~~~~

========= ========== ===== =================================
Field     Type       Value Description
========= ========== ===== =================================
sceneID   int32      (0)   Target scene ID
objectIDs list/int32       Index of objects in Scene.Objects
========= ========== ===== =================================

Move
~~~~

======== ===== ======================================================
Field    Type  Description
======== ===== ======================================================
x        float Robot x coordinate
y        float Robot y coordinate
angle    float Robot current angle
speed    float Robot moving speed in facing direction, cm/s
rotating float Robot rotation speed, degrees/s, positive is clockwise
======== ===== ======================================================

CameraList
~~~~~~~~~~

======= ========= ===============
Field   Type      Description
======= ========= ===============
sceneID int32     Target scene ID
cameras list/enum CameraName
======= ========= ===============

CameraName:

-  Head_Color: Head RGB camera

-  Head_Depth: Head depth camera

-  Head_Segment: Head Segment camera

-  Chest_Color: Chest RGB camera

-  Waist_Color: Waist RGB camera

-  Waist_Depth: Waist depth camera

   **Usage:**

::

   GrabSim_pb2.CameraList(scene=0, cameras=[
       GrabSim_pb2.CameraName.Head_Depth, GrabSim_pb2.CameraName.Head_Color,
       GrabSim_pb2.CameraName.Head_Segment
   ])

CameraData
~~~~~~~~~~

========= ===================== ==========================
Field     Type                  Description
========= ===================== ==========================
images    list/CameraData.Image Image data
timestamp int64                 Nanoseconds since 1970/1/1
========= ===================== ==========================

CameraData.Image
~~~~~~~~~~~~~~~~

========== =========================== =================================
Field      Type                        Description
========== =========================== =================================
name       string                      Camera name
data       bytes                       Byte array
dtype      string                      Data format (uint8, float16, etc)
location   Location                    Camera position
rotation   Rotation                    Camera rotation angles
width      int                         Image width
height     int                         Image height
channels   int                         Number of channels
parameters CamaraData.Image.Parameters Camera intrinsics
========== =========================== =================================

CameraData.Image.Parameters
~~~~~~~~~~~~~~~~~~~~~~~~~~~

+----+---------+-------------------------------------------------------+
| F  | Type    | Description                                           |
| ie |         |                                                       |
| ld |         |                                                       |
+====+=========+=======================================================+
| fx | float   |                                                       |
+----+---------+-------------------------------------------------------+
| fy | float   |                                                       |
+----+---------+-------------------------------------------------------+
| cx | float   |                                                       |
+----+---------+-------------------------------------------------------+
| cy | float   |                                                       |
+----+---------+-------------------------------------------------------+
| ma | arra    | Transform matrix from camera to robot coordinates     |
| tr | y/float | (4x4, flattened)                                      |
| ix |         |                                                       |
+----+---------+-------------------------------------------------------+

WalkerList
~~~~~~~~~~

======= ========= =====================================================
Field   Type      Description
======= ========= =====================================================
walkers list/enum walker_list can be appended through WalkerList.Walker
scene   int32     Target scene ID
======= ========= =====================================================

**Usage:**

::

   walker_list.append(GrabSim_pb2.WalkerList.Walker(id=i, pose=GrabSim_pb2.Pose(X=loc[0], Y=loc[1], Yaw=90)))
   GrabSim_pb2.WalkerList(walkers=walker_list, scene=0)

WalkerList.Walker
~~~~~~~~~~~~~~~~~

===== ================ ===============================================
Field Type             Description
===== ================ ===============================================
id    int32            The serial number of the walker to join. From 0
pose  GrabSim_pb2.Pose X, Y, Yaw
===== ================ ===============================================

**Usage:**

::

   GrabSim_pb2.WalkerList.Walker(id=i, pose=GrabSim_pb2.Pose(X=loc[0], Y=loc[1], Yaw=90))

WalkerControls
~~~~~~~~~~~~~~

+------+-------+-------------------------------------------------------+
| F    | Type  | Description                                           |
| ield |       |                                                       |
+======+=======+=======================================================+
| cont | list  | controls_list can be appended through                 |
| rols | /enum | WalkerControls.WControl                               |
+------+-------+-------------------------------------------------------+
| s    | int32 | Target scene ID                                       |
| cene |       |                                                       |
+------+-------+-------------------------------------------------------+

**Usage:**

::

   controls.append(GrabSim_pb2.WalkerControls.WControl(id=i, autowalk=is_autowalk, speed=200, pose=pose))
   GrabSim_pb2.WalkerControls(controls=controls, scene=0)

WalkerControls.WControl
~~~~~~~~~~~~~~~~~~~~~~~

======== ================ ==================================
Field    Type             Description
======== ================ ==================================
id       int32            The joined walker’s serial number.
autowalk str              Usually set to is_autowalk.
speed    int32            The speed to move. (cm/s)
pose     GrabSim_pb2.Pose X, Y, Yaw
======== ================ ==================================

**Usage:**

::

   GrabSim_pb2.WalkerControls.WControl(id=i, autowalk=is_autowalk, speed=200, pose=pose)

RemoveList
~~~~~~~~~~

===== ========= ==================================
Field Type      Description
===== ========= ==================================
IDs   list/enum The joined walker’s serial number.
scene int32     Target scene ID
===== ========= ==================================

**Usage:**

::

   GrabSim_pb2.RemoveList(IDs=[1, 3], scene=scene_id)

Interface Methods
-----------------

First you need to do
~~~~~~~~~~~~~~~~~~~~

Install grpc environment

::

   pip install grpcio

You need to download GrabSim_pb2_grpc.py and GrabSim_pb2.py
corresponding to the simulator version. Then import them.

.. code:: python

   import grpc
   import GrabSim_pb2_grpc
   import GrabSim_pb2

Define the channel
~~~~~~~~~~~~~~~~~~

Define information such as ports to communicate with the simulator. If
you run the simulator on other machines, please change localhost to the
IP address of the machine, and make sure your machine can access port
30001.

.. code:: python

   channel = grpc.insecure_channel('localhost:30001',options=[
               ('grpc.max_send_message_length', 1024*1024*1024),
               ('grpc.max_receive_message_length', 1024*1024*1024)
           ])

If you mapped the port before (e.g. mapped to port 30020), you can run
directly on the server without entering the container (but keep the
container running):

.. code:: python

   channel = grpc.insecure_channel('localhost:30020',options=[
               ('grpc.max_send_message_length', 1024*1024*1024),
               ('grpc.max_receive_message_length', 1024*1024*1024)
           ])

Create a client
~~~~~~~~~~~~~~~

Create a client。

**Request**: Channel

**Response**: Scene - initial scene state

**Usage:**

.. code:: python

   stub = GrabSim_pb2_grpc.GrabSimStub(channel)

Init
~~~~

Initialize the simulation environment. ( In version 1)

**Request**: Channel

**Response**: Scene - initial scene state

**Usage:**

.. code:: python

   initworld = stub.Init(GrabSim_pb2.Count(value=1))
   scene = initworld.scenes[0]

SetWorld
~~~~~~~~

Initialize the simulation environment. (In version 2)

**Request**: count - number of scenes to initialize.

​Meaning of mapID:

​3 : Coffee

​4 : Restaurant

​5 : Nurse home

**Response**: Scene - initial scene state

**Usage:**

.. code:: python

   initworld = stub.SetWorld(GrabSim_pb2.BatchMap(count=1, mapID=4))
   scene = initworld.scenes[0]

your can get the object type in the scenes

::

   obj_list = set()
   area_list = set()
   for i in range(len(scene.objects)):
       object = scene.objects[i]
       name = object.name
       if 'Room' in name:
           obj_list.add(name)
       else:
           area_list.add(name)
   print(obj_list)
   print(area_list)

Reset
~~~~~

Reset a scene.

**Request**: ResetParams

**Response**: Scene - reset scene state

**Usage:**

.. code:: python

   reset_response = stub.Reset(GrabSim_pb2.ResetParams(scene=0))

Observe
~~~~~~~

Get object and robot poses in the scene.

**Request**: SceneID

**Response**: Scene

**Usage:**

.. code:: python

   scene = stub.Observe(GrabSim_pb2.SceneID(value=0))
   objects = scene.objects

ObservePose
~~~~~~~~~~~

Get the position and angle of each joint of the robot

**Request**: SceneID

**Response**: Scene

**Usage:**

::

   pose = stub.ObservePose(GrabSim_pb2.SceneID(value=0))

Capture
~~~~~~~

There are 3 cameras on the head of the robot in the simulator, which are
depth and segmentation cameras. You can also specify the cameras of
other parts of the robot (eg. Chest_Color/Waist_Color/Waist_Depth). See
the Data Types section/CamerList for details.

**Request**: SceneID

**Response**: Scene

**Usage:**

::

   images = stub.Capture(GrabSim_pb2.CameraList(sceneID=0, cameras=[
       GrabSim_pb2.CameraName.Head_Depth, GrabSim_pb2.CameraName.Head_Color,
       GrabSim_pb2.CameraName.Head_Segment
   ])).images

   depth = np.frombuffer(images[0].data, dtype=images[0].dtype).reshape(
       (images[0].height, images[0].width, images[0].channels))
   rgb = np.frombuffer(images[1].data, dtype=images[1].dtype).reshape(
       (images[1].height, images[1].width, images[1].channels))
   # convert to BGR format
   rgb = rgb[:, :, [2, 1, 0]]

Do
~~

Execute an action in the scene. Support WalkTo, Grasp, Release, Move
actions. See the Data Types section/Action for details.

Flexible angle and can walk to any reachable area. In VLN tasks, when
executing the predicted action, you can customize the rotation angle and
displacement distance corresponding to each action.

**Request**: Action

**Response**: Scene - updated scene state

**Usage:**

Navigate to (-650.0, -1550.0, -15.0), if you can’t reach it, you will
find the target within 10 cm

.. code:: python

   Scene = stub.Do(GrabSim_pb2.Action(
       scene=0,
       action = GrabSim_pb2.Action.ActionType.WalkTo,
       values = [ -650.0, -1550.0,-15.0,-1,10]
   ))

GenerateObject
~~~~~~~~~~~~~~

Generate an object in the scene. You can add object in the specified
position you need.

**Request**: ObjectList

**Response**: Scene - updated scene with object

**Usage:**

.. code:: python

   obj_list = [GrabSim_pb2.ObjectList.Object(X=25, Y=2, Yaw=15, Z=100, type=0)]
   scene = stub.MakeObjects(GrabSim_pb2.ObjectList(objects=obj_list, scene=4))

Create an item of type “ADMilk” at the coordinates (X=30, Y=-260, Z=84)

.. code:: python

   scene = stub.GenerateObject(GrabSim_pb2.Object(name = "AA",type =
   6,location = GrabSim_pb2_pb2.Location(X=30,Y=-260,Z=84)))

AcquireWalkers
~~~~~~~~~~~~~~

Query the model category of all walkers.

**Request**: Nothing

**Response**: model category of all walkers.

**Usage:**

::

   print(stub.AcquireWalkers(GrabSim_pb2.NUL()))

All walker model categories to the appendix for details. We provide 52
categories of walker models with different appearances.

AddWalker
~~~~~~~~~

Add walkers to the scene.

**Request**: WalkerList - list of walkers to add

**Response**: Scene - updated scene with walkers

**Usage:**

.. code:: python

   updated_scene = stub.AddWalker(GrabSim_pb2.WalkerList(walkers=[walker1, walker2]))

Add 4 walkers at specified positions to the scene and update the scene:

.. code:: python

   scene = stub.Observe(GrabSim_pb2.SceneID(value=0))

   walker_loc = [[120, -500], [-35, -385], [115, -360], [50,-392]]
   walker_list = []
   for i in range(len(walker_loc)):
       loc = walker_loc[i]
       action = GrabSim_pb2.Action(scene=0, action=GrabSim_pb2.Action.ActionType.WalkTo, 
                                   values=[loc[0], loc[1], 0, 0, 0])
       scene = sim_client.Do(action)
       print(scene.info)
       walker_list.append(GrabSim_pb2.WalkerList.Walker(id=i, pose=GrabSim_pb2.Pose(X=loc[0], Y=loc[1], Yaw=90)))
   scene = stub.AddWalker(GrabSim_pb2.WalkerList(walkers=walker_list, scene=0))

ControlWalkers
~~~~~~~~~~~~~~

Control walker movements.

**Request**: WalkerControls - controls for each walker

**Response**: Scene - updated scene with walker positions

**Usage:**

.. code:: python

   updated_scene = stub.ControlWalkers(GrabSim_pb2.WalkerControls(controls=[control1, control2]))

Designate 4 walkers to go to the designated location respectively, using
ControlWalkers will formulate the route and move according to the
designated pose and speed.

.. code:: python

   scene = stub.Observe(GrabSim_pb2.SceneID(value=0))

   walker_loc = [[95, 140], [93, -356], [123, 400], [97,-381]]
   controls = []
   for i in range(len(scene.walkers)):
       loc = walker_loc[i]
       is_autowalk = True
       pose = GrabSim_pb2.Pose(X=loc[0], Y=loc[1], Yaw=180)
       controls.append(GrabSim_pb2.WalkerControls.WControl(id=i, autowalk=is_autowalk, speed=200, pose=pose))
   scene = stub.ControlWalkers(GrabSim_pb2.WalkerControls(controls=controls, scene=0))

RemoveWalkers
~~~~~~~~~~~~~

Remove walkers from the scene.

**Request**: RemoveList - IDs of walkers to remove

**Response**: Scene - updated scene without removed walkers / delete
specific walkers.

**Usage:**

.. code:: python

   updated_scene = stub.RemoveWalkers(GrabSim_pb2.RemoveList(walker_ids=[1, 2]))

Remove the walker and update the scene:

::

   scene = stub.Observe(GrabSim_pb2.SceneID(value=scene_id))
   # print(scene.walkers)
   scene = stub.RemoveWalkers(GrabSim_pb2.RemoveList(IDs=[1, 3], scene=scene_id))

CleanWalkers
~~~~~~~~~~~~

Remove all walkers from the scene.

**Request**: SceneID

**Response**: Scene - updated scene without walkers / delete all
walkers.

**Usage:**

.. code:: python

   update_scene = stub.CleanWalkers(GrabSim_pb2.SceneID(value=0)) 

BindCommand
~~~~~~~~~~~

Receive commands from the grpc server through BindCommand binding, such
as clicking the record button in the VR scene, you will receive the
“record” command

**Request**: SceneID

**Response**: Nothing

**Usage:**

.. code:: python

   for cmd in stub.BindCommand(GrabSim_pb2.SceneID(value=0)):
   	print(cmd)

demo
----

easy beginning for Vision and Language navigation
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. code:: python

   from google.protobuf import message
   import grpc
   import numpy as np
   import cv2
   import matplotlib.pyplot as plt

   import GrabSim_pb2_grpc
   import GrabSim_pb2

   channel = grpc.insecure_channel('localhost:30001',options=[
               ('grpc.max_send_message_length', 1024*1024*1024),
               ('grpc.max_receive_message_length', 1024*1024*1024)
           ])  # define the channel

   stub = GrabSim_pb2_grpc.GrabSimStub(channel) # create a client
   initworld = stub.SetWorld(GrabSim_pb2.BatchMap(count=4, mapID=3)) # init the world and creat 4 scenes(Caffe)

   for i in range(len(initworld.scenes)):
       scene=initworld.scenes[i] # choose the world's scenes[i] and print agent's location in the scene
       print("scene %d, ginger location (%d,%d) direction %d"%(i,scene.location.X,scene.location.Y,scene.rotation.Yaw))
       for j in range(len(initworld.scenes[i].objects)): # get the object and location in the scene
           object = scene.objects[j]
           print("scene %d, object %d: name %s, location (%d,%d,%d)"%(i,j,object.name,object.location.X,object.location.Y,object.location.Z))
           
       scene = stub.Do(GrabSim_pb2.Action(scene=i, action=GrabSim_pb2.Action.WalkTo, values=[-500, 100, 90, -1, 10])) 
       # the agent naviagtion to (-500, 100, 90) and update scene.
       print("scene %d, ginger moved to location %d,%d direction %d"%(i,scene.location.X,scene.location.Y,scene.rotation.Yaw))
       
   message = stub.Capture(GrabSim_pb2.CameraList(scene=0, cameras=[GrabSim_pb2.CameraName.Head_Depth, GrabSim_pb2.CameraName.Head_Color])) # use the carmer in agent's head to get observation(depth, RGB, segmentation)
   images = message.images

   depth = np.frombuffer(images[0].data, dtype=images[0].dtype).reshape(
       (images[0].height, images[0].width, images[0].channels))
   rgb = np.frombuffer(images[1].data, dtype=images[1].dtype).reshape(
       (images[1].height, images[1].width, images[1].channels))
   seg = np.frombuffer(images[0].data, dtype=images[0].dtype).reshape(
       (images[0].height, images[0].width, images[0].channels))
   items = message.info.split(';')
   seg_object_names = {}
   	for item in items:
           key, value = item.split(':')
           seg_object_names[int(key)] = value
   # convert to BGR format
   rgb = rgb[:, :, [2, 1, 0]]
     
   scene = stub.Reset(GrabSim_pb2.ResetParams(scene=0)) # reset scene[0] in the world

add objects in simulator
~~~~~~~~~~~~~~~~~~~~~~~~

.. code:: python

   import grpc
   import GrabSim_pb2_grpc
   import GrabSim_pb2

   channel = grpc.insecure_channel('localhost:30001',options=[
               ('grpc.max_send_message_length', 1024*1024*1024),
               ('grpc.max_receive_message_length', 1024*1024*1024)
           ])  # define the channel

   stub = GrabSim_pb2_grpc.GrabSimStub(channel) # create a client
   initworld = stub.SetWorld(GrabSim_pb2.BatchMap(count=1, mapID=3)) # set world
   scene = initworld.scenes[0] # choose scene

   obj_list = [GrabSim_pb2.ObjectList.Object(X=25, Y=2, Yaw=15, Z=100, type="Mug")]
   scene = stub.MakeObjects(GrabSim_pb2.ObjectList(objects=obj_list, scene=0))
   objects = scene.objects

add walkers in simulator
~~~~~~~~~~~~~~~~~~~~~~~~

.. code:: python

   import grpc
   import GrabSim_pb2_grpc
   import GrabSim_pb2

   channel = grpc.insecure_channel('localhost:30001',options=[
               ('grpc.max_send_message_length', 1024*1024*1024),
               ('grpc.max_receive_message_length', 1024*1024*1024)
           ])  # define the channel

   stub = GrabSim_pb2_grpc.GrabSimStub(channel) # create a client
   initworld = stub.SetWorld(GrabSim_pb2.BatchMap(count=1, mapID=3)) # set world
   scene = stub.Observe(GrabSim_pb2.SceneID(value=0))

   # acquire walkers
   print(stub.AcquireWalkers(GrabSim_pb2.NUL()))

   # add walkers
   walker_loc = [[120, -500], [-35, -385], [115, -360], [50,-392]]
   walker_list = []
   for i in range(len(walker_loc)):
       loc = walker_loc[i]
       action = GrabSim_pb2.Action(scene=0, action=GrabSim_pb2.Action.ActionType.WalkTo, 
                                   values=[loc[0], loc[1], 0, 0, 0])
       scene = sim_client.Do(action)
       print(scene.info)
       walker_list.append(GrabSim_pb2.WalkerList.Walker(id=i, pose=GrabSim_pb2.Pose(X=loc[0], Y=loc[1], Yaw=90)))
   scene = stub.AddWalker(GrabSim_pb2.WalkerList(walkers=walker_list, scene=0))

   # control walkers
   walker_loc = [[95, 140], [93, -356], [123, 400], [97,-381]]
   controls = []
   for i in range(len(scene.walkers)):
       loc = walker_loc[i]
       is_autowalk = True
       pose = GrabSim_pb2.Pose(X=loc[0], Y=loc[1], Yaw=180)
       controls.append(GrabSim_pb2.WalkerControls.WControl(id=i, autowalk=is_autowalk, speed=200, pose=pose))
   scene = stub.ControlWalkers(GrabSim_pb2.WalkerControls(controls=controls, scene=0))

   # remove walkers
   print(scene.walkers)
   scene = stub.RemoveWalkers(GrabSim_pb2.RemoveList(IDs=[1, 3], scene=0))
   print(scene.walkers)

   # clean walkers
   scene = stub.CleanWalkers(GrabSim_pb2.SceneID(value=0)) 

The walker’s start location and target location must be reachable.
Otherwise the the scene.info will be unreachable. You can test whether
the target location is reachable by the following method:

::

   msg = stub.Do(GrabSim_pb2.Action(
       action = GrabSim_pb2.Action.WalkTo,
       values = [-2150,-1350,-100, 0, 10] 
       # 0: Query only, not move
       # -1: Teleport to target position
       # 1: Navigation to target position
   ))
   print(msg.info)

appendix
--------

List of scenes
~~~~~~~~~~~~~~

map id : 1 – Separate Tables
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. figure:: https://mligg23.github.io/CorNav-Site/images/banner5.png
   :alt: img

   img

map id : 2 – Indoor Scene
^^^^^^^^^^^^^^^^^^^^^^^^^

.. figure:: https://mligg23.github.io/CorNav-Site/images/banner6.png
   :alt: img

   img

map id : 3 – Coffee
^^^^^^^^^^^^^^^^^^^

.. figure:: https://mligg23.github.io/CorNav-Site/images/banner4.png
   :alt: img

   img

map id : 4 – Restaurant
^^^^^^^^^^^^^^^^^^^^^^^

.. figure:: https://mligg23.github.io/CorNav-Site/images/banner2.png
   :alt: img

   img

map id : 5 – Nursing Room
^^^^^^^^^^^^^^^^^^^^^^^^^

.. figure:: https://mligg23.github.io/CorNav-Site/images/banner3.png
   :alt: img

   img

List of objects inherent to the scene
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Coffee
^^^^^^

+-----------------+
| Item            |
+=================+
| apple           |
+-----------------+
| Cake            |
+-----------------+
| Drinks          |
+-----------------+
| Glass           |
+-----------------+
| Saucer          |
+-----------------+
| Door            |
+-----------------+
| Knife           |
+-----------------+
| Machine         |
+-----------------+
| Bread           |
+-----------------+
| Mug             |
+-----------------+
| Packaged Coffee |
+-----------------+
| Spoon           |
+-----------------+
| Cube Sugar      |
+-----------------+
| Tray            |
+-----------------+
| Straw           |
+-----------------+
| Drink           |
+-----------------+
| Take-Away Cup   |
+-----------------+
| Tongs           |
+-----------------+
| Vacuum          |
+-----------------+
| Trash Bin       |
+-----------------+

Restaurant
^^^^^^^^^^

+------------------------+
| Item                   |
+========================+
| Room-Elevator          |
+------------------------+
| Room-Dining            |
+------------------------+
| Room-Bar               |
+------------------------+
| Room-Drinking          |
+------------------------+
| Room-Lobby             |
+------------------------+
| Cake                   |
+------------------------+
| Plate                  |
+------------------------+
| Drinking Machine       |
+------------------------+
| Bread                  |
+------------------------+
| Red Bull Drink         |
+------------------------+
| Alcoholic Drink        |
+------------------------+
| Kettle                 |
+------------------------+
| Fork                   |
+------------------------+
| Knife                  |
+------------------------+
| Alcoholic Drink (Bulk) |
+------------------------+
| Trolley                |
+------------------------+
| Table                  |
+------------------------+
| Chair                  |
+------------------------+
| Teapot                 |
+------------------------+
| Glass                  |
+------------------------+
| Teacup                 |
+------------------------+
| Dixie Cup              |
+------------------------+
| Tongs                  |
+------------------------+

Nursing Room
^^^^^^^^^^^^

+----------------+
| Item           |
+================+
| Monitor        |
+----------------+
| Curtain        |
+----------------+
| Knife          |
+----------------+
| Pot            |
+----------------+
| Disc           |
+----------------+
| Plants         |
+----------------+
| Elevator       |
+----------------+
| Elevator Panel |
+----------------+
| Trash Bin      |
+----------------+
| Door           |
+----------------+
| Chair          |
+----------------+
| Desk           |
+----------------+
| Chess          |
+----------------+
| WheelChair     |
+----------------+
| Gate           |
+----------------+
| Case           |
+----------------+
| Front Desk     |
+----------------+
| Closet         |
+----------------+
| Sofa           |
+----------------+
| TV             |
+----------------+
| Books          |
+----------------+
| Medicine       |
+----------------+
| Armrest        |
+----------------+
| Table          |
+----------------+
| Cloth          |
+----------------+
| Fridge         |
+----------------+
| Plant          |
+----------------+
| Teapot         |
+----------------+
| Microwave      |
+----------------+
| EmergencyKit   |
+----------------+
| Bed            |
+----------------+
| Fruit          |
+----------------+

Controllable list Walker’s model categories(total 50 categories of walkers)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

+---------+---------+----------+--------+--------+--------+--------+
| Type    |         |          |        |        |        |        |
+=========+=========+==========+========+========+========+========+
| walker: | walker: | walker:  |        |        |        |        |
| “Boy01” | “Boy02” | “Boy03”  |        |        |        |        |
+---------+---------+----------+--------+--------+--------+--------+
| walker: |         |          |        |        |        |        |
| “Boy    |         |          |        |        |        |        |
| Euro01” |         |          |        |        |        |        |
+---------+---------+----------+--------+--------+--------+--------+
| walker: | walker: | walker:  |        |        |        |        |
| “       | “       | “Girl03” |        |        |        |        |
| Girl01” | Girl02” |          |        |        |        |        |
+---------+---------+----------+--------+--------+--------+--------+
| walker: |         |          |        |        |        |        |
| “Girl   |         |          |        |        |        |        |
| Euro01” |         |          |        |        |        |        |
+---------+---------+----------+--------+--------+--------+--------+
| walker: | walker: | walker:  |        |        |        |        |
| “       | “       | “Male03” |        |        |        |        |
| Male01” | Male02” |          |        |        |        |        |
+---------+---------+----------+--------+--------+--------+--------+
| walker: | walker: |          |        |        |        |        |
| “Male   | “Male   |          |        |        |        |        |
| Afro01” | Afro02” |          |        |        |        |        |
+---------+---------+----------+--------+--------+--------+--------+
| walker: |         |          |        |        |        |        |
| “MaleAf |         |          |        |        |        |        |
| roOw01” |         |          |        |        |        |        |
+---------+---------+----------+--------+--------+--------+--------+
| walker: | walker: | walker:  | w      |        |        |        |
| “Male   | “Male   | “Mal     | alker: |        |        |        |
| Amer01” | Amer02” | eAmer03” | “MaleA |        |        |        |
|         |         |          | mer04” |        |        |        |
+---------+---------+----------+--------+--------+--------+--------+
| walker: | walker: |          |        |        |        |        |
| “Male   | “Male   |          |        |        |        |        |
| Asia01” | Asia02” |          |        |        |        |        |
+---------+---------+----------+--------+--------+--------+--------+
| walker: | walker: | walker:  |        |        |        |        |
| “MaleAs | “MaleAs | “MaleAsi |        |        |        |        |
| iaOw01” | iaOw02” | aOwOw03” |        |        |        |        |
+---------+---------+----------+--------+--------+--------+--------+
| walker: | walker: |          |        |        |        |        |
| “Male   | “Male   |          |        |        |        |        |
| Euro01” | Euro02” |          |        |        |        |        |
+---------+---------+----------+--------+--------+--------+--------+
| walker: | walker: |          |        |        |        |        |
| “MaleEu | “MaleEu |          |        |        |        |        |
| roOw01” | roOw02” |          |        |        |        |        |
+---------+---------+----------+--------+--------+--------+--------+
| walker: | walker: | walker:  | w      | walke  | walke  |        |
| “Fe     | “Fe     | “F       | alker: | r:“Fem | r:“Fem |        |
| male01” | male02” | emale03” | “Fem   | ale05” | ale06” |        |
|         |         |          | ale04” |        |        |        |
+---------+---------+----------+--------+--------+--------+--------+
| walker: | walker: | walke    | w      | w      | w      | w      |
| “Female | “Female | r:“Femal | alker: | alker: | alker: | alker: |
| Afro01” | Afro02” | eAfro03” | “F     | “F     | “F     | “F     |
|         |         |          | emaleA | emaleA | emaleA | emaleA |
|         |         |          | fro04” | fro05” | fro06” | fro07” |
+---------+---------+----------+--------+--------+--------+--------+
| walker: | walker: | walker:  |        |        |        |        |
| “F      | “F      | “FemaleA |        |        |        |        |
| emaleAf | emaleAf | froOw03” |        |        |        |        |
| roOw01” | roOw02” |          |        |        |        |        |
+---------+---------+----------+--------+--------+--------+--------+
| walker: | walker: | walker:  | w      |        |        |        |
| “Female | “Female | “Femal   | alker: |        |        |        |
| Asia01” | Asia02” | eEuro01” | “F     |        |        |        |
|         |         |          | emaleE |        |        |        |
|         |         |          | uro02” |        |        |        |
+---------+---------+----------+--------+--------+--------+--------+
| walker: | walker: | walker:  |        |        |        |        |
| “F      | “F      | “FemaleE |        |        |        |        |
| emaleEu | emaleEu | uroOw03” |        |        |        |        |
| roOw01” | roOw02” |          |        |        |        |        |
+---------+---------+----------+--------+--------+--------+--------+

List of controllable generated objects
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. figure:: https://mligg23.github.io/CorNav-Site/images/banner7.png
   :alt: img

   img

== ====================
ID Name
== ====================
0  Mug
1  Banana
2  Toothpaste
3  Bread
4  Softdrink
5  Yogurt
6  ADMilk
7  VacuumCup
8  Bernachon
9  BottledDrink
10 PencilVase
11 Teacup
12 Caddy
13 Dictionary
14 Cake
15 Date
16 Stapler
17 LunchBox
18 Bracelet
19 MilkDrink
20 CocountWater
21 Walnut
22 HamSausage
23 GlueStick
24 AdhensiveTape
25 Calculator
26 Chess
27 Orange
28 Glass
29 Washbowl
30 Durian
31 Gum
32 Towl
33 OrangeJuice
34 Cardcase
35 RubikCube
36 StickyNotes
37 NFCJuice
38 SpringWater
39 Apple
40 Coffee
41 Gauze
42 Mangosteen
43 SesameSeedCake
44 Glove
45 Mouse
46 Kettle
47 Atomize
48 Chips
49 SpongeGourd
50 Garlic
51 Potato
52 Tray
53 Hemomanometer
54 TennisBall
55 ToyDog
56 ToyBear
57 TeaTray
58 Sock
59 Scarf
60 ToiletPaper
61 Milk
62 Soap
63 Novel
64 Watermelon
65 Tomato
66 CleansingFoam
67 CocountMilk
68 SugarlessGum
69 MedicalAdhensiveTape
70 SourMilkDrink
71 PaperCup
72 Tissue
== ====================

Joint Information
~~~~~~~~~~~~~~~~~

=================== ===================
Action.values param Name
=================== ===================
0                   Knee_X_Anchorn
1                   Back_Z_Anchorn
2                   Back_X_Anchorn
3                   Back_Y_Anchorn
4                   Neck_Z_Anchorn
5                   Neck_X_Anchorn
6                   Head_Y_Anchorn
7                   LShlouder_X_Anchorn
8                   LShlouder_Y_Anchorn
9                   LElbow_Z_Anchorn
10                  LElbow_X_Anchorn
11                  LWrist_Z_Anchorn
12                  LWrist_X_Anchorn
13                  LWrist_Y_Anchorn
14                  RShlouder_X_Anchorn
15                  RShlouder_Y_Anchorn
16                  RElbow_Z_Anchorn
17                  RElbow_X_Anchorn
18                  RWrist_Z_Anchorn
19                  RWrist_X_Anchorn
20                  RWrist_Y_Anchorn
=================== ===================

.. [1]
   https://docs.unrealengine.com/5.0/en-US/quick-start-guide-for-using-container-images-in-unreal-engine/
