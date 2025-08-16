# Sensitivity Analysis Using Machine Learning Models

![GitHub repo header](https://placehold.co/1200x300/7e57c2/ffffff?text=Sensitivity+Analysis&font=inter)

In this repository, you will find MATLAB code for calculating the sensitivity of an objective function to a set of decision variables. The function evaluations, which can often be computationally expensive, are efficiently handled by pre-trained machine learning models.

## 📜 License

This project is publicly available and open source under the [MIT License](https://opensource.org/licenses/MIT). You are free to use, modify, and distribute this code for any purpose.

---

## 🔬 Methodology: Variance-Based Sensitivity Analysis

This project employs a **variance-based sensitivity analysis** method, specifically using a deterministic grid-based approach to approximate the first-order Sobol indices. This technique is a global sensitivity analysis method, which means it assesses the influence of input variables across their entire range of uncertainty.

The core idea is to decompose the total variance of the objective function's output into parts attributable to each input variable (and their interactions). The **first-order sensitivity index ($S_i$)** measures the direct effect of a single input variable, $x_i$, on the output variance. It quantifies the expected reduction in the output variance if we could fix the value of $x_i$.

### Formulation

The first-order sensitivity index for a variable $x_i$ is defined by the following formula:

$$S_i = \frac{V_{x_i}(E_{\mathbf{x}_{\sim i}}(Y | x_i))}{V(Y)}$$

Where:
- **$Y$** is the output of the objective function.
- **$V(Y)$** is the total variance of the output.
- **$x_i$** is the input variable of interest.
- **$\mathbf{x}_{\sim i}$** represents all input variables *except* $x_i$.
- **$E_{\mathbf{x}_{\sim i}}(Y | x_i)$** is the expected value (or mean) of the output $Y$ when the variable $x_i$ is fixed at a specific value. This effectively averages out the influence of all other variables.
- **$V_{x_i}(E_{\mathbf{x}_{\sim i}}(Y | x_i))$** is the variance of this conditional expectation, taken over all possible values of $x_i$. This term captures how much the *mean* of the output changes as we vary $x_i$.

In simple terms, $S_i$ represents the proportion of the total output variance that is directly caused by the variation in input $x_i$ alone. A higher $S_i$ value indicates that the objective function is more sensitive to changes in that variable.

The code implements this by:
1.  Creating a deterministic grid of points that covers the entire design space.
2.  Evaluating the objective function (using the machine learning models) at each point on the grid.
3.  Calculating the total variance $V(Y)$ of all the outputs.
4.  For each variable $x_i$, it groups the data by its discrete values, calculates the mean output for each group ($E(Y|x_i)$), and then finds the variance of these means ($V(E(Y|x_i))$).
5.  Finally, it computes $S_i$ by dividing $V(E(Y|x_i))$ by $V(Y)$.

---

## 🚀 How to Use

1.  **Prerequisites:** Ensure you have MATLAB installed with the Deep Learning Toolbox (to load the neural network models).

2.  **Load Models:** Make sure your pre-trained `.mat` files (`net_efficiency.mat`, `net_electromagnetic_torque.mat`, etc.) are in the `machine_learning_models` directory or in the MATLAB path.

3.  **Configure Parameters:** Open the main analysis script and adjust the following parameters at the top of the file:
    * `num_vars`: The number of decision variables you are analyzing.
    * `num_points_per_var`: The number of discrete points for each variable's range. A higher number increases accuracy but also significantly increases computation time.
    * `lower_bounds` and `upper_bounds`: The ranges for your decision variables.

4.  **Run the Script:** Execute the main sensitivity analysis script in MATLAB. The script will:
    * Generate the grid of sample points.
    * Evaluate the objective function for every sample point using the loaded models.
    * Calculate the sensitivity indices ($S_i$) for each variable.
    * Display the results in the command window.
    * Generate a bar chart visualizing the sensitivity indices.
    * Save the calculated sensitivity indices to a `.csv` file.

---

## 📂 File Structure

```
Sensitivity_analysis_using_machine_learning_models/
│
├── 📁 machine_learning_models/
│   ├── net_efficiency.mat
│   ├── net_electromagnetic_torque.mat
│   ├── net_magnet_mass.mat
│   └── net_torque_ripple.mat
│
├── 📁 Sensitivity_analysis/
│   └── (Main analysis script).m
│
└── 📄 README.md
```
* **`machine_learning_models/`**: This directory contains the pre-trained neural network models (`.mat` files) that act as surrogates for the actual objective function evaluations.
* **`Sensitivity_analysis/`**: This directory holds the main MATLAB script that performs the deterministic grid sensitivity analysis.
* **`README.md`**: This file.

---

## 📊 Results and Interpretation

The script produces two main outputs:

1.  **Bar Chart:** A plot titled "Sensitivity Indices S(x_i)" visually represents the sensitivity of the objective function to each decision variable. Taller bars indicate higher sensitivity.

2.  **CSV File:** A file (e.g., `S_Average_Torque.csv`) containing the raw numerical values of the sensitivity indices.

**How to interpret the results:** A variable with a high $S_i$ value contributes more to the output's variance. In a design or optimization context, this means that focusing on accurately setting and controlling this variable will have the most significant impact on achieving the desired outcome.
