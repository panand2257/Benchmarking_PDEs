# Benchmarking_PDEs
This is a final project for the course offered by MIT - Deep Learning (6.7960)

<!DOCTYPE html>
<html lang="en">
<head>
    <script>
        window.MathJax = {
            tex: {
                inlineMath: [['$', '$'], ['\\(', '\\)']],
                displayMath: [['$$', '$$'], ['\\[', '\\]']],
                processEscapes: true
            }
        };
    </script>
    <script src="js/MathJax/es5/tex-chtml.js" id="MathJax-script"></script>
    
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta name="description" content="A formal AI-focused blogpost for final project presentation.">
    <meta property="og:title" content="PDE Benchmarking">
    <title>Benchmarking of Maxwell's and Schrodinger's Equation</title>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/mathjax/3.2.2/es5/tex-mml-chtml.min.js"></script>
    <link rel="shortcut icon" href="images/icon.ico">
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0/css/all.min.css">
    <style>
        /* Fonts & Global Styles */
        @import url('https://fonts.googleapis.com/css2?family=Lato:wght@400;700&display=swap');

        body {
            font-family: 'Lato', sans-serif;
            background-color: #ffffff;
            color: #333333;
            margin: 0;
            padding: 0;
            line-height: 1.8;
        }

        a {
            color: #007BFF;
            text-decoration: none;
            font-weight: bold;
            transition: color 0.3s ease-in-out;
        }

        a:hover {
            color: #0056b3;
        }

        /* Header Styling */
        .header {
            text-align: center;
            padding: 40px 20px;
            background-color: #f8f9fa;
            border-bottom: 1px solid #eaeaea;
        }

        .header h1 {
            font-size: 2.5rem;
            margin: 0;
            color: #333333;
        }

        .header p {
            font-size: 1rem;
            margin: 5px 0;
            color: #555555;
        }

        /* Main Content Layout */
        .content-container {
            max-width: 1200px;
            margin: 20px auto;
            padding: 20px;
        }

        .content-container h2 {
            font-size: 2rem;
            margin-bottom: 20px;
            color: #333333;
            border-bottom: 2px solid #007BFF;
            padding-bottom: 10px;
        }

        .content-container p {
            font-size: 1rem;
            color: #444444;
            margin-bottom: 20px;
        }

        .content-container iframe, .content-container img {
            display: block;
            margin: 20px auto;
            max-width: 100%;
        }

        /* Footer Styling */
        footer {
            text-align: center;
            padding: 20px;
            font-size: 0.9rem;
            color: #666666;
            background-color: #f8f9fa;
            border-top: 1px solid #eaeaea;
        }
        .date {
            font-size: 0.9rem;
            color: #666;
            margin-bottom: 20px;
        }
        section#References {
            margin-top: 40px;
        }
        section#References h2 {
            border-bottom: 2px solid #007BFF;
            padding-bottom: 10px;
            margin-bottom: 20px;
        }
        section#References ol {
            padding-left: 20px;
        }
        section#References li {
            margin-bottom: 10px;
        }
    </style>
</head>
<body>

    <header class="header">
        <h1>Neural Architectures for PDEs: A Comparative Benchmark</h1>
        <p>
            <a href="https://scholar.google.com/citations?user=gOHWuaMAAAAJ&hl=en" target="_blank">Pratyush Anand (MIT ID: 913509161)</a> |
            <a href="https://scholar.google.com/citations?user=_03ge3YAAAAJ&hl=en" target="_blank">Louis Follet (MIT ID: 964344306)</a>
        </p>
        <p>Final Project | MIT Deep Learning 6.7960</p>
        <p class="date" id="blogDate">Dec 10, 2024</p>
    </header>

    <div class="content-container">
        <section id="Abstract">
            <h2>Abstract</h2>
            <p>
                Partial differential equations (PDEs), such as Maxwell’s and Schrödinger’s equations are critical for modeling electromagnetic systems (e.g., antenna design) and quantum systems (e.g., quantum computing). Traditional numerical solvers, like finite-difference time-domain (FDTD) methods for electromagnetic simulations or quantum master equation solvers for quantum systems, are computationally expensive, particularly for high-dimensional problems like multi-qubit quantum systems. Recent advances in machine learning (ML) have opened the door to efficient PDE solvers that maintain interpretability while reducing computational costs.

                In this work, we benchmark three neural architectures—Fourier Neural Operator (FNO), Neural ODE (N-ODE) and Kolmogorov-Arnold Networks ODE (KAN-ODE) on two different PDEs. Our evaluation systematically examines scaling laws, computational efficiency, and robustness to new testing conditions, highlighting the strengths and limitations of each approach. This study provides insights into how neural architectures can complement traditional solvers and guide future developments in ML-based PDE modeling.</p>
        </section>

        <section id="intro">
            <h2>Introduction</h2>
            <p>
                PDEs are essential for describing complex physical phenomena across various domains of science and engineering <a href="#ref1">[1]</a><a href="#ref2">[2]</a><a href="#ref3">[3]</a>. From modeling electromagnetic wave propagation to quantum systems, PDEs provide the mathematical foundation for advancing technologies such as antenna design, circuit optimization, and quantum computing. Traditional solvers, such as FDTD methods for electromagnetism and quantum master equation solvers for quantum systems, rely on discretizing spatial and temporal domains. While accurate, these methods are computationally expensive, especially for problems involving high-dimensional systems, such as multi-qubit quantum systems, where computational costs scale exponentially with the system size.          </p>
        
            <p> 
                The growing interest in ML has introduced the possibility of using neural networks as efficient surrogates for PDE solvers. Unlike traditional solvers, ML-based methods promise significant computational speedups while offering competitive accuracy. However, interpretability and robustness remain key challenges for deploying these models in scientific applications.
            </p>

            <p>
                Our study aims at answering the following question: <strong> What neural-based architectures are best suited for solving PDEs in terms of accuracy, generalization, and efficiency?</strong>
            </p>

            <p>
                We investigate three neural architectures— FNO<a href="#ref4">[4]</a><a href="#ref5">[5]</a>, N-ODE<a href="#ref6">[6]</a><a href="#ref7">[7]</a>, and KAN-ODE <a href="#ref8">[8]</a><a href="#ref9">[9]</a><a href="#ref10">[10]</a>—for solving PDEs. These models represent diverse approaches to learning PDE dynamics, each with unique strengths: FNO leverages<a href="#ref8">[11]</a><a href="#ref8">[12]</a> global Fourier transforms, Neural ODE provides a continuous-time framework, and KAN-ODE employs universal function approximators inspired by the Kolmogorov-Arnold theorem.
            </p>

            <p>
                To assess their effectiveness, we benchmark these architectures on two canonical PDEs: Maxwell’s equations<a href="#ref13">[13]</a> and the Schrödinger equation. Maxwell’s equations, foundational to electromagnetism, challenge solvers with their intricate wave dynamics and boundary conditions. The Schrödinger equation, central to quantum mechanics, presents additional complexities with its complex-valued solutions and potential nonlinearities. By comparing their performance on scaling laws, computational efficiency, and generalization to new initial conditions, we aim to identify the capabilities and limitations of each model. Our findings contribute to the broader effort to develop efficient, interpretable, and accurate ML-based solvers for scientific and engineering applications.
            </p>
        </section>

        <section id="Methods">
            <h2>Methods</h2>
        
            <h3>Neural Architectures</h3>
            <div style="border: 1px solid #ccc; padding: 20px; background-color: #f9f9f9; border-radius: 8px;">
                <h4><u>(a) Fourier Neural Operator (FNO)</u></h4>
            
                <p>
                    The <strong>FNO</strong>, introduced by <strong>Li et al. (2020)</strong><a href="#ref4">[4]</a>, is designed to learn mappings between function spaces, making it suitable for solving PDEs where inputs and outputs are continuous functions. Unlike traditional neural networks, which operate on fixed-dimensional vectors, <strong>FNO</strong> works in the function space and generalizes to different resolutions.
                </p>
            
                <p>
                    The <strong>FNO</strong> architecture consists of three main steps: <strong>lifting</strong>, applying multiple <strong>Fourier layers</strong>, and <strong>projecting</strong> back to the target function space, as shown in Fig. A.
                </p>
            
                <p>
                    First, the input function $a(x)$ is mapped to a higher-dimensional feature space using a learnable linear transformation $P$:
                </p>
            
                <p>
                    $$v_0(x) = P(a(x)).$$
                </p>
            
                <p>
                    In each <strong>Fourier layer</strong>, the lifted feature $v_i(x)$ undergoes a <strong>Fourier Transform</strong> $\mathcal{F}$, a learnable <strong>spectral transformation</strong> $R$ in the frequency domain, and an <strong>Inverse Fourier Transform</strong> $\mathcal{F}^{-1}$:
                </p>
            
                <p>
                    $$v_{i+1}(x) = \mathcal{F}^{-1}(R \cdot \mathcal{F}(v_i))(x) + W(v_i(x)).$$
                </p>
            
                <p>
                    Finally, the output of the last Fourier layer $v_T(x)$ is projected back to the target function space using a learnable transformation $Q$:
                </p>
            
                <p>
                    $$u(x) = Q(v_T(x)).$$
                </p>
            
                <figure style="text-align: center;">
                    <img src="images/FNO_picture.png" alt="FNO Visualization" style="max-width: 100%; height: auto;">
                    <figcaption style="font-size: 0.9em; color: #555; margin-top: 8px;">
                        Figure A: <strong>FNO architecture<a href="#ref4">[4]</a>.</strong> (a) Illustrating the lifting step, multiple Fourier layers, and projection back to the target function space. (b) Detailed view of a Fourier layer, including the Fourier Transform, spectral transformation, Inverse Fourier Transform, and local linear transformation.
                    </figcaption>
                </figure>
                

                <p>
                    In our study, we vary two key parameters of the FNO. The <strong>number of Fourier modes</strong> retained in the spectral transformation $R$ controls the frequency resolution by determining how many frequency components are learned. The <strong>width</strong> of the network, defined by the dimensionality of the lifted feature space, affects the capacity of the model to capture complex patterns in the data.
                </p>


                <h4><u>(b) Kolmogorov Arnold Networks ODE (KAN-ODE)</u></h4>

                <p>
                    The <strong>KAN-ODE</strong>, introduced by <strong>Koenig et al. (2024)</strong><a href="#ref8">[8]</a>, extends KAN<a href="#ref8">[9]</a> to model the dynamics of ordinary differential equations (ODEs). KAN-ODEs are based on the <strong>Kolmogorov-Arnold representation theorem</strong>, which states that any multivariate continuous function can be represented as a combination of univariate functions and summations. This structure allows KAN to express complex functions through learnable univariate functions.
                </p>
            
                <p>
                    Given a state vector \( \mathbf{u}(t) \), the dynamics are modeled as:
                </p>
            
                <p>
                    $$\frac{d\mathbf{u}(t)}{dt} = \text{KAN}(\mathbf{u}(t), t; \theta),$$
                </p>
            
                <p>
                    where \( \theta \) represents the learnable parameters of the univariate functions within the KAN. The solution to the ODE is obtained by integrating these dynamics over time:
                </p>
            
                <p>
                    $$\mathbf{u}(t) = \mathbf{u}_0 + \int_{t_0}^t \text{KAN}(\mathbf{u}(\tau), \tau; \theta) \, d\tau.$$
                </p>
            
                <p>
                    In KAN, the function is decomposed into sums of univariate functions:
                </p>
            
                <p>
                    $$\text{KAN}(x_1, x_2, \ldots, x_d) = \sum_{q=1}^{2d+1} \phi_q\left(\sum_{j=1}^d \psi_{qj}(x_j)\right),$$
                </p>
            
                <p>
                    where \( \phi_q \) and \( \psi_{qj} \) are learnable univariate functions.
                </p>
            
                <p>
                    The training process for KAN-ODE involves solving the ODE forward in time and using <strong>adjoint sensitivity analysis</strong> to compute gradients for backpropagation. Fig. B illustrates this process: the forward solve computes the state trajectory \( \mathbf{u}(t) \), and the backward solve computes gradients of the loss function with respect to the parameters \( \theta \).
                </p>


                <figure style="text-align: center;">
                    <img src="images/KANODE_picture.png" alt="FNO Visualization" style="max-width: 100%; height: auto;">
                    <figcaption style="font-size: 0.9em; color: #555; margin-top: 8px;">
                        Figure B: <strong>Schematic of the KAN-ODE training cycle<a href="#ref8">[8]</a>.</strong> The green loop represents the forward solve for the state vector u(t) using a KAN. The blue loop shows the backward solve via adjoint sensitivity analysis, computing the gradients of the loss function to update the KAN parameters.
                    </figcaption>
                </figure>

            
                <p>
                    In this work, we explore how the performance of KAN-ODE is influenced by two key parameters. The <strong>number of discretization points</strong> controls how finely the input space is sampled for the univariate functions, affecting the level of detail captured. Additionally, the <strong>number of nodes</strong> in the hidden layers determines the model's capacity to represent complex dynamics.
                </p>



                <h4><u>(c) Neural ODE (N-ODE)</u></h4>

                <p>
                    The <strong>Neural ODE</strong> model, introduced by <strong>Chen et al. (2018)</strong><a href="#ref6">[6]</a>, was the first proposed framework to explicitly formulate the transformation of a hidden state in a neural network as a continuous-time ODE. This approach offers a continuous-depth perspective, where the dynamics of the hidden state are governed by an ODE rather than a sequence of discrete transformations.
                </p>

                <p>
                    Given a hidden state \( \mathbf{h}(t) \), the dynamics are described by:
                </p>

                <p>
                    $$\frac{d\mathbf{h}(t)}{dt} = f(\mathbf{h}(t), t; \theta),$$
                </p>

                <p>
                    where \( f \) is a neural network parameterized by \( \theta \). The hidden state at a later time \( t_1 \) is obtained by integrating the ODE from an initial time \( t_0 \):
                </p>

                <p>
                    $$\mathbf{h}(t_1) = \mathbf{h}(t_0) + \int_{t_0}^{t_1} f(\mathbf{h}(t), t; \theta) \, dt.$$
                </p>

                <p>
                    In Neural ODEs, the function \( f \) typically consists of a series of fully connected layers with nonlinear activation functions (e.g., ReLU). This formulation can be viewed as the continuous-depth limit of residual networks<a href="#ref13">[13]</a> (ResNets), where the depth of the network becomes a continuous variable.
                </p>

                <p>
                    Training Neural ODEs relies on the <strong>adjoint sensitivity method</strong> to compute gradients, similar to the process illustrated in Fig. B. The forward solve integrates the ODE to compute the hidden state trajectory, while the backward solve computes gradients of the loss function with respect to the parameters \( \theta \).
                </p>

                <p>
                    In this study, we investigate how the performance of Neural ODEs is influenced by the <strong>number of neurons in the hidden layers</strong> of \( f \). This parameter determines the network’s capacity to model the dynamics of the system.
                </p>



            </div>

            <h3>Problem Setup</h3>
            <div style="border: 1px solid #ccc; padding: 20px; background-color: #f9f9f9bb; border-radius: 8px;">
            <h4><u>Maxwell's Equation</u></h4>
                <p>
                    To evaluate the performance of neural network-based PDE solvers, we simulate the 1D Maxwell's equations governing the coupled dynamics of electric (\(E_y\)) and magnetic (\(H_z\)) fields:
                </p>
                
                <div class="equation">
                    <!-- Maxwell's Equations -->
                    <p>
                        $$\frac{\partial E_y}{\partial x} = -\mu(x) \frac{\partial H_z}{\partial t}$$
                    </p>
                    <p>
                        $$\frac{\partial H_z}{\partial x} = -\varepsilon(x) \frac{\partial E_y}{\partial t}$$
                    </p>
                </div>
                
                <p>
                    Here, \(\mu(x)\) and \(\varepsilon(x)\) represent the spatially varying permeability and permittivity, respectively. For simplicity, we consider constant values of \(\mu\) and \(\varepsilon\) in this implementation.
                    To generate the ground truth values, we numerically solve the above equations using the Runge-Kutta method of order 5(4) with the following parameters:</p>
            <ul>
                <li><b>Spatial domain (\(x\)):</b> \(x \in [-5.0, 5.0]\), discretized into \(\Delta x = 0.1\).</li>
                <li><b>Temporal domain (\(t\)):</b> \(t \in [0.0, 5.0]\), discretized into \(\Delta t = 0.01\).</li>
                <li><b>Boundary conditions:</b> Perfect Electric Conductor (PEC), ensuring \(E_y = 0\) at the boundaries.</li>
                <li><b>Initial conditions:</b>
                    <ul>
                        <li>\(E_y(x, 0)\): A Gaussian pulse centered at \(x = 0\).</li>
                        <li>\(H_z(x, 0)\): Zero everywhere.</li>
                    </ul>
                </li>
            </ul>
            
                    </ul>
                </li>
            </ul>


            <h4><u>Schrödinger's Equation</u></h4>

            <p>We also simulate the 1D nonlinear Schrödinger equation governing the evolution of the complex wave function \( u(t, x) \):</p>

                <div>
                    \[
                    i \frac{\partial u}{\partial t} + \frac{1}{2} \frac{\partial^2 u}{\partial x^2} + |u|^2 u = 0,
                    \]
                </div>

            <p>Here, \( |u|^2 = u_\text{real}^2 + u_\text{imag}^2 \) represents the wave intensity.</p>
            <p>\( u(t, x) \) is a complex-valued function of space and time, with real and imaginary components. This equation describes wave propagation in nonlinear media, such as Bose-Einstein condensates and nonlinear optics.</p>
                To generate the ground truth values, we numerically solve the above equation using the Runge-Kutta method of order 5(4) with the following parameters:</p>
            <ul>
                <li><b>Spatial domain (\( x \)):</b> \( x \in [-5.0, 5.0] \), discretized into \( \Delta x = 0.1 \).</li>
                <li><b>Temporal domain (\( t \)):</b> \( t \in [0.0, 5.0] \), discretized into \( \Delta t = 0.01 \).</li>
                <li><b>Boundary conditions:</b>
                    <ul>
                        <li>Periodic boundary conditions (\( u(-5, t) = u(5, t) \)).</li>
                        <li>Neumann boundary conditions (\( \frac{\partial u}{\partial x}(-5, t) = \frac{\partial u}{\partial x}(5, t) \)).</li>
                    </ul>
                </li>
                <li><b>Initial conditions:</b>
                    <ul>
                        <li>\( u(x, 0) = 2\text{sech}(x) \), a hyperbolic secant wave packet.</li>
                    </ul>
                </li>
            </ul>

            </div>

            <h3>Training Setup</h3>
            <div style="border: 1px solid #ccc; padding: 20px; background-color: #f9f9f9; border-radius: 8px;">

                <h4><u>Training Parameters</u></h4>
                <p>The three neural architecture models—FNO, KAN-ODE, and Neural-ODE—were trained on data sampled at discrete time steps (\( \Delta t \)) from the following range:</p>
                <div>
                    \[
                    \Delta t_\text{train} = [0.1, 0.3, 0.5, 0.7, 0.9, 1.1, 1.3, 1.5]
                    \]
                </div>
                <p>The goal was to learn the evolution of the solutions effectively across varying temporal resolutions. The training hyperparameters for each model are detailed below.</p>

                <h4><u>Model-Specific Training Configurations</u></h4>

                <h4>(a) FNO</h4>
                <p>The FNO was trained for 10,000 epochs with a learning rate (\( lr \)) of \( 1 \times 10^{-3} \). The key tunable hyperparameters included:</p>
                <ul>
                    <li><b>Modes (\( M \)):</b> Ranging from \( [2, 4, 8, 16, 32] \), where \( M \leq N/2 + 1 \) (with \( N = 51 \)).</li>
                    <li><b>Width of Layers:</b> Ranging from \( [32, 64, 128] \).</li>
                </ul>
                <p>The mean squared error (MSE) loss function was used to minimize the error between the predicted and ground truth solutions.</p>

                <h4>(b) KAN-ODE</h4>
                <p>KAN-ODE was trained for 1,000 epochs with a learning rate (\( lr \)) of \( 1 \times 10^{-2} \). The architecture involved:</p>
                <ul>
                    <li><b>KAN Grid (\( KAN_\text{grid} \)):</b> Ranging from \( [5, 10, 15] \).</li>
                    <li><b>KAN Nodes (\( KAN_\text{node} \)):</b> Ranging from \( [10, 20, 30] \).</li>
                </ul>
                <p>The MSE loss was applied to optimize the model, focusing on capturing the spatial and temporal dynamics effectively.</p>

                <h4>(c) Neural ODE</h4>
                <p>The Neural ODE was trained for 1,000 epochs with a learning rate (\( lr \)) of \( 1 \times 10^{-2} \). The architecture included:</p>
                <ul>
                    <li><b>Hidden Dimensions:</b> Ranging from \( [50, 75, 100, 125, 150, 175, 200, 225, 250] \).</li>
                </ul>
                <p>As with the other models, the MSE loss function was used to evaluate and update the model during training.</p>

                <h4><u>Evaluation Metrics</u></h4>
                <p>To compare the performance of the trained models, the following metrics were used:</p>
                <ul>
                    <li><b>Training Time:</b> Time taken to train the models across all epochs.</li>
                    <li><b>Inference Time:</b> Time required to generate predictions for a given dataset.</li>
                    <li><b>Number of Parameters:</b> Total trainable parameters in the model.</li>
                    <li><b>Minimum Training Loss:</b> The lowest loss achieved during training.</li>
                </ul>

                <h4><u>Testing Datasets</u></h4>
                <p>After training, the models were tested on three distinct datasets to evaluate their generalization capabilities:</p>
                <ul>
                    <li><b>Test Set A (Interpolated Time):</b> Time steps within the range used for training (\( \Delta t_\text{train} \)).</li>
                    <li><b>Test Set B (Extrapolated Time):</b> Time steps outside the range used during training.</li>
                    <li><b>Test Set C (Change in Initial Condition):</b> Data with initial conditions differing from the training dataset.</li>
                </ul>
                <p>The performance on these test sets provided insights into each model’s ability to generalize and adapt to unseen scenarios, including interpolations, extrapolations, and variations in initial conditions.</p>
            
                <h4><u>Computational Setup</u></h4>
                <p>All models were trained on a machine with:</p>
                <ul>
                    <li><b>Hardware:</b> Tesla T4 GPU with CUDA cores: 2560, Tensor cores: 320, VRAM: 16 GiB.</li>
                    <li><b>Software Framework:</b> PyTorch</li>
                    <li><b>Training Time:</b> Approximately 1-2 hours per model, depending on the dataset size and architecture.</li>
                </ul>
            </div>
    
        

        <section id="Analysis">
            <h2>Results and Analysis</h2>
            <h3>Maxwell's Equations</h3>
            <div style="border: 1px solid #ccc; padding: 20px; background-color: #f9f9f9; border-radius: 8px;">
                <h4><u>Field Visualization</u></h4>
            <p>
                Fig. 1a, 1b and 1c show the ground truth and predicted E-field on the full time-scale for FNO (Mode = 8, Width = 32), N-ODE (Hidden dimension = 250) and KAN-ODE (Grid = 5, Node = 10)
                respectively. If we focus on the three predictions, the ticks on the time-scale correspond to the training times, with the
                dashed black line corresponding to the last training time. Thus, prediction on the left (right) of the dashed line corresponds to
                interpolation (extrapolation) test. For the FNO prediction, we observe that the model doesn't learn much of temporal evolution in the extrapolation 
                regime. In terms of temporal evolution, N-ODE and KAN-ODE do better as model learns that E-field changes in the
                extrapolated time but they show some noisy signature towards the later time. The error contour clearly shows a boundary
                at the training end time, and the contrast between interpolation and extrapolation error is highest in FNO, whereas 
                for N-ODE and KAN-ODE the distinction is not as visible.

            </p>

            <h4><u>Scaling Laws and Computation Time</u></h4>
            <p>
                We train the three model for different sets of hyperparameter configurations as mentioned in the training section. 
                Fig 2a, 2b, and 2c show the scaling laws for the three models. Given that \(N\) is the number of parameters in a model, for FNO, we observe a scaling law of \(1/N^{2-3}\) with the minimum training loss ranging from 
                \(10^{-12}-10^{-7}\).  For N-ODE, we observe a scaling law of \(\sim 1/N^{1.3}\) with the minimum training loss being around
                \(10^{-7}\). For KAN-ODE, we observe a scaling law of \(1/N^{2.5-6}\) with the minimum training loss being around
                \(10^{-7}-10^{-5}\). From fig 2d, 2e, and 2f, we can observe that for roughly the same number parameters for the three models, the respective training times for N-ODE and KAN-ODE is about 4 times and 8 times larger than
                that of FNO. Similarly the respective inference times for N-ODE and KAN-ODE is about 70 times and 100 times larger than
                that of FNO. The main reason for N-ODE and KAN-ODE being computationally expensive in compared to FNO is that during forward pass 
                they implement an ODE solver at each epoch.

            </p>

            <h4><u>Testing Loss</u></h4>
            <p>
                As mentioned in the previous section, we test the trained model on the following three different test cases:  
                <ul>
                    <li><b>Test Set A:</b> (Prediction at interpolated time) <code>numpy.arange(0,1.6,0.2)</code></li>
                    <li><b>Test Set B:</b> (Prediction at extrapolated time) <code>numpy.arange(1.6,3,0.2)</code></li>
                    <li><b>Test Set C:</b> (Prediction at training timesteps with changed intial E-field) Gaussian field centered at x = 2 (and not x = 0)</li>
                </ul>
            </p>
            From Fig. 3a, we observe that for FNO, the interpolation test loss converges to \(10^{-5}\) while the extrapolation test loss converges around \(10^{-2}\). We also 
            observe that the FNO is not able to perform well with the changing initial conditions. From Fig. 3b, we observe that even for N-ODE, the interpolation test loss converges to \(10^{-5}\) while the extrapolation test 
            loss converges around \(10^{-2}\). Both N-ODE and KAN-ODE perform roughly ten times better than FNO on initial conditions test part. 


            <iframe src="images/Maxwell_field.png" width="700%" height="1400px" frameborder="0"></iframe>
            </div>
            <h3>Schrödinger's Equation</h3>
            <div style="border: 1px solid #ccc; padding: 20px; background-color: #f9f9f9; border-radius: 8px;">
                <h4><u>Field Visualization</u></h4>
                <p>
                    Fig. 4a, 4b and 4c show the ground truth and predicted real part of the wavefunction u(x,t) on the full time-scale for FNO (Mode = 8, Width = 32), N-ODE (Hidden dimension = 75) and KAN-ODE (Grid = 5, Node = 10)
                    respectively. As mentioned earlier, the ticks on the time-scale correspond to the training times, with the
                    dashed black line corresponding to the last training time. In contrast to Maxwell's equation, for the FNO prediction of Schrodinger's equation, we observe that the model does indeed learn much of the temporal periodic evolution in the extrapolation 
                    regime. In terms of the temporal evolution, N-ODE and KAN-ODE perform worse than FNO and they even show some noisy signature towards the later time. The error contour plot show similar
                    trend as that for the previous case.
    
                </p>
    
                <h4><u>Scaling Laws and Computation Time</u></h4>
                <p> 
                    Fig 5a, 5b, and 5c show the scaling laws for the three models. For FNO, we observe a scaling law of \(1/N^{1-1.5}\) with the minimum training loss ranging from 
                    \(10^{-13}-10^{-5}\).  For N-ODE, we observe a scaling law of \(\sim 1/N^{0.5}\) with the minimum training loss being around
                    \(10^{-5}\). For KAN-ODE, it's a bit tricky to deduce the scaling law as there might be some over-fitting issues. From fig 5d, 5e, and 5f, we can observe that for roughly the same number parameters for the three models, the respective training times for N-ODE and KAN-ODE is about 70 times and 1000 times larger than
                    that of FNO. Similarly the respective inference times for N-ODE and KAN-ODE is about 1500 times and 2000 times larger than
                    that of FNO. <b>Here, we observe that for the same model, depending on the complexity of the PDE, different evaluation metrics scale differently.</b>
    
                </p>
    
                <h4><u>Testing Loss</u></h4>
                <p>
                    As mentioned in the previous section, we test the trained model on the three different test cases for which test set A, and B are same as before, but test set C is given by:  
                    <ul>
                        <li><b>Test Set A:</b> Same as before</li>
                        <li><b>Test Set B:</b> Same as before</li>
                        <li><b>Test Set C:</b> (Prediction at training timesteps with changed intial amplitude) \( u(x, 0) = 1.8\text{sech}(x) \)</li>
                    </ul>
                </p>
                From Fig. 6a, 6b and 6c, we observe that for N-ODE performs the best in terms of interpolation test loss (\(\sim 3\times10^{-3}\)) and KAN-ODE performs the worst (\(\sim 3\times10^{-2}\)). We see a similar trend for extrapolation test loss.
                We also observe that none of the three models is  able to perform well with the changing initial conditions. <b>It is very interesting that depending upon the 
                inherent symmetries/structure of the PDEs, some architectures might be more effective at learning than others.</b>
    
    
            <iframe src="images/Schrodinger_field.png" width="700%" height="1400px" frameborder="0"></iframe>
            </div>
        </section>


        <section id="Conclusion">
            <h2>Conclusion</h2>

        
            <p>
                In this study, we benchmarked three architectures — <strong>Fourier Neural Operator (FNO)</strong>, <strong>Neural ODE</strong>, and <strong>Kolmogorov-Arnold Network ODE (KAN-ODE)</strong> — on solving <strong>Maxwell’s and Schrödinger’s equations</strong>. To facilitate a fair comparison, we selected configurations for these models with a comparable number of parameters (~30,000) for the results summarized in the Table below.
            </p>
        
            <p>
                Our findings show that:
            </p>
        
            <ul>
                <li>
                    <strong>FNO</strong> achieves the <strong>fastest training and inference times</strong> and performs well on interpolation tasks. However, it struggles to generalize to <strong>unseen initial conditions</strong>, indicating limitations in capturing variations beyond the training distribution.
                </li>
                <li>
                    <strong>Neural ODE</strong> provides <strong>robust interpolation performance</strong> and handles different initial conditions better than FNO. 
                </li>
                <li>
                    <strong>KAN-ODE</strong> offers a balance between <strong>performance and flexibility</strong>, performing slightly better on both interpolation and unseen initial conditions. However, it requires careful tuning of the <strong>number of grid points</strong> and <strong>nodes</strong>, and its training time is longer compared to FNO.
                    It is also the <strong>most computationally expensive</strong>, with significantly longer training and inference times.
                </li>
            </ul>
        
            <!-- <h4>Key Insight</h4> -->
            <p>
                Neural operator-based architectures like <strong>FNO</strong> (introduced by <strong>Li et al. 2020</strong><a href="#ref4">[4]</a>) demonstrate <strong>superior computational efficiency</strong> for PDE solving compared to traditional neural networks. However, generalization to new conditions remains a challenge. In contrast, <strong>KAN-ODE</strong> provides better generalization at the cost of higher computational overhead.
            </p>
            


            <h1>Comparison of Neural Models (Maxwell's Equation)</h1>

    <style>
        table {
            border-collapse: collapse;
            width: 100%; /* Optional: Set the table width */
        }
        th, td {
            padding: 15px; /* Adjust the padding as needed */
            text-align: center; /* Optional: Center-align text */
        }
    </style>

    <table>
        <thead>
            <tr>
                <th>Aspect</th>
                <th>Fourier Neural Operator (FNO)</th>
                <th>Neural-ODE (N-ODE)</th>
                <th>Kolmogorov-Arnold Networks ODE (KAN-ODE)</th>
            </tr>
        </thead>
        <tbody>
            <tr>
                <td><b>Hyperparameters</b></td>
                <td>Mode = 8, Width = 32</td>
                <td>Hidden Dimension = 75</td>
                <td>Grid = 5, Node = 10</td>
            </tr>
            <tr>
                <td><b>Scaling Law</b></td>
                <td>\( 1/N^{2-3} \)</td>
                <td>\( 1/N^{1.3} \)</td>
                <td>\( 1/N^{2.5-6} \)</td>
            </tr>
            <tr>
                <td><b>Minimum Training Loss</b></td>
                <td>\( 10^{-12} \, \text{to} \, 10^{-7} \)</td>
                <td>\( \sim 10^{-7} \)</td>
                <td>\( 10^{-7} \, \text{to} \, 10^{-5} \)</td>
            </tr>
            <tr>
                <td><b>Training Time</b></td>
                <td>Fastest (Baseline)</td>
                <td>~4x FNO</td>
                <td>~8x FNO</td>
            </tr>
            <tr>
                <td><b>Inference Time</b></td>
                <td>Fastest (Baseline)</td>
                <td>~70x FNO</td>
                <td>~100x FNO</td>
            </tr>
            <tr>
                <td><b>Interpolation Test Loss</b></td>
                <td>\( 10^{-5} \)</td>
                <td>\( 10^{-5} \)</td>
                <td>\( 10^{-5} \)</td>
            </tr>
            <tr>
                <td><b>Extrapolation Test Loss</b></td>
                <td>\( 10^{-2} \)</td>
                <td>\( 10^{-2} \)</td>
                <td>\( 10^{-2} \)</td>
            </tr>
            <tr>
                <td><b>Initial Condition Test Loss</b></td>
                <td>Struggles with larger variation in initial conditions</td>
                <td>Better performance than FNO</td>
                <td>Better performance than FNO</td>
            </tr>
            <tr>
                <td><b>Key Insights</b></td>
                <td>Fastest and most efficient</td>
                <td>Computationally expensive, but robust</td>
                <td>Balances performance and flexibility</td>
            </tr>
        </tbody>
    </table>

    <h3>Limitations</h3>
    <p>
        This study has several limitations due to constraints in time and computational resources. Our benchmarking was restricted to a subset of metrics, preventing a more exhaustive analysis. For instance, while <strong>FNO</strong> is known for its grid independence, where models can be trained on coarse grids and applied to finer grids, we did not fully explore this capability. Additionally, the generalization of these models across different types of PDEs remains untested, and using generic architectures for distinct PDEs may have led to suboptimal performance.   </p>

    <p>
        Another notable limitation is the training process for <strong>KAN-ODE</strong> and <strong>Neural ODE</strong>. Due to limited resources, we had to restrict the number of training epochs and use relatively high learning rates. This likely hindered the models from reaching their full potential, affecting the final results. A more comprehensive training approach could yield better performance and more reliable comparisons.
    </p>    
    
    <h3>Future Work</h3>
    <p>
        In future, we plan to extend this study in several directions. Expanding the benchmarking to a wider range of PDEs and exploring additional metrics will provide a more complete understanding of these architectures. Incorporating physics-informed models, such as PINNs and PINO, could improve performance by embedding physical constraints into the learning process. Furthermore, developing hybrid architectures that combine the strengths of neural networks and neural operators may offer a promising path toward more flexible and efficient PDE solvers.
    </p>

    <p>
        These directions will help address the limitations of our current study and contribute to the development of more robust neural-based methods for solving complex PDEs.
    </p>
</section>

        <section id="References">
        <h2>References</h2>
        <ol>
            <li id="ref1">
                Cuomo, S., Di Cola, V.S., Giampaolo, F., Rozza, G., Raissi, M. and Piccialli, F., 2022. Scientific machine learning through physics–informed neural networks: Where we are and what’s next. Journal of Scientific Computing, 92(3), p.88.<em></em>
                <a href="https://link.springer.com/article/10.1007/s10915-022-01939-z" target="_blank">Read Paper</a>
            </li>
            <li id="ref2">
                Cai, S., Mao, Z., Wang, Z., Yin, M. and Karniadakis, G.E., 2021. Physics-informed neural networks (PINNs) for fluid mechanics: A review. Acta Mechanica Sinica, 37(12), pp.1727-1738.
                <a href="https://link.springer.com/article/10.1007/s10409-021-01148-1" target="_blank">Read Paper</a>
            </li>
            <li id="ref3">
                Raissi, M., Perdikaris, P. and Karniadakis, G.E., 2019. Physics-informed neural networks: A deep learning framework for solving forward and inverse problems involving nonlinear partial differential equations. Journal of Computational physics, 378, pp.686-707.
                <a href="https://www.sciencedirect.com/science/article/pii/S0021999118307125" target="_blank">Read Paper</a>
            </li>
            <li id="ref4">
                Li, Z., Kovachki, N., Azizzadenesheli, K., Liu, B., Bhattacharya, K., Stuart, A. and Anandkumar, A., 2020. Fourier neural operator for parametric partial differential equations. arXiv preprint arXiv:2010.08895.
                <a href="https://arxiv.org/abs/2010.08895" target="_blank">Read Paper</a>
            </li>
            <li id="ref5">
                Kovachki, N., Li, Z., Liu, B., Azizzadenesheli, K., Bhattacharya, K., Stuart, A. and Anandkumar, A., 2023. Neural operator: Learning maps between function spaces with applications to pdes. Journal of Machine Learning Research, 24(89), pp.1-97.
                <a href="https://arxiv.org/abs/2108.08481" target="_blank">Read Paper</a>
            </li>
            <li id="ref6">
                Chen, R.T., Rubanova, Y., Bettencourt, J. and Duvenaud, D.K., 2018. Neural ordinary differential equations. Advances in neural information processing systems, 31.
                <a href="https://arxiv.org/abs/1806.07366" target="_blank">Read Paper</a>
            </li>
            <li id="ref7">
                Yan, H., Du, J., Tan, V.Y. and Feng, J., 2019. On robustness of neural ordinary differential equations. arXiv preprint arXiv:1910.05513.
                <a href="https://arxiv.org/abs/1910.05513" target="_blank">Read Paper</a>
            </li>
            <li id="ref8">
                Koenig, B.C., Kim, S. and Deng, S., 2024. KAN-ODEs: Kolmogorov–Arnold network ordinary differential equations for learning dynamical systems and hidden physics. Computer Methods in Applied Mechanics and Engineering, 432, p.117397.
                <a href="https://arxiv.org/abs/2407.04192" target="_blank">Read Paper</a>
            </li>
            
            <li id="ref9">
                Liu, Z., Wang, Y., Vaidya, S., Ruehle, F., Halverson, J., Soljačić, M., Hou, T.Y. and Tegmark, M., 2024. Kan: Kolmogorov-arnold networks. arXiv preprint arXiv:2404.19756.
                <a href="https://arxiv.org/abs/2404.19756" target="_blank">Read Paper</a>
            </li>
            <li id="ref10">
                Vaca-Rubio, C.J., Blanco, L., Pereira, R. and Caus, M., 2024. Kolmogorov-arnold networks (kans) for time series analysis. arXiv preprint arXiv:2405.08790.
                <a href="https://arxiv.org/abs/2405.08790" target="_blank">Read Paper</a>
            </li>
            
            <li id="ref11">
                Li, Z., Zheng, H., Kovachki, N., Jin, D., Chen, H., Liu, B., Azizzadenesheli, K. and Anandkumar, A., 2024. Physics-informed neural operator for learning partial differential equations. ACM/JMS Journal of Data Science, 1(3), pp.1-27.
                <a href="https://arxiv.org/abs/2111.03794" target="_blank">Read Paper</a>
            </li>
            <li id="ref12">
                Goswami, S., Bora, A., Yu, Y. and Karniadakis, G.E., 2023. Physics-informed deep neural operator networks. In Machine Learning in Modeling and Simulation: Methods and Applications (pp. 219-254). Cham: Springer International Publishing.
                <a href="https://arxiv.org/abs/2207.05748" target="_blank">Read Paper</a>
            </li>
            <li id="ref13">
                Piao, S., Gu, H., Wang, A. and Qin, P., 2024. A Domain-adaptive Physics-informed Neural Network for Inverse Problems of Maxwell's Equations in Heterogeneous Media. IEEE Antennas and Wireless Propagation Letters.
                <a href="https://arxiv.org/abs/2308.06436" target="_blank">Read Paper</a>
            </li>
            <li id="ref14">
                He, K., Zhang, X., Ren, S. and Sun, J., 2016. Deep residual learning for image recognition. In Proceedings of the IEEE conference on computer vision and pattern recognition (pp. 770-778).
                <a href="https://arxiv.org/abs/1512.03385" target="_blank">Read Paper</a>
            </li>
        </ol>
        </section>
    </div>

    <footer>
        <p>&copy; 2024 MIT Deep Learning 6.7960 Final Project | 
            <a href="https://scholar.google.com/citations?user=gOHWuaMAAAAJ&hl=en" target="_blank">Pratyush Anand</a> & 
            <a href="https://scholar.google.com/citations?user=_03ge3YAAAAJ&hl=en" target="_blank">Louis Follet</a>
        </p>
    </footer>

</body>
</html>


