---
layout: post
title:  "ROS Basics"
date:   2017-02-04 13:50:39
categories: ROS
---

ROS is short for Robot Operating System(of course, you already knew that). It isn’t an actual operating system as such. You can’t think of it as a replacement for a Linux distro or macOS or windows but it comes pretty close.

**ROS is a set of tools, libraries and interfaces which help running and managing the software typically used to run a robot.** 

There is a lot of documentation about ROS, the what and the hows. But, as a beginner, I found it difficult to understand the why. I’ve tried to summarize the main features here to illustrate why ROS is so useful for complex robot software.

All the content here mostly focuses on the Pre-ROS 2.0 and ROS-HW releases. As of writing, these are in early stages so we’ll focus on things that are stable right now.

## Why ROS?

These are the cool(and useful) things that you can do with ROS

1. Separate different parts of your code into different modules, manage their builds and dependencies and develop them separately. [ROS packages and Catkin]
2. Launch multiple modules whenever you want to (No need to write your own custom bash script anymore) [Roslaunch]
3. Have a central store for shared parameters and files, accessible to all your components (no need to implement a custom config file for those parameters) [Rosparam server and Yaml config files]
4. Transfer data between nodes through a simple abstracted solution. Forget worrying about  IPC and sharing data pipes, shared memory etc, ROS has a built in system of topics which help share data across nodes.
5. Ability to have distributed nodes - Since the core topics functionality has TCP under the hood, you can have nodes run on two different systems and still have them talk to each other! 
6. Basic mechanisms for running common one-time tasks(services) or repetitive tasks(actions) which are somewhat independent from your code. Like moving a motor, or retreiving data from a sensor or web service.
7. Last but definitely not the least is that ROS includes a vast range of libraries for standard tasks. Most tasks related to Planning, Perception, Visualisation can already be done by existing ROS libraries. Including Robotic Arms, Diff drive wheeled robots(ROS-Navigation), Environment mapping using RGBD cameras and many more.
8. Actively developed with a huge community of talented developers and amazing organisations.

Of course, this list is not exhaustive since ROS is evolving day by day and many new features are being added. But yes, these are the most fundamental features that would benefit most users.

So, looking at the big picture, most robots have at least 2 or 3 different types of tasks. These tasks are mostly independent from each other, except for small data exchanges. Managing the code for all these separate tasks, testing them independently, integrating them and getting them to run properly on the robot are very cumbersome tasks. 
More often than not, lot of time is spent in the integration of these modules, which can be avoided if ROS is used.

Let’s start with the basics of ROS. You can get a lot of this information on the Official ROS tutorials as well. But they can get a bit complicated for a beginner, with this article I aim to prepare you so that you can go through the tutorials faster and comfortably.


## ROS Basics

 
Every program in ROS is called a node. Every independent task can be separated into nodes  which communicate with each other through channels. These channels are also known as topics.

Once you write a program, you need to ensure its compilation rules are set propery and the dependencies are satisfied. This build and dependency management is simplified in ROS using a tool called `catkin`. It is built over `CMake` and works like `make`.

Every program in ROS needs to be part of a Workspace.


    A workspace is a set of directories which contain related ROS nodes.
    You can have multiple workspaces but you can use(run code, compile stuff, etc) only one workspace at a time.
    The simple way to think about this is that in your nodes, you can only see nodes, topics and other ROS stuff, that are in your current workspace. So if you are writing a program which has 3 nodes communicating with each other, you need to have them in one workspace. 

Each workspace further contains packages. Packages are isolated blocks of code containing one or more nodes. Packages help with easy file referencing using programs like `roscd` .

### Common ROS tools
 
`rosrun` is used to run a single ros program(node), but most of the times, a robot contains multiple nodes, hence roslaunch is used(discussed later)

`roscore` - The Main program to run initiate ros. It sets up the basic architecture for the channels, allowing nodes to communicate. We need to run roscore every time before starting any ROS node.

Since ROS programs(nodes) communicate with each other through predefined channels, it is difficult to visualize the current channels and the messages. Hence there are tools like `rostopic` and `rqt_graph` which make this visualization easier.

Every channel can have any number of broadcasters and receivers, hence it is trivial to make simple loggers for any ROS channel.

Namespaces and Remapping are used to handle nodes which are similar in features but different in function(eg two cameras or two arms in a robot)

Following the convention of Unix paths and Internet URIs, ROS uses the forward slash (/) to delimit namespaces. a robot with two cameras could launch two camera drivers in separate namespaces, such as left and right , which would result in image streams named left/image and right/image.

Pushing a node into a namespace can be accomplished with a special `__ns` namespace-remapping syntax (note the double underscore). For example, if the working directory contains the camera program, the following shell command would launch camera into the namespace right:

    user@hostname$ ./camera __ns:=right` 

Remapping is used on the receiver end to listen to channels on different namespaces. In ROS, any string in a program that defines a name can be remapped at runtime.
For example, if the working directory contains the image_view program, one could type the following to map image to right/image:

    user@hostname$ ./image_view image:=right/image

Just like filesystems, web URLs, and countless other domains, ROS names must be unique. If the same node is launched twice, roscore directs the older node to exit to make way for the newer instance of the node.

Roslaunch

`roslaunch`  is used to automate launching multiple nodes at once files have a suffix of .launch 
It also closes all nodes when `Ctrl-C` is pressed in the console where it was launched.
It automatically starts `roscore`, if not previously started.


### The `tf` package

One problem that might not be immediately obvious, but is extremely important, is the management of coordinate frames. Seriously, coordinate frames are a big deal in robotics.
Let’s establish some terminology. In our 3D world, a position is a vector of three numbers (x, y, z) that describe how far we have translated along each axis, with respect to some origin. Similarly, an orientation is a vector of three numbers (roll, pitch, yaw)that describe how far we have rotated about each axis, again with respect to some origin. Taken together, a (position, orientation) pair is called a pose. For clarity, this kind of pose, which varies in six dimensions (three for translation plus three for rotation) is sometimes called a 6D pose. Given the pose of one thing relative to another, we can transform data between their frames of reference, a process that usually involves some matrix multiplications.

Any node can be the authority that publishes the current information for some transform(s), and any node can subscribe to transform data, gathering from all the various authorities a complete picture of the robot. This system is implemented in the tf (short for transform) package, which is extremely widely used throughout ROS software.

As is often the case for a powerful system, tf is relatively complex, and there are a variety of ways in which things can go wrong. Consequently, there a number of tf-specific introspection and debugging tools to help you understand what’s happening, from printing a single transform on the console to rendering a graphical view of the entire transform hierarchy. This is just a basic summary of `tf` . Check the docs for a detailed description.

----------
### ROS Topics

Topics implement a publish/subscribe communication mechanism, one of the more common ways to exchange data in a distributed system. Before nodes start to transmit data over topics, they must first announce, or advertise, both the topic name and the types of messages that are going to be sent. Then they can start to send, or publish, the actual data on the topic. Nodes that want to receive messages on a topic can subscribe to that topic by making a request to roscore. After subscribing, all messages on the topic are delivered to the node that made the request. One of the main advantages to using ROS is that all the messy details of setting up the necessary connections when nodes advertise or subscribe to topics is handled for you by the underlying communication mechanism so that you don’t have to worry about it yourself
In ROS, all messages on the same topic must be of the same data type. Although ROS does not enforce it, topic names often describe the messages that are sent over them. For example, on the PR2 robot, the topic /wide_stereo/right/image_color is used for color images from the rightmost camera of the wide-angle stereo pair.

Publishing to a Topic
To send data on a topic, you have to be a publisher. It involves "announcing" that you will publish on a topic and mention the Data type that will be published.
Example in python

    #!/usr/bin/env python
    
    import rospy
    from std_msgs.msg import Int32
    
    rospy.init_node('topic_publisher')
    
     # announcing that the topic name is "counter" and data type is Int32
    pub = rospy.Publisher('counter', Int32)
    
    rate = rospy.Rate(2) # defining the frequency of messages in Hz (msgs per sec)
    
    count = 0
    while not rospy.is_shutdown():
        pub.publish(count) # Sending the actual message
        count += 1
        rate.sleep() # uses previously set value to determine delay

Also, remember since topic names are unique, if you announce a topic who's name already exists, it get overwritten.(Check docs)

You can use `rostopic` to see the status. `rostopic list` shows all channels currently online. `rostopic echo topicname` will show the messages on the topic called topicname. Adding the `-n <num_msgs>` flag will limit the number of messages to `<num_msgs>` 

Subscribing to a topic
For listening to a topic, ROS provides an asynchronous way to listen to the messages.
Example here,

    #!/usr/bin/env python
    
    import rospy
    from std_msgs.msg import Int32
    
    def callback(msg): # callback which is called everytime there is a message
        print msg.data
    
    rospy.init_node('topic_subscriber')
    
    # Send the subscribe info to roscore, if channel doesnt exist, it simply waits till a message comes on this channel
    sub = rospy.Subscriber('counter', Int32, callback) 
    
    rospy.spin() # give back control to ROS

Latched Topics
By default, you receive a message on a topic only when it is published. So if a subscriber subscribes on a channel after the message is published, it misses the message.
Latched topics ensure that every new subscriber receives the last broadcast message on the topic, when they subscribe. This is how a topic is marked "latched"

    pub = rospy.Publisher('map', nav_msgs/OccupancyGrid, latched=True) 

Custom Message Types

ROS messages are defined by special message-definition files in the `msg` directory of a package.
These files are then compiled into language-specific implementations that can be used in your code. So, even if you’re using an interpreted language such as Python, you need to run `catkin_make` if you’re going to define your own message types. Otherwise, the language-specific implementation will not be generated, and Python will not be able to find your new message type. Furthermore, if you don’t rerun catkin_make after you change the message definition, Python will still be using the older version of the message type.

Message-definition files are typically quite simple and short. Each line specifies a type and a field name. Types can be built-in ROS primitive types, message types from other packages, arrays of types (either primitive or from other packages, and either fixed or variable length), or the special Header type
For example,
Message definition file for a new type `Complex` 

    Complex.msg 
    float32 real
    float32 imaginary

Once the message is defined, we need to run `catkin_make` to generate the language-specific code that will let us use it. This code includes a definition of the type, and code to marshal and unmarshal it for transmission down a topic. This allows us to use the message in all of the languages that ROS supports; nodes written in one language can subscribe to topics from nodes written in another. Moreover, it allows us to use messages to communicate seamlessly between computers with different architectures.

Services

Services are another way to pass data between nodes in ROS. Services are just synchronous remote procedure calls; they allow one node to call a function that executes in another node.
Service calls are well suited to things that you only need to do occasionally and that take a bounded amount of time to complete.

How to

The first step in creating a new service is to define the service call inputs and outputs. This is done in a service-definition file , which has a similar structure to the message-definition files we’ve already seen. However, since a service call has both inputs and outputs, it’s a bit more complicated than a message.

Example

    string words
    ---
    uint32 count

The inputs to the service call come first. In this case, we’re just going to use the ROS built-in string type. Three dashes ( --- ) mark the end of the inputs and the start of
the output definition. We’re going to use a 32-bit unsigned integer ( uint32 ) for our
output. The file holding this definition is called WordCount.srv and is traditionally in
a directory called srv in the main package directory (although this is not strictly
required).
After saving this, run `catkin_make` to create the code and definitions. The `find_package()` call in CMakeLists.txt needs to include `message_generation`.
Also another update required is the addition of `add_service_files()` section.
Also the package.xml needs to be updated as well(check book)
Refer Page 52 in the ROSBook
After configuring everything as mentioned in the book, `catkin_make` will generate the classes - WordCount, WordCountRequest and WordCountResponse
Check the book for an explaination on services.

Actions

ROS actions are the best way to implement interfaces to time-extended, goal-oriented behaviors like `goto_position` .

While services are synchronous, actions are asynchronous. Similar to the request and response of a service, an action uses a goal to initiate a behavior and sends a result when the behavior is complete. But the action further uses feedback to provide updates on the behavior’s progress toward the goal and also allows for goals to be canceled.

Actions are themselves implemented internally using topics. It is essentially a higher-level protocol that specifies how a set of topics (goal, result, feedback, etc.) should be used in combination.

For example - Using an action interface to `goto_position` , you send a goal, then move on to other tasks while the robot is driving. Along the way, you receive periodic progress updates (distance traveled, estimated time to goal, etc.), culminating in a result message (did the robot make it to the goal or was it forced to give up?). And if something more important comes up, you can at any time cancel the goal and send the robot somewhere else.

Actions require only a little more effort to define and use than do services, and they provide a lot more power and flexibility.

How To

The first step in creating a new action is to define the goal, result, and feedback message formats in an action definition file, which by convention has the suffix .action. The .action file format is similar to the .srv format used to define services, just with an additional field. And, as with services, each field within an .action file will become its own message.
Just like with service-definition files, we use three dashes ( --- ) as the separator between the parts of the definition. While service definitions have two parts (request and response), action definitions have three parts (goal, result, and feedback).
This is an action definition file for simple Timer , which has three parts: the goal, the result, and the feedback.

    # Part 1: the goal, to be sent by the client
    
    # The amount of time we want to wait
    duration time_to_wait
    ---
    
    # Part 2: the result, to be sent by the server upon completion
    
    # How much time we waited
    duration time_elapsed
    
    # How many updates we provided along the way
    uint32 updates_sent
    
    ---
    
    # Part 3: the feedback, to be sent periodically by the server during
    # execution.
    
    # The amount of time that has elapsed from the start
    duration time_elapsed
    
    # The amount of time remaining until we're done
    duration time_remaining

---
Thanks to [Tarang](https://github.com/t27) who has equal contributions in this series of posts on ROS.
