 
 *** Model Predictive Control ***
  
Self-Driving Car Engineer Nanodegree Program
---

Video Reference:

You can see how the car performs on an unseen track in a simulated environment here : 

### Track1: 

[![Alt text](https://img.youtube.com/vi/ZXKrKh6msiE/0.jpg)](https://www.youtube.com/watch?v=ZXKrKh6msiE)



# The MPC Model : 

Here, I'll describe the model in detail. This includes the state, actuators and update equations.

In this project, I have used Model Predictive Control (MPC) to drive the car around the track. The MPC minimize the cross track and orientation errors of the vehicle with respect to a line.The simulation output displays the MPC trajectory path in green, and the polynomial fitted reference path in yellow at the same time. The points get displayed in reference to the vehcle's coordinate system and the waypoints in reference to the map's coordinate system.The wayspoints are transformed to the vehicle's coordiate system.

The reference trajectory is passed as a third-degree polynomials since they can fit most roads.A controller actuates the vehicle to follow the reference trajectory.The controller minimizes the error between the reference trajectory and the vehicle’s actual path.The error is minimized predicting the vehicle’s actual path and then adjusting the actuators to minimize the difference between the prediction and the reference trajectory.The cross track error (cte) and the the psi error (epsi) which is angle between the vehicle orientation and trajectory orientation are minimized.I have set the speed to 40 mph and converted that to meters per second.



## State
The state is six elements vectors:
    
    State is [x,y,ψ,v,cte,eψ].

    x: the x-position
    y: the y-position
    ψ: the vehicle orientation
    v: the velocity
    cte: the cross trak error
    eψ : the the vehicle orientation error

## Actuators [δ, a]

We have two actuators: 

    δ (the steering angle) and
    a (acceleration, i.e. throttle and brake pedals).


## Update equations 

    x = x + v*cos(ψ)* dt
    y = y + v sin(psi) dt
    v = v+a∗dt * a in [-1,1]
    ψ = ψ+(v/L_f)*δ∗dt


# Timestep Length and Elapsed Duration (N & dt) : 

I assined 25 to N which is the timestep length, or the length of the trajectory and .1 to dt which is the duration of each timestep. 
I performed tuning with slightly different values but at the end I found this combination helps to decrease the CTE and drive the car around the track smoother. 

While trying a different combinations of N (8~50) and dt (0.05~1.0), I observed the varying magnitude of cross track error,erratic behaviour,shaky wheel and jerking to smooth manueveraround the track.When I tried with increased N and decreased dt.I then observed improved fit and smooth driving at the begining but it was less steady in curvature.At the end,I chose 25 for N and, 0.1 for dt.  



# Polynomial Fitting and MPC Preprocessing

I used Polyfit function to fit a 3rd order polynomial to the given x and y coordinates of waypoints. The coefficients were used to estimate cross track and desired orientation errors. 

The processing steps includes, 

```
>Converting waypoints to vehicle coordinates.
> Fitting a polynomial.
> Computing the initial cross-track and orientation errors.
> Defining the current state.
> Running the optimizer using MPC solver
> Setting the steering and throttle values.
```

The initial vehicle state, lower and upper limits for delta steeting angle and throttle,constraints were set in MPC class. Using these values MPC solver returned actuator values and MPC trajectory paths.

# Here is the MPC algorithm:

We pass the current state as the initial state to the model predictive controller.We call the optimization solver. Given the initial state, the solver returns the vector of control inputs that minimizes the cost function.The solverused is called Ipopt.We apply the first control input to the vehicle. The process gets repeated.



## Setup:

1. Define the length of the trajectory, N, and duration of each timestep, dt.

2. Define vehicle dynamics and actuator limitations along with other constraints.

3. Define the cost function.

Loop:

1. We pass the current state as the initial state to the model predictive controller.

2. We call the optimization solver. Given the initial state, the solver will return the vector of control inputs that minimizes the cost function. The solver we'll use is called Ipopt.

3. We apply the first control input to the vehicle.

4. Back to 1.



# Model Predictive Control with Latency

To mimic real driving conditions where the car does actuate the commands instantly, a 100 millisecond latency was incrporated in to the model.
In this model,We have incorporated an expected latency of 100 ms by predicting the car's state in 100 ms using the vehicle model.The resulting state from the simulation is the new initial state for MPC.



## Dependencies

* cmake >= 3.5
 * All OSes: [click here for installation instructions](https://cmake.org/install/)
* make >= 4.1
  * Linux: make is installed by default on most Linux distros
  * Mac: [install Xcode command line tools to get make](https://developer.apple.com/xcode/features/)
  * Windows: [Click here for installation instructions](http://gnuwin32.sourceforge.net/packages/make.htm)
* gcc/g++ >= 5.4
  * Linux: gcc / g++ is installed by default on most Linux distros
  * Mac: same deal as make - [install Xcode command line tools]((https://developer.apple.com/xcode/features/)
  * Windows: recommend using [MinGW](http://www.mingw.org/)
* [uWebSockets](https://github.com/uWebSockets/uWebSockets)
  * Run either `install-mac.sh` or `install-ubuntu.sh`.
