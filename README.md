# muscle_model

Starting from nueral excitation, let's model the contractile dynamics of muscle.

For a deeper understand of the problem at hand, view the uploaded powerpoint slides.  All work in this repository is for the final project in computation physics fall semester 2022 (class page: https://www.wgilpin.com/cphy/).

# description

wfer

# model limitations
In this model, muscle fatigue from overuse is not included.  Fatigue is an important feature of muscle: obviously we cannot force our muscles to actuate indefinitely.  At some point, muscle will tire and perform at suboptimal standards.  Other models of muscle contraction (specifically those that implement motor-unit based models) can account for muscle fatigue, however, this feature of muscle is neglected here.  If you wish to address tiring, please see this other repository (https://github.com/iandanforth/pymuscle/blob/master/README.md) that simulates the relationship between excitatory input and motor-unit output as well as fatigue over time.

Our model does not account for a fixed supply of ATP.  ATP hydrolysis drives muscle contraction, however, this model does not specify a limited supply of energy (ATP) during contraction.

Some other deficits of this model are not limited to but include: impaired blood flow, ion imbalance, nervous fatigue, and lactic acid accumulation.
