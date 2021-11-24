---
title: Motion
keywords: (insert comma-separated keywords here)
order: 15 # Lecture number for 2020
---

**Lorem ipsum** dolor sit amet, consectetur adipiscing elit, sed do eiusmod
tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam,
quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo
consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum
dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident,
sunt in culpa qui officia deserunt mollit anim id est laborum.

## What is visual intelligence?

Visual intelligence describes the ability to see and understand the deeper context of what is happening. Visual intelligence is critical for planning, informing future action, and understanding high-level semantics such as goals and intents. For example, a still image of a tiger towering over a man may suggest an attack is in progress. However, a video showing the tiger gently standing over the man (never laying its paws on his body), playfully retreating, and repeating this behavior, would suggest the tiger is simply playing—humans have ability to perceive such high-level semantics from visual observation. Why is visual intelligence important? Human memories are deeply shaped by the context of events and actions; our lived experiences extend beyond simple object recognition. 

## How could machines perceive events? 

How do we build machines that can see and understand events as humans do? Many human experiences are captured on videos. Understanding how the human brain perceives an example video may offer some insight. 

Image 

Imagine we are viewing a spaghetti and meatball cooking instructional video (Figure 1). Over the temporal sequence of the video (likely a few minutes), the task of “making spaghetti and meatballs” can be subdivided into smaller components: sub-tasks (e.g. preparing the sauce), complex actions (e.g. “adding vegetables to the sauce while stirring”, down to atomic actions (e.g. “cut onions”). Events are naturally organized in such hierarchical fashion, down to component atomic actions that could be captured in a single video frame. This is the framework that can inform computational visual intelligence. 

	Computer vision has seen much success and interest at the atomic action level; from segmentation to detection and facial recognition, many algorithms can intuit static images. Standard visual recognition algorithms can output an action label from a video input, yet the models themselves are frequently black boxes that don’t understand how objects are related to one another, and the semantics of components of actions captured on the video. There is a critical need for algorithms that can interpret higher levels in the event hierarchy. 
  
Computer visual intelligence requires interpreting event structures as a function of its components, and the relationships of objects within each component. For example, for a computer to understand a video of a person sitting down on a sofa, the algorithm must not only recognize the person and the sofa, but the changing spatio-temporal relationship between the person and the sofa. To build a system that can decompose and recognize such actions, we need a new representation, database, and algorithm. We introduce the Action Genome, a representation decomposing actions into spatio-temporal scene graphs.

**Action genome**

## II. Action Genome: From Scene Graphs to Action Recognition
## 1. Intuition

Standard video recognition can be segmented as follows: an input video clip is passed through a classification network and an output action label is produced. Here, the classification network is a ‘Black box’ neural network, actions in the video are treated as monolithic entities, and time is just another dimension in the video clip. 

Figure 2.1 

Revisiting the partonomy of events introduced earlier, we can rethink the action recognition model. More specifically, the first step of recognizing and labeling atomic actions. To further understand the intuition behind this new method of action recognition, let’s analyze an example of a man sitting on a sofa. The fundamental question is what are the cues for recognizing the action being performed. In this case, those cues include spotting the person and the sofa and how the relationship between those two objects changes over time. 

That said, in order to leverage the decomposition of videos for recognizing actions, we are introduced to Action Genome, which is a representation of decomposing actions into spatio-temporal scene graphs.

## 2. Action Genome

Taking a video input, one first creates scene specific labels. In the lecture, the video input used as an example is a video of a woman sitting at a table drinking coffee while looking out a window. Taking each of our actions, we can uniformly sample five frames, resulting in the following: 

figure 2.2 

We then sample frames from the video input allowing us to annotate all objects, and how certain objects interact with each other. In the case of a woman drinking coffee, how the woman interacts with other objects such as the cup, table, chair, and window. 

figure 2.3 

We then annotate the pairwise human-object relationships, which results in the spatio-temporal scene graphs. It should be noted here that all the data annotations including the spatio-temporal scene graphs are hand labeled. 

figure 2.4 

## 3. Recap 

We are taking an input video and predicting the scene graphs of the frames in the video. Next, those scene graphs are embedded into a feature space. Simultaneously, we extract short-term clip features using 3D CNN as well. Finally, we merge the features with feature bank operators to predict actions.

figure 2.5 

**III. A new approach: Automated video captioning via bottom-up graph construction**

## 1. Intuition

In the scene graph method that we just discussed, recall that there were two major limitations: First, the method needed supervision for decomposing actions -- in other words, a large number of frames annotated with their corresponding graphs was needed to train the model.  Generating so much annotated data requires a large amount of human labor.  Second, the method can only output an action out of a pre-selected list of actions: but this doesn’t grant the flexibility of any action that doesn’t fall neatly into this pre-set list. 

In order to resolve the two issues above, we can try to rethink the action recognition task.  In particular, instead of trying to treat it as a classification problem, where we take the clip and output an action out of a fixed set of action classes -- we can instead try to generate a natural language description of the clip.  This is the task of video captioning: describing a video with language narrating what’s happening in the clip.  By framing action recognition in this way, we overcome the second issue described above: we are no longer constrained to just a limited set of action classes; with language, we can now produce much more accurate and specific action tags for the video.  Obviously, this task is also more complex and difficult than simple classification, but the flexibility it affords is enormous. 

We will now introduce a new method for action recognition based on this new paradigm.  We will still use the concept of object and interaction graphs from the method described in the previous section -- these graphs still have enormous value with the spatio-temporal information they provide.  However, instead of needing supervision for building the graphs, the intuition behind this next approach is to build the graphs in a bottom-up fashion, and translating these graphs into language descriptions.  Building graphs bottom-up overcomes the first issue described above: we no longer need tedious annotation of scene graphs in all the training images, as we can build the graph for a new image directly from the frame.






