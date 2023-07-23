# JFET_SOURCE
Constant Current Source and Voltage Reference from N-JFETs
## Short description of concept:
Zener Diode is supplied by a constant current source and protected by a series resistor. As long as the current draw of the circuit that is connected to the reference is low enough, on the order of tens to hundreds of microamps, the Zener will provide a roughly constant reference voltage.

![image](https://github.com/Riggstadt/JFET_SOURCE/assets/127757267/3d72a976-02f2-4292-a8d1-622775a2854a)

[Image source: Toshiba Semiconductor][1]

## Where the idea came from:
Why not use the reverse polarity protection diode of a Constant Current Diode as a voltage reference?

![image](https://github.com/Riggstadt/JFET_SOURCE/assets/127757267/d2c5a6f3-09b5-448d-8950-60b88e6b53f8)

[Image source: allaboutcircuits.com][2]

[1]: https://toshiba.semicon-storage.com/eu/semiconductor/knowledge/faq/diode/how-can-i-use-a-zener-diode-to-create-a-relatively-constant-volt.html
[2]: https://www.allaboutcircuits.com/textbook/experiments/chpt-5/jfet-current-regulator/

## How it works:
My initial idea was to connect LEDs to this reference voltage to better see it working. LEDs draw too much current to reach adequate brightness and so starve the zener of its potential.

The constant current source is built out of the J109 N-JFET and a series resistor. The load can be connected to either end of the source.
$V_{DS}$ varies depending on the supply voltage and the voltage drop upon the load. After a certain $V_{DS}$ the current will be more or less constant irrespective of drain to source voltage drop.
In its saturated state the JFET has the following characteristic electrical equation:
$$I_{D}=I_{DSS}\cdot \left(1-\frac{V_{GS}}{V_{GS(off)}} \right)^{2}$$

$V_{GS(off)}$ and $I_{DSS}$ are usually given in datasheets, but because we are working with JFETs the parameters have very big value spreads and need to be determined empirically.

For a given $I_{D}$, $V_{GS}$ will be:
$$V_{GS}=V_{GS(off)}\cdot\left(1-\sqrt{\frac{I_{D}}{I_{DSS}}}\right)$$

We want to set a constant current. We need to ensure that $V_{GS}$ has the right value to give us the desired current. How do we do that? We add a series resistor between Source and Gate, such that $V_{GS}=R_{S}\cdot I_{D}$ or in other words we choose the preferred value closest to $R_{S}=\frac{V_{GS(off)}\cdot\left(1-\sqrt{\frac{I_{D}}{I_{DSS}}}\right)}{I_{D}}$.

Theoretically this is all we need, but we don't really know the value of $V_{GS(off)}$ and $I_{DSS}$.
For the J109 $V_{GS(off)}$ is given as a range of values from -0.5V to -3V and $I_{DSS}$ is only specified as a minimum of 2mA.

We need to determine these parameters accurately if we don't want to live with ginormous errors.
We will built two different circuits with the same transistor, but with two different resistor values.

