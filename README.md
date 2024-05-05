# Zener Voltage Reference biased by Constant Current Source

## Short description of concept:
Zener Diode is supplied by a constant current source and protected by a series resistor. As long as the current draw of the circuit that is connected to the reference is low enough, on the order of tens to hundreds of microamps, the Zener will provide a roughly constant reference voltage.


<p align="center">
    <img width="400" src="https://github.com/Riggstadt/JFET_SOURCE/assets/127757267/3d72a976-02f2-4292-a8d1-622775a2854a">
    <br>
    <br>
    <a href="https://toshiba.semicon-storage.com/eu/semiconductor/knowledge/faq/diode/how-can-i-use-a-zener-diode-to-create-a-relatively-constant-volt.html">Image source: Toshiba Semiconductor</a>
</p>



## Where the idea came from:
Why not use the reverse polarity protection diode of a Constant Current Diode as a voltage reference?

<p align="center">
    <img  src="https://github.com/Riggstadt/JFET_SOURCE/assets/127757267/d2c5a6f3-09b5-448d-8950-60b88e6b53f8">
    <br>
    <br>
    <a href="https://www.allaboutcircuits.com/textbook/experiments/chpt-5/jfet-current-regulator/ 
">Image source: allaboutcircuits.com</a>
    <br>
    <br>
</p>
<p align="center">
    <img height="600" src = "https://github.com/Riggstadt/JFET_SOURCE/assets/127757267/7b9215c3-dd94-4856-b939-ba80faa5f8f9">
    <br>
    <br>
    <a>KiCad 7.0 schematic of the Zener Reference</a>
</p>

## How it works:
The constant current source is built out of the J109 N-JFET and a series resistor. The load can be connected to either end of the source.
$V_{DS}$ varies depending on the supply voltage and the voltage drop upon the load. After a certain $V_{DS}$ the current will be more or less constant irrespective of drain to source voltage drop.
In its saturated state the JFET has the following characteristic electrical equation:
$$I_{D}=I_{DSS}\cdot \left(1-\frac{V_{GS}}{V_{GS(off)}} \right)^{2}\quad(1)$$

$V_{GS(off)}$ and $I_{DSS}$ are usually given in datasheets, but because we are working with JFETs the parameters have very big value spreads and need to be determined empirically.

For a given $I_{D}$, $V_{GS}$ will be:
$$V_{GS}=V_{GS(off)}\cdot\left(1-\sqrt{\frac{I_{D}}{I_{DSS}}}\right)\quad(2)$$

We want to set a constant current. We need to ensure that $V_{GS}$ has the right value to give us the desired current. How do we do that? We add a series resistor between Source and Gate, such that $V_{GS}=R_{S}\cdot I_{D}$ or in other words we choose the preferred value closest to $R_{S}=\frac{V_{GS(off)}\cdot\left(1-\sqrt{\frac{I_{D}}{I_{DSS}}}\right)}{I_{D}}\quad(3)$.

Theoretically this is all we need, but we don't really know the value of $V_{GS(off)}$ and $I_{DSS}$.
For the J109 $V_{GS(off)}$ is given as a range of values from -0.5V to -3V and $I_{DSS}$ is only specified as a minimum of 2mA.

We need to determine these parameters accurately if we don't want to live with ginormous errors.
We will built two different circuits with the same transistor, but with two different resistor values.

<p align="center">
    <img height = "600" src="https://github.com/Riggstadt/JFET_SOURCE/assets/127757267/532a1a8c-f108-4604-9d56-e71e61f27702">
    <br>
    <br>
    <a>KiCad 7.0 schematic of the two test circuits</a>
</p>

For the one on the left we obtain $I_{D1}$ = 1.55 mA.
For the one on the right we obtain $I_{D2}$ = 2.09 mA.

We will input the resulting currents in equation 3:

$$\begin{cases}
    R_{S1}\cdot I_{D1}=V_{GS(off)}\cdot\left(1-\sqrt{\frac{I_{D1}}{I_{DSS}}}\right) \\
    R_{S2}\cdot I_{D2}=V_{GS(off)}\cdot\left(1-\sqrt{\frac{I_{D2}}{I_{DSS}}}\right)
  \end{cases}$$

We devide the two equations and obtain:
  
$$\frac{V_{GS(off)}\cdot\left(1-\left(\frac{I_{D1}}{I_{DSS}}\right)^{\frac{1}{2}}\right)}{V_{GS(off)}\cdot\left(1-\left(\frac{I_{D2}}{I_{DSS}}\right)^{\frac{1}{2}}\right)} \cdot \frac{I_{D2}}{I_{D1}}=\frac{R_{S1}}{R_{S2}}\quad(4)$$

$V_{GS(off)}$ is the same for both configurations. After plugging in the known values for the resistors and currents we finally get $I_{DSS}$ = 8.05252mA.
We plug $I_{DSS}$ in one of the two equations and get that $V_{GS(off)}$ = -0.91602505992V.

We need to test that the values we got are correct and can be used to design the current source and to that end we will be following these steps:
-  Replace series resistor with known value resistor
-  Measure drain current
-  Input drain current into the original constant current equation 
-  Check how well the resistance value obtained matches the real life resistor’s
-  If the error is smaller than 1% we did our job well

For our test case we will be using a resistor with a nominal resistance of 2 $k\Omega$ and a measured resistance of 1.993 $k\Omega$.
We measure a current $I_{D}$ = 0.36mA. Plugging it in equation 3 we get $R_{S(theo)}$ = 2.0064 $k\Omega$, which is very close to  $R_{S(meas)}$ = 1.993 $k\Omega$.

The relative error will be 0.65% $\leq$ 1%, small enough to fit our precision criterion.

## Testing the current regulation ability of the source:
The JFET will act as a constant current source only when $V_{DS}\geq V_{GS}-V_{GS(off)}$ and $\|V_{GS}\|\leq\|V_{GS(off)}\|$. Outside of this saturation region its current regulation abilities are nullified.

My initial idea was to connect LEDs to this reference voltage to better see it working. LEDs draw too much current to reach adequate brightness and so starve the zener of its potential. I scrapped the idea of the Zener voltage reference and tested only the current regulation abilities of the JFET.

I tested the JFET source, sans the Zener, by connecting multiple LEDs of various colours in series with the Drain and measuring with my ammeter. I plotted the results below:
<p align="center">
    <img src="https://github.com/Riggstadt/JFET_SOURCE/assets/127757267/f817e0db-dc3a-42cb-a5b1-6f94692a74cc">
</p>

Even within the saturation region the drain current is not really constant, but varies slightly. This is caused by channel length modulation, the reduction of the length of the FET's channel with increasing $V_{DS}$. In the real word the JFET characteristic equation needs to be tweaked a little bit to accurately predict the behaviour:

$$I_{D}=I_{DSS}\cdot \left(1-\frac{V_{GS}}{V_{GS(off)}} \right)^{2}\cdot\left(1+\lambda\cdot V_{DS}\right)\quad(5)$$

$\lambda$ or the channel length modulation factor is not given in datasheets and is usually found in Spice models of transistors. It can however be empirically determined by choosing two points at the oposite ends of the saturation region and defining the slope and Y-intercept of the line that connects the two.

We pick the two points defined as such: $P_{n}\left(V_{D(n)},I_{D(n)}\right)$ and $P_{n+1}\left(V_{D(n+1)},I_{D(n+1)}\right)$

$$\begin{cases}
I_{D(n)}=I_{DSS}\left(1-\frac{V_{GS}}{V_{GS(off)}}\right)^{2}\cdot (1+\lambda V_{DS(n)})\\
I_{D(n+1)}=I_{DSS}\left(1-\frac{V_{GS}}{V_{GS(off)}}\right)^{2}\cdot (1+\lambda V_{DS(n+1)})
\end{cases}$$

After some minor substitutions we obtain that: 
$$\lambda = \frac{I_{D(n)}-I_{D(n+1)}}{I_{D(n+1)}V_{DS(n)}-I_{D(n)}V_{DS(n+1)}}$$

And most usefully for our future research into the subject of electret microphones and JFET pre-amplifiers we can also find the value of the large-signal output resistance:

We define $r_{o} = \frac{\Delta V_{DS}}{\Delta I_{D}}=\left(\frac{I_{D(n+1)}-I_{D(n)}}{V_{DS(n+1)}-V_{DS(n+1)}}\right)^{-1}$
$$r_{o}^{-1}=\frac{I_{DSS}\left(1-\frac{V_{GS}}{V_{GS(off)}}\right)^{2}\cdot \lambda\left(V_{DS(n+1)}-V_{DS(n)}\right)}{V_{DS(n+1)}-V_{DS(n)}}$$
$$r_{o}^{-1}=\frac{\frac{I_{D(n)}}{1+\lambda V_{DS(n)}}\cdot \lambda\left(V_{DS(n+1)}-V_{DS(n)}\right)}{V_{DS(n+1)}-V_{DS(n)}}$$
$$r_{o}^{-1}=\frac{\lambda I_{D(n)}}{(1+\lambda V_{DS(n)})}$$
$$r_{o}\approx\frac{1}{\lambda I_{D(n)}}$$
## Conclusions:
-  I firmly understand why JFETs are no longer popular. 
-  Constant current sources are not really constant 
-  Zeners make awful voltage references
-  Test Probes can erroneously load a circuit

## Equipment and materials used:
- Solderless breadboard
- Fairchild J109 N-JFET
- 250mW metal film and carbon film resistor
- UNI-T UT125C multimeter

## Useful Resources:
-  <a href="http://www.ece.mcgill.ca/~grober4/SPICE/SPICE_Decks/1st_Edition/chapter5/Chapter%205%20MOSFETs%20web%20version.html#:~:text=5.3.1%20JFET%20Element%20Description">Spice model parameters of JFETs</a>
-  <a href="https://www.vishay.com/docs/70596/70596.pdf">Vishay/AN103/The FET Constant-Current Source/Limiter</a>
-  <a href="https://www.farnell.com/datasheets/2304095.pdf">Fairchild J109 Datasheet</a>
