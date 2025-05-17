# Problem 1


# Investigating the Range as a Function of the Angle of Projection

---

## 1. Theoretical Foundation

### Governing Equations of Projectile Motion

We start from Newton’s Second Law for a projectile launched in a uniform gravitational field (ignoring air resistance):

$$
\mathbf{F} = m \mathbf{a}
$$

For a projectile of mass $m$:

* Horizontal direction ($x$): no acceleration (neglecting air resistance), so

$$
a_x = 0 \implies \frac{d^2 x}{dt^2} = 0
$$

* Vertical direction ($y$):

$$
a_y = -g \implies \frac{d^2 y}{dt^2} = -g
$$

---

### Initial Conditions

At $t = 0$:

$$
x(0) = 0, \quad y(0) = h
$$

$$
v_x(0) = v_0 \cos \theta, \quad v_y(0) = v_0 \sin \theta
$$

where

* $v_0$ is the initial speed,
* $\theta$ is the angle of projection,
* $h$ is the initial height,
* $g$ is gravitational acceleration.

---

### Solutions to the Equations

Solving the second order ODEs:

* Horizontal motion:

$$
x(t) = v_0 \cos \theta \cdot t
$$

* Vertical motion:

$$
y(t) = h + v_0 \sin \theta \cdot t - \frac{1}{2} g t^2
$$

---

### Time of Flight

The time when projectile hits the ground ($y=0$):

$$
0 = h + v_0 \sin \theta \cdot t - \frac{1}{2} g t^2
$$

Solve quadratic in $t$:

$$
t = \frac{v_0 \sin \theta + \sqrt{(v_0 \sin \theta)^2 + 2 g h}}{g}
$$

(Only the positive root is physically meaningful.)

---

### Horizontal Range

Using $t$ above, range $R$ is

$$
R(\theta) = x(t) = v_0 \cos \theta \times t
$$

This expression defines a *family of solutions* parametrized by $\theta$, $v_0$, $h$, and $g$.

---

## 2. Analysis of the Range

* The range depends on the angle $\theta$ via $\sin\theta$ and $\cos\theta$.
* For $h=0$, the well-known formula reduces to

$$
R = \frac{v_0^2}{g} \sin 2\theta
$$

which peaks at $\theta = 45^\circ$.

* When $h \neq 0$, the maximum range angle shifts.
* Changes in $v_0$ scale the range proportionally.
* Increasing $g$ reduces the range.

---

## 3. Practical Applications

* **Uneven terrain**: Can model $h\neq 0$.
* **Air resistance**: Requires adding drag force, typically non-linear; analytical solutions become complicated, often requiring numerical simulation.
* **Wind**: Adds horizontal acceleration component.
* **Sports and Engineering**: Ballistics, sports (soccer, basketball), launch vehicles, fireworks.

---

## 4. Implementation: Python Simulation & Visualization

```python
import numpy as np
import matplotlib.pyplot as plt

def time_of_flight(v0, theta, h, g=9.81):
    """
    Calculate time of flight until projectile hits ground y=0.
    
    Parameters:
        v0: initial speed (m/s)
        theta: launch angle in radians
        h: initial height (m)
        g: gravitational acceleration (m/s^2)
        
    Returns:
        time_of_flight (float)
    """
    term1 = v0 * np.sin(theta)
    discriminant = term1**2 + 2 * g * h
    t_flight = (term1 + np.sqrt(discriminant)) / g
    return t_flight

def range_projectile(v0, theta, h, g=9.81):
    """
    Calculate horizontal range of projectile.
    
    Parameters:
        v0: initial speed (m/s)
        theta: launch angle in radians
        h: initial height (m)
        g: gravitational acceleration (m/s^2)
        
    Returns:
        horizontal range (float)
    """
    t_flight = time_of_flight(v0, theta, h, g)
    return v0 * np.cos(theta) * t_flight

# Parameters
v0 = 20  # m/s
h = 0    # initial height in meters
g = 9.81 # m/s^2

angles_deg = np.linspace(0, 90, 500)
angles_rad = np.radians(angles_deg)

ranges = np.array([range_projectile(v0, angle, h, g) for angle in angles_rad])

# Plot
plt.figure(figsize=(10,6))
plt.plot(angles_deg, ranges)
plt.title("Projectile Range vs Launch Angle")
plt.xlabel("Launch Angle (degrees)")
plt.ylabel("Range (meters)")
plt.grid(True)
plt.show()
```

---


![graphic](../images/m1_1.png)


### Exploring Parameters

You can vary $v_0$, $h$, and $g$ and plot multiple curves on the same graph:

```python
h_values = [0, 5, 10]  # Different launch heights

plt.figure(figsize=(10,6))

for h_i in h_values:
    ranges = np.array([range_projectile(v0, angle, h_i, g) for angle in angles_rad])
    plt.plot(angles_deg, ranges, label=f'h = {h_i} m')

plt.title("Projectile Range vs Launch Angle for Different Launch Heights")
plt.xlabel("Launch Angle (degrees)")
plt.ylabel("Range (meters)")
plt.legend()
plt.grid(True)
plt.show()
```

---

![graphic](../images/m1_2.png)

## 5. Discussion on Limitations and Extensions

* **Limitations of the Idealized Model:**

  * Ignores air resistance which can significantly reduce range and alter optimal angle.
  * Assumes uniform gravitational field.
  * Assumes flat ground at final landing spot (can be adapted for uneven terrain but complex).

* **Possible Extensions:**

  * **Air Drag:** Model drag force $\mathbf{F}_d = -\frac{1}{2} C_d \rho A v^2 \hat{v}$, numerical integration required.
  * **Wind Effects:** Add horizontal wind velocity component.
  * **Variable Gravity:** For long-range trajectories (e.g., missiles, rockets), gravity changes with altitude.
  * **Spin & Magnus Effect:** For sports balls.

---

# Summary

* Derived general projectile motion equations from first principles.
* Obtained formula for range as function of launch angle, initial velocity, initial height, and gravity.
* Demonstrated Python simulation with visualizations.
* Discussed real-world applicability and model limitations.
