# muscle_model

Starting from neural excitation, let's model the contractile dynamics of muscle.

For a deeper understanding of the problem at hand, view the uploaded powerpoint slides.  All work in this repository is for the final project in [computation physics](https://www.wgilpin.com/cphy/) fall semester 2022 w/ Prof William Gilpin.

Some helpful resources:
* [Standford muscle model](https://simtk-confluence.stanford.edu:8443/display/OpenSim/Muscle+Model+Theory+and+Publications)
* [A kinematic model of the muscular contraction](https://hrcak.srce.hr/file/114131)
* [Good summary of muscle contraction](https://www.nature.com/scitable/topicpage/the-sliding-filament-theory-of-muscle-contraction-14567666/)
* [Guided tour](https://simtk-confluence.stanford.edu:8443/download/attachments/2624181/CompleteDescriptionOfTheThelen2003MuscleModel.pdf?version=1&modificationDate=1319838594036&api=v2)
          
# description

This model is inspired by [Adjustment of Muscle Mechanics Model Parameters to Simulate Dynamic Contractions in Older Adults](https://doi.org/10.1115/1.1531112).  The goal is to map neural excitation to activation dynamics and then transform activation to drive muscle contraction.  The general code structure is show below

![alt text](https://github.com/jakemcgrath1999/muscle_model/blob/main/extras/Screenshot%202022-12-06%20at%2011.08.26%20AM.png)

For a demo with how to use the provided code, please see the demo folder.  Here is a link to [activation demos](https://github.com/jakemcgrath1999/muscle_model/blob/main/demos/activation_demo.ipynb) and here is a link to [contraction dynamics demos](https://github.com/jakemcgrath1999/muscle_model/blob/main/demos/contraction_demo%20(1).ipynb).

FYI, the activation demo is fully built out, however, the contraction dynamics demo has a bug in it.

## model inputs and outputs

The idea of this model is as follows:  for a given time $t$, input into the model a neural excitation input $u(t) \in [0,1]$, an activation value $a(t) \in [0,1]$ and a muscle fiber length $l^M(t)$.  The model will then output the entire actuator's force $F^{MT}(t)$ and, at the next timestep $t + \Delta t$, the time derivative of activation $\dot{a}(t + \Delta t)$ and fiber contraction velocity $\dot{l}^M(t + \Delta t)$.

![alt text](https://github.com/jakemcgrath1999/muscle_model/blob/main/extras/Screenshot%202022-12-06%20at%2011.05.46%20AM.png)

# background

Below we explain the physiology needed to understand how the model works.

## hill model

The model implemented here follows the commonly-used Hill model of muscle and accurately represents three intrinsic properties of muscle (muscle's force-length, force-velocity, and tendon's force-strain relationships).  The Hill model is commonly depicted as a muscle unit in series with a passive tendon unit.  The muscle unit consists of two parallel components:  a passive element that models the behavior of connective tissue and a contralie element which simulates the dynamics of actin-myosin interactions.  The series tendon unit is represented by a nonlinear spring that captures the elastic properties of the tendon.  Below is a schematic of the Hill muscle model and its three components

<p align="center">
  <img src="https://github.com/jakemcgrath1999/muscle_model/blob/main/extras/Hill-muscle-model-comprising-the-contractile-element-CE-whose-length-is-indicated-as.png">
</p>

The Hill muscle model gives the nonlinear relationship between muscle tension and contraction velocity as

$$ {(V+b)} \cdot {(F+a)} = {b \cdot (F_o+a)} $$

where $F$ and $V$ are the muscle's tension and contraction velocity, $F_o$ is the maximum tension generated, $a$ is the coefficient of shortening heat, and $b = a \cdot {V_o \over F_o}$.  In normalized form, the Hill model becomes

$$ f = {{1 - v} \over {1 + \alpha \cdot v}} $$

where $f$ and $v$ are normalized force and velocity, and the dimensionless parameter $\alpha$ characterizes the degree of nonlinearity.  For a good discussion on muscle's nonlinear force-velocity relationship, see [the following](https://www.brown.edu/Departments/Engineering/Courses/En123/Lectures/HillEqn.htm#:~:text=Therefore%20power%20is%20force%20X,power%20output%20for%20the%20muscle.).

The other two properties of muscle that our model considers is its force-length properties and tendon's force-strain relationship.  Muscle's force-length relationship models muscle as nonlinear springs that become exponentially strong as they are stretched too far.  To see how muscle's passive elastic properties and active contractile properties play into this relationship, see [this discussion](https://www.brown.edu/Departments/Engineering/Courses/En123/MuscleExp/Length_Tension.htm).

The curves representing muscle's intrinsic properties that we will try to model as well as a schematic of the Hill model are shown below:

<p align="center">
  <img src="https://asmedc.silverchair-cdn.com/asmedc/content_public/journal/biomechanical/125/1/10.1115_1.1531112/4/004301j.1.jpeg?Expires=1673363444&Signature=pPblMs8CAh13FzDwQki4XE72z484CxEmgGOMUooAFXLm-IXNkbYiLumMCaTS40YyN2pKWbOuo9B5GkWmh9y5qh2kPHMEP64~i1ehIp7Ua9G9O4J~xS2FKVL7KQI3jJKEvkSyw6Dt6KU~nqDeeF4yI-ekOG-uK9pW6Qe40NagIB4iXnAp7snmj~PIV7A1MdQ1la-AMSJJswMYPOcxup13hXW7JWeH5AE~PPVM~sqiWLuXzvjunEhpiPCmUNCF11HgtizZ2H2d1BwEM2XlTy3xjxTx-WnwUmp0N2tw32iuZi-Y9B5UBszoXBTrTw6LMH80DIRIF6pm-2y-dnMvI0jQrw__&Key-Pair-Id=APKAIE5G5CRDK6RD3PGA">
</p>

We can use the image above as a validation metric of our muscle model:  if our model outputs behavior like muscle's in-vivo properties above, then we are confident in the results of our model.

## activation

Given a neural input $u(t) \in [0,1]$ and current activation value $a(t)$, the activation models spit out the time derivation of activation $\dot{a}(t)$.  [Click here](https://github.com/jakemcgrath1999/muscle_model/blob/main/demos/activation_demo.ipynb) for a demo.

Muscle cannot generate force nor relax instantaneously: activation functions as an intermediate step between neural input and muscle contraction to account for these time delays.  Activation dynamics is a differential equation that describes the change in calcium ion concentration within the muscle.  Calcium ions singal cross-bridge formation of actin-myosin proteins which is modeled by the contraction dynamics portion of this code.  When muscle is neurally excited, activation increases rapidly; whereas if excitation decreases, muscle activation gradually decreases.  In this code, we use the timescale of activation $\tau_{act} = 10$ ms and the decactivation timescale to be $\tau_{deact} = 40$ ms.

There exist many different differential equations that govern activation dynamics.  Here, I implement 9 different models found in the literature to see how they generate different contraction dynamics.  I created one parent class called Activation that houses the general methods that each activation model uses -- underneath this Activation class live the 9 different models of activation dynamics.

![alt text](https://github.com/jakemcgrath1999/muscle_model/blob/main/extras/Screenshot%202022-12-06%20at%2011.07.12%20AM.png)

## contraction dynamics

The contractile dynamics portion of this model will give us the entire actuator's force $F^{MT}(t)$ and, at the next timestep $t + \Delta t$, fiber contraction velocity $\dot{l}^M(t + \Delta t)$.  Please refer to the links under 'helpful resources' above to see the exact equations used (or, the equations exist in our code so check it out there too!).  Here is a [demo](https://github.com/jakemcgrath1999/muscle_model/blob/main/demos/contraction_demo%20(1).ipynb) that illustrates how the contraction dynamics code runs.

The muscle model consists of two main parts:  the muscle fibers and tendon.  The muscle fibers themselves have two avenues of force production:  first, the passive elements of muscle have stiffness and produce force when stretched; second, the contractile element produces force from actin-myosin interactions stimulated by activation.

To find the force produced by the muscle-tendon actuator, we do the following.  At a time $t$, we find the length of the entire actuator.  We can then compute the tendon-length and then tendon-strain.  With strain and length, we can find the tendon's force.  Because the tenon attaches the muscle fibers to bone, the force calculated here in the tendon is equal to the force generated by the muscle as a whole.

To find the muscle-fiber contraction velocity, we follow the procedure outlined below in the diagram:

![alt text](https://github.com/jakemcgrath1999/muscle_model/blob/main/extras/Screenshot%202022-12-07%20at%202.03.07%20PM.png)

# model limitations
In this model, muscle fatigue from overuse is not included.  Fatigue is an important feature of muscle: obviously we cannot force our muscles to actuate indefinitely.  At some point, muscle will tire and perform at suboptimal standards.  Other models of muscle contraction (specifically those that implement motor-unit based models) can account for muscle fatigue, however, this feature of muscle is neglected here.  If you wish to address tiring, please [see this other repository](https://github.com/iandanforth/pymuscle/blob/master/README.md) that simulates the relationship between excitatory input and motor-unit output as well as fatigue over time.

Our model does not account for a fixed supply of ATP.  ATP hydrolysis drives muscle contraction, however, this model does not specify a limited supply of energy (ATP) during contraction.

Some other deficits of this model are not limited to but include: impaired blood flow, ion imbalance, nervous fatigue, and lactic acid accumulation.
