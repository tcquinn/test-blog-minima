---
layout: post
title:  "Redesigning the API for our sequential Monte Carlo code"
author: Ted Quinn
categories: ted
math: true
---

A sequential Monte Carlo model is defined by four (conditional) probability models:
* An *initial state model* which describes how the system starts out
* A *transition model* which describes how the system evolves from one time to the next
* An *observation model* which how our measurements depend upon the state of the system, and
* A *parameter model* which describes the likelihood of different parameter values (for simple cases, we might just say that are parameters are fixed values with probability 1)

These four components can take different forms depending on our assumptions (e.g., the observation model for a linear Gaussian system is typically specified by two matrices, while a general nonlinear system requires that we specify a multivariate function) and the kinds of inferences we want to make (e.g., an observation model in log PDF form is sufficient for inferring the state of the system from measurements, but simulating the evolution of the state and our measurements together requires a sampling function). Here is a list of potential objects that might define a sequential Monte Carlo model.

<table>
  <thead>
    <tr>
      <th>Component of model</th>
      <th>Description</th>
      <th>Notation</th>
      <th>Form</th>
      <th>Python object</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Initial state model</td>
      <td>Probability of a particular initial state given parameters</td>
      <td>$$P(\mathbf{X}_0 \vert \mathbf{\Theta})$$</td>
      <td>Log PDF</td>
      <td><code>initial_state_given_parameters_log_pdf(initial_state, parameters)</code></td>
    </tr>
    <tr>
      <td></td>
      <td></td>
      <td></td>
      <td>Sample generator</td>
      <td><code>initial_state_given_parameters_samples(parameters, N)</code></td>
    </tr>
    <tr>
      <td></td>
      <td></td>
      <td></td>
      <td>Linear Gaussian model</td>
      <td><code>initial_state_gaussian(parameters)</code></td>
    </tr>
    <tr>
      <td>Transition model</td>
      <td>Probability of a particular current state given previous state and parameters</td>
      <td>$$P(\mathbf{X}_t \vert \mathbf{X}_{t-1}, \mathbf{\Theta})$$</td>
      <td>Log PDF</td>
      <td><code>current_state_given_previous_state_and_parameters_log_pdf(current_state, previous_state, current_t, previous_t, parameters)</code></td>
    </tr>
    <tr>
      <td></td>
      <td></td>
      <td></td>
      <td>Sample generator</td>
      <td><code>current_state_given_previous_state_and_parameters_samples(previous_state, current_t, previous_t, parameters, N)</code></td>
    </tr>
    <tr>
      <td></td>
      <td></td>
      <td></td>
      <td>Linear Gaussian model</td>
      <td><code>transition_matrix(current_t, previous_t, parameters)<br>transition_noise_covariance(current_t, previous_t, parameters)<br>control_vector(current_t, previous_t, parameters)</code></td>
    </tr>
    <tr>
      <td>Observation model</td>
      <td>Probability of a particular observation given state and parameters</td>
      <td>$$P(\mathbf{Y}_t \vert \mathbf{X}_{t}, \mathbf{\Theta})$$</td>
      <td>Log PDF</td>
      <td><code>current_observation_given_current_state_and_parameters_log_pdf(current_observation, current_state, current_t, parameters)</code></td>
    </tr>
    <tr>
      <td></td>
      <td></td>
      <td></td>
      <td>Sample generator</td>
      <td><code>current_observation_given_current_state_and_parameters_samples(current_state, current_t, parameters, N)</code></td>
    </tr>
    <tr>
      <td></td>
      <td></td>
      <td></td>
      <td>Linear Gaussian model</td>
      <td><code>observation_matrix(current_t, parameters)<br>observation_noise_covariance(current_t, parameters)</code></td>
    </tr>
    <tr>
      <td>Parameter model</td>
      <td>Probability of a particular set of parameter values</td>
      <td>$$P(\mathbf{\Theta})$$</td>
      <td>Log PDF</td>
      <td><code>parameters_log_pdf(parameters)</code></td>
    </tr>
    <tr>
      <td></td>
      <td></td>
      <td></td>
      <td>Sample generator</td>
      <td><code>parameters_samples(N)</code></td>
    </tr>
  </tbody>
</table>

Once we've defined such a model, here are some of the things we might want to calculate with it:

<table>
  <thead>
    <tr>
      <th>Output</th>
      <th>Description</th>
      <th>Notation</th>
      <th>Form</th>
      <th>Python object</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Infer state over time from measurements over time</td>
      <td>TBD</td>
      <td>TBD</td>
      <td>TBD</td>
      <td><code>TBD</code></td>
    </tr>
    <tr>
      <td>Simulate state over time and measurements over time</td>
      <td>TBD</td>
      <td>TBD</td>
      <td>TBD</td>
      <td><code>TBD</code></td>
    </tr>
    <tr>
      <td>Infer parameters from state over time and measurements over time</td>
      <td>TBD</td>
      <td>TBD</td>
      <td>TBD</td>
      <td><code>TBD</code></td>
    </tr>
  </tbody>
</table>
