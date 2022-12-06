# muscle_model

Starting from neural excitation, let's model the contractile dynamics of muscle.

For a deeper understanding of the problem at hand, view the uploaded powerpoint slides.  All work in this repository is for the final project in [computation physics](https://www.wgilpin.com/cphy/) fall semester 2022 w/ Prof William Gilpin.

# description

This model is inspired by [Adjustment of Muscle Mechanics Model Parameters to Simulate Dynamic Contractions in Older Adults](https://doi.org/10.1115/1.1531112).  The goal is to map neural excitation to activation dynamics and then transform activation to drive muscle contraction.

## hill model

The model implemented here follows the commonly-used Hill model of muscle and accurately represents three intrinsic properties of muscle (muscle's force-length, force-velocity, and tendon's force-strain relationships).  The Hill model is commonly depicted as a muscle unit in series with a passive tendon unit (shown in the figure below).  The muscle unit consists of two parallel components:  a passive element that models the behavior of connective tissue and a contralie element which simulates the dynamics of actin-myosin interactions.  The series tendon unit is represented by a nonlinear spring that captures the elastic properties of the tendon.  The Hill muscle model gives the nonlinear relationship between muscle tension and contraction velocity as

$$ {(V+b)} \cdot {(F+a)} = {b \cdot (F_o+a)} $$

where $F$ and $V$ are the muscle's tension and contraction velocity, $F_o$ is the maximum tension generated, $a$ is the coefficient of shortening heat, and $b = a \cdot {V_o \over F_o}$.  In normalized form, the Hill model becomes

$$ f = {{1 - v} \over {1 + \alpha \cdot v}} $$

where $f$ and $v$ are normalized force and velocity, and $\alpha$ characterizes the degree of nonlinearity.  The other two properties of muscle that our model considers is its Force-Length properties and tendon's Force-Strain relationship.  The curves representing muscle's intrinsic properties that we will try to model are shown below:

<p align="center">
  <img src="https://asmedc.silverchair-cdn.com/asmedc/content_public/journal/biomechanical/125/1/10.1115_1.1531112/4/004301j.1.jpeg?Expires=1673363444&Signature=pPblMs8CAh13FzDwQki4XE72z484CxEmgGOMUooAFXLm-IXNkbYiLumMCaTS40YyN2pKWbOuo9B5GkWmh9y5qh2kPHMEP64~i1ehIp7Ua9G9O4J~xS2FKVL7KQI3jJKEvkSyw6Dt6KU~nqDeeF4yI-ekOG-uK9pW6Qe40NagIB4iXnAp7snmj~PIV7A1MdQ1la-AMSJJswMYPOcxup13hXW7JWeH5AE~PPVM~sqiWLuXzvjunEhpiPCmUNCF11HgtizZ2H2d1BwEM2XlTy3xjxTx-WnwUmp0N2tw32iuZi-Y9B5UBszoXBTrTw6LMH80DIRIF6pm-2y-dnMvI0jQrw__&Key-Pair-Id=APKAIE5G5CRDK6RD3PGA">
</p>

We can use the image above as a validation metric of our muscle model:  if our model outputs behavior like muscle's in-vivo properties above, then we are confident in the results of our model.

## model inputs and outputs

The idea of this model is as follows:  for a given time $t$, input into the model an activation value $a(t) \in [0,1]$ and a muscle fiber length $l^M(t)$.  The model will then output the entire actuator's force $F^{MT}(t)$ and, at the next timestep $t + \Delta t$, the time derivative of activation $\dot{a}(t + \Delta t)$ and fiber contraction velocity $\dot{l}^M(t + \Delta t)$.

input image from powerpoint showing how inputs map to outputs...

## activation

Muscle cannot generate force nor relax instantaneously: activation functions as an intermediate step between neural input and muscle contraction to account for these time delays.  Activation dynamics is a differential equation that describes the change in calcium ion concentration within the muscle.  Calcium ions singal cross-bridge formation of actin-myosin proteins which is modeled by the contraction dynamics portion of this code.  When muslde is neurally excited, activation gradually increases; whereas if excitation decreases, muscle activation gradually decreases.

There exist many different differential equations that govern activation dynamics.  Here, I implement 9 different models found in the literature to see how they generate different contraction dynamics.

# model limitations
In this model, muscle fatigue from overuse is not included.  Fatigue is an important feature of muscle: obviously we cannot force our muscles to actuate indefinitely.  At some point, muscle will tire and perform at suboptimal standards.  Other models of muscle contraction (specifically those that implement motor-unit based models) can account for muscle fatigue, however, this feature of muscle is neglected here.  If you wish to address tiring, please [see this other repository](https://github.com/iandanforth/pymuscle/blob/master/README.md) that simulates the relationship between excitatory input and motor-unit output as well as fatigue over time.

Our model does not account for a fixed supply of ATP.  ATP hydrolysis drives muscle contraction, however, this model does not specify a limited supply of energy (ATP) during contraction.

Some other deficits of this model are not limited to but include: impaired blood flow, ion imbalance, nervous fatigue, and lactic acid accumulation.
