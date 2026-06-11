Numerical Method for Moving-Boundary Charring/Ablation Heat Equation
====================================================================

This repository contains the Jupyter notebook used for the numerical experiments in the report.

Main notebook:
    numerical_method_part II.ipynb

The notebook develops and tests finite-difference methods for a one-dimensional heat conduction model with temperature-dependent conductivity, moving boundary effects, advection-like terms from coordinate transformation, and an additional density-loss term motivated by pyrolysis.


Project Description
-------------------

The main model is based on a transformed moving-boundary heat equation of the form

    T_t = 1/delta(t)^2 * d/dxi( K(T) T_xi )
          - xi*s'(t)/delta(t) * T_xi
          + Gamma/delta(t) * T_xi
          + C_rho * rho_t,

where

    delta(t) = L - s(t).

Here xi is the transformed computational coordinate in [0,1], while the physical coordinate is

    x = xi * (L - s(t)).

The notebook builds the model step by step, starting from simpler explicit finite-difference schemes and then extending to nonlinear implicit methods.


Contents of the Notebook
------------------------

1. Moving-boundary heat equation using explicit FTCS

   The notebook first applies a coordinate transformation to map the changing physical domain onto a fixed computational domain. This introduces an additional first-derivative term, which behaves like an advection term in the transformed coordinate.

2. Time-dependent conductivity K(t)

   The effect of a time-dependent thermal diffusivity is explored. This is used as an intermediate step before introducing temperature-dependent conductivity.

3. Temperature-dependent conductivity K(T)

   Several representative forms are tested, including

       K(T) = K0(1 + alpha*T),
       K(T) = K0/(1 + alpha*T),
       K(T) = K0 exp(alpha*T).

   The nonlinear diffusion term is discretised in conservative flux form:

       d/dx( K(T) T_x ).

4. Flux-form discretisation

   Conductivity is evaluated at cell faces:

       K_{i+1/2} = K( (T_i + T_{i+1})/2 ).

   The corresponding heat flux is approximated by

       q_{i+1/2} = K_{i+1/2}(T_{i+1}-T_i)/dx.

5. Moving boundary with K(T) and advection

   The nonlinear diffusion model is combined with the transformed moving-boundary advection term.

6. Gamma term

   An additional drift term Gamma/delta(t) * T_xi is included and combined with the moving-boundary advection velocity.

7. Explicit instability

   The notebook demonstrates that the explicit method becomes unstable when the effective diffusion number becomes too large, consistent with the condition

       max(K(T))*dt/dx^2 <= 1/2.

8. Implicit method

   To improve stability, the nonlinear problem is solved using an implicit backward Euler method with Picard iteration. The temperature-dependent conductivity is lagged from the previous Picard iterate, giving a sequence of linear systems:

       A(T^(m)) T^(m+1) = b.

9. Analytical validation

   The implicit nonlinear solver is tested against an analytical solution for

       T_t = d/dx( K(T) T_x ),  K(T) = aT.

   The maximum final-time error is reported in the notebook.

10. Neumann boundary condition

   The notebook discusses how to replace fixed-temperature Dirichlet boundary conditions with Neumann boundary conditions using ghost points or finite-difference boundary equations.

11. Additional density-loss term

   A simplified Arrhenius-type density-loss term is introduced:

       rho_t = -A rho exp(-E/(RT)).

   The density is updated using a lagged Arrhenius update, where the temperature is frozen at the previous time level.


Dependencies
------------

The notebook requires:

    Python 3.x
    NumPy
    Matplotlib
    Jupyter Notebook or JupyterLab

To install the required packages, run:

    pip install numpy matplotlib notebook

or, if using JupyterLab:

    pip install numpy matplotlib jupyterlab


Important Parameters
--------------------

Common parameters used in the notebook include:

    xi in [0,1]          transformed coordinate
    dxi = 0.01           spatial step size
    dt                   time step size
    L                    initial material thickness
    s(t)                 recession depth
    delta(t) = L - s(t)  remaining material thickness
    Ta, Tb               boundary temperatures
    T0                   initial temperature
    Gamma                additional drift coefficient
    K(T)                 temperature-dependent diffusivity/conductivity model
    rho                  material density
    A, E, R              Arrhenius parameters
    C_rho                coupling coefficient for density-loss source term


Notes on Apollo 4 / AVCOAT Data
-------------------------------

Some later sections use material parameters motivated by Apollo 4 AVCOAT heat-shield data, including:

    virgin density,
    char density,
    char-layer conductivity,
    char specific heat,
    recession-rate polynomial,
    Arrhenius density-loss parameters.

The notebook uses these values in a simplified model. It should not be interpreted as a full Apollo 4 thermal-response simulation, because the boundary condition is simplified and does not fully implement the surface energy balance from the original ablation model.


Output
------

The notebook generates plots including:

    temperature profiles in transformed coordinate xi,
    temperature profiles in physical coordinate x,
    remaining material length L - s(t),
    comparison between explicit and implicit methods,
    comparison between numerical and analytical solutions,
    effect of the additional rho_t source term,
    effect of Neumann boundary conditions.


Code Availability
-----------------

This notebook is intended to accompany the numerical-method section of the report. The full implementation is provided here so that the simulations and figures in the report can be reproduced.


Limitations
-----------

1. Several parts of the notebook are exploratory and compare different modelling assumptions.
2. Some physical parameters are simplified or nondimensionalised.
3. The fixed high-temperature boundary condition is a simplified forcing condition.
4. A full Apollo 4 AVCOAT model would require a surface heat-flux energy balance, radiation loss, blowing correction, and fully coupled material response.
5. The density-loss term is treated as a lagged source term rather than a fully coupled implicit solve.


Author
------

Zhezhong Ding