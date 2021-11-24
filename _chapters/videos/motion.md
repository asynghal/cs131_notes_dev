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

## Action genome

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

## III. A new approach: Automated video captioning via bottom-up graph construction

## 1. Intuition

In the scene graph method that we just discussed, recall that there were two major limitations: First, the method needed supervision for decomposing actions -- in other words, a large number of frames annotated with their corresponding graphs was needed to train the model.  Generating so much annotated data requires a large amount of human labor.  Second, the method can only output an action out of a pre-selected list of actions: but this doesn’t grant the flexibility of any action that doesn’t fall neatly into this pre-set list. 

In order to resolve the two issues above, we can try to rethink the action recognition task.  In particular, instead of trying to treat it as a classification problem, where we take the clip and output an action out of a fixed set of action classes -- we can instead try to generate a natural language description of the clip.  This is the task of video captioning: describing a video with language narrating what’s happening in the clip.  By framing action recognition in this way, we overcome the second issue described above: we are no longer constrained to just a limited set of action classes; with language, we can now produce much more accurate and specific action tags for the video.  Obviously, this task is also more complex and difficult than simple classification, but the flexibility it affords is enormous. 

We will now introduce a new method for action recognition based on this new paradigm.  We will still use the concept of object and interaction graphs from the method described in the previous section -- these graphs still have enormous value with the spatio-temporal information they provide.  However, instead of needing supervision for building the graphs, the intuition behind this next approach is to build the graphs in a bottom-up fashion, and translating these graphs into language descriptions.  Building graphs bottom-up overcomes the first issue described above: we no longer need tedious annotation of scene graphs in all the training images, as we can build the graph for a new image directly from the frame.

## 2. Two-branch framework

There are three main steps underlying this method.  First, we do object detection of each frame: simply identifying the objects present.  Second, we model how each object moves through time across frames (temporal information).  This is done by keeping track of the same object across frames and seeing how its bounding box changes position.  Third, we model how the objects relate to one another, i.e. the interactions between them (spatial information).  This is done by creating edges between each pair of objects where the edge length corresponds to the distance between the two objects in the frame.  In summation, these three components allow us to capture how objects in the scene change through time and relate to each other.  Succinctly, this method can be described as joint spatio-temporal object relation modeling, where we have constructed graphs capturing spatiotemporal information about the scene.  Figure 3.1 shows a pictorial diagram of this approach. 

figure 3.1 

Next, we will describe the architecture of the model more concretely.  In this method, the model has a two-branch framework.  One branch, the object branch, does the spatiotemporal modeling we described above.  It takes the set of frames from the input video, and generates graphs for the objects and their relationships over time and between each other.  The output of this branch is 1 feature vector for the input.  The second branch, the scene branch, simply processes the pixel information from the video.  This branch is essentially using the classical action recognition techniques that simply use pixel information and treat time as an additional dimension.  This branch also has a final output of a feature vector. 

Now, from these 2 branches, we have two feature vectors for the input video: one from the object branch that captures the knowledge of the graphs, and one from the scene graph that captures the pixel information.  These 2 feature vectors are concatenated.  Then, this concatenated vector is finally passed into a state-of-the-art natural language generation model (transformer), which generates the caption for the video.  Figure 3.2 shows a helpful diagram visualizing this entire 2-branch framework. 

figure 3.2 

Now that we’ve described the architecture of this new modeling approach, let’s take a moment to reflect on what we’ve accomplished.  First off, by building up graphs in a bottom-up fashion, we’re still able to get the benefits of the structural information captured by it, without needing full supervision for building it.  This represents a major advancement over the previously described scene graph method which needed tedious annotation.  Secondly, by outputting natural language descriptions of the clip, we have much more rich and accurate tags of what’s happening in the video, as opposed to just having to pigeonhole it into a broad class.  This also represents a major advancement over the scene graph method, which only did classification. 


## 3. Quantitative results


Next, we can look at some of the results of this new spatiotemporal graph approach.  In demonstrating the significance of any new algorithm, it’s important to show how your method performs on benchmark datasets for the field.  In the case of video captioning, two key benchmark datasets are MSVD and MSR-VTT.  As shown in Figure 3.3, the new method markedly outperformed previous state-of-the-art on MVSD, while also having competitive performance on MSR-VTT. 


figure 3.3 

## 4. Qualitative results

To understand why a new model is performing better on a benchmark task, it’s also important to dig deeper into the qualitative results of the model on specific examples to understand what the model is learning and how it’s performing better.  In the task of scene understanding, one commonly-used method is to generate a saliency map of the given frame.  The saliency map is a heatmap over the frame that shows which pixels are most “important” for action recognition -- i.e., which pixels the model is weighting most heavily in determining what’s occurring in the scene.  The saliency map results demonstrate that the new approach is better at identifying the important regions in the scene.  For example, consider Figure 3.4 below.  We can see that when compared to using scene information alone, the new approach attends more tightly to the head of the man and the ping pong paddle he is holding: which are both critical objects for understanding what’s going on.  This example shows that the object-centric graph helps the model focus on the more important parts, and correspondingly generates a better caption for the scene.  For instance, in Figure 3.4, note that the new approach is able to identify that the man is wearing a black-colored shirt, which the previous approach could not. 

figure 3.4 

## 5. Limitations 

Although the new approach made several key strides over previous methods, there are still a few limitations.  For one, the model can only generate simple captions of a clip.  If we are seeking to make a more detailed or complex description, then unfortunately, the model is not able to do that -- it is limited to relatively short descriptions.  Secondly, the model is still only outputting a caption for the clip.  Although internally there is a graph representation being used to generate the caption, in the end the model only generates a caption, so there is no way to understand the structure of an event.  If we are seeking to get information about event structure, then unfortunately, this model is not suited for that.  The process of tackling this task of event structure analysis is the focus of the next section. 

## Parsing Video Demonstrations

Overview Questions: 
How do we obtain the event structure from a video?
How do we understand tasks and sub-tasks?

In order to understand this concept, we can think about an instructional video. We can easily create a step-by-step linear timeline of the sequence of different events in this kind of video, but how do we understand how the steps relate to one another?

The answer is to create a grounded dependency structure to understand which acts in this structure occur independently and which ones have to occur in order for others to occur. We can do this by visually grounding verbal cues or in other words, we match a noun spoken to an object in the frame.

Consider these two consecutive frames:

figure 3.5 

In the second frame, we need to connect the pronoun “it” to the previous instruction step in order to disambiguate. 

This can be solved with two models:

**Reference Resolution model (Grounding-Aware)**
Grounding pronouns to names and finding relationship between one step and previous step

And then, once we know the relationship between the steps and the references, we  use 

**Visual Grounding model (Reference-Aware)**
Ground the object referenced with its corresponding frame ie. ground “it” with the salad bowl.

figure 3.6 

On a larger scale, we can create reference edges and grounding edges in order to understand causal relationships between different frames. For example, in lecture, we see this method applied to the instructional video to make spaghetti and meatballs which then allows us to create this grounded dependency structure graph.

There are many applications of this approach:
1. Virtual coaches for training people
2. Robot learning from demonstrations

**Human Pose Estimation**

This area of research attempts to think about alternatives to capturing high resolution images/videos of individuals given the privacy concerns involved. 

**Question:**
Is there a way to estimate your pose without capturing high resolution video of a user?

**Answer:**
If we acquire a very blurred image where we distort the image by changing the lens of the camera, we can protect the privacy of the user and also estimate the pose very accurately. 

## Conclusion

**Open Research Questions**

1. How do we deal with this hierarchy (at varying levels of the temporal line)
  	a. When do we use action classification versus sentences?
2. When do we discretize units?
3. When do we drive by open language?














