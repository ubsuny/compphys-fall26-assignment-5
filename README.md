---
geometry:
- margin=1.25in
mainfont: Palatino
header-includes: 
- \usepackage[document]{ragged2e}
---

# Assignment 5

### Instructions
- PHY410: Do problems 1a–c and 2a–b.
- PHY505: Do all parts, including the PHY505-only parts 1d, 2c, and 2d.

Accept the assignment from github classroom: <https://classroom50.org/ubsuny/compphys-fall26/assignments/assignment-5/accept>. This will create a new repository for you on github, titled something like `compphys-assignment5-username`.
You should submit your code through github classroom, and your writeup through UBLearns. The suggested writeup format is to use Markdown cells in the notebook itself; then convert the notebook to HTML/PDF/etc., and upload the converted notebooks to UBLearns. But feel free to use another format if you prefer.

You can use AI to help write code (it is not necessary, though). Any interpretations and explanations must be your own. Describe any use of AI in your answer. 


\newpage

## Problem 1: Duffing oscillator
*25 points (PHY410) or 35 points (PHY505)*

Do your work in `problem1/problem1.ipynb`. The driven, damped Duffing oscillator describes a particle in a double-well potential $V(x) = -\frac{1}{2}x^2 + \frac{1}{4}x^4$:

$$
\ddot{x} + \gamma \dot{x} - x + x^3 = F_D \cos(\omega_D t)
$$

Starting from the `Pendulum` class in `CompPhys/odes/pendulum_nonlinear.ipynb`, write a corresponding `Duffing` class. Note that $x$ is a position, not an angle, so it should **not** be wrapped.

Start with the following parameters:

```
ode_method = "RK45"
rtol = 1e-9
atol = 1e-9
gamma = 0.3       # Damping parameter
omega_D = 1.2     # Driving frequency
x_init = 1.0      # Initial position
v_init = 0.0      # Initial velocity
T_D = 2*np.pi/omega_D
n_periods = 200   # Total drive periods to integrate
n_discard = 100   # Drive periods to discard (transient)
```

As in class, call `solve_ivp` with `dense_output=True`, so that you can sample the trajectory with uniform time spacing, as well as at exact multiples of $T_D$ for the Poincaré section. Remember that most of the code was already covered in the in-class examples, and you are free to copy that code. 

### Problem 1a
*5 points*

Plot the potential $V(x)$. Then, for $F_D = 0.20$ and $F_D = 0.28$, use `scipy.integrate.solve_ivp` to compute the trajectory (discard the transient) and plot the phase-space trajectory ($v$ vs. $x$). Based on the plots alone, how many cycles does each trajectory have? Which potential well(s) does the motion occupy?

### Problem 1b
*10 points*

1. Write a function that converts a trajectory into a Poincaré section, i.e. samples $(x, v)$ once per drive period $T_D$, after the transient. 
2. Count the number of cycles in the Poincaré section. You may use the `count_cycles` function from `pendulum_nonlinear.ipynb` (a copy is provided in the starter notebook, with the angle wrapping removed). It returns the smallest period $p \le 32$ for which the section repeats to within `tol = 1e-3`, or 0 if there is none (i.e., chaos).

For each of $F_D = 0.20,\ 0.28,\ 0.29,\ 0.32$, overlay the Poincaré section on the phase-space trajectory, and annotate each plot with the number of cycles found by `count_cycles` (or "chaos").

### Problem 1c
*10 points*

1. Write a function that projects a Poincaré section onto one dimension (choose a sensible projection).
2. Write a function that loops over a set of $F_D$ values and assembles the projections into a bifurcation diagram.

Produce the bifurcation diagram for `F_D = np.linspace(0.25, 0.45, 401)`. For each new value of $F_D$, use the final state of the previous value as the initial state; you may then reduce `n_discard` to 50 and `n_periods` to 150. The full scan takes several minutes, so you may want to develop and debug with a coarser grid before launching the full grid.

Describe all the features you see in the diagram. At approximately which values of $F_D$ do you see period-doubling bifurcations? How about the onset of chaos, and any periodic windows inside the chaotic region? Use your plot of $V(x)$ from (a) to explain the sudden change near $F_D \approx 0.30$.

### Problem 1d (PHY505 only)
*10 points*

For each of the four values of $F_D$ from (b), sample $x(t)$ on a uniform time grid covering the last 64 drive periods, with 32 samples per period (2048 samples; no need for padding or windowing with this choice), using the dense-output solution `res.sol`. 

1. Plot the four time series.
2. Compute the power spectrum $|\tilde{x}(\omega)|^2$ of each time series with an FFT (subtract the mean first), and plot all four on a log scale, with the frequency axis in units of $\omega_D$.

Describe how the spectrum changes as the system goes from a 1-cycle to a 2-cycle, to a 4-cycle, and to chaos. Which new frequencies appear at each period doubling? 

---

\newpage

## Problem 2: perturbed harmonic oscillator
*25 points (+15 points PHY505)*

Do your work in the notebook `Problem2/Problem2.ipynb`. Starting from the shooting-method code in the `CompPhys/BVPs/schoedinger.ipynb` notebook, investigate a cubic and a quartic perturbation to the quantum harmonic oscillator. Work in units where $\hbar = m = \omega = 1$, so that the unperturbed energies are $E_n^{(0)} = n + \frac{1}{2}$, and use

$$
\begin{aligned}
V_1(x) &= \frac{1}{2} x^2 + \lambda x^3 \\
V_2(x) &= \frac{1}{2} x^2 + \lambda x^4
\end{aligned}
\qquad \text{with } \lambda = \frac{1}{40}.
$$

Be sure to increase the left and right boundaries if needed. Note, however, that $V_1(x) \to -\infty$ as $x \to -\infty$ (it has a barrier peaking near $x \approx -13$), so keep the left boundary well inside the barrier, e.g. $x > -10$.

### Problem 2a
*15 points*

Compute the five lowest energy levels ($n = 0$–$4$) and their eigenfunctions for the unperturbed oscillator, $V_1$, and $V_2$. Make sure your energies are converged to at least $10^{-4}$. Tabulate the energies, and plot the eigenfunctions for each potential.

### Problem 2b
*10 points*

Discuss how each type of perturbation modifies the energy levels (including the spacing between levels) and the eigenfunctions. 

### Problem 2c (PHY505 only)
*10 points*

Compare your numerical energies for $V_2$ with perturbation theory, to first and second order. Write $H = H_0 + H'$, with $H_0 = \frac{1}{2}p^2 + \frac{1}{2}x^2$ and $H' = \lambda x^4$. The first- and second-order energy shifts of the state $|n\rangle$ are

$$
E_n^{(1)} = \langle n | H' | n \rangle, \qquad
E_n^{(2)} = \sum_{m \neq n} \frac{|\langle m | H' | n \rangle|^2}{E_n^{(0)} - E_m^{(0)}} .
$$

**Hint:** The matrix elements are easiest to compute with the ladder operators. Writing $x = (a + a^\dagger)/\sqrt{2}$, with $a|n\rangle = \sqrt{n}\,|n-1\rangle$ and $a^\dagger|n\rangle = \sqrt{n+1}\,|n+1\rangle$, the matrix elements of $x$ in the oscillator basis are

$$
\langle m | x | n \rangle = \frac{1}{\sqrt{2}}\left(\sqrt{n}\,\delta_{m,n-1} + \sqrt{n+1}\,\delta_{m,n+1}\right).
$$

So you can build $x$ as an $N \times N$ matrix (indices $0, \dots, N-1$), and get the matrix of $x^4$ with matrix multiplication. Since $x^4$ only connects $|n\rangle$ to $|n \pm 2\rangle$ and $|n \pm 4\rangle$, a modest truncation such as $N = 20$ is exact for $n \le 4$. (You may also derive closed-form expressions instead, but this is not required.)

For $n = 0$–$4$, tabulate $E_n^{(0)} + E_n^{(1)}$, $E_n^{(0)} + E_n^{(1)} + E_n^{(2)}$, and your numerical result from (a). How does the agreement depend on $n$ and on the order of perturbation theory? Explain why perturbation theory works less well for larger $n$.

References for perturbation theory and the ladder operators:

- Griffiths & Schroeter, *Introduction to Quantum Mechanics*, 3rd ed.: §2.3.1 (ladder operators) and Ch. 7 (time-independent perturbation theory). In the 2nd edition, perturbation theory is Ch. 6.
- Sakurai & Napolitano, *Modern Quantum Mechanics*: §2.3 (harmonic oscillator) and §5.1 (non-degenerate perturbation theory).
- Shankar, *Principles of Quantum Mechanics*: Ch. 7 (harmonic oscillator) and Ch. 17 (time-independent perturbation theory).
- Wikipedia: [Perturbation theory (quantum mechanics)](https://en.wikipedia.org/wiki/Perturbation_theory_(quantum_mechanics)) and [Quantum harmonic oscillator, ladder operator method](https://en.wikipedia.org/wiki/Quantum_harmonic_oscillator#Ladder_operator_method).

### Problem 2d (PHY505 only)
*5 points*

For each of your normalized eigenfunctions of $V_1$ ($n = 0$–$4$), compute $\langle x \rangle$ and $\langle x^2 \rangle$ numerically. 

Ehrenfest's theorem states that $\frac{d\langle p \rangle}{dt} = -\langle V'(x) \rangle$. For a stationary state, $\langle p \rangle$ is constant, so $\langle V'(x) \rangle = 0$. For $V_1$, this implies $\langle x \rangle = -3\lambda \langle x^2 \rangle$. Verify this relation with your numerical eigenfunctions. Plot $\langle x \rangle$ versus $n$, and explain physically why the center of the wavefunction shifts in the direction it does, and why the shift grows with $n$.

---
