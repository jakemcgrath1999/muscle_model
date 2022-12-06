# muscle_model

Starting from neural excitation, let's model the contractile dynamics of muscle.

For a deeper understanding of the problem at hand, view the uploaded powerpoint slides.  All work in this repository is for the final project in [computation physics](https://www.wgilpin.com/cphy/) fall semester 2022 w/ Prof William Gilpin.

# description

This model is inspired by [Adjustment of Muscle Mechanics Model Parameters to Simulate Dynamic Contractions in Older Adults](https://doi.org/10.1115/1.1531112).  The goal is to map neural excitation to activation dynamics and then transform activation to drive muscle contraction.  The model implemented here follows the commonly-used Hill model of muscle and accurately represents three intrinsic properties of muscle.  The Hill muscle model was originally introduced by physiologist AV Hill in 1939 and it describes the nonlinear relationship between muscle tension and contraction velocity 

$$ {(V+b)} \cdot {(F+a)} = {b \cdot (F_o+a)} $$

where $F$ and $V$ are the muscle's tension and contraction velocity, $F_o$ is the maximum tension generated, $a$ is the coefficient of shortening heat, and $b = a \cdot {V_o \over F_o}$.  In normalized form, the Hill model becomes

$$ f = {{1 - v} \over {1 + \alpha \cdot v}} $$

where $f$ and $v$ are normalized force and velocity, and $\alpha$ characterizes the degree of nonlinearity.  The other two properties of muscle that our model considers is its Force-Length properties and tendon's Force-Strain relationship.  The curves representing muscle's intrinsic properties that we will try to model are shown below

Inline-style: 
![alt text](https://github.com/adam-p/markdown-here/raw/master/src/common/images/icon48.png "Logo Title Text 1")

# model limitations
In this model, muscle fatigue from overuse is not included.  Fatigue is an important feature of muscle: obviously we cannot force our muscles to actuate indefinitely.  At some point, muscle will tire and perform at suboptimal standards.  Other models of muscle contraction (specifically those that implement motor-unit based models) can account for muscle fatigue, however, this feature of muscle is neglected here.  If you wish to address tiring, please [see this other repository](https://github.com/iandanforth/pymuscle/blob/master/README.md) that simulates the relationship between excitatory input and motor-unit output as well as fatigue over time.

Our model does not account for a fixed supply of ATP.  ATP hydrolysis drives muscle contraction, however, this model does not specify a limited supply of energy (ATP) during contraction.

Some other deficits of this model are not limited to but include: impaired blood flow, ion imbalance, nervous fatigue, and lactic acid accumulation.
