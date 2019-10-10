							#Dancing-Agents

##Fall 2019 CS 6476 Computer Vision: Class Project
##Georgia Tech

#Jayanta Bhowmick
#Purva Tendulkar
#Sushrut Kulkarni
#Venkata Subramanian Srinivasan


Abstract:
Reinforcement Learning is typically used to solve well-defined problems like finding the optimal strategy for solving a game or reaching a certain destination. Reward functions are used to provide high-level guidelines for achieving this, which encourage or discourage certain behaviors. 

In this project, we would like to explore if we can design reward functions that understand notions of creativity, specifically for dance. For example, for a creative field like dance, there are certain high-level rules which dancers comply with, such as sticking to the beat, showing some periodic movements, maybe interspersed with some elements of surprise. If we can train an agent to follow these high-level guidelines, then given some input signal such as music beats, it would be interesting to see if the agent can come up with its own dance form. We provide this input signal to our agent by extracting beats from a dance video, and design reward functions to allow the agent to dance in interesting ways.



Problem Statement:

To train a reinforcement learning agent to dance in sync with the beat information from a dance video(without audio). 

https://github.com/skulkarni322/Dancing-Agents/issues/1#issue-504964723

The 1-D representation must encapsulate the beat information of the audio to which the entity or individual is dancing in accordance with.

Typical components of beat information:
Pulse - Periodic unit of measure
Rhythm - Regular recurring pattern/motion
Tempo - Speed of the piece
Groove - Effect of changing pattern in a propulsive rhythm





This music beat signal will then be used to train an RL agent to move around in a grid world by syncing inflections with the beats in music in interesting ways, effectively allowing it to dance!

Assumption:
The dance movements/motion in the video is in sync/relevant with the audio for the particular video.
Approach:

Our approach consists of 2 parts -
(1) Extracting beat information from a dance video
(2)Training an RL agent to sync its actions with the input signal in interesting ways. 

We describe them briefly below.

Extracting beat information from a dance video:

	Preprocessing:
	Audio-
While training, instead of using the original audio of every video, we intend to preprocess the audio signal to include only the relevant beat information. The required beat information can be extracted by using the Short-time Fourier Transform (STFT).



Here the peaks in the frequency are corresponding to the recurring pattern or the “beat” information.

Video-
Since the video might be susceptible to camera motion which might impede in capturing the 1-D representation. We need to consider camera motion to obtain better representations. 

We are planning to use the method proposed in Heng Wang et al.[1] To estimate camera motion, they match feature points between frames using SURF descriptors and dense optical flow. These matches are, then, used to robustly estimate a homography with RANSAC. Based on the estimation, the camera motion is canceled out from the optical flow and the background trajectories are compensated.



We plan to implement one of the following two approaches to extract the “beat“ information:
Deep Learning-based approach
Non-Deep Learning based approach

Deep Learning-based approach:

We propose to develop a 1-D representation model for the input video data trained with the audio-video data pair. 

At inference, given an audio-less input dance video, we would expect to obtain beat related information corresponding to movements in the input video.

We are currently evaluating the various visual representation models to extract the 1-D signal:

Relja Arandjelovic et al.[2] develops a visual representation model based on audio-visual data pair, hence the vision subnetwork weights can be used as feature extraction and eventually create 1-D representation.  


Ishan Misra et al.[3] developed a representation model based on temporal order verification, hence it might be relevant to extract motion representation from it. It can be subsequently processed through an RNN to generate 1-D data which correlates with the beats information from the audio track. Since their model follows AlexNet architecture, it’s easier to extract information from the frames of the video. 


	Non Deep Learning based approach:
We would define criteria for movements by computing gradients of the optical flow of motions of the target object in our input videos. Then based on the intensity of our extracted signals, and available signal information at other timesteps, we would perform post-processing operations to retrieve the final beats. We would make some assumptions about expected periodic characteristics in the music to extrapolate lossy signals.


Optical flow example 

Training an RL agent
We will explore various reward functions incrementally to add more complexity to the agent's movements and notions of creativity.
We start off with a simple agent defined as a dot in a 1D grid world. We define our agent’s action space as {L, R, S} indicating “right”, “left” and “stay” respectively. The left and right movements are of unit step. Our preliminary experiments involve simply making sure the agent doesn't hit walls or doesn't stay still for too long.

We then move on to more complex tasks such as covering states and periodic behaviors (possibly interspersed with some non-periodic movements for diversity).
Next, we add losses that allow our agent to understand notions of music and take actions to sync with music beats.
We would eventually evolve our agent (and our environment) by having a 2D world, increasing the agent's action space, giving it its own memory (LSTM), and develop its notions of creativity, so that given new input signals, we would expect to see the emergence of new, more interesting dance.


Experiments
Our experiments would be separate for the two tasks defined above. We describe them briefly below:
Extracting beat information from a dance video
We describe our tentative experiments for each of the two possible approaches for this subtask.
Deep Learning-based approach:
Some datasets under consideration are as follows-
YouTube 8M
Let's Dance: Learning From Online Dance Videos
We intend to use a pre-trained deep net model for visual representation for a chosen dataset. We would then finetune this by adding an RNN and some fully-connected layers to predict a beat intensity at every timestep. We would then perform post-processing operations such as thresholding to obtain the final beat sequence. Our loss would be computed by comparing the processed music signal intensities at every timestep.
Non-Deep Learning based approach:
We will extract optical flow from our dance video data, and compute gradients at every timestep. On detecting change in a motion (high gradient values), we would measure the intensity of change and thus get a signal for presence of a beat at every timestep. We would then perform similar post-processing as above to get our final beat sequence. It would be computed as mentioned above by comparing our signal with the processed music signal.
Post-processing music audio signal:
Some libraries under consideration are as follows-
Python music21
Python librosa
Training an RL agent
We state our experiments in incremental steps as follows-
(Ramp up task) Ignore music. Train an agent to move around its world without taking invalid actions i.e. going right when you are already at the rightmost square
(Ramp up task) #1 + add rewards to ensure that it does not stay still for too long and keeps moving.
(Ramp up task) #2 + add rewards to ensure that it is able to explore all states.
#3 + add rewards that will encourage periodic motion sequences / repetitive motions for a given fixed periodicity.. This might involve an LSTM on top, that keeps track of recent movements.
#4 + add rewards that bring about diversity within groups of periodic sequences.
#5 + add rewards to model hierarchical periodicity.
#6 + add rewards for dynamic periodicity, i.e., sync with music beats in a simple way.
Increase degrees of freedom for the agent.

References
Wang, Heng, and Cordelia Schmid. "Action recognition with improved trajectories." Proceedings of the IEEE international conference on computer vision. 2013.
Relja Arandjelovic and Andrew Zisserman. “Look, Listen and Learn.”
IEEE International Conference on Computer Vision (ICCV) 2017


